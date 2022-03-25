# TogoVar registered access docker

## Configration

### .env example

```
$ cat .env

VERSION=2020.07

NGINX_PORT=
PUBLIC_DIR=

VIRTUOSO_VOLUMES_LOAD=
VIRTUOSO_VOLUMES_DATA=
```
### togovar-dev.env example

```
$ cat togovar-dev.env

# SPARQList
ADMIN_PASSWORD=
NODE_OPTIONS=--max-old-space-size=16384
SPARQLIST_TOGOVAR_SPARQL=
SPARQLIST_TOGOVAR_SPARQLIST=
SPARQLIST_TOGOVAR_SEARCH_API=
# Registered Access
SPARQLIST_TOGOVAR_APP_HOST=
SPARQLIST_TOGOVAR_SPARQL_REGISTERED_ACCESS=
SPARQLIST_DDBJ_KEYCLOAK_URL=
SPARQLIST_DDBJ_KEYCLOAK_CLIENT_ID=
SPARQLIST_DDBJ_KEYCLOAK_CLIENT_SECRET=

# Virtuoso
DBA_PASSWORD=
SPARQL_UPDATE=
DEFAULT_GRAPH=
VIRT_Parameters_DirsAllowed=
VIRT_SPARQL_MaxQueryExecutionTime=600
VIRT_SPARQL_ResultSetMaxRows=100000000
VIRT_Parameters_CheckpointInterval=10
#VIRT_MaxQueryCostEstimationTime=40000   ; in seconds

## Uncomment next two lines if there is 2 GB system memory free
#VIRT_Parameters_NumberOfBuffers=170000
#VIRT_Parameters_MaxDirtyBuffers=130000
## Uncomment next two lines if there is 4 GB system memory free
#VIRT_Parameters_NumberOfBuffers=340000
#VIRT_Parameters_MaxDirtyBuffers=250000
## Uncomment next two lines if there is 8 GB system memory free
#VIRT_Parameters_NumberOfBuffers=680000
#VIRT_Parameters_MaxDirtyBuffers=500000
## Uncomment next two lines if there is 16 GB system memory free
#VIRT_Parameters_NumberOfBuffers=1360000
#VIRT_Parameters_MaxDirtyBuffers=1000000
## Uncomment next two lines if there is 32 GB system memory free
VIRT_Parameters_NumberOfBuffers=2720000
VIRT_Parameters_MaxDirtyBuffers=2000000
## Uncomment next two lines if there is 48 GB system memory free
#VIRT_Parameters_NumberOfBuffers=4000000
#VIRT_Parameters_MaxDirtyBuffers=3000000
## Uncomment next two lines if there is 64 GB system memory free
#VIRT_Parameters_NumberOfBuffers=5450000
#VIRT_Parameters_MaxDirtyBuffers=4000000
```

## Run application

```
$ docker-compose build
$ docker-compose up -d

```

