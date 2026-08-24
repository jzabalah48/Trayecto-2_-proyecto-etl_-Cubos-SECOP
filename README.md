Taller ETL – Cubo SECOP
Autor: Jurani Zabala Hernandez
El objetivo de este documento es el registro de los resultados obtenidos en Jupyterlab en los 4 notebooks solicitados en el ejercicio + 2 adicionales: T2BD_00_VerificacionEntorno.ipynb y T2BD_ConsultasSparkSQL.ipynb, correspondientes a de la implementación de ETL en todo el taller.
Se prepara inicialmente un notebook de verificación del entorno con el fin de Confirmar, antes de ejecutar el flujo ETL completo (Extraccion.ipynb -CuboDatos.ipynb- Transformacion.ipynb - Cargue.ipynb), que el entorno contenerizado (JupyterLab + Spark) está correctamente disponible: que se está ejecutando dentro del contenedor Docker (no en Windows nativo), que Spark inicializa, y que se puede leer y escribir en formato Parquet.
Extracción de datos SECOP II (Bronce)
Taller ETL – Cubo SECOP Autor: Jurani Zabala Hernandez Objetivo: Extraer la información de contratación pública desde la API oficial del SECOP II (SODA v3 / datos.gov.co) y depositarla, sin transformar, en el área bronce del data lake (HDFS), como insumo para los siguientes notebooks del taller. Alcance notebook: 1. Conexión a Spark con soporte HDFS/Hive de acuerdo a requerimientos solicitados 2. Extracción paginada y robusta desde la API pública del SECOP II, con manejo de errores y reintentos. 3. Persistencia cruda (sin transformar) en el data lake, área bronce, página por página (no se acumula el dataset completo en memoria). 4. Validación básica de la extracción (conteo de registros, esquema).


Implementación del Cubo de Datos SECOP (Hive) Taller ETL – Cubo SECOP Autor: Jurani Zabala Hernandez Objetivo: Traducir a un modelo tabular en Hive el diseño dimensional definido en CuboContratosPostgreSQL.drawio y dejarlo implementado en el data lake, listo para ser poblado por Transformacion.ipynb y Cargue.ipynb. Basado también en el esquema estrella con 7 dimensiones — incluyendo dim_tiempo como dimensión de rol, referenciada 3 veces desde el hecho (fecha de firma, inicio y fin) — y 1 tabla de hechos (hecho_contratos).


Normalización al Modelo del Cubo (Plata)
Taller ETL – Cubo SECOP Objetivo: Tomar los datos crudos del área bronce y normalizarlos exactamente en las 7 dimensiones + 1 hecho definidos en CuboDatos.ipynb, aplicando limpieza, tipado y generación de llaves. El resultado se persiste en el área silver (plata). Nota sobre dim_tiempo (dimensión de rol): el hecho referencia la misma dimensión de tiempo 3 veces (sk_tiempo_firma, sk_tiempo_inicio, sk_tiempo_fin). Para esto, dim_tiempo se construye a partir de la unión de las 3 fechas (firma, inicio, fin) — cada fecha distinta que aparezca en cualquiera de los 3 roles genera una sola fila en dim_tiempo, y las 3 llaves foráneas del hecho usan la misma función de hash sobre la fecha correspondiente, garantizando que apunten correctamente a esa fila compartida. El notebook es robusto a columnas faltantes (se rellenan como nulas y se reportan).
El notebook es robusto a columnas faltantes (se rellenan como nulas y se reportan).













Cargue del Cubo de Datos (Oro) Taller ETL – Cubo SECOP Autor: Jurani Zabala Hernandez Objetivo: Cargar en las tablas Hive del cubo (secop_dw, creadas en CuboDatos.ipynb) los datos normalizados del área SILVER (plata) (generados en Transformacion.ipynb), y validar el resultado con consultas SQL sobre hecho_contratos y sus 7 dimensiones — incluyendo la dimensión de rol dim_tiempo, usada 3 veces..




Explotación Analítica del Cubo SECOP Taller ETL – Cubo SECOP Autor: Jurani Zabala Hernandez Objetivo: Explotar el cubo secop_dw (poblado por Cargue.ipynb) con consultas SQL de negocio, distintas a las de validación de Cargue.ipynb.






