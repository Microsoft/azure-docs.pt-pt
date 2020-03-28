---
title: 'Tutorial: App Java para carregar dados de amostras numa tabela DaPi Cassandra em Azure Cosmos DB'
description: Este tutorial mostra como carregar dados de utilizadores de amostras para uma tabela Cassandra API em Azure Cosmos DB usando uma aplicação java.
author: kanshiG
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: govindk
ms.reviewer: sngun
Customer intent: As a developer, I want to build a Java application to load data to a Cassandra API table in Azure Cosmos DB so that customers can store and manage the key/value data and utilize the global distribution, elastic scaling, multi-master, and other capabilities offered by Azure Cosmos DB.
ms.openlocfilehash: 66c292bcb02e3b2b215cabe4968fa30a45422cef
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75445656"
---
# <a name="tutorial-load-sample-data-into-a-cassandra-api-table-in-azure-cosmos-db"></a>Tutorial: Carregue dados da amostra numa tabela DaPi Cassandra em Azure Cosmos DB

Como desenvolvedor, pode ter aplicações que usam pares chave/valor. Pode utilizar a conta Cassandra API no Azure Cosmos DB para armazenar e gerir dados chave/valor. Este tutorial mostra como carregar dados de utilizadores de amostras para uma tabela numa conta Cassandra API em Azure Cosmos DB usando uma aplicação Java. A aplicação Java utiliza o [controlador Java](https://github.com/datastax/java-driver) e carrega dados de utilizador como id do utilizador, nome do utilizador e cidade utilizadora. 

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Carregue os dados numa tabela cassandra
> * Executar a aplicação

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Este artigo pertence a um tutorial com várias partes. Antes de começar com este doc, certifique-se de [criar a conta Cassandra API, keyspace e tabela](create-cassandra-api-account-java.md).   

## <a name="load-data-into-the-table"></a>Carregar dados para a tabela

Utilize os seguintes passos para carregar dados para a sua tabela da API para Cassandra:

1. Abra o ficheiro "UserRepository.java" sob a pasta "src\main\java\com\azure\cosmosdb\cassandra" e apreenda o código para inserir os campos de user_id, user_name e user_bcity na tabela:

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
 
2. Abra o ficheiro "UserProfile.java" sob a pasta "src\main\java\com\azure\cosmosdb\cassandra". Esta classe contém o método principal que chama os métodos createKeyspace e createTable definidos anteriormente. Agora, anexe o seguinte código para inserir alguns dados de exemplo na tabela da API para Cassandra.

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

Abra uma janela de comando ou terminal e mude o caminho da pasta para onde criou o projeto. Execute o comando "mvn clean install" para gerar o ficheiro cosmosdb-cassandra-exemplos.jar dentro da pasta alvo e executar a aplicação. 

```bash
cd "cassandra-demo"

mvn clean install

java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
```

Agora, pode abrir o Data Explorer no portal do Azure para verificar se as informações de utilizador foram adicionadas à tabela.
    
## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a carregar dados de amostras para uma conta da Cassandra API em Azure Cosmos DB. Pode agora avançar para o artigo seguinte:

> [!div class="nextstepaction"]
> [Consultar dados da conta da API para Cassandra](cassandra-api-query-data.md)
