# BLOQUE 4 – Resolución de incidencias en el proceso ETL
## 1. Problema detectado
Durante la ejecución del pipeline ETL **P02_transport_data.hpl** se detectó un error durante la fase de carga de datos en el esquema `staging`.
Mensaje observado en el log de Apache Hop:
ERROR: duplicate key value violates unique constraint
"staging_res_partner_pkey"
Esto indica que el proceso intentó insertar registros que ya existían en la
tabla destino.
El problema se detectó al revisar el log de ejecución del pipeline y verificar que el proceso finalizó con errores.
---
# 2. Análisis de la posible causa
Tras revisar la configuración del pipeline y los datos del sistema se
identificaron las siguientes posibles causas:
### 2.1 Duplicación de datos en origen
La tabla `res_partner` del ERP contiene registros que ya habían sido cargados
previamente en el esquema `staging`.
### 2.2 Carga completa sin control de duplicados
El pipeline estaba configurado como **carga completa**, lo que provoca que en
cada ejecución se intente insertar todos los registros nuevamente.
### 2.3 Falta de transformación de control
El flujo ETL no incluía ningún paso para:
- eliminar duplicados
- comprobar existencia previa en destino
- aplicar estrategia de actualización
Esto provocó la violación de la clave primaria.
---
# 3. Solución técnica aplicada
Se aplicaron las siguientes acciones correctivas.
## 3.1 Limpieza de la tabla staging
Para evitar conflictos con datos previamente cargados se realizó una limpieza
inicial de la tabla destino.
Ejemplo de consulta SQL ejecutada en PostgreSQL:
```sql
TRUNCATE TABLE staging.res_partner;
Esto permitió reiniciar el proceso de carga.
```