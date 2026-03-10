# 1. Parámetros del sistema en Windows
### 1.2 CPU, memoria y procesos activos
```
systeminfo
```
### 1.3 Información del sistema operativo
```
Nombre de host:                            TF1-A08-PC07
Nombre del sistema operativo:              Microsoft Windows 11 Pro
Versión del sistema operativo:             10.0.26200 N/D Compilación 26200
Fabricante del sistema operativo:          Microsoft Corporation
Configuración del sistema operativo:       Estación de trabajo independiente
Tipo de compilación del sistema operativo: Multiprocessor Free
```
### 1.4 Información de memoria física

```
Memoria física total:                      16.121 MB
Memoria física disponible:                 5.715 MB
Memoria virtual: tamaño máximo:            28.409 MB
Memoria virtual: disponible:               15.325 MB
Memoria virtual: en uso:                   13.084 MB
```
### 1.5 Información de uso CPU

```
Get-Process | Sort-Object CPU -Descending | Select-Object -First 10
```
```
Handles  NPM(K)    PM(K)      WS(K)     CPU(s)     Id  SI ProcessName
-------  ------    -----      -----     ------     --  -- -----------
   1690      83   566612     367552     185,34   9744  12 java
    633      54   334556     345816     103,53   7916  12 Code
   1160      66   139084     160136      93,61   4432  12 com.docker.backend
    363      27    90740     140960      88,67   2708  12 Docker Desktop
   5398     144   229700     319616      79,53  18988  12 explorer
   1875      84   101384     207180      50,77  11112  12 chrome
   1342      46   223548     155204      44,53  17888  12 chrome
    505      52   376084     316360      42,30  21176  12 Code
    412      42   213452     219380      34,11  13900  12 chrome
    793      29   165332     172748      30,61   7460  12 Code
```
### 1.6 Información de la CPU
```
Get-CimInstance Win32_Processor | Select-Object Name,NumberOfCores,NumberOfLogicalProcessors,MaxClockSpeed
```

