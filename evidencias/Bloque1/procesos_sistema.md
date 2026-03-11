# BLOQUE 1.2 – Procesos principales del sistema
## 1. Objetivo
Describir los procesos que intervienen en el flujo de datos entre:
- ERP (Odoo 18)
- Proceso ETL (Apache Hop)
- Almacén de datos (PostgreSQL – esquema staging)
Además, identificar las herramientas que permiten monitorizar el
funcionamiento del sistema y detectar incidencias.
---
# 2. Arquitectura del flujo de datos
## 2.1 Esquema general del flujo
Usuarios ERP
│
▼
Odoo 18 (contenedor Docker)
│
▼
PostgreSQL transaccional (contenedor Docker)
│
▼
Apache Hop ETL (ejecutándose en el host Windows)
│
▼
PostgreSQL – esquema staging (almacén de datos)
Descripción breve del flujo:
- Los usuarios interactúan con el sistema ERP Odoo.
- Las operaciones se almacenan en PostgreSQL transaccional.
- Apache Hop ejecuta procesos ETL que extraen datos desde PostgreSQL.
- Los datos transformados se cargan en el esquema **staging** del almacén de
datos.
---
# 3. Procesos del sistema ERP
## 3.1 Proceso de gestión en Odoo
**Sistema:** Odoo 18
**Ubicación:** Contenedor Docker
Descripción del proceso:
Odoo gestiona las operaciones del negocio, como:
- gestión de clientes
- registro de ventas
- gestión de productos
- pedidos y facturación
Estas operaciones generan datos que se almacenan en la base de datos
PostgreSQL utilizada por el ERP.
---
## 3.2 Base de datos transaccional
**Sistema gestor:** PostgreSQL
**Ubicación:** Contenedor Docker
Descripción del proceso:
La base de datos PostgreSQL almacena toda la información generada por el ERP,
incluyendo:
- clientes
- productos
- pedidos
- facturas
- transacciones comerciales
Esta base de datos actúa como **fuente de datos para el proceso ETL**.
---
# 4. Proceso ETL
## 4.1 Herramienta utilizada
Apache Hop ejecutándose en el **host Windows**.
El proceso ETL se encarga de:
- **Extract:** extraer datos desde PostgreSQL del ERP
- **Transform:** limpiar y transformar datos
- **Load:** cargar los datos en el almacén de datos
---
## 4.2 Transformaciones realizadas
Ejemplos de transformaciones que pueden realizarse en el pipeline:
- limpieza de espacios
- sustitución de valores nulos
- conversión de tipos de datos
- filtrado de registros
- estandarización de formatos
Estas transformaciones permiten preparar los datos para su análisis posterior.
---
# 5. Almacén de datos
## 5.1 Base de datos del almacén