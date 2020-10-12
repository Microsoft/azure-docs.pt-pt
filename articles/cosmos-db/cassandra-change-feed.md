---
title: Mude o feed na API DB AZure Cosmos para Cassandra
description: Saiba como utilizar o feed de mudança na AZure Cosmos DB API para a Cassandra obter as alterações feitas nos seus dados.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 11/25/2019
ms.author: thvankra
ms.openlocfilehash: 417a1dbc72c3b3c35c501351dcc8bda9dc95a78d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84431602"
---
# <a name="change-feed-in-the-azure-cosmos-db-api-for-cassandra"></a>Mude o feed na API DB AZure Cosmos para Cassandra

[O](change-feed.md) suporte de feed de mudança no Azure Cosmos DB API para Cassandra está disponível através dos predicados de consulta na Língua Desafetação de Cassandra (CQL). Utilizando estas condições predicados, pode consultar a API de alimentação de alteração. As aplicações podem obter as alterações feitas a uma tabela usando a chave primária (também conhecida como chave de partição) como é exigido no CQL. Em seguida, pode tomar outras ações com base nos resultados. As alterações nas linhas da tabela são capturadas na ordem do seu tempo de modificação e a ordem de classificação é garantida por tecla de partição.

O exemplo a seguir mostra como obter um feed de mudança em todas as linhas de uma tabela de keyspace Cassandra API usando .NET. O predicado COSMOS_CHANGEFEED_START_TIME() é utilizado diretamente no CQL para consultar itens no feed de alteração a partir de uma hora de início especificada (neste caso, a data atual). Você pode baixar a amostra completa, para C# [aqui](https://docs.microsoft.com/samples/azure-samples/azure-cosmos-db-cassandra-change-feed/cassandra-change-feed/) e para Java [aqui](https://github.com/Azure-Samples/cosmos-changefeed-cassandra-java).

Em cada iteração, a consulta retoma no último ponto as alterações foram lidas, utilizando o estado de paging. Podemos ver um fluxo contínuo de novas alterações na tabela no Keyspace. Veremos alterações nas linhas que são inseridas ou atualizadas. Atualmente, não é suportado o serviço de observação de operações de eliminação utilizando o feed de alteração na API de Cassandra.

# <a name="java"></a>[Java](#tab/java)

```java
        Session cassandraSession = utils.getSession();

        try {
              DateTimeFormatter dtf = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");  
               LocalDateTime now = LocalDateTime.now().minusHours(6).minusMinutes(30);  
               String query="SELECT * FROM uprofile.user where COSMOS_CHANGEFEED_START_TIME()='" 
                    + dtf.format(now)+ "'";
               
             byte[] token=null; 
             System.out.println(query); 
             while(true)
             {
                 SimpleStatement st=new  SimpleStatement(query);
                 st.setFetchSize(100);
                 if(token!=null)
                     st.setPagingStateUnsafe(token);
                 
                 ResultSet result=cassandraSession.execute(st) ;
                 token=result.getExecutionInfo().getPagingState().toBytes();
                 
                 for(Row row:result)
                 {
                     System.out.println(row.getString("user_name"));
                 }
             }
                    

        } finally {
            utils.close();
            LOGGER.info("Please delete your table after verifying the presence of the data in portal or from CQL");
        }

```

# <a name="c"></a>[C#](#tab/csharp)

```C#
    //set initial start time for pulling the change feed
     DateTime timeBegin = DateTime.UtcNow;

    //initialise variable to store the continuation token
    byte[] pageState = null;
    while (true)
    {
        try
        {

            //Return the latest change for all rows in 'user' table    
            IStatement changeFeedQueryStatement = new SimpleStatement(
            $"SELECT * FROM uprofile.user where COSMOS_CHANGEFEED_START_TIME() = '{timeBegin.ToString("yyyy-MM-ddTHH:mm:ss.fffZ", CultureInfo.InvariantCulture)}'");
            if (pageState != null)
            {
                changeFeedQueryStatement = changeFeedQueryStatement.SetPagingState(pageState);
            }
            Console.WriteLine("getting records from change feed at last page state....");
            RowSet rowSet = session.Execute(changeFeedQueryStatement);

            //store the continuation token here
            pageState = rowSet.PagingState;

            List<Row> rowList = rowSet.ToList();
            if (rowList.Count != 0)
            {
                for (int i = 0; i < rowList.Count; i++)
                {
                    string value = rowList[i].GetValue<string>("user_name");
                    int key = rowList[i].GetValue<int>("user_id");
                    // do something with the data - e.g. compute, forward to another event, function, etc.
                    // here, we just print the user name field
                    Console.WriteLine("user_name: " + value);
                }
            }
            else
            {
                Console.WriteLine("zero documents read");
            }
        }
        catch (Exception e)
        {
            Console.WriteLine("Exception " + e);
        }
    }

```
---

Para obter as alterações a uma única linha por tecla primária, pode adicionar a chave primária na consulta. O exemplo a seguir mostra como rastrear as mudanças para a linha onde "user_id = 1"

# <a name="c"></a>[C#](#tab/csharp)

```C#
    //Return the latest change for all row in 'user' table where user_id = 1
    IStatement changeFeedQueryStatement = new SimpleStatement(
    $"SELECT * FROM uprofile.user where user_id = 1 AND COSMOS_CHANGEFEED_START_TIME() = '{timeBegin.ToString("yyyy-MM-ddTHH:mm:ss.fffZ", CultureInfo.InvariantCulture)}'");

```

# <a name="java"></a>[Java](#tab/java)

```java
    String query="SELECT * FROM uprofile.user where user_id=1 and COSMOS_CHANGEFEED_START_TIME()='" 
                    + dtf.format(now)+ "'";
    SimpleStatement st=new  SimpleStatement(query);
```
---
## <a name="current-limitations"></a>Limitações atuais

As seguintes limitações são aplicáveis quando se utiliza o feed de alteração com a API Cassandra:

* As inserções e atualizações são atualmente suportadas. A operação de eliminação ainda não está suportada. Como uma solução alternativa, pode adicionar um marcador suave em linhas que estão a ser apagadas. Por exemplo, adicione um campo na linha chamado "apagado" e coloque-o como "verdadeiro".
* A última atualização é persistiu, uma vez que no núcleo da API SQL e não estão disponíveis atualizações intermédias para a entidade.


## <a name="error-handling"></a>Processamento de erros

Os seguintes códigos de erro e mensagens são suportados ao utilizar o feed de alteração na API cassandra:

* **Código de erro HTTP 429** - Quando o feed de alteração é limitado, devolve uma página vazia.

## <a name="next-steps"></a>Passos seguintes

* [Gerir recursos da Azure Cosmos DB Cassandra API utilizando modelos de Gestor de Recursos Azure](manage-cassandra-with-resource-manager.md)