```
Name                                           NumberOfCores NumberOfLogicalProcessors MaxClockSpeed
----                                           ------------- ------------------------- -------------
11th Gen Intel(R) Core(TM) i5-1135G7 @ 2.40GHz             4                         8          2419

```
### 1.7 Espacio en disco
```
Get-PSDrive -PSProvider FileSystem
```
```
Name           Used (GB)     Free (GB) Provider      Root                                                                                                                                                           CurrentLocation
----           ---------     --------- --------      ----                                                                                                                                                           --------------- 
C                 179,44         58,39 FileSystem    C:\                                                                                                                                    Users\Alumno\Desktop\UF1886_PPF-E2-Noel 
D                  32,66        205,18 FileSystem    D:\
```
```
wmic logicaldisk get size,freespace,caption
```
```
Caption  FreeSpace     Size
C:       62741442560   255371243520  
D:       220306825216  255374389248
```
## 2. Estado de Odoo y PostgreSQL en Docker
### 2.1 Ver contenedores activos
```
docker ps
```
```
CONTAINER ID   IMAGE                COMMAND                  CREATED       STATUS       PORTS                                                                                      NAMES
9256787f7787   odoo:18.0            "/usr/bin/odoo -c /e…"   2 hours ago   Up 2 hours   0.0.0.0:8001->8069/tcp, [::]:8001->8069/tcp, 0.0.0.0:8002->8072/tcp, [::]:8002->8072/tcp   odoo.18
14e42a8ff366   postgres:16-alpine   "docker-entrypoint.s…"   2 hours ago   Up 2 hours   0.0.0.0:5432->5432/tcp, [::]:5432->5432/tcp                                                postgres.db
```
### 2.2 Ver consumo de recursos
```
docker stats --no-stream
```
```
CONTAINER ID   NAME          CPU %     MEM USAGE / LIMIT     MEM %     NET I/O           BLOCK I/O        PIDS
9256787f7787   odoo.18       0.01%     377.5MiB / 7.629GiB   4.83%     80.8MB / 90.3MB   221MB / 22.4MB   6
14e42a8ff366   postgres.db   0.00%     156.8MiB / 7.629GiB   2.01%     74.6MB / 80.5MB   125MB / 444MB    10
```
### 2.3 Ver detalle de un contenedor
```
docker inspect odoo.18 
docker inspect postgres-db
```
```
[
    {
        "Id": "9256787f7787c3723540c11183f95375f09a95643783b98f9f3d066522d98cdf",
        "Created": "2026-03-10T09:53:08.777473805Z",
        "Path": "/usr/bin/odoo",
        "Args": [
            "-c",
            "/etc/odoo/odoo.conf"
        ],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 651,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2026-03-10T09:53:09.406036891Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:d4cb3f0913d956b6f2dd6a0cd7b74b5df77f01b3d9e3291b36cafeddfaac6e5f",
        "ResolvConfPath": "/var/lib/docker/containers/9256787f7787c3723540c11183f95375f09a95643783b98f9f3d066522d98cdf/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/9256787f7787c3723540c11183f95375f09a95643783b98f9f3d066522d98cdf/hostname",
        "HostsPath": "/var/lib/docker/containers/9256787f7787c3723540c11183f95375f09a95643783b98f9f3d066522d98cdf/hosts",
        "LogPath": "/var/lib/docker/containers/9256787f7787c3723540c11183f95375f09a95643783b98f9f3d066522d98cdf/9256787f7787c3723540c11183f95375f09a95643783b98f9f3d066522d98cdf-json.log",
        "Name": "/odoo.18",
        "RestartCount": 0,
        "Driver": "overlayfs",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": [
```
```
docker inspect postgres-db
```
```
[
    {
        "Id": "14e42a8ff366ae2fa3cfd0caa6dbbe4c44246fa00654d1b97b7e6ddf8e72662a",
        "Created": "2026-03-10T09:53:08.6951511Z",
        "Path": "docker-entrypoint.sh",
        "Args": [
            "postgres"
        ],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 561,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2026-03-10T09:53:09.021090187Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:a5074487380d4e686036ce61ed6f2d363939ae9a0c40123d1a9e3bb3a5f344b4",
        "ResolvConfPath": "/var/lib/docker/containers/14e42a8ff366ae2fa3cfd0caa6dbbe4c44246fa00654d1b97b7e6ddf8e72662a/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/14e42a8ff366ae2fa3cfd0caa6dbbe4c44246fa00654d1b97b7e6ddf8e72662a/hostname",
        "HostsPath": "/var/lib/docker/containers/14e42a8ff366ae2fa3cfd0caa6dbbe4c44246fa00654d1b97b7e6ddf8e72662a/hosts",
        "LogPath": "/var/lib/docker/containers/14e42a8ff366ae2fa3cfd0caa6dbbe4c44246fa00654d1b97b7e6ddf8e72662a/14e42a8ff366ae2fa3cfd0caa6dbbe4c44246fa00654d1b97b7e6ddf8e72662a-json.log",
        "Name": "/postgres.db",
        "RestartCount": 0,
        "Driver": "overlayfs",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": [
                "uf1886_ppf-e2-noel_odoo-db-data:/var/lib/postgresql/data/pgdata:rw"
            ],
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "uf1886_ppf-e2-noel_default",
            "PortBindings": {
                "5432/tcp": [
                    {
                        "HostIp": "",
                        "HostPort": "5432"
                    }
                ]
```
## 3. Revisión de logs básicos
```
docker logs --tail 100 odoo.18
```
```
2026-03-10 11:45:09,318 1 DEBUG ? odoo.service.server: cron1 polling for jobs 
2026-03-10 11:45:18,460 1 DEBUG ? odoo.service.server: cron0 polling for jobs 
2026-03-10 11:46:10,389 1 DEBUG ? odoo.service.server: cron1 polling for jobs
2026-03-10 11:46:18,520 1 DEBUG ? odoo.service.server: cron0 polling for jobs
2026-03-10 11:47:11,446 1 DEBUG ? odoo.service.server: cron1 polling for jobs
2026-03-10 11:47:18,579 1 DEBUG ? odoo.service.server: cron0 polling for jobs
2026-03-10 11:48:12,468 1 DEBUG ? odoo.service.server: cron1 polling for jobs
2026-03-10 11:48:18,639 1 DEBUG ? odoo.service.server: cron0 polling for jobs
2026-03-10 11:49:13,539 1 DEBUG ? odoo.service.server: cron1 polling for jobs
2026-03-10 11:49:18,662 1 DEBUG ? odoo.service.server: cron0 polling for jobs
```
```
docker logs --tail 100 postgres.db
```
```
The files belonging to this database system will be owned by user "postgres".
This user must also own the server process.

The database cluster will be initialized with locale "en_US.utf8".
The default database encoding has accordingly been set to "UTF8".
The default text search configuration will be set to "english".

Data page checksums are disabled.

fixing permissions on existing directory /var/lib/postgresql/data/pgdata ... ok
creating subdirectories ... ok
selecting dynamic shared memory implementation ... posix
selecting default max_connections ... 100
selecting default shared_buffers ... 128MB
selecting default time zone ... UTC
creating configuration files ... ok
running bootstrap script ... ok
sh: locale: not found
2026-03-10 09:53:10.035 UTC [36] WARNING:  no usable system locales were found
performing post-bootstrap initialization ... ok
initdb: warning: enabling "trust" authentication for local connections
initdb: hint: You can change this by editing pg_hba.conf or using the option -A, or --auth-local and --auth-host, the next time you run initdb.
syncing data to disk ... ok


Success. You can now start the database server using:

    pg_ctl -D /var/lib/postgresql/data/pgdata -l logfile start
```
## 4. Conexiones y actividad de PostgreSQL
### 4.1 Acceso a PostgreSQL
```
docker exec -it postgres.db psql -U user -d postgres
```
```
SELECT version(); SELECT datname, numbackends FROM pg_stat_database ORDER BY numbackends DESC; SELECT pid, usename, datname, state, wait_event_type, wait_event FROM pg_stat_activity ORDER BY state, datname; SHOW max_connections; SHOW shared_buffers; SHOW work_mem;
```
