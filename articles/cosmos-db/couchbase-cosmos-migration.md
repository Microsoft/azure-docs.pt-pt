---
title: Migrar de CouchBase para Azure Cosmos DB SQL API
description: Orientação passo a passo para migrar da CouchBase para Azure Cosmos DB SQL API
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/11/2020
ms.author: mansha
author: manishmsfte
ms.custom: devx-track-java
ms.openlocfilehash: 0e8859eebf97b8d2788153e74e36f31fda3323c5
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92282468"
---
# <a name="migrate-from-couchbase-to-azure-cosmos-db-sql-api"></a>Migrar de CouchBase para Azure Cosmos DB SQL API

Azure Cosmos DB é uma base de dados escalável, globalmente distribuída e totalmente gerida. Proporciona acesso garantido de baixa latência aos seus dados. Para saber mais sobre a Azure Cosmos DB, veja o artigo [Visão Geral.](introduction.md) Este artigo fornece instruções para migrar aplicações Java que estão ligadas à Couchbase a uma conta API SQL em Azure Cosmos DB.

## <a name="differences-in-nomenclature"></a>Diferenças na nomenclatura

Seguem-se as principais características que funcionam de forma diferente no Azure Cosmos DB quando comparadas com a Couchbase:

|   Couchbase     |   Azure Cosmos DB   |
| ---------------|-------------------|
|Servidor de base de sofás| Conta       |
|Balde           | Base de Dados      |
|Balde           | Contentor/Coleção |
|Documento JSON    | Item / Documento |

## <a name="key-differences"></a>Diferenças principais

* AZure Cosmos DB tem um campo de "ID" dentro do documento, enquanto couchbase tem o ID como parte do balde. O campo "ID" é único em toda a partição.

* Azure Cosmos DB escala utilizando a técnica de partição ou de fragmentos. O que significa que divide os dados em múltiplos fragmentos/divisórias. Estas divisórias/fragmentos são criados com base na propriedade chave de partição que você fornece. Pode selecionar a chave de partição para otimizar a leitura, bem como escrever operações ou ler/escrever também otimizado. Para saber mais, veja o artigo [de partição.](./partitioning-overview.md)

* Na Azure Cosmos DB, não é necessário que a hierarquia de alto nível denote a coleção porque o nome da coleção já existe. Esta funcionalidade torna a estrutura JSON muito mais simples. Segue-se um exemplo que mostra diferenças no modelo de dados entre Couchbase e Azure Cosmos DB:

   **Base do**sofá : Documento ID = "99FF4444"

    ```json
    {
      "TravelDocument":
      {
       "Country":"India",
      "Validity" : "2022-09-01",
        "Person":
        {
          "Name": "Manish",
          "Address": "AB Road, City-z"
        },
        "Visas":
        [
          {
          "Country":"India",
          "Type":"Multi-Entry",
          "Validity":"2022-09-01"
          },
          {
          "Country":"US",
          "Type":"Single-Entry",
          "Validity":"2022-08-01"
          }
        ]
      }
    }
   ```

   **Azure Cosmos DB**: Consulte "ID" dentro do documento, como mostrado abaixo

    ```json
    {
      "id" : "99FF4444",
    
      "Country":"India",
       "Validity" : "2022-09-01",
        "Person":
        {
          "Name": "Manish",
          "Address": "AB Road, City-z"
        },
        "Visas":
        [
          {
          "Country":"India",
          "Type":"Multi-Entry",
          "Validity":"2022-09-01"
          },
          {
          "Country":"US",
          "Type":"Single-Entry",
          "Validity":"2022-08-01"
          }
        ]
      }
    
    ```
         
## <a name="java-sdk-support"></a>Suporte Java SDK

A Azure Cosmos DB tem sDKs seguintes para suportar diferentes quadros java:

* Async SDK
* SDK de bota de primavera

As seguintes secções descrevem quando utilizar cada um destes SDKs. Considere um exemplo onde temos três tipos de cargas de trabalho:

## <a name="couchbase-as-document-repository--spring-data-based-custom-queries"></a>Base de sofá como repositório de documentos & consultas personalizadas baseadas em dados de primavera

Se a carga de trabalho que está a migrar for baseada em SDK baseado em botas de mola, então pode utilizar os seguintes passos:

1. Adicione o pai ao ficheiro POM.xml:

   ```java
   <parent>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-parent</artifactId>
      <version>2.1.5.RELEASE</version>
      <relativePath/>
   </parent>
   ```

1. Adicione propriedades ao ficheiro POM.xml:

   ```java
   <azure.version>2.1.6</azure.version>
   ```

1. Adicione dependências ao ficheiro POM.xml:

   ```java
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
       <version>2.1.6</version>
   </dependency>
   ```

1. Adicione propriedades de aplicação em recursos e especifique o seguinte. Certifique-se de substituir os parâmetros de nome URL, chave e base de dados:

   ```java
      azure.cosmosdb.uri=<your-cosmosDB-URL>
      azure.cosmosdb.key=<your-cosmosDB-key>
      azure.cosmosdb.database=<your-cosmosDB-dbName>
   ```

1. Defina o nome da coleção no modelo. Também pode especificar outras anotações. Por exemplo, ID, chave de partição para denotar explicitamente:

   ```java
   @Document(collection = "mycollection")
       public class User {
           @id
           private String id;
           private String firstName;
           @PartitionKey
           private String lastName;
       }
   ```

Seguem-se os códigos de corte para as operações crud:

### <a name="insert-and-update-operations"></a>Inserir e atualizar operações

Onde *_repo* é objeto de repositório e *doc* é o objeto da classe POJO. Pode utilizar `.save` para inserir ou aumentar (se encontrar documento com ID especificado). O seguinte corte de código mostra como inserir ou atualizar um objeto doc:

```_repo.save(doc);```

### <a name="delete-operation"></a>Eliminar operação

Considere o seguinte corte de código, onde o objeto doc terá iD e chave de partição obrigatória para localizar e eliminar o objeto:

```_repo.delete(doc);```

### <a name="read-operation"></a>Ler Operação

Pode ler o documento com ou sem especificar a chave de partição. Se não especificar a chave de partição, é tratada como uma consulta de partição cruzada. Considere as seguintes amostras de código, a primeira irá executar a operação utilizando o campo de identificação e partição. O segundo exemplo utiliza um campo regular & sem especificar o campo de chaves de partição.

* ```_repo.findByIdAndName(objDoc.getId(),objDoc.getName());```
* ```_repo.findAllByStatus(objDoc.getStatus());```

É isso, agora podes usar a tua aplicação com a Azure Cosmos DB. A amostra de código completa para o exemplo descrito neste doc está disponível no [repo CouchbaseToCosMosDB-SpringCosmos](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/SpringCosmos) GitHub.

## <a name="couchbase-as-a-document-repository--using-n1ql-queries"></a>Base de sofá como um repositório de documentos & usando consultas N1QL

Consultas N1QL é a forma de definir consultas na Base de Sofás.

|Consulta N1QL | Consulta Azure CosmosDB|
|-------------------|-------------------|
|SELECT META( `TravelDocument` .id AS id, `TravelDocument` .* FROM `TravelDocument` WHERE = `_type` "com.xx.xx.xxx.xxx.xxx.xxxx " e país = 'Índia' e ANY m in Visas SATISFIES m.type == 'Multi-Entry' e m.Country IN ['Índia', Butão'] ORDER BY ` Validity` DESC LIMIT 25 OFFSET 0 0   | SELECT c.id,c FROM c JOIN m in c.country='India' WHERE c._type = " com.xx.xxx.xxx.xxxx" e c.country = 'Índia' e m.type = 'Multi-Entry' e m.Country IN ('Índia', 'Butão') ORDER BY c.Validity DESC 0 LIMIT 25 |

Pode notar as seguintes alterações nas suas consultas N1QL:

* Não é necessário utilizar a palavra-chave META ou consultar o documento de primeiro nível. Em vez disso, pode criar a sua própria referência ao recipiente. Neste exemplo, consideramos que é "c" (pode ser qualquer coisa). Esta referência é utilizada como prefixo para todos os campos de primeiro nível. Fr exemplo, c.id, c.country etc.

* Em vez de "ANY" agora pode fazer uma junção ao subdocumento e remexá-lo com um pseudónimo dedicado como "m". Uma vez criado um pseudónimo para um subdocument, precisa de usar o pseudónimo. Por exemplo, m.Country.

* A sequência de OFFSET é diferente na consulta DB do Azure Cosmos, primeiro tens de especificar OFFSET e depois LIMIT. Recomenda-se que não utilize o Spring Data SDK se estiver a utilizar as consultas máximas definidas sob medida, uma vez que pode ter sobrecargas desnecessárias do lado do cliente enquanto passa a consulta para a Azure Cosmos DB. Em vez disso, temos um Async Java SDK direto, que pode ser utilizado de forma muito eficiente neste caso.

### <a name="read-operation"></a>Ler operação

Utilize o Async Java SDK com os seguintes passos:

1. Configure a seguinte dependência no ficheiro POM.xml:

   ```java
   <!-- https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb -->
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmos</artifactId>
       <version>3.0.0</version>
   </dependency>
   ```

1. Crie um objeto de ligação para Azure Cosmos DB utilizando o `ConnectionBuilder` método como mostrado no exemplo seguinte. Certifique-se de que coloca esta declaração no feijão de modo a que o seguinte código seja executado apenas uma vez:

   ```java
   ConnectionPolicy cp=new ConnectionPolicy();
   cp.connectionMode(ConnectionMode.DIRECT);
    
   if(client==null)
    client= CosmosClient.builder()
        .endpoint(Host)//(Host, MasterKey, dbName, collName).Builder()
        .connectionPolicy(cp)
        .key(MasterKey)
        .consistencyLevel(ConsistencyLevel.EVENTUAL)
        .build();   
   
   container = client.getDatabase(_dbName).getContainer(_collName);
   ```

1. Para executar a consulta, é necessário executar o seguinte corte de código:

   ```java
   Flux<FeedResponse<CosmosItemProperties>> objFlux= container.queryItems(query, fo);
   ```

Agora, com a ajuda de método acima você pode passar várias consultas e executar sem qualquer ausos. Caso tenha a obrigação de executar uma grande consulta, que pode ser dividida em múltiplas consultas, tente o seguinte corte de código em vez do anterior:

```java
for(SqlQuerySpec query:queries)
{
    objFlux= container.queryItems(query, fo);
    objFlux .publishOn(Schedulers.elastic())
            .subscribe(feedResponse->
                {
                    if(feedResponse.results().size()>0)
                    {
                        _docs.addAll(feedResponse.results());
                    }
                
                },
                Throwable::printStackTrace,latch::countDown);
    lstFlux.add(objFlux);
}
                        
        Flux.merge(lstFlux);
        latch.await();
}
```

Com o código anterior, pode executar consultas em paralelo e aumentar as execuções distribuídas para otimizar. Além disso, pode executar as operações de inserção e atualização também:

### <a name="insert-operation"></a>Inserir operação

Para inserir o documento, execute o seguinte código:

```java 
Mono<CosmosItemResponse> objMono= container.createItem(doc,ro);
```

Em seguida, subscreva a Mono como:

```java
CountDownLatch latch=new CountDownLatch(1);
objMono .subscribeOn(Schedulers.elastic())
        .subscribe(resourceResponse->
        {
            if(resourceResponse.statusCode()!=successStatus)
                {
                    throw new RuntimeException(resourceResponse.toString());
                }
            },
        Throwable::printStackTrace,latch::countDown);
latch.await();              
```

### <a name="upsert-operation"></a>Operação Upsert

A operação upsert requer que especifique o documento que precisa de ser atualizado. Para obter o documento completo, pode utilizar o corte mencionado na operação de leitura de títulos e, em seguida, modificar o campo ou campo necessários. O seguinte código corta o documento:

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
Em seguida, subscreva o mono. Consulte o corte de assinatura mono em funcionamento de inserção.

### <a name="delete-operation"></a>Eliminar operação

Após o corte irá eliminar a operação:

```java     
CosmosItem objItem= container.getItem(doc.Id, doc.Tenant);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

Em seguida, subscreva o mono, remeta o corte de subscrição mono em operação de inserção. A amostra de código completa está disponível no [repo Dossb-AsyncInSpring](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/AsyncInSpring) GitHub.

## <a name="couchbase-as-a-keyvalue-pair"></a>Base de sofá como um par chave/valor

Este é um tipo simples de carga de trabalho em que você pode realizar lookups em vez de consultas. Utilize os seguintes passos para pares de chaves/valor:

1. Considere ter "/ID" como chave primária, o que garante que pode realizar uma operação de procura diretamente na partição específica. Crie uma coleção e especifique "/ID" como chave de partição.

1. Desligue completamente a indexação. Como vai executar operações de procura, não vale a pena carregar a indexação. Para desativar a indexação, inscreva-se no portal Azure, conta DB goto Azure Cosmos. Abra o **Data Explorer,** selecione a **base de dados** e o **Contentor.** Abra o **separador Escala & Definições** e selecione a  **Política de Indexação**. Atualmente, a política de indexação parece ser a seguinte:
    
   ```json
   {
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/\"_etag\"/?"
        }
    ]
    }
   ````

   Substitua a política de indexação acima pela seguinte política:

   ```json
   {
    "indexingMode": "none",
    "automatic": false,
    "includedPaths": [],
    "excludedPaths": []
    }
   ```

