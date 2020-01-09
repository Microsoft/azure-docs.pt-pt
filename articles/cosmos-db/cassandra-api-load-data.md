---
title: 'Tutorial: aplicativo Java para carregar dados de exemplo em uma tabela API do Cassandra no Azure Cosmos DB'
description: Este tutorial mostra como carregar dados de usuário de exemplo em uma tabela API do Cassandra no Azure Cosmos DB usando um aplicativo Java.
author: kanshiG
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: govindk
ms.reviewer: sngun
Customer intent: As a developer, I want to build a Java application to load data to a Cassandra API table in Azure Cosmos DB so that customers can store and manage the key/value data and utilize the global distribution, elastic scaling, multi-master, and other capabilities offered by Azure Cosmos DB.
ms.openlocfilehash: 66c292bcb02e3b2b215cabe4968fa30a45422cef
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445656"
---
# <a name="tutorial-load-sample-data-into-a-cassandra-api-table-in-azure-cosmos-db"></a>Tutorial: carregar dados de exemplo em uma tabela de API do Cassandra no Azure Cosmos DB

Como desenvolvedor, você pode ter aplicativos que usam pares chave/valor. Você pode usar API do Cassandra conta no Azure Cosmos DB para armazenar e gerenciar dados de chave/valor. Este tutorial mostra como carregar dados de usuário de exemplo em uma tabela em uma conta de API do Cassandra no Azure Cosmos DB usando um aplicativo Java. O aplicativo Java usa o [Driver Java](https://github.com/datastax/java-driver) e carrega dados de usuário, como ID de usuário, nome de usuário e cidade do usuário. 

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Carregar dados em uma tabela Cassandra
> * Executar a aplicação

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Este artigo pertence a um tutorial com várias partes. Antes de começar este documento, certifique-se de que [cria a conta, um keyspace e uma tabela da API para Cassandra](create-cassandra-api-account-java.md).   

## <a name="load-data-into-the-table"></a>Carregar dados para a tabela

Utilize os seguintes passos para carregar dados para a sua tabela da API para Cassandra:

1. Abra o arquivo "userrepository. java" na pasta "src\main\java\com\azure\cosmosdb\cassandra" e acrescente o código para inserir os campos user_id, user_name e user_bcity na tabela:

   ```java
   /**
   * Insert a row into user table
   *
   * @param id   user_id
   * @param name user_name
   * @param city user_bcity
   */
   public void insertUser(PreparedStatement statement, int id, String name, String city) {
        BoundStatement boundStatement = new BoundStatement(statement);
        session.execute(boundStatement.bind(id, name, city));
   }

   /**
   * Create a PrepareStatement to insert a row to user table
   *
   * @return PreparedStatement
   */
   public PreparedStatement prepareInsertStatement() {
      final String insertStatement = "INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (?,?,?)";
   return session.prepare(insertStatement);
   }
   ```
 
2. Abra o arquivo "UserProfile. java" na pasta "src\main\java\com\azure\cosmosdb\cassandra". Esta classe contém o método principal que chama os métodos createKeyspace e createTable definidos anteriormente. Agora, anexe o seguinte código para inserir alguns dados de exemplo na tabela da API para Cassandra.

   ```java
   //Insert rows into user table
   PreparedStatement preparedStatement = repository.prepareInsertStatement();
     repository.insertUser(preparedStatement, 1, "JohnH", "Seattle");
     repository.insertUser(preparedStatement, 2, "EricK", "Spokane");
     repository.insertUser(preparedStatement, 3, "MatthewP", "Tacoma");
     repository.insertUser(preparedStatement, 4, "DavidA", "Renton");
     repository.insertUser(preparedStatement, 5, "PeterS", "Everett");
   ```

## <a name="run-the-app"></a>Executar a aplicação

Abra um prompt de comando ou janela do terminal e altere o caminho da pasta para onde você criou o projeto. Execute o comando "instalação limpa do MVN" para gerar o arquivo cosmosdb-Cassandra-examples. jar dentro da pasta de destino e executar o aplicativo. 

```bash
cd "cassandra-demo"

mvn clean install

java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
```

Agora, pode abrir o Data Explorer no portal do Azure para verificar se as informações de utilizador foram adicionadas à tabela.
    
## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu a carregar dados de exemplo em uma conta de API do Cassandra no Azure Cosmos DB. Pode agora avançar para o artigo seguinte:

> [!div class="nextstepaction"]
> [Consultar dados da conta da API para Cassandra](cassandra-api-query-data.md)
