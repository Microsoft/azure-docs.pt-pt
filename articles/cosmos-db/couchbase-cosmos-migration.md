---
title: Migrar da CouchBase para a Azure Cosmos DB SQL API
description: Orientação passo a passo para migrar da CouchBase para a Azure Cosmos DB SQL API
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: mansha
author: manishmsfte
ms.openlocfilehash: 9713d963978e34ad874dc032676a6e1f14e4657c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77210947"
---
# <a name="migrate-from-couchbase-to-azure-cosmos-db-sql-api"></a>Migrar da CouchBase para a Azure Cosmos DB SQL API

Azure Cosmos DB é uma base de dados escalável, distribuída globalmente, totalmente gerida. Proporciona acesso garantido de baixa latência aos seus dados. Para saber mais sobre o Azure Cosmos DB, consulte o artigo sobre [visão geral.](introduction.md) Este artigo fornece instruções para migrar aplicações java que estão ligadas à Couchbase a uma conta SQL API em Azure Cosmos DB.

## <a name="differences-in-nomenclature"></a>Diferenças na nomenclatura

Seguem-se as principais características que funcionam de forma diferente em Azure Cosmos DB quando comparadas com a Couchbase:

|   Couchbase     |   Azure Cosmos DB   |
| ---------------|-------------------|
|Servidor couchbase| Conta       |
|Balde           | Base de Dados      |
|Balde           | Contentor/Recolha |
|Documento JSON    | Item / Documento |

## <a name="key-differences"></a>Diferenças principais

* A Azure Cosmos DB tem um campo de identificação dentro do documento, enquanto a Couchbase tem o ID como parte do balde. O campo "ID" é único em toda a divisória.

* Azure Cosmos DB escala utilizando a técnica de partição ou sharding. O que significa que divide os dados em vários fragmentos/divisórias. Estas divisórias/fragmentos são criados com base na propriedade chave de partição que você fornece. Pode selecionar a chave de partição para otimizar a leitura, bem como escrever operações ou ler/escrever otimizado também. Para saber mais, consulte o artigo [de partilha.](./partition-data.md)

* Em Azure Cosmos DB, não é necessário que a hierarquia de alto nível denote a coleção porque o nome da coleção já existe. Esta funcionalidade torna a estrutura JSON muito mais simples. Segue-se um exemplo que mostra diferenças no modelo de dados entre a Couchbase e a Azure Cosmos DB:

   **Couchbase**: Id de documento = "99FF4444"

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

   **Azure Cosmos DB**: Consulte "ID" dentro do documento como mostrado abaixo

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

A Azure Cosmos DB tem seguindo os SDKs para apoiar diferentes estruturas java:

* Async SDK
* SDK de botas de primavera

As seguintes secções descrevem quando usar cada um destes SDKs. Considere um exemplo onde temos três tipos de cargas de trabalho:

## <a name="couchbase-as-document-repository--spring-data-based-custom-queries"></a>Base de sofá como repositório de documentos & consultas personalizadas baseadas em dados de mola

Se a carga de trabalho que está a migrar for baseada no SDK baseado em Botas de primavera, então pode utilizar os seguintes passos:

1. Adicione o progenitor ao ficheiro POM.xml:

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

1. Adicione propriedades de aplicação sob recursos e especifique o seguinte. Certifique-se de substituir os parâmetros de nome URL, chave e base de dados:

   ```java
      azure.cosmosdb.uri=<your-cosmosDB-URL>
      azure.cosmosdb.key=<your-cosmosDB-key>
      azure.cosmosdb.database=<your-cosmosDB-dbName>
   ```

1. Defina o nome da coleção no modelo. Também pode especificar mais anotações. Por exemplo, ID, chave de partição para denotá-los explicitamente:

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

Seguem-se os códigos de snippets para operações crud:

### <a name="insert-and-update-operations"></a>Insira e atualize as operações

Onde *_repo* é objeto de repositório e *doc* é objeto da classe POJO. Pode utilizar `.save` para inserir ou sermantes (se o documento tiver sido encontrado com identificação especificada). O seguinte código de corte mostra como inserir ou atualizar um objeto doc:

```_repo.save(doc);```

### <a name="delete-operation"></a>Eliminar operação

Considere o seguinte código snippet, onde o objeto do doc terá identificação e chave de partição obrigatória para localizar e eliminar o objeto:

```_repo.delete(doc);```