1. Utilize o seguinte corte de código para criar o objeto de ligação. Objeto de ligação (a ser colocado @Bean ou torná-lo estático):

   ```java
   ConnectionPolicy cp=new ConnectionPolicy();
   cp.connectionMode(ConnectionMode.DIRECT);
   
   if(client==null)
    client= CosmosClient.builder()
        .endpoint(Host)//(Host, MasterKey, dbName, collName).Builder()
        .connectionPolicy(cp)
        .key(MasterKey)
        .consistencyLevel(ConsistencyLevel.EVENTUAL)
        .build();
    
   container = client.getDatabase(_dbName).getContainer(_collName);
   ```

Agora pode executar as operações da CRUD da seguinte forma:

### <a name="read-operation"></a>Ler operação

Para ler o artigo, utilize o seguinte corte:

```java        
CosmosItemRequestOptions ro=new CosmosItemRequestOptions();
ro.partitionKey(new PartitionKey(documentId));
CountDownLatch latch=new CountDownLatch(1);
        
var objCosmosItem= container.getItem(documentId, documentId);
Mono<CosmosItemResponse> objMono = objCosmosItem.read(ro);
objMono .subscribeOn(Schedulers.elastic())
        .subscribe(resourceResponse->
        {
            if(resourceResponse.item()!=null)
            {
                doc= resourceResponse.properties().toObject(UserModel.class);
            }
        },
        Throwable::printStackTrace,latch::countDown);
latch.await();
```

### <a name="insert-operation"></a>Inserir operação

Para inserir um item, pode executar o seguinte código:

```java 
Mono<CosmosItemResponse> objMono= container.createItem(doc,ro);
```

Em seguida, subscreva o mono como:

```java
CountDownLatch latch=new CountDownLatch(1);
objMono.subscribeOn(Schedulers.elastic())
        .subscribe(resourceResponse->
        {
            if(resourceResponse.statusCode()!=successStatus)
                {
                    throw new RuntimeException(resourceResponse.toString());
                }
            },
        Throwable::printStackTrace,latch::countDown);
latch.await();
```

### <a name="upsert-operation"></a>Operação Upsert

Para atualizar o valor de um item, consulte o corte de código abaixo:

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
Em seguida, subscreva o mono, remeta o corte de subscrição mono em operação de inserção.

### <a name="delete-operation"></a>Eliminar operação

Utilize o seguinte corte para executar a operação de eliminação:

```java     
CosmosItem objItem= container.getItem(id, id);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

Em seguida, subscreva o mono, remeta o corte de subscrição mono em operação de inserção. A amostra de código completa está disponível no [repo DoCosmosDB-AsyncKeyValue](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/AsyncKeyValue) GitHub.

## <a name="data-migration"></a>Migração de Dados

Há duas maneiras de migrar dados.

* **Utilize a Fábrica de Dados Azure:** Este é o método mais recomendado para migrar os dados. Configure a fonte como Couchbase e afundar como Azure Cosmos DB SQL API, consulte o artigo de conector Azure [Cosmos DB Data Factory](../data-factory/connector-azure-cosmos-db.md) para etapas detalhadas.

* **Utilize a ferramenta de importação de dados Azure Cosmos DB:** Esta opção é recomendada para migrar usando VMs com menos quantidade de dados. Para obter etapas detalhadas, consulte o artigo [do importador de dados.](./import-data.md)

## <a name="next-steps"></a>Passos Seguintes

* Para fazer testes de desempenho, consulte testes de desempenho e escala com o artigo [DB da Azure Cosmos.](./performance-testing.md)
* Para otimizar o código, consulte as dicas de desempenho para o artigo [DB da Azure Cosmos.](./performance-tips-async-java.md)
* Explore Java Async V3 SDK, [referência SDK](https://github.com/Azure/azure-cosmosdb-java/tree/v3) GitHub repo.
