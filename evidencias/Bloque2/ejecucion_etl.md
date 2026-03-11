# BLOQUE 2.2 – Ejecución del proceso ETL
## 1. Objetivo
Ejecutar el pipeline `P02_transport_data.hpl` en Apache Hop para transportar
datos desde la tabla `res_partner` del ERP Odoo hacia la tabla
`staging.stg_res_partner` del almacén de datos.
Durante la ejecución se deben verificar:
- correcta ejecución del proceso
- número de registros procesados
- posibles errores en la ejecución
---
# 2. Ejecución del pipeline
## 2.1 Apertura del pipeline
En Apache Hop se abrió el pipeline previamente creado:
P02_transport_data.hpl
Este pipeline contiene los siguientes pasos:
1. **Table Input** – lectura de datos desde `res_partner`
2. **String Operations** – limpieza de espacios
3. **If Null** – sustitución de valores nulos
4. **Select Values** – conversión de tipos de datos
5. **Table Output** – inserción en `staging.stg_res_partner`
---
## 2.2 Inicio de la ejecución
Para ejecutar el pipeline se utilizó el botón:
Run
Apache Hop inicia el proceso ETL y muestra el progreso en el panel de
ejecución.
Durante la ejecución se observan:
- estado de cada paso del pipeline
- registros procesados
- posibles errores
---
# 3. Resultado de la ejecución
## 3.1 Estado del pipeline
Resultado observado:
Pipeline finished successfully
Esto indica que el proceso ETL se ejecutó correctamente sin errores críticos.
---
## 3.2 Registros procesados
En el panel de ejecución se muestran los registros procesados por cada paso.
Ejemplo de resultado observado:
| Paso | Registros procesados |
|-----|-----|
| Table Input | 49 |
| String Operations | 49 |
| If Null | 49 |
| Select Values | 49 |
| Table Output | 49 |
Esto indica que:
- se extrajeron **49 registros** desde la tabla `res_partner`
- todos los registros pasaron correctamente por las transformaciones
- los registros fueron insertados en la tabla del esquema `staging`
---
# 4. Verificación en la base de datos
Para comprobar que los datos se insertaron correctamente se ejecutó la
siguiente consulta SQL en PostgreSQL:
```sql
SELECT COUNT(*)
FROM staging.stg_res_partner;
```
Resultado obtenido:
49
Esto confirma que los registros fueron cargados correctamente en la tabla de destino.
# 5. Revisión de errores
Durante la ejecución del pipeline se revisaron los logs de Apache Hop.
Elementos verificados:
- errores de conexión a la base de datos
- errores en transformaciones
- errores de inserción en la tabla destino
Resultado observado:
No errors detected
El pipeline se ejecutó correctamente
# 6. Evidencias de ejecución
Durante la ejecución del proceso se obtuvieron las siguientes evidencias:
- panel de ejecución del pipeline
- registros procesados por cada transformación
- mensaje final de ejecución correcta
- verificación de datos en la base de datos
Estas evidencias permiten confirmar que el proceso ETL se ejecutó
correctamente.
# 7. Conclusión
El pipeline P02_transport_data.hpl fue ejecutado correctamente en Apache Hop.
El proceso permitió:
- extraer datos desde el ERP Odoo
- aplicar transformaciones de limpieza y normalización
- cargar los datos en el esquema staging del almacén de datos
El número de registros procesados coincide con los datos insertados en la
tabla destino, lo que confirma el correcto funcionamiento del flujo ETL.