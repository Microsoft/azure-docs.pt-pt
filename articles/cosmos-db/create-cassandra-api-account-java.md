---
title: 'Tutorial: Criar uma conta da API de Cassandra através de uma aplicação de Java - Azure Cosmos DB'
description: Este tutorial mostra como criar uma conta da API de Cassandra, adicionar uma base de dados (também chamado de um keyspace) e adicionar uma tabela a essa conta com uma aplicação Java.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 12/06/2018
ms.custom: seodec18
Customer intent: As a developer, I want to build a Java application to access and manage Azure Cosmos DB resources so that customers can store key/value data and utilize the global distribution, elastic scaling, multi-master, and other capabilities offered by Azure Cosmos DB.
ms.openlocfilehash: b6876bf8210d47729ad8e765ccffe709a0fccacc
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56958699"
---
# <a name="tutorial-create-a-cassandra-api-account-in-azure-cosmos-db-by-using-a-java-application-to-store-keyvalue-data"></a>Tutorial: Criar uma conta da API de Cassandra na Azure Cosmos DB ao utilizar uma aplicação Java para armazenar dados de chave/valor

Como desenvolvedor, pode ter aplicações que utilizam pares chave/valor. Pode utilizar uma conta da API de Cassandra na Azure Cosmos DB para armazenar os dados de chave/valor. Este tutorial descreve como utilizar uma aplicação Java para criar uma conta da API de Cassandra na Azure Cosmos DB, adicionar uma base de dados (também chamado de um keyspace) e adicionar uma tabela. A aplicação de Java utiliza a [controlador Java](https://github.com/datastax/java-driver) para criar uma base de dados do utilizador que contém detalhes como o ID de utilizador, nome de utilizador e a cidade de utilizador.  

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Criar uma conta de base de dados Cassandra
> * Obter a cadeia de ligação da conta
> * Criar um projeto Maven e dependências
> * Adicionar uma base de dados e uma tabela
> * Executar a aplicação

## <a name="prerequisites"></a>Pré-requisitos 

* Se não tiver uma subscrição do Azure, crie uma  [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)  antes de começar. 

* Obter a versão mais recente do [Kit de desenvolvimento Java (JDK)](https://aka.ms/azure-jdks). 

* [Baixe](https://maven.apache.org/download.cgi) e [instale](https://maven.apache.org/install.html) o [Maven](https://maven.apache.org/) arquivo binário. 
  - No Ubuntu, pode executar  `apt-get install maven`  para instalar o Maven. 

## <a name="create-a-database-account"></a>Criar uma conta de base de dados 

1. Inicie sessão no  [portal do Azure](https://portal.azure.com/). 

2. Selecione **Criar um recurso** > **Bases de dados** > **Azure Cosmos DB**. 

3. Na **nova conta** painel, introduza as definições para a nova conta do Cosmos do Azure. 

   |Definição   |Valor sugerido  |Descrição  |
   |---------|---------|---------|
   |ID   |   Introduza um nome exclusivo    | Introduza um nome exclusivo para identificar esta conta do Cosmos do Azure. <br/><br/>Uma vez que cassandra.cosmosdb.azure.com é anexado ao ID que indica para criar o seu ponto de contacto, utilize um ID exclusivo, mas identificável.         |
   |API    |  Cassandra   |  A API determina o tipo de conta a criar. <br/> Selecione **Cassandra**, uma vez que este artigo, irá criar uma base de dados de coluna ampla que pode ser consultada ao utilizar a sintaxe de linguagem de consulta de Cassandra (CQL).  |
   |Subscrição    |  A sua subscrição        |  Selecione a subscrição do Azure que pretende utilizar para esta conta do Cosmos do Azure.        |
   |Grupo de Recursos   | Introduzir um nome    |  Selecione **criar novo**e, em seguida, introduza um nome de grupo de recursos novo para a sua conta. Para simplicidade, pode utilizar o mesmo nome do ID.    |
   |Localização    |  Selecione a região mais próxima dos seus utilizadores    |  Selecione a localização geográfica na qual vai alojar a sua conta do Cosmos do Azure. Utilize a localização mais próxima dos seus utilizadores, para lhes dar o acesso mais rápido aos dados.    |

   ![Criar conta com o portal](./media/create-cassandra-api-account-java/create-account.png)

4. Selecione **Criar**. <br/>A criação da conta demora alguns minutos. Depois do recurso é criado, pode ver o **implementação concluída com êxito** notificação no lado direito do portal.

## <a name="get-the-connection-details-of-your-account"></a>Obter os detalhes de ligação da conta  

Obter as informações de cadeia de ligação do portal do Azure e copie-as para o ficheiro de configuração de Java. A cadeia de ligação permite que a aplicação comunique com a base de dados alojada. 

1. Partir do [portal do Azure](https://portal.azure.com/), aceda à sua conta do Cosmos do Azure. 

2. Abra o painel  **Cadeia de Ligação**.  

3. Copie os valores de **PONTO DE CONTACTO**, **PORTA**, **NOME DE UTILIZADOR** e  **PALAVRA-PASSE PRINCIPAL** para utilizá-los nos passos seguintes.

## <a name="create-the-project-and-the-dependencies"></a>Criar o projeto e as dependências 

O projeto de exemplo de Java que utilizar neste artigo está alojado no GitHub. Pode executar os passos neste documento ou transferir o exemplo a partir da [azure-cosmos-db-cassandra-java-getting-started](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started) repositório. 

Depois de transferir os ficheiros, atualizar as informações da cadeia de ligação dentro do `java-examples\src\main\resources\config.properties` de ficheiros e executá-lo.  

```java
cassandra_host=<FILLME_with_CONTACT POINT> 
cassandra_port = 10350 
cassandra_username=<FILLME_with_USERNAME> 
cassandra_password=<FILLME_with_PRIMARY PASSWORD> 
```

Utilize os seguintes passos para criar o exemplo a partir do zero: 

1. No terminal ou na linha de comandos, crie um novo projeto Maven com o nome Cassandra-demo. 

   ```bash
   mvn archetype:generate -DgroupId=com.azure.cosmosdb.cassandra -DartifactId=cassandra-demo -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false 
   ```
 
2. Localize a pasta `cassandra-demo`. Com um editor de texto, abra o ficheiro `pom.xml` que foi gerado. 

   Adicione as dependências de Cassandra e criar plug-ins exigidas pelo seu projeto, como mostra a [pom. XML](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/pom.xml) ficheiro.  

3. Na pasta `cassandra-demo\src\main`, crie uma nova pasta com o nome `resources`.  Na pasta de recursos, adicione os ficheiros config.properties e log4j.properties:

   - O [Config. Properties](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/resources/config.properties) arquivo armazena os valores de ponto final e a chave de ligação da conta do Cassandra API. 
   
   - O [log4j.properties](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/resources/log4j.properties) arquivo define o nível de registo necessário para interagir com a API de Cassandra.  

4. Navegue para o `src/main/java/com/azure/cosmosdb/cassandra/` pasta. Na pasta cassandra, crie outra pasta com o nome `utils`. A nova pasta armazena as classes de utilitários necessárias para ligar à conta da API para Cassandra. 

   Adicione a classe [CassandraUtils](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/java/com/azure/cosmosdb/cassandra/util/CassandraUtils.java) para criar o cluster e abrir e fechar as sessões de Cassandra. O cluster liga-se à conta de API para Cassandra na Azure Cosmos DB e devolve uma sessão para aceder. Utilize a classe [Configurações](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/java/com/azure/cosmosdb/cassandra/util/Configurations.java) para ler as informações da cadeia de ligação do ficheiro config.properties. 

5. O exemplo de Java cria uma base de dados com informações de utilizador, como o nome de utilizador, ID de utilizador e a cidade de utilizador. Terá de definir os métodos obter e definir, para aceder aos detalhes do utilizador na função principal.
 
   Criar uma [User.java](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/java/com/azure/cosmosdb/cassandra/User.java) classe sob o `src/main/java/com/azure/cosmosdb/cassandra/` pasta com get e defina métodos. 

## <a name="add-a-database-and-a-table"></a>Adicionar uma base de dados e uma tabela  

Esta secção descreve como adicionar uma base de dados (keyspace) e uma tabela, utilizando CQL.

1. Na pasta `src\main\java\com\azure\cosmosdb\cassandra`, crie uma nova pasta com o nome `repository`. 

2. Criar o `UserRepository` Java de classe e adicione o seguinte código ao mesmo: 

   ```java
   package com.azure.cosmosdb.cassandra.repository; 
   import java.util.List; 
   import com.datastax.driver.core.BoundStatement; 
   import com.datastax.driver.core.PreparedStatement; 
   import com.datastax.driver.core.Row; 
   import com.datastax.driver.core.Session; 
   import org.slf4j.Logger; 
   import org.slf4j.LoggerFactory; 
   
   /** 
    * Create a Cassandra session 
    */ 
   public class UserRepository { 
   
       private static final Logger LOGGER = LoggerFactory.getLogger(UserRepository.class); 
       private Session session; 
       public UserRepository(Session session) { 
           this.session = session; 
       } 
   
       /** 
       * Create keyspace uprofile in cassandra DB 
        */ 
   
       public void createKeyspace() { 
            final String query = "CREATE KEYSPACE IF NOT EXISTS uprofile WITH REPLICATION = { 'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1 }"; 
           session.execute(query); 
           LOGGER.info("Created keyspace 'uprofile'"); 
       } 
   
       /** 
        * Create user table in cassandra DB 
        */ 
   
       public void createTable() { 
           final String query = "CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)"; 
           session.execute(query); 
           LOGGER.info("Created table 'user'"); 
       } 
   } 
   ```

3. Localize a pasta `src\main\java\com\azure\cosmosdb\cassandra` e crie uma nova subpasta com o nome `examples`.

4. Criar o `UserProfile` classe Java. Esta classe contém o método principal que chama os métodos createKeyspace e createTable definidos anteriormente: 

   ```java
   package com.azure.cosmosdb.cassandra.examples; 
   import java.io.IOException; 
   import com.azure.cosmosdb.cassandra.repository.UserRepository; 
   import com.azure.cosmosdb.cassandra.util.CassandraUtils; 
   import com.datastax.driver.core.PreparedStatement; 
   import com.datastax.driver.core.Session; 
   import org.slf4j.Logger; 
   import org.slf4j.LoggerFactory; 
   
   /** 
    * Example class which will demonstrate following operations on Cassandra Database on CosmosDB 
    * - Create Keyspace 
    * - Create Table 
    * - Insert Rows 
    * - Select all data from a table 
    * - Select a row from a table 
    */ 
   
   public class UserProfile { 
   
       private static final Logger LOGGER = LoggerFactory.getLogger(UserProfile.class); 
       public static void main(String[] s) throws Exception { 
           CassandraUtils utils = new CassandraUtils(); 
           Session cassandraSession = utils.getSession(); 
   
           try { 
               UserRepository repository = new UserRepository(cassandraSession); 
               //Create keyspace in cassandra database 
               repository.createKeyspace(); 
               //Create table in cassandra database 
               repository.createTable(); 
   
           } finally { 
               utils.close(); 
               LOGGER.info("Please delete your table after verifying the presence of the data in portal or from CQL"); 
           } 
       } 
   } 
   ```
 
## <a name="run-the-app"></a>Executar a aplicação 

1. Abra uma linha de comandos ou janela de terminal. Cole o seguinte bloco de código. 

   Esse código altera o diretório (cd) para o caminho da pasta onde criou o projeto. Em seguida, executa o comando `mvn clean install` para gerar o ficheiro `cosmosdb-cassandra-examples.jar` na pasta de destino. Por fim, executa a aplicação Java.

   ```bash
   cd cassandra-demo

   mvn clean install 

   java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile 
   ```

   A janela de terminal apresenta notificações quando o keyspace e a tabela estão criados. 
   
2. Agora, no portal do Azure, abra o **Data Explorer** para confirmar que o keyspace e a tabela foram criados.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu como criar uma conta da API de Cassandra na Azure Cosmos DB, uma base de dados e uma tabela com uma aplicação de Java. Pode agora avançar para o artigo seguinte:

> [!div class="nextstepaction"]
> [carregar dados de exemplo para a tabela da API para Cassandra](cassandra-api-load-data.md).
