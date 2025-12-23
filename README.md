# Data Warehouse & ETL para Análisis Bancario

> **Trabajo Práctico Integrador** > **Cátedra:** Análisis de Datos Empresariales (ADE) - 2025  
> **Institución:** U.T.N. Facultad Regional Resistencia  

Este repositorio contiene el desarrollo integral de una solución de **Business Intelligence** para una entidad bancaria. El proyecto abarca desde el diseño del modelo dimensional hasta la implementación de procesos ETL complejos y la construcción de cubos OLAP para la toma de decisiones estratégicas sobre préstamos otorgados.



## Tabla de Contenidos

1. [Descripción del Proyecto](#descripción-del-proyecto)
2. [Arquitectura de la Solución](#arquitectura-de-la-solución)
3. [Modelo de Datos](#modelo-de-datos)
4. [Proceso ETL (SSIS)](#proceso-etl-ssis)
5. [Cubo OLAP y Análisis (SSAS)](#cubo-olap-y-análisis-ssas)
6. [Equipo de Desarrollo](#equipo-de-desarrollo)



## Descripción del Proyecto

El objetivo principal es construir un **Data Warehouse (DW)** centralizado que permita al banco analizar el comportamiento de sus préstamos. La solución busca responder preguntas críticas de negocio, tales como:

* ¿Cuál es la tasa de interés promedio por tipo de préstamo y sucursal?
* ¿Qué porcentaje de préstamos resultan impagos por región y año?
* ¿Cómo impactan los descuentos (fidelidad, seguros, VIP) en la tasa final?
* Análisis de tendencias de montos otorgados (muy alto, alto, medio, bajo).

El proyecto se dividió en tres fases estratégicas:
1.  **Diseño Conceptual y Lógico:** Definición de hechos, dimensiones y granularidad.
2.  **Implementación ETL:** Carga, limpieza y transformación de datos usando SQL Server Integration Services (SSIS).
3.  **Explotación de Datos:** Creación de cubos multidimensionales con SQL Server Analysis Services (SSAS) y visualización.



## Arquitectura de la Solución

La solución sigue una arquitectura clásica de **Business Intelligence**:

1.  **Fuentes de Datos:** Archivos planos y bases de datos transaccionales con información de préstamos, clientes y sucursales.
2.  **Staging Area:** Base de datos intermedia (`staging_prestamos`) donde se realiza la limpieza, normalización de tipos de datos y pre-procesamiento antes de cargar el DW.
3.  **Data Warehouse:** Base de datos relacional con esquema en estrella.
4.  **Cubo OLAP:** Modelo multidimensional para consultas analíticas rápidas.

### Tecnologías Utilizadas
* **Motor de Base de Datos:** Microsoft SQL Server.
* **ETL:** SQL Server Integration Services (SSIS).
* **OLAP:** SQL Server Analysis Services (SSAS).
* **Modelado:** ER-Studio / SQL Server Management Studio.
* **Lenguajes:** T-SQL.



## Modelo de Datos

El diseño sigue un **Esquema en Estrella (Star Schema)** centrado en el hecho "Préstamo".

### Tabla de Hechos: `FactPrestamos`
Almacena cada préstamo emitido con una granularidad de **1 fila por préstamo**.
* **Métricas:** `MontoPrestamo`, `TasaFinal`, `EstadoPago` (Pagado/Impago).
* **Foreign Keys:** Hacia todas las dimensiones mencionadas abajo.

### Dimensiones
* **DimPrestatario:** Datos de los clientes (Nombre, Ingreso, Edad, Género). *Incluye lógica SCD Tipo 2.*
* **DimSucursal:** Información de las sucursales bancarias.
* **DimTiempo:** Dimensión canónica para análisis temporal (Año, Semestre, Trimestre, Mes, Día, Flag Feriado).
* **DimUbicacion:** Geografía normalizada (Ciudad, Provincia, Región). Compartida por Sucursales y Prestatarios (Role-Playing Dimension conceptual).
* **DimTasa:** Tipos de tasa (Fija/Variable) y valores históricos. *Incluye lógica SCD Tipo 2.*
* **DimDescuento:** Tipos de descuentos aplicados (Fidelidad, VIP, Seguros).



## Proceso ETL (SSIS)

El proceso de Extracción, Transformación y Carga es el núcleo del proyecto, orquestado mediante **Control Flows** y **Data Flows** en Visual Studio.

### Características Clave del ETL:
1.  **Limpieza de Staging:** Truncado y recarga de tablas intermedias en cada ejecución para asegurar frescura.
2.  **Normalización de Datos:**
    * Unificación de géneros (ej: 'M', 'Masculino' -> 'M').
    * Conversión de tipos de datos para fechas y montos.
3.  **Manejo de Dimensiones Cambiantes (SCD - Tipo 2):**
    * Implementado en `DimPrestatario` (para cambios en *Ingreso Anual* y *Régimen*) y `DimTasa`.
    * Permite mantener historia de los cambios, cerrando la vigencia del registro anterior (actualizando `FechaHasta`) e insertando uno nuevo.
4.  **Carga Incremental:**
    * Lógica para detectar y procesar solo los nuevos registros o modificaciones en las fuentes (`CambiosPrestamos`, `CambiosClientes`), optimizando el tiempo de procesamiento.
5.  **Lookups y Merge Joins:** Utilizados extensivamente para resolver claves subrogadas y cruzar información entre fuentes heterogéneas (ej: cruzar Clientes con Ubicaciones).



## Cubo OLAP y Análisis (SSAS)

Se diseñó un cubo para facilitar operaciones de **Slice & Dice**, **Drill Down** y **Roll Up**.

### Medidas Principales
* **Recuento de Préstamos:** Cantidad total de operaciones.
* **Promedios:** Tasa de interés promedio, Monto promedio.
* **Mínimos/Máximos:** Tasas extremas por tipo de préstamo.

### Consultas de Negocio Resueltas
El cubo permite responder preguntas complejas visualizadas en herramientas de BI:
1.  **Análisis Geográfico:** Cantidad de préstamos por *Provincia* y *Ciudad*.
2.  **Riesgo:** Evolución de préstamos impagos por *Año* y *Región*.
3.  **Segmentación:** Préstamos categorizados por monto (Muy Alto, Alto, Medio, Bajo, Muy Bajo) y *Sucursal*.
4.  **Evolución Temporal:** Tendencia de entrega de préstamos mes a mes (Drill down de Año a Mes).


## Equipo de Desarrollo

**Grupo N°5 - UTN FRRe**

* **Almiron, Carlos Federico**
* **Cordoba, Rodrigo**
* **Gonzalez Urbieta, Enzo**
* **Lezcano, Rafael**
* **Gonzalez Zago, Joaquin**

---
*Este proyecto fue realizado con fines académicos para la cátedra de Análisis de Datos Empresariales, ciclo 2025.*