### <a name="read-operation"></a>Ler Operação

Pode ler o documento com ou sem especificar a chave de partição. Se não especificar a chave de partição, então é tratada como uma consulta de partição cruzada. Considere as seguintes amostras de código, a primeira executará o funcionamento utilizando o id e o campo chave da partição. O segundo exemplo utiliza um campo regular & sem especificar o campo chave da divisória.

* ```_repo.findByIdAndName(objDoc.getId(),objDoc.getName());```
* ```_repo.findAllByStatus(objDoc.getStatus());```

É isso, agora podes usar a tua aplicação com o Azure Cosmos DB. A amostra completa de código para o exemplo descrito neste doc está disponível no [repo CouchbaseToCosmosDB-SpringCosmos GitHubs.](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/SpringCosmos)

## <a name="couchbase-as-a-document-repository--using-n1ql-queries"></a>Couchbase como um repositório de documentos & usando consultas N1QL

Consultas N1QL é a maneira de definir consultas na Base de Sofá.

|Consulta N1QL | Consulta Azure CosmosDB|
|-------------------|-------------------|
|SELECT`TravelDocument`META(.id AS `TravelDocument`id, `TravelDocument` `_type` .* FROM WHERE = "com.xx.xx.xx.xx.xxx.xxx.xxxx" e país = 'Índia' e ANY m em Vistos SATISFIES m.type == 'Multi-Entry' e m.Country IN ['Índia', Butão'] ORDEM BY ` Validity` DESC LIMIT 25 OFFSET 0   | SELECT c.id,c FROM c JOIN m in c.country='India' WHERE c._type = " com.xx.xx.xx.xx.xxx.xxx.xxxx" e c.country = 'Índia' e m.type = 'Multi-Entry' e m.Country IN ('Índia', 'Butão') ORDER BY c.Validade DESC OFFSET 0 LIMIT 25 |

Pode notar as seguintes alterações nas suas consultas N1QL:

* Não precisa de usar a palavra-chave META nem consultar o documento de primeiro nível. Em vez disso, pode criar a sua própria referência ao recipiente. Neste exemplo, consideramos-o "c" (pode ser qualquer coisa). Esta referência é usada como prefixo para todos os campos de primeiro nível. Exemplo fr, c.id, c.country etc.

* Em vez de "ANY" agora pode fazer uma adesão ao subdocumento e encaminhá-lo com um pseudónimo dedicado como "m". Depois de ter criado pseudónimo para um subdocumento, precisa de usar pseudónimo. Por exemplo, m.Country.

* A sequência de OFFSET é diferente na consulta do Azure Cosmos DB, primeiro tens de especificar OFFSET e DEPOIS LIMIT. Recomenda-se que não utilize o Spring Data SDK se estiver a utilizar consultas personalizadas máximas, uma vez que pode ter despesas desnecessárias ao lado do cliente ao passar a consulta para o Azure Cosmos DB. Em vez disso, temos um SDK Async Java, que pode ser utilizado de forma muito eficiente neste caso.

### <a name="read-operation"></a>Continuar a funcionar

Utilize o SDK Async Java com os seguintes passos:

1. Configure a seguinte dependência no ficheiro POM.xml:

   ```java
   <!-- https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb -->
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmos</artifactId>
       <version>3.0.0</version>
   </dependency>
   ```

1. Crie um objeto de ligação para `ConnectionBuilder` o Azure Cosmos DB utilizando o método como mostrado no exemplo seguinte. Certifique-se de que coloca esta declaração no feijão de modo a que o seguinte código seja executado apenas uma vez:

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

1. Para executar a consulta, tem de executar o seguinte código:

   ```java
   Flux<FeedResponse<CosmosItemProperties>> objFlux= container.queryItems(query, fo);
   ```

Agora, com a ajuda do método acima, pode passar várias consultas e executar sem qualquer aborrecimento. No caso de ter a obrigação de executar uma grande consulta, que pode ser dividida em múltiplas consultas, experimente o seguinte código em vez do anterior:

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

Com o código anterior, pode executar consultas paralelas e aumentar as execuções distribuídas para otimizar. Mais à frente, também pode executar as operações de inserção e atualização:

### <a name="insert-operation"></a>Inserção de operação

Para inserir o documento, execute o seguinte código:

```java 
Mono<CosmosItemResponse> objMono= container.createItem(doc,ro);
```

Em seguida, subscreva mono como:

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

A operação upsert requer que especifique o documento que precisa de ser atualizado. Para obter o documento completo, pode utilizar o corte mencionado na operação de leitura de posição e, em seguida, modificar os campos necessários. O seguinte código snippet upserts o documento:

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
Em seguida, subscreva o mono. Consulte o corte de subscrição mono na operação de inserção.

### <a name="delete-operation"></a>Eliminar operação

O seguinte corte eliminará o funcionamento:

```java     
CosmosItem objItem= container.getItem(doc.Id, doc.Tenant);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

Em seguida, subscreva o mono, consulte o corte de subscrição mono na operação de inserção. A amostra completa de código está disponível no [repo CouchbaseToCosmosDB-AsyncInSpring](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/AsyncInSpring) GitHub.

## <a name="couchbase-as-a-keyvalue-pair"></a>Couchbase como um par chave/valor

Este é um tipo simples de carga de trabalho em que você pode realizar procuras em vez de consultas. Utilize os seguintes passos para pares chave/valor:

1. Considere ter "/ID" como chave principal, o que fará com que possa realizar a operação de procuração diretamente na partição específica. Crie uma coleção e especifique "/ID" como chave de partição.

1. Desligue completamente o índice. Como vai executar operações de procuração, não faz sentido carregar as despesas de indexação. Para desligar a indexação, assine no portal Azure, goto Azure Cosmos DB Account. Abra o **Data Explorer,** selecione a sua **Base de Dados** e o **Recipiente**. Abra o separador **Definições de escala &** e selecione a **Política de Indexação**. Atualmente, a política de indexação parece ser a seguinte:
    
   ```json
   {
       "indexingMode": "consistent",
       "includedPaths": 
       [
           {
            "path": "/*",
            "indexes": 
             [
                {
                  "kind": "Range",
                  "dataType": "Number"
                },
                {
                  "kind": "Range",
                  "dataType": "String"
                },
                {
                   "kind": "Spatial",
                   "dataType": "Point"
                }
             ]
          }
       ],
       "excludedPaths": 
       [
         {
             "path": "/path/to/single/excluded/property/?"
         },
         {
             "path": "/path/to/root/of/multiple/excluded/properties/*"
         }
      ]
   }
   ````

   Substitua a política de indexação acima pela seguinte política:

   ```json
   {
       "indexingMode": "none"
   }
   ```

1. Utilize o seguinte fragmento de código para criar o objeto de ligação. Objeto de ligação @Bean (a ser colocado ou estática):

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

Agora pode executar as operações crud da seguinte forma:

### <a name="read-operation"></a>Continuar a funcionar

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

### <a name="insert-operation"></a>Inserção de operação

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

Para atualizar o valor de um item, consulte o código abaixo:

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
Em seguida, subscreva o mono, consulte o corte de subscrição mono na operação de inserção.

### <a name="delete-operation"></a>Eliminar operação

Utilize o seguinte corte para executar a operação de exclusão:

```java     
CosmosItem objItem= container.getItem(id, id);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

Em seguida, subscreva o mono, consulte o corte de subscrição mono na operação de inserção. A amostra completa de código está disponível no [repo CouchbaseToCosmosDB-AsyncKeyValue](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/AsyncKeyValue) GitHub.

## <a name="data-migration"></a>Migração de Dados

Há duas formas de migrar dados.

* Utilize a Fábrica de **Dados Azure:** Este é o método mais recomendado para migrar os dados. Configure a fonte como Couchbase e afunde como Azure Cosmos DB SQL API, consulte o artigo de [conector](../data-factory/connector-azure-cosmos-db.md) Azure Cosmos DB Data Factory para passos detalhados.

* Utilize a ferramenta de importação de **dados Azure Cosmos DB:** Esta opção é recomendada para migrar usando VMs com menos quantidade de dados. Para obter passos detalhados, consulte o artigo do importador de [dados.](./import-data.md)

## <a name="next-steps"></a>Passos Seguintes

* Para fazer testes de desempenho, consulte o desempenho e os testes de escala com o artigo da [Azure Cosmos DB.](./performance-testing.md)
* Para otimizar o código, consulte dicas de desempenho para o artigo [da Azure Cosmos DB.](./performance-tips-async-java.md)
* Explore java Async V3 SDK, [sdk referência](https://github.com/Azure/azure-cosmosdb-java/tree/v3) GitHub repo.
