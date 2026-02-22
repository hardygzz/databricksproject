# 📊 Proyecto ETL – Morosidad de Cajas Municipales (CMAC)
📌 Descripción del Proyecto

Este proyecto implementa un pipeline ETL completo en Databricks (PySpark) bajo arquitectura Medallion (Bronze, Silver, Gold) utilizando:

    Azure Data Lake Storage Gen2 (Raw)
    Unity Catalog
    Managed Identity (MI)
    Delta Lake
    Databricks SQL Dashboard
    CI/CD con GitHub Actions

El objetivo es analizar la morosidad de las Cajas Municipales (CMAC) para los años 2024 y 2025, a partir de los boletines oficiales en formato Excel publicados por la SBS.

# 🏗️ Arquitectura
🔹 Arquitectura General
            
            ADLS Gen2 (RAW - abfss)
                    ↓
            Databricks (PySpark ETL)
                    ↓
            Bronze (Delta)
                    ↓
            Silver (Delta)
                    ↓
            Gold (Modelo Analítico)
                    ↓
            Databricks SQL Dashboard

🔹 Componentes

    Fuente RAW: Azure Data Lake Storage Gen2
    Conexión: Managed Identity
    Procesamiento: Databricks Notebooks en PySpark
    Gobernanza: Unity Catalog
    Visualización: Databricks SQL Dashboard
    CI/CD: GitHub Actions

# 📂 Estructura del Repositorio

    .github/workflow/     → Pipeline CI/CD
    certificaciones/      → Evidencias visuales de certificación / ejecución
    dashboard/            → JSON / imágenes del dashboard
    datasets/             → Muestras de datos (csv/json si aplica)
    evidencias/           → Capturas de ejecución Bronze/Silver/Gold
    PrepaMB/              → Scripts de preparación de ambiente
    PROCESO/              → Notebooks ETL (Bronze, Silver, Gold)
    REVERSION/            → Scripts DROP y limpieza lógica
    SEGURIDAD/            → Scripts de GRANTS y permisos
    README.md

# 📥 Fuente de Datos (RAW)

    Ubicación: abfss://raw@<storage-account>.dfs.core.windows.net/
    Formato: .xls
    Periodos: 2024 y 2025
    Conexión: Managed Identity
    No se utiliza DBFS ni Volumes como capa raw.

# 🔄 Pipeline ETL
🥉 Bronze Layer

    Lectura de archivos .xls
    Identificación dinámica de hojas:
        Sheet 13 → Ratios de morosidad por días
        Sheet 16 → Morosidad por tipo y modalidad
    Agregado de metadata:
        anio
        periodo_mes
        sheet_num
        source_file
    Escritura en tablas Delta:
        bronze_sbs_morosidad_2024
        bronze_sbs_morosidad_2025

# 🥈 Silver Layer
Dataset 1 – Morosidad por Días (Wide)
    
    Tabla:
        silver_morosidad_dias_wide

Transformaciones:

    Filtrado por CMAC
    Limpieza de valores
    Conversión a tipo numérico

Dataset 2 – Morosidad por Tipo (Long)
    
    Tabla:
        silver_morosidad_tipo_long

Transformaciones:

    Detección dinámica del header 
    Limpieza de conceptos
    Eliminación de filas informativas
    Unpivot dinámico
    Conversión a double

# 🥇 Gold Layer

Modelo analítico listo para consumo:

    1️⃣ Tabla Long Analítica
        gold_morosidad_tipo_long
            
    2️⃣ Tabla Pivot para BI
        gold_morosidad_tipo_pivot
    
Pivot por concepto 
246 filas finales 
Dataset listo para visualización

# 📊 Dashboard

Se construyó un Dashboard en Databricks SQL que incluye:
        
        Evolución temporal de morosidad por CMAC
        Comparación por tipo de crédit
        Top CMAC con mayor morosidad
        Análisis por modalidad

Archivos relacionados disponibles en:

        dashboard/

# 🔐 Seguridad
Scripts ubicados en: SEGURIDAD/
Incluyen:

        Creación de esquemas
        GRANT SELECT sobre tablas Gold
        Asignación a usuarios/grupos
        
Ejemplo:

        GRANT SELECT ON TABLE sbsrisk_dev.gold.gold_morosidad_tipo_pivot TO `analistas`;

# 🔁 Reversión
Ubicado en:

        REVERSION/

Incluye scripts DROP:

        DROP TABLE IF EXISTS sbsrisk_dev.gold.gold_morosidad_tipo_pivot;
        DROP TABLE IF EXISTS sbsrisk_dev.silver.silver_morosidad_tipo_long;
        DROP TABLE IF EXISTS sbsrisk_dev.bronze.bronze_sbs_morosidad_2024;

# ⚙️ Preparación de Ambiente

Ubicado en:

        PrepaMB/

ncluye:

    Creación de catalog
    Creación de schemas
    External locations
    Storage credentials
    Configuración Managed Identity

# 🚀 CI/CD

Pipeline ubicado en:

    .github/workflow/

Incluye:

    Validación del código
    Deploy automático
    Ejecución de bundle / notebooks
    Integración con Databricks

# 📈 Resultados

    ✔ 2 datasets procesados
    ✔ Arquitectura Medallion implementada
    ✔ Uso exclusivo de Managed Identity
    ✔ 2 años procesados (2024–2025)
    ✔ 22 conceptos detectados
    ✔ 11 CMAC procesadas
    ✔ Modelo Gold listo para BI
    ✔ Dashboard implementado

# 📌 Consideraciones Técnicas

    ETL desarrollado 100% en PySpark
    No se utiliza DBFS como raw
    No se utilizan Volumes
    Unity Catalog habilitado
    Modelo preparado para escalabilidad futura

# 📎 Entregable Final

Repositorio público de GitHub que contiene:

    Código ETL
    Arquitectura
    Dashboard
    CI/CD
    Evidencias
    Scripts de seguridad
    Scripts de reversión

# 👨‍💻 Autor

Proyecto desarrollado como parte del curso de Ingeniería de Datos con Databricks.

By: HardyGzz

Data Engineering | Azure Databricks | Delta Lake | CI/CD
