---
title: Emigre a sua aplicação da Amazon DynamoDB para a Azure Cosmos DB
description: Saiba como migrar a sua aplicação .NET do Dínamo da Amazon para o Azure Cosmos DB
author: manishmsfte
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 04/29/2020
ms.author: mansha
ms.openlocfilehash: 9b4b5fca8017a906fa44b02edcf5f0bdcf6166b3
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93334230"
---
# <a name="migrate-your-application-from-amazon-dynamodb-to-azure-cosmos-db"></a>Emigre a sua aplicação da Amazon DynamoDB para a Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB é uma base de dados escalável, globalmente distribuída e totalmente gerida. Proporciona acesso garantido de baixa latência aos seus dados. Para saber mais sobre a Azure Cosmos DB, veja o artigo [Visão Geral.](introduction.md) Este artigo descreve como migrar a sua aplicação .NET do DínamoDB para a Azure Cosmos DB com alterações mínimas de código.

## <a name="conceptual-differences"></a>Diferenças conceptuais

Seguem-se as principais diferenças conceptuais entre Azure Cosmos DB e DynamoDB:

|  DynamoDB | Azure Cosmos DB  |
|---|---|
|Não aplicável|  Base de Dados |
|Tabela      |  Coleção |
|  Item |  Documento |
|Atributo|Campo|
|Índice Secundário|Índice Secundário|
|Chave Primária – Chave de partição|Chave de Partição|
|Chave Primária – Chave de Classificação| Não é necessário |
|Fluxo|ChangeFeed|
|Escrever Unidade de Cálculo|Unidade de Pedido (Flexível, pode ser usada para leituras ou escritas)|
|Ler Unidade de Cálculo    |Unidade de Pedido (Flexível, pode ser usada para leituras ou escritas)|
|Tabelas Globais| Não é necessário. Pode selecionar diretamente a região enquanto fornece a conta Azure Cosmos (pode alterar a região mais tarde)|

## <a name="structural-differences"></a>Diferenças estruturais

AZure Cosmos DB tem uma estrutura JSON mais simples quando comparada com a do DínamoDB. O exemplo a seguir mostra as diferenças

**Dínamo:**

O seguinte objeto JSON representa o formato de dados no DínamoDB

```json
{
TableName: "Music",
KeySchema: [
{ 
  AttributeName: "Artist",
  KeyType: "HASH", //Partition key
},
{ 
  AttributeName: "SongTitle",
  KeyType: "RANGE" //Sort key
}
],
AttributeDefinitions: [
{ 
  AttributeName: "Artist",
  AttributeType: "S"
},
{ 
  AttributeName: "SongTitle",
  AttributeType: "S"
}
],
ProvisionedThroughput: {
  ReadCapacityUnits: 1,
  WriteCapacityUnits: 1
 }
}
 ```

**Azure Cosmos DB:**

O seguinte objeto JSON representa o formato de dados em Azure Cosmos DB

```json
{
"Artist": "",
"SongTitle": "",
"AlbumTitle": "",
"Year": 9999,
"Price": 0.0,
"Genre": "",
"Tags": ""
}
```

## <a name="migrate-your-data"></a>Migrar os dados

Existem várias opções disponíveis para migrar os seus dados para a Azure Cosmos DB. Para saber mais, consulte as [Opções para migrar os seus dados no local ou em nuvem para o artigo DB da Azure Cosmos.](cosmosdb-migrationchoices.md)

## <a name="migrate-your-code"></a>Migrar o código

Este artigo é traçado para migrar o código de uma aplicação para Azure Cosmos DB, que é o aspeto crítico da migração da base de dados. Para ajudá-lo a reduzir a curva de aprendizagem, as seguintes secções incluem uma comparação de código lado a lado entre o Amazon DynamoDB e o código equivalente de Azure Cosmos DB.

Para descarregar o código fonte, clone o seguinte repo:

```bash
git clone https://github.com/Azure-Samples/DynamoDB-to-CosmosDB
```

### <a name="pre-requisites"></a>Pré-requisitos

- .Net Framework 4.7.2
- Visual Studio 2019
- Acesso à Conta AZure Cosmos DB API
- Instalação local do Amazon DynamoDB
- Java 8
- Execute a versão transferível do Amazon DynamoDB no porto 8000 (pode alterar e configurar o código)

### <a name="set-up-your-code"></a>Configurar o seu código

Adicione o seguinte "pacote NuGet" ao seu projeto:

```bash
Install-Package Microsoft.Azure.Cosmos 
```

### <a name="establish-connection"></a>Estabelecer ligação

**Dínamo:**

No Dínamo Amazon, é utilizado o seguinte código para ligar:

```csharp
    AmazonDynamoDBConfig addbConfig = new AmazonDynamoDBConfig() ;
        addbConfig.ServiceURL = "endpoint";
        try { aws_dynamodbclient = new AmazonDynamoDBClient( addbConfig ); }
```

**Azure Cosmos DB:**

Para ligar a Azure Cosmos DB, atualize o seu código para:

```csharp
client_documentDB = new CosmosClient("your connectionstring from the Azure portal");
```

**Otimizar a ligação em Azure Cosmos DB**

Com a Azure Cosmos DB, pode utilizar as seguintes opções para otimizar a sua ligação:

* **ConnectionMode** - Utilize o modo de ligação direta para ligar aos nós de dados no serviço DB Azure Cosmos. Utilize o modo gateway apenas para inicializar e cache os endereços lógicos e atualizar as atualizações. Consulte o artigo [modos de conectividade](sql-sdk-connection-modes.md) para obter mais detalhes.

* **ApplicationRegion** - Esta opção é usada para definir a região geo-replicada preferida que é usada para interagir com Azure Cosmos DB. Para saber mais veja o artigo [de distribuição global.](distribute-data-globally.md)

* **ConsistênciaLevel** - Esta opção é usada para anular o nível de consistência predefinido. Para saber mais, consulte o artigo [Níveis de Consistência.](consistency-levels.md)

* **BulkExecutionMode** - Esta opção é usada para executar operações a granel, definindo a propriedade *AllowBulkExecution* para ser verdadeira. Para saber mais veja o artigo [de importação em massa.](tutorial-sql-api-dotnet-bulk-import.md)

   ```csharp
   client_cosmosDB = new CosmosClient(" Your connection string ",new CosmosClientOptions()
   { 
    ConnectionMode=ConnectionMode.Direct,
    ApplicationRegion=Regions.EastUS2,
    ConsistencyLevel=ConsistencyLevel.Session,
    AllowBulkExecution=true  
   });
   ```

### <a name="provision-the-container"></a>Fornecimento do contentor

**Dínamo:**

Para armazenar os dados no Amazon DynamoDB, é necessário criar a tabela primeiro. Neste processo, define o esquema, o tipo de chave e os atributos como mostrado no seguinte código:

```csharp
// movies_key_schema
public static List<KeySchemaElement> movies_key_schema
  = new List<KeySchemaElement>
{
  new KeySchemaElement
  {
    AttributeName = partition_key_name,
    KeyType = "HASH"
  },
  new KeySchemaElement
  {
    AttributeName = sort_key_name,
    KeyType = "RANGE"
  }
};

// key names for the Movies table
public const string partition_key_name = "year";
public const string sort_key_name      = "title";
  public const int readUnits=1, writeUnits=1; 

    // movie_items_attributes
    public static List<AttributeDefinition> movie_items_attributes
  = new List<AttributeDefinition>
{
  new AttributeDefinition
  {
    AttributeName = partition_key_name,
    AttributeType = "N"
  },
  new AttributeDefinition
  {
    AttributeName = sort_key_name,
    AttributeType = "S"
  }

CreateTableRequest  request;
CreateTableResponse response;

// Build the 'CreateTableRequest' structure for the new table
request = new CreateTableRequest
{
  TableName             = table_name,
  AttributeDefinitions  = table_attributes,
  KeySchema             = table_key_schema,
  // Provisioned-throughput settings are always required,
  // although the local test version of DynamoDB ignores them.
  ProvisionedThroughput = new ProvisionedThroughput( readUnits, writeUnits );
};
```

**Azure Cosmos DB:**

No Amazon DynamoDB, você precisa providenciar as unidades de computação de leitura & escrever unidades de computação. Enquanto que em Azure Cosmos DB especifica a produção como [Unidades de Pedido (RU/s),](request-units.md)que podem ser usadas para qualquer operação dinamicamente. Os dados são organizados como base de dados -- > contentor -- > item. Pode especificar a produção a nível de base de dados ou a nível de recolha ou ambos.

Para criar uma base de dados:

```csharp
await client_cosmosDB.CreateDatabaseIfNotExistsAsync(movies_table_name);
```

Para criar o recipiente:

```csharp
await cosmosDatabase.CreateContainerIfNotExistsAsync(new ContainerProperties() { PartitionKeyPath = "/" + partitionKey, Id = new_collection_name }, provisionedThroughput);
```

### <a name="load-the-data"></a>Carregar os dados

**Dínamo:**

O código que se segue mostra como carregar os dados no Amazon DynamoDB. Os filmesArray consistem na lista de documento json, então você precisa iterar e carregar o documento JSON no Amazon DynamoDB:

```csharp
int n = moviesArray.Count;
for( int i = 0, j = 99; i < n; i++ )
    {
  try
  {
    string itemJson = moviesArray[i].ToString();
    Document doc = Document.FromJson(itemJson);
    Task putItem = moviesTable.PutItemAsync(doc);
    if( i >= j )
    {
      j++;
      Console.Write( "{0,5:#,##0}, ", j );
      if( j % 1000 == 0 )
        Console.Write( "\n " );
      j += 99;
    }
    await putItem;
```

**Azure Cosmos DB:**

Em Azure Cosmos DB, pode optar pelo stream e escrever com `moviesContainer.CreateItemStreamAsync()` . No entanto, nesta amostra, o JSON será deseralizado no tipo *MovieModel* para demonstrar a fundição de tipo. O código é multi-threaded, que usará a arquitetura distribuída da Azure Cosmos DB e acelerará o carregamento:

```csharp
List<Task> concurrentTasks = new List<Task>();
for (int i = 0, j = 99; i < n; i++)
{
  try
  {
      MovieModel doc= JsonConvert.DeserializeObject<MovieModel>(moviesArray[i].ToString());
      doc.Id = Guid.NewGuid().ToString();
      concurrentTasks.Add(moviesContainer.CreateItemAsync(doc,new PartitionKey(doc.Year)));
      {
          j++;
          Console.Write("{0,5:#,##0}, ", j);
          if (j % 1000 == 0)
              Console.Write("\n               ");
          j += 99;
      }
      
  }
  catch (Exception ex)
  {
      Console.WriteLine("\n     ERROR: Could not write the movie record #{0:#,##0}, because:\n       {1}",
                          i, ex.Message);
      operationFailed = true;
      break;
  }
}
await Task.WhenAll(concurrentTasks);
```

### <a name="create-a-document"></a>Criar um documento

**Dínamo:**

Escrever um novo documento no Amazon DynamoDB não é seguro para escrever, o seguinte exemplo usa o newItem como tipo de documento:

```csharp
Task<Document> writeNew = moviesTable.PutItemAsync(newItem, token);
await writeNew;
```

**Azure Cosmos DB:**

A Azure Cosmos DB fornece-lhe a segurança do tipo através de um modelo de dados. Utilizamos um modelo de dados chamado 'MovieModel':

```csharp
public class MovieModel
{
    [JsonProperty("id")]
    public string Id { get; set; }
    [JsonProperty("title")]
    public string Title{ get; set; }
    [JsonProperty("year")]
    public int Year { get; set; }
    public MovieModel(string title, int year)
    {
        this.Title = title;
        this.Year = year;
    }
    public MovieModel()
    {

    }
    [JsonProperty("info")]
    public   MovieInfo MovieInfo { get; set; }

    internal string PrintInfo()
    {
        if(this.MovieInfo!=null)
        return            string.Format("\nMovie with title:{1}\n Year: {2}, Actors: {3}\n Directors:{4}\n Rating:{5}\n", this.Id, this.Title, this.Year, String.Join(",",this.MovieInfo.Actors), this.MovieInfo, this.MovieInfo.Rating);
        else
            return string.Format("\nMovie with  title:{0}\n Year: {1}\n",  this.Title, this.Year);
    }
}
```

Em Azure Cosmos DB newItem será MovieModel:

```csharp
 MovieModel movieModel = new MovieModel()
            {
                Id = Guid.NewGuid().ToString(),
                Title = "The Big New Movie",
                Year = 2018,
                MovieInfo = new MovieInfo() { Plot = "Nothing happens at all.", Rating = 0 }
            };
    var writeNew= moviesContainer.CreateItemAsync(movieModel, new Microsoft.Azure.Cosmos.PartitionKey(movieModel.Year));
    await writeNew;
```

### <a name="read-a-document"></a>Leia um documento

**Dínamo:**

Para ler no Dínamo da Amazon, é preciso definir primitivos:

```csharp
// Create Primitives for the HASH and RANGE portions of the primary key
Primitive hash = new Primitive(year.ToString(), true);
Primitive range = new Primitive(title, false);

  Task<Document> readMovie = moviesTable.GetItemAsync(hash, range, token);
  movie_record = await readMovie;
```

**Azure Cosmos DB:**

No entanto, com Azure Cosmos DB a consulta é natural (linq):

```csharp
IQueryable<MovieModel> movieQuery = moviesContainer.GetItemLinqQueryable<MovieModel>(true)
                        .Where(f => f.Year == year && f.Title == title);
// The query is executed synchronously here, but can also be executed asynchronously via the IDocumentQuery<T> interface
    foreach (MovieModel movie in movieQuery)
    {
      movie_record_cosmosdb = movie;
    }
```

A recolha de documentos no exemplo acima referido será:

- tipo seguro
- fornecer uma opção de consulta natural.

### <a name="update-an-item"></a>Atualizar um item

**DynamoDB** : Para atualizar o item no Amazon DynamoDB:

