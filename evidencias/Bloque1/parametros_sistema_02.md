# BLOQUE 1.1 – Parámetros del sistema que influyen en el rendimiento
## 1. Objetivo
Identificar los principales parámetros técnicos que afectan al rendimiento del entorno compuesto por:
- Odoo 18 en contenedor Docker
- PostgreSQL transaccional en contenedor Docker
- PostgreSQL para almacén de datos / staging
- Apache Hop ejecutándose en el host Windows
---
## 2. Análisis del sistema anfitrión (Windows)
### 2.1 CPU
**Comando utilizado:**
```powershell
Get-CimInstance Win32_Processor | Select-Object Name,NumberOfCores,NumberOfLogicalProcessors,MaxClockSpeed
```
```Resultado resumido:
● Procesador:
● Núcleos:
● Procesadores lógicos:
● Velocidad máxima:
```
```
Interpretación técnica: La CPU disponible influye en el rendimiento de Docker Desktop, Odoo, PostgreSQL y Apache Hop. Una alta carga puede ralentizar la ejecución del ERP y de los procesos ETL.
```
### 2.2 Memoria
```Comando utilizado: Get-CimInstance Win32_OperatingSystem | Select-Object TotalVisibleMemorySize,FreePhysicalMemory
```` 
Resultado resumido:
● Memoria total:
● Memoria libre:
Interpretación técnica: La memoria disponible condiciona el comportamiento de Docker Desktop, los contenedores y Apache Hop. Si la memoria libre es baja, puede producirse degradación del rendimiento general.
### 2.3 Espacio en disco
Comando utilizado: Get-PSDrive -PSProvider FileSystem
Resultado resumido:
● Unidad principal:
● Espacio usado:
● Espacio libre:
Interpretación técnica: El espacio en disco afecta al almacenamiento de datos, logs, imágenes Docker, volúmenes y ficheros temporales generados por PostgreSQL y Apache Hop.
2.4 Procesos activos
Comandos utilizados: Get-Process | Sort-Object CPU -Descending | Select-Object -First 10 Get-Process | Sort-Object WorkingSet -Descending | Select-Object -First 10
Resultado resumido:
● Procesos con mayor CPU:
● Procesos con mayor uso de memoria:
Interpretación técnica: El análisis de procesos permite detectar si Docker Desktop, Java/Apache Hop u otros procesos del sistema están consumiendo demasiados recursos.
3. Análisis de contenedores Docker
3.1 Contenedores activos
Comando utilizado: docker ps
Resultado resumido:
● Contenedor Odoo:
● Contenedor PostgreSQL:
● Estado:
3.2 Consumo de recursos de contenedores
Comando utilizado: docker stats --no-stream
Resultado resumido:
● CPU usada por Odoo:
● RAM usada por Odoo:
● CPU usada por PostgreSQL:
● RAM usada por PostgreSQL:
Interpretación técnica: El consumo de recursos de los contenedores permite valorar si existe saturación en el ERP o en la base de datos.
4. Análisis de PostgreSQL
4.1 Conexiones y actividad
Consultas utilizadas: SELECT datname, numbackends FROM pg_stat_database ORDER BY numbackends DESC; SELECT pid, usename, datname, state, wait_event_type, wait_event FROM pg_stat_activity ORDER BY state, datname; SHOW max_connections; SHOW shared_buffers; SHOW work_mem;
Resultado resumido:
● Número de conexiones observadas:
● Bases de datos activas:
● Parámetros relevantes:
Interpretación técnica: Las conexiones abiertas y los parámetros de configuración influyen directamente en el rendimiento de Odoo y en la ejecución de procesos ETL sobre el almacén de datos.
5. Relación con Apache Hop
5.1 Procesos de Hop / Java en el host
Comando utilizado: Get-Process | Where-Object {$_.ProcessName -match "java|hop"} | Select-Object ProcessName,Id,CPU,WorkingSet
Resultado resumido:
● Procesos detectados:
● Consumo aproximado:
Interpretación técnica: Apache Hop se ejecuta en el host Windows y puede consumir CPU y memoria durante las transformaciones ETL. Esto puede repercutir en el rendimiento global del sistema.
6. Parámetros críticos identificados
Parámetro
Componente afectado
Posible impacto
CPU del host
Docker / Odoo / Hop
Lentitud general
RAM libre
Docker / PostgreSQL / Hop
Degradación o bloqueo
Espacio en disco
PostgreSQL / Docker / staging
Errores o falta de capacidad
Procesos activos
Windows / Docker / Hop
Competencia por recursos
Conexiones PostgreSQL
Odoo / DW / ETL
Saturación o latencia
7. Conclusión
Los parámetros más relevantes del entorno son el uso de CPU, la memoria disponible, el espacio en disco, los procesos activos y las conexiones a PostgreSQL. Estos elementos condicionan el rendimiento de Odoo 18, del gestor transaccional, del almacén de datos y de Apache Hop ejecutado en el host Windows.