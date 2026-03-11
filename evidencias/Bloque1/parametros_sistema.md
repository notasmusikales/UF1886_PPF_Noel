# BLOQUE 1.1 – Parámetros del sistema que influyen en el rendimiento
## 1. Objetivo
Identificar los principales parámetros técnicos que afectan al rendimiento del
entorno compuesto por:
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
Resultado resumido:
- Procesador: 11th Gen Intel(R) Core(TM) i5-1135G7 @ 2.40GHz
- Núcleos: 4
- Procesadores lógicos: 8
- Velocidad máxima: 2419

Interpretación técnica:
- La CPU disponible influye en el rendimiento de Docker Desktop, Odoo,
PostgreSQL y Apache Hop. Una alta carga puede ralentizar la ejecución del ERP
y de los procesos ETL.

### 2.2 Memoria
**Comando utilizado:**
```powershell
Get-CimInstance Win32_OperatingSystem | Select-Object TotalVisibleMemorySize,FreePhysicalMemory
```

Resultado resumido:

- Memoria total: 16507756
- Memoria libre: 6868236

Interpretación técnica:
- La memoria disponible condiciona el comportamiento de Docker Desktop, los
contenedores y Apache Hop. Si la memoria libre es baja, puede producirse
degradación del rendimiento general.

### 2.3 Espacio en disco
**Comando utilizado:**
```powershell
Get-PSDrive -PSProvider FileSystem
```
Resultado resumido:
- Unidad principal: C:\  
- Espacio usado:  178,90GB
- Espacio libre:  58,93GB

Interpretación técnica:
- El espacio en disco afecta al almacenamiento de datos, logs, imágenes Docker,
volúmenes y ficheros temporales generados por PostgreSQL y Apache Hop.

### 2.4 Procesos activos
**Comandos utilizados:**
```powershell
Get-Process | Sort-Object CPU -Descending | Select-Object -First 10
Get-Process | Sort-Object WorkingSet -Descending | Select-Object -First 10
```
Resultado resumido:
- Procesos con mayor CPU:
```
  Handles  NPM(K)    PM(K)      WS(K)     CPU(s)     Id  SI ProcessName
-------  ------    -----      -----     ------     --  -- -----------
    597      50   315788     347608      49,98  13112  13 Code
    516      52   376332     410496      21,61  10204  13 Code
   1272      58   140396     170820      19,08  14012  13 com.docker.backend
    360      27    86956     145596      18,98  11992  13 Docker Desktop
    376      35   174544     247928      18,84   3776  13 chrome
    791      30   164316     193820      18,70   1416  13 Code
   4935     142   203432     352428      18,09  13252  13 explorer
   1810      80    83312     217416      16,08  14748  13 chrome
   1306      70   121236     183028      14,95  15316  13 Code
   1054      38   198180     227580      13,84   1608  13 chrome
```
- Procesos con mayor uso de memoria:
```
Handles  NPM(K)    PM(K)      WS(K)     CPU(s)     Id  SI ProcessName
-------  ------    -----      -----     ------     --  -- -----------
      0       0  1339064    1235844              7868   0 vmmemWSL
    518      52   386172     423588      23,95  10204  13 Code
   1081     247   405360     375072              4840   0 MsMpEng
    597      51   322092     352952      57,86  13112  13 Code
   4923     142   203624     352668      18,95  13252  13 explorer
    376      35   174544     247928      18,84   3776  13 chrome
   1054      38   198180     227580      13,84   1608  13 chrome
      0       0     2356     222764              3584   0 Memory Compression
   1810      79    83336     217424      16,20  14748  13 chrome
    795      30   163456     199048      20,44   1416  13 Code
```


Interpretación técnica:
- El análisis de procesos permite detectar si Docker Desktop, Java/Apache Hop u
otros procesos del sistema están consumiendo demasiados recursos.

## 3. Análisis de contenedores Docker
### 3.1 Contenedores activos
**Comando utilizado:**
```powershell
docker ps
```
```
CONTAINER ID   IMAGE                COMMAND                  CREATED        STATUS          PORTS                                                                                      NAMES
9256787f7787   odoo:18.0            "/usr/bin/odoo -c /e…"   23 hours ago   Up 12 minutes   0.0.0.0:8001->8069/tcp, [::]:8001->8069/tcp, 0.0.0.0:8002->8072/tcp, [::]:8002->8072/tcp   odoo.18
14e42a8ff366   postgres:16-alpine   "docker-entrypoint.s…"   23 hours ago   Up 12 minutes   0.0.0.0:5432->5432/tcp, [::]:5432->5432/tcp                                                postgres.db
```

Resultado resumido:
- Contenedor Odoo: odoo.18
- Contenedor PostgreSQL: postgres.db
- Estado: Up

### 3.2 Consumo de recursos de contenedores
**Comando utilizado:**
```powershell
docker stats --no-stream
```
```
CONTAINER ID   NAME          CPU %     MEM USAGE / LIMIT     MEM %     NET I/O           BLOCK I/O        PIDS
9256787f7787   odoo.18       0.01%     90.09MiB / 7.629GiB   1.15%     4.41kB / 2.52kB   109MB / 0B       4
14e42a8ff366   postgres.db   0.00%     31.03MiB / 7.629GiB   0.40%     4.22kB / 2.97kB   30.2MB / 311kB   8
```

Resultado resumido:
- CPU usada por Odoo: 0.01%
- RAM usada por Odoo: 398MiB
- CPU usada por PostgreSQL: 0.00%
- RAM usada por PostgreSQL: 31.03MiB

Interpretación técnica:
- El consumo de recursos de los contenedores permite valorar si existe
saturación en el ERP o en la base de datos.