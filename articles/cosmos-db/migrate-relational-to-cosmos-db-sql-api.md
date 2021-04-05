---
title: Migrar dados relacionais de um a poucos para a Azure Cosmos DB SQL API
description: Saiba como lidar com a migração complexa de dados para relacionamentos entre si e poucos na API SQL
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 12/12/2019
ms.author: thvankra
ms.openlocfilehash: 53a3317f38cc22ffa3745f5f0e58cc01a54b825c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93096755"
---
# <a name="migrate-one-to-few-relational-data-into-azure-cosmos-db-sql-api-account"></a>Migrar dados relacionais de um a poucos para a conta Azure Cosmos DB SQL API
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Para migrar de uma base de dados relacional para a AZure Cosmos DB SQL API, pode ser necessário fazer alterações no modelo de dados para otimização.

Uma transformação comum é desnormalizar os dados incorporando subíncos relacionados dentro de um documento JSON. Aqui olhamos para algumas opções para isso usando Azure Data Factory ou Azure Databricks. Para obter orientações gerais sobre modelação de dados para Cosmos DB, por favor reveja [a modelação de dados em Azure Cosmos DB](modeling-data.md).  

## <a name="example-scenario"></a>Cenário de Exemplo

Assuma que temos as duas tabelas seguintes na nossa base de dados SQL, Encomendas e Encomendas.


:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/orders.png" alt-text="Screenshot que mostra as tabelas Encomendas e OrderDetails na base de dados SQL." border="false" :::

Queremos combinar esta relação entre poucos e poucos num documento JSON durante a migração. Para isso, podemos criar uma consulta T-SQL usando "FOR JSON" como abaixo:

```sql
SELECT
  o.OrderID,
  o.OrderDate,
  o.FirstName,
  o.LastName,
  o.Address,
  o.City,
  o.State,
  o.PostalCode,
  o.Country,
  o.Phone,
  o.Total,
  (select OrderDetailId, ProductId, UnitPrice, Quantity from OrderDetails od where od.OrderId = o.OrderId for json auto) as OrderDetails
FROM Orders o;
```

Os resultados desta consulta seriam abaixo: 

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/for-json-query-result.png" alt-text="Detalhes do pedido" lightbox="./media/migrate-relational-to-cosmos-sql-api/for-json-query-result.png":::

Idealmente, pretende utilizar uma única atividade de cópia da Azure Data Factory (ADF) para consultar os dados sql como fonte e escrever a saída diretamente para a pia Azure Cosmos DB como objetos JSON apropriados. Atualmente, não é possível realizar a transformação JSON necessária numa única atividade de cópia. Se tentarmos copiar os resultados da consulta acima num contentor API API AZure Cosmos DB SQL, veremos o campo OrderDetails como uma propriedade de corda do nosso documento, em vez da matriz JSON esperada.

Podemos contornar esta limitação atual de uma das seguintes formas:

* **Utilize a Azure Data Factory com duas atividades de cópia:** 
  1. Obtenha dados formatados por JSON do SQL para um ficheiro de texto num local de armazenamento de bolhas intermediárias, e 
  2. Carregue os dados do ficheiro de texto JSON para um recipiente em Azure Cosmos DB.

* **Use Azure Databricks para ler a partir de SQL e escreva para Azure Cosmos DB** - apresentaremos duas opções aqui.


Vejamos estas abordagens com mais detalhes:

## <a name="azure-data-factory"></a>Azure Data Factory

Embora não possamos incorporar o OrderDetails como um conjunto JSON no documento de destino Cosmos DB, podemos trabalhar em torno do problema usando duas Atividades de Cópia separadas.

### <a name="copy-activity-1-sqljsontoblobtext"></a>#1 de Atividade de Cópia: SqlJsonToBlobText

Para os dados de origem, utilizamos uma consulta SQL para obter o conjunto de resultados como uma única coluna com um objeto JSON (representando a Ordem) por linha utilizando as capacidades DO SQL Server OPENJSON e FOR JSON PATH:

