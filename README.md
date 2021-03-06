# HandsOnBigData TSDB
------------------

![InfluxDB](https://miro.medium.com/max/2560/1*QePvkDVkr7bcdgnOzeYyAQ.jpeg)

Como parte del ejercicio, vamos a:
1. Configurar r谩pidamente el InfluxDB2.0 en nuestra m谩quina local
2. Llenar algunos datos de muestra
3. Consultar con Flux

## Pasos: 

### 1. Crear un servicio utilizando docker-compose 馃殌

1.1 Creamos el archivo docker-compose.yml


```yaml
version: "2.1"

services:

  influxdb:
    hostname: influxdb
    image: quay.io/influxdb/influxdb:v2.0.3
    container_name: influxdb
    ports:
        - 8099:8086
```

1.2 Luego corremos el docker-compose


```bash
docker-compose up
```

1.3 Chequeamos que el servicio est茅 funcionando bien
Para eso abrimos el siguiente url: [http://localhost:8099](http://localhost:8099)

Deber铆a aparecer lo siguiente:

![get started](src/1.png)

1.4 Hacemos click en "Get Started"

### 2. Ingresamos la configuraci贸n del usuario 鈿欙笍

2.1 Luego ingresamos los datos correspondientes:

```
Organization name: ufm
Bucket name: handson
```

![setup initial user](src/2.png)

2.2 Despu茅s de dar click en "Continue", se nos pedir谩 que completemos la configuraci贸n.

2.3 Seleccionar la opci贸n "Quick Start" para realizar la configuraci贸n r谩pida y omitir complejidades.

![quick start](src/3.png)

2.4 Cuando la configuraci贸n este lista, nos mostrar谩 la siguiente p谩gina de control:

![dashboard page](src/4.png)

#### 3. Comprobar los Buckets disponibles 馃搵

```
Un bucket es una ubicaci贸n con nombre donde se almacenan los datos de las series temporales. Todos los buckets tienen una pol铆tica de retenci贸n, una duraci贸n de tiempo que cada punto de datos persiste. Un bucket pertenece a una organizaci贸n.
```

Para verificar r谩pidamente el bucket que hemos creado anteriormente, podemos navegar a:

```
Data > Buckets
```


![buckets](src/5.png)

#### 4. Cargar data en el bucket 馃搫

Para cargar una muestra de datos en influxDB, podemos entrar en el bucket haciendo click en "Bucket Name". En la siguiente pantalla, podemos ver un mont贸n de opciones.

Cargaremos los datos utilizando el comando flux. Para ejecutar los comandos flux, haga clic en "Script Editor" como se muestra a continuaci贸n:

![data loading](src/6.png)

Cuando el editor este abierto, correremos el siguiente comando:

```
import "experimental/csv"

relativeToNow = (tables=<-) =>
  tables
    |> elapsed()
    |> sort(columns: ["_time"], desc: true)
    |> cumulativeSum(columns: ["elapsed"])
    |> map(fn: (r) => ({ r with _time: time(v: int(v: now()) - (r.elapsed * 1000000000))}))

csv.from(url: "https://influx-testdata.s3.amazonaws.com/noaa.csv")
  |> relativeToNow()
  |> to(bucket: "handson", org: "ufm")
```

Si los datos fueron cargados con 茅xito veremos inmediatamente un gr谩fico:


![plotted graph](src/7.png)

Tome en cuenta que el bucket name y la organizaci贸n son las que pusimos antes.

Ahora si volvemos al "Query Builder" y refrescamos la p谩gina, veremos todos los campos sobre los que se puede aplicar el filtrado.

![filtering](src/8.png)

Eso es todo 馃槉