```csharp
updateResponse = await client.UpdateItemAsync( updateRequest );
````

**Azure Cosmos DB:**

Em Azure Cosmos DB, a atualização será tratada como operação Upsert significando inserir o documento se não existir:

```csharp
await moviesContainer.UpsertItemAsync<MovieModel>(updatedMovieModel);
```

### <a name="delete-a-document"></a>Eliminar um documento

**Dínamo:**

Para eliminar um item no Amazon DynamoDB, é necessário voltar a apostar em primitivos:

```csharp
Primitive hash = new Primitive(year.ToString(), true);
      Primitive range = new Primitive(title, false);
      DeleteItemOperationConfig deleteConfig = new DeleteItemOperationConfig( );
      deleteConfig.ConditionalExpression = condition;
      deleteConfig.ReturnValues = ReturnValues.AllOldAttributes;
      
  Task<Document> delItem = table.DeleteItemAsync( hash, range, deleteConfig );
        deletedItem = await delItem;
```

**Azure Cosmos DB:**

Em Azure Cosmos DB, podemos obter o documento e apagá-los assíncronamente:

```csharp
var result= ReadingMovieItem_async_List_CosmosDB("select * from c where c.info.rating>7 AND c.year=2018 AND c.title='The Big New Movie'");
while (result.HasMoreResults)
{
  var resultModel = await result.ReadNextAsync();
  foreach (var movie in resultModel.ToList<MovieModel>())
  {
    await moviesContainer.DeleteItemAsync<MovieModel>(movie.Id, new PartitionKey(movie.Year));
  }
  }
```

### <a name="query-documents"></a>Consultar documentos

**Dínamo:**

No Dínamo da Amazon, as funções da API são necessárias para consultar os dados:

```csharp
QueryOperationConfig config = new QueryOperationConfig( );
  config.Filter = new QueryFilter( );
  config.Filter.AddCondition( "year", QueryOperator.Equal, new DynamoDBEntry[ ] { 1992 } );
  config.Filter.AddCondition( "title", QueryOperator.Between, new DynamoDBEntry[ ] { "B", "Hzz" } );
  config.AttributesToGet = new List<string> { "year", "title", "info" };
  config.Select = SelectValues.SpecificAttributes;
  search = moviesTable.Query( config ); 
```

**Azure Cosmos DB:**

Em Azure Cosmos DB, você pode fazer projeção e filtro dentro de uma simples consulta sql:

```csharp
var result = moviesContainer.GetItemQueryIterator<MovieModel>( 
  "select c.Year, c.Title, c.info from c where Year=1998 AND (CONTAINS(Title,'B') OR CONTAINS(Title,'Hzz'))");
```

Para operações de alcance, por exemplo, 'entre', é necessário fazer uma varredura no Dínamo da Amazon:

```csharp
ScanRequest sRequest = new ScanRequest
{
  TableName = "Movies",
  ExpressionAttributeNames = new Dictionary<string, string>
  {
    { "#yr", "year" }
  },
  ExpressionAttributeValues = new Dictionary<string, AttributeValue>
  {
      { ":y_a", new AttributeValue { N = "1960" } },
      { ":y_z", new AttributeValue { N = "1969" } },
  },
  FilterExpression = "#yr between :y_a and :y_z",
  ProjectionExpression = "#yr, title, info.actors[0], info.directors, info.running_time_secs"
};

ClientScanning_async( sRequest ).Wait( );
```

Em Azure Cosmos DB, você pode usar consulta SQL e uma declaração de uma linha única:

```csharp
var result = moviesContainer.GetItemQueryIterator<MovieModel>( 
  "select c.title, c.info.actors[0], c.info.directors,c.info.running_time_secs from c where BETWEEN year 1960 AND 1969");
```

### <a name="delete-a-container"></a>Eliminar um contentor

**Dínamo:**

Para eliminar a tabela no Dínamo Amazon, pode especificar:

```csharp
client.DeleteTableAsync( tableName );
```

**Azure Cosmos DB:**

Para eliminar a coleção em Azure Cosmos DB, pode especificar:

```csharp
await moviesContainer.DeleteContainerAsync();
```
Em seguida, apague também a base de dados se precisar:

```csharp
await cosmosDatabase.DeleteAsync();
```

Como pode ver, a Azure Cosmos DB suporta consultas naturais (SQL), as operações são assíncronas e muito mais fáceis. Pode facilmente migrar o seu código complexo para Azure Cosmos DB, que se torna mais simples após a migração.

### <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [a otimização de desempenho.](performance-tips.md)
- Saiba [como otimizar leituras e escrever](key-value-store-cost.md)
- Saiba [mais sobre monitorização em Cosmos DB](monitor-cosmos-db.md)