```sql
SELECT [value] FROM OPENJSON(
  (SELECT
    id = o.OrderID,
    o.OrderDate,
    o.FirstName,
    o.LastName,
    o.Address,
    o.City,
    o.State,
    o.PostalCode,
    o.Country,
    o.Phone,
    o.Total,
    (select OrderDetailId, ProductId, UnitPrice, Quantity from OrderDetails od where od.OrderId = o.OrderId for json auto) as OrderDetails
   FROM Orders o FOR JSON PATH)
)
```

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf1.png" alt-text="Cópia ADF":::


Para a pia da atividade de cópia SqlJsonToBlobText, escolhemos "Texto Delimitado" e apontamos para uma pasta específica no Azure Blob Storage com um nome de ficheiro único gerado dinamicamente (por exemplo, ' @concat (pipeline). RunId,'.json').
Uma vez que o nosso ficheiro de texto não é realmente "delimitado" e não queremos que seja analisado em colunas separadas usando vírgulas e queremos preservar as citações duplas ("), definimos "Delimitador de colunas" a um Separador ("\t") - ou outro personagem que não ocorre nos dados - e "Quote character" para "No quote character".

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf2.png" alt-text="Screenshot que realça as definições de caracteres delimiter e citação da coluna.":::

### <a name="copy-activity-2-blobjsontocosmos"></a>#2 de Atividade de Cópia: BlobJsonToCosmos

Em seguida, modificamos o nosso pipeline ADF adicionando a segunda Atividade de Cópia que procura no Azure Blob Storage para o ficheiro de texto que foi criado pela primeira atividade. Processa-o como fonte "JSON" para inserir na pia Da Cosmos DB como um documento por linha JSON encontrado no ficheiro de texto.

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf3.png" alt-text="Screenshot que realça o ficheiro de origem JSON e os campos de caminho de arquivo.":::

Opcionalmente, também adicionamos uma atividade "Eliminar" ao pipeline de modo a eliminar todos os ficheiros anteriores restantes na /Encomendas/pasta antes de cada execução. O nosso oleoduto ADF agora é parecido com isto:

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf4.png" alt-text="Screenshot que realça a atividade De Excluir.":::

Depois de acionarmos o oleoduto acima, vemos um ficheiro criado no nosso local de armazenamento Azure Blob, contendo um objeto JSON por linha:

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf5.png" alt-text="Screenshot que mostra o ficheiro criado que contém os objetos JSON.":::

Também vemos documentos encomendas com OrderDetails devidamente incorporados inseridos na nossa coleção Cosmos DB:

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf6.png" alt-text="Screenshot que mostra os detalhes da encomenda como parte do documento cosmos DB":::


## <a name="azure-databricks"></a>Azure Databricks

Também podemos usar a Spark in [Azure Databricks](https://azure.microsoft.com/services/databricks/) para copiar os dados da nossa fonte de base de dados SQL para o destino DB Azure Cosmos sem criar os ficheiros intermediários de texto/JSON no Azure Blob Storage. 

> [!NOTE]
> Para maior clareza e simplicidade, os snippets de código abaixo incluem senhas de base de dados falsas explicitamente em linha, mas deve sempre usar os segredos de Azure Databricks.
>

Em primeiro lugar, criamos e anexamos as necessárias bibliotecas de [conector SQL](https://docs.databricks.com/data/data-sources/sql-databases-azure.html) e [Azure Cosmos DB](https://docs.databricks.com/data/data-sources/azure/cosmosdb-connector.html) ao nosso cluster Azure Databricks. Reinicie o cluster para se certificar de que as bibliotecas estão carregadas.

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks1.png" alt-text="Screenshot que mostra onde criar e anexar as bibliotecas de conector SQL e Azure Cosmos DB necessárias ao nosso cluster Azure Databricks.":::

Em seguida, apresentamos duas amostras para Scala e Python. 

### <a name="scala"></a>Scala
Aqui, obtemos os resultados da consulta SQL com a saída "FOR JSON" num DataFrame:

```scala
// Connect to Azure SQL https://docs.databricks.com/data/data-sources/sql-databases-azure.html
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._
val configSql = Config(Map(
  "url"          -> "xxxx.database.windows.net",
  "databaseName" -> "xxxx",
  "queryCustom"  -> "SELECT o.OrderID, o.OrderDate, o.FirstName, o.LastName, o.Address, o.City, o.State, o.PostalCode, o.Country, o.Phone, o.Total, (SELECT OrderDetailId, ProductId, UnitPrice, Quantity FROM OrderDetails od WHERE od.OrderId = o.OrderId FOR JSON AUTO) as OrderDetails FROM Orders o",
  "user"         -> "xxxx", 
  "password"     -> "xxxx" // NOTE: For clarity and simplicity, this example includes secrets explicitely as a string, but you should always use Databricks secrets
))
// Create DataFrame from Azure SQL query
val orders = sqlContext.read.sqlDB(configSql)
display(orders)
```

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks2.png" alt-text="Screenshot que mostra a saída de consulta SQL num DataFrame.":::

Em seguida, conectamo-nos à nossa base de dados e recolha da Cosmos DB:

```scala
// Connect to Cosmos DB https://docs.databricks.com/data/data-sources/azure/cosmosdb-connector.html
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.CosmosDBSpark
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.apache.spark.sql.functions._
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.CosmosDBSpark
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.apache.spark.sql.functions._
val configMap = Map(
  "Endpoint" -> "https://xxxx.documents.azure.com:443/",
  // NOTE: For clarity and simplicity, this example includes secrets explicitely as a string, but you should always use Databricks secrets
  "Masterkey" -> "xxxx",
  "Database" -> "StoreDatabase",
  "Collection" -> "Orders")
val configCosmos = Config(configMap)
```

Finalmente, definimos o nosso esquema e utilizamos from_json para aplicar o DataFrame antes de o guardarmos para a coleção CosmosDB.

```scala
// Convert DataFrame to proper nested schema
import org.apache.spark.sql.types._
val orderDetailsSchema = ArrayType(StructType(Array(
    StructField("OrderDetailId",IntegerType,true),
    StructField("ProductId",IntegerType,true),
    StructField("UnitPrice",DoubleType,true),
    StructField("Quantity",IntegerType,true)
  )))
val ordersWithSchema = orders.select(
  col("OrderId").cast("string").as("id"),
  col("OrderDate").cast("string"),
  col("FirstName").cast("string"),
  col("LastName").cast("string"),
  col("Address").cast("string"),
  col("City").cast("string"),
  col("State").cast("string"),
  col("PostalCode").cast("string"),
  col("Country").cast("string"),
  col("Phone").cast("string"),
  col("Total").cast("double"),
  from_json(col("OrderDetails"), orderDetailsSchema).as("OrderDetails")
)
display(ordersWithSchema)
// Save nested data to Cosmos DB
CosmosDBSpark.save(ordersWithSchema, configCosmos)
```

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks3.png" alt-text="Screenshot que destaca a matriz adequada para guardar para uma coleção cosmos DB.":::


### <a name="python"></a>Python

Como uma abordagem alternativa, poderá ter de executar transformações JSON em Spark (se a base de dados de origem não suportar "FOR JSON" ou uma operação semelhante), ou se desejar utilizar operações paralelas para um conjunto de dados muito grande. Aqui apresentamos uma amostra de PySpark. Comece por configurar as ligações de base de dados de origem e alvo na primeira célula:

```python
import uuid
import pyspark.sql.functions as F
from pyspark.sql.functions import col
from pyspark.sql.types import StringType,DateType,LongType,IntegerType,TimestampType
 
#JDBC connect details for SQL Server database
jdbcHostname = "jdbcHostname"
jdbcDatabase = "OrdersDB"
jdbcUsername = "jdbcUsername"
jdbcPassword = "jdbcPassword"
jdbcPort = "1433"
 
connectionProperties = {
  "user" : jdbcUsername,
  "password" : jdbcPassword,
  "driver" : "com.microsoft.sqlserver.jdbc.SQLServerDriver"
}
jdbcUrl = "jdbc:sqlserver://{0}:{1};database={2};user={3};password={4}".format(jdbcHostname, jdbcPort, jdbcDatabase, jdbcUsername, jdbcPassword)
 
#Connect details for Target Azure Cosmos DB SQL API account
writeConfig = {
    "Endpoint": "Endpoint",
    "Masterkey": "Masterkey",
    "Database": "OrdersDB",
    "Collection": "Orders",
    "Upsert": "true"
}
```

Em seguida, consultaremos a Base de Dados de Origem (neste caso, SQL Server) para os registos de encomendas e de detalhes de encomenda, colocando os resultados em Dataframes de Faísca. Também criaremos uma lista contendo todos os IDs de encomenda, e uma piscina Thread para operações paralelas:

```python
import json
import ast
import pyspark.sql.functions as F
import uuid
import numpy as np
import pandas as pd
from functools import reduce
from pyspark.sql import SQLContext
from pyspark.sql.types import *
from pyspark.sql import *
from pyspark.sql.functions import exp
from pyspark.sql.functions import col
from pyspark.sql.functions import lit
from pyspark.sql.functions import array
from pyspark.sql.types import *
from multiprocessing.pool import ThreadPool
 
#get all orders
orders = sqlContext.read.jdbc(url=jdbcUrl, table="orders", properties=connectionProperties)
 
#get all order details
orderdetails = sqlContext.read.jdbc(url=jdbcUrl, table="orderdetails", properties=connectionProperties)
 
#get all OrderId values to pass to map function 
orderids = orders.select('OrderId').collect()
 
#create thread pool big enough to process merge of details to orders in parallel
pool = ThreadPool(10)
```

Em seguida, crie uma função para escrever Encomendas na coleção API target SQL. Esta função irá filtrar todos os detalhes da encomenda para o ID de encomenda dado, convertê-los em uma matriz JSON, e inserir a matriz em um documento JSON que escreveremos na coleção API target SQL para esta ordem:

```python
def writeOrder(orderid):
  #filter the order on current value passed from map function
  order = orders.filter(orders['OrderId'] == orderid[0])
  
  #set id to be a uuid
  order = order.withColumn("id", lit(str(uuid.uuid1())))
  
  #add details field to order dataframe
  order = order.withColumn("details", lit(''))
  
  #filter order details dataframe to get details we want to merge into the order document
  orderdetailsgroup = orderdetails.filter(orderdetails['OrderId'] == orderid[0])
  
  #convert dataframe to pandas
  orderpandas = order.toPandas()
  
  #convert the order dataframe to json and remove enclosing brackets
  orderjson = orderpandas.to_json(orient='records', force_ascii=False)
  orderjson = orderjson[1:-1] 
  
  #convert orderjson to a dictionaory so we can set the details element with order details later
  orderjsondata = json.loads(orderjson)
  
  
  #convert orderdetailsgroup dataframe to json, but only if details were returned from the earlier filter
  if (orderdetailsgroup.count() !=0):
    #convert orderdetailsgroup to pandas dataframe to work better with json
    orderdetailsgroup = orderdetailsgroup.toPandas()
    
    #convert orderdetailsgroup to json string
    jsonstring = orderdetailsgroup.to_json(orient='records', force_ascii=False)
    
    #convert jsonstring to dictionary to ensure correct encoding and no corrupt records
    jsonstring = json.loads(jsonstring)
    
    #set details json element in orderjsondata to jsonstring which contains orderdetailsgroup - this merges order details into the order 
    orderjsondata['details'] = jsonstring
 
  #convert dictionary to json
  orderjsondata = json.dumps(orderjsondata)
 
  #read the json into spark dataframe
  df = spark.read.json(sc.parallelize([orderjsondata]))
  
  #write the dataframe (this will be a single order record with merged many-to-one order details) to cosmos db using spark the connector
  #https://docs.microsoft.com/azure/cosmos-db/spark-connector
  df.write.format("com.microsoft.azure.cosmosdb.spark").mode("append").options(**writeConfig).save()
```

Finalmente, chamaremos o acima usando uma função de mapa na piscina de fios, para executar em paralelo, passando na lista de IDs de ordem que criamos anteriormente:

```python
#map order details to orders in parallel using the above function
pool.map(writeOrder, orderids)
```
Em qualquer uma das abordagens, no final, devemos ser devidamente guardados embutidas OrderDetails dentro de cada documento da Ordem na coleção Cosmos DB:

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks4.png" alt-text="Databricks":::

## <a name="next-steps"></a>Passos seguintes
* Conheça [a modelação de dados em Azure Cosmos DB](./modeling-data.md)
* Saiba [como modelar e dividir dados sobre a Azure Cosmos DB](./how-to-model-partition-example.md)