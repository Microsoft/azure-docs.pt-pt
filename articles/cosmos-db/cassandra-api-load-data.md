---
title: 'Tutorial: Carregar dados de exemplo para uma tabela de Cassandra API no Azure Cosmos DB através de uma aplicação de Java'
description: Este tutorial mostra como carregar dados de utilizador de exemplo para uma tabela Cassandra API no Azure Cosmos DB, através de uma aplicação de java.
author: kanshiG
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: govindk
ms.reviewer: sngun
Customer intent: As a developer, I want to build a Java application to load data to a Cassandra API table in Azure Cosmos DB so that customers can store and manage the key/value data and utilize the global distribution, elastic scaling, multi-master, and other capabilities offered by Azure Cosmos DB.
ms.openlocfilehash: e9fc96b9f26344045aa7e45fe7bdbe389e329377
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66472693"
---
# <a name="tutorial-load-sample-data-into-a-cassandra-api-table-in-azure-cosmos-db"></a>Tutorial: Carregar dados de exemplo para uma tabela de Cassandra API no Azure Cosmos DB

Como desenvolvedor, pode ter aplicações que utilizam pares chave/valor. Pode utilizar a conta de API para Cassandra na Azure Cosmos DB para armazenar e gerir dados de chave/valor. Este tutorial mostra como carregar dados de utilizador de exemplo para uma tabela numa conta de API para Cassandra na Azure Cosmos DB, através de uma aplicação de Java. A aplicação de Java utiliza a [controlador Java](https://github.com/datastax/java-driver) e carrega dados de utilizador, tais como o ID de utilizador, nome de utilizador e a cidade de utilizador. 

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Carregar dados para uma tabela Cassandra
> * Executar a aplicação

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Este artigo pertence a um tutorial com várias partes. Antes de começar este documento, certifique-se de que [cria a conta, um keyspace e uma tabela da API para Cassandra](create-cassandra-api-account-java.md).   

## <a name="load-data-into-the-table"></a>Carregar dados para a tabela

Utilize os seguintes passos para carregar dados para a sua tabela da API para Cassandra:

1. Abra o ficheiro de "UserRepository.java" sob a pasta "src\main\java\com\azure\cosmosdb\cassandra" e acrescentar o código para inserir os campos user_id, user_name e user_bcity na tabela:

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
 
2. Abra o ficheiro de "UserProfile.java" sob a pasta "src\main\java\com\azure\cosmosdb\cassandra". Esta classe contém o método principal que chama os métodos createKeyspace e createTable definidos anteriormente. Agora, anexe o seguinte código para inserir alguns dados de exemplo na tabela da API para Cassandra.

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

Abra uma linha de comandos ou janela de terminal e altere o caminho de pasta para onde criou o projeto. Execute o comando "install arquétipo limpo" para gerar o ficheiro cosmosdb-cassandra-examples dentro da pasta de destino e execute a aplicação. 

```bash
cd "cassandra-demo"

mvn clean install

java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
```

Agora, pode abrir o Data Explorer no portal do Azure para verificar se as informações de utilizador foram adicionadas à tabela.
    
## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu a carregar dados de exemplo para uma conta da API de Cassandra na Azure Cosmos DB. Pode agora avançar para o artigo seguinte:

> [!div class="nextstepaction"]
> [Consultar dados da conta da API para Cassandra](cassandra-api-query-data.md)
