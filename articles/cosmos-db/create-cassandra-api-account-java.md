---
title: 'Tutorial: Construir app Java para criar conta API Da API da Azure Cosmos DB Cassandra'
description: Este tutorial mostra como criar uma conta Cassandra API, adicionar uma base de dados (também chamada de espaço chave), e adicionar uma tabela a essa conta usando uma aplicação Java.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 4122e575fc7823fd2e79a26f210f06e5c1b0a835
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857980"
---
# <a name="tutorial-create-a-cassandra-api-account-in-azure-cosmos-db-by-using-a-java-application-to-store-keyvalue-data"></a>Tutorial: Criar uma conta Cassandra API em Azure Cosmos DB utilizando uma aplicação Java para armazenar dados chave/valor

Como desenvolvedor, pode ter aplicações que usam pares chave/valor. Pode utilizar uma conta Cassandra API no Azure Cosmos DB para armazenar os dados chave/valor. Este tutorial descreve como usar uma aplicação Java para criar uma conta Cassandra API em Azure Cosmos DB, adicionar uma base de dados (também chamada de espaço chave), e adicionar uma tabela. A aplicação Java utiliza o [controlador Java](https://github.com/datastax/java-driver) para criar uma base de dados de utilizador que contém detalhes como ID do utilizador, nome do utilizador e cidade utilizadora.  

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Criar uma conta de base de dados Cassandra
> * Obter a cadeia de ligação da conta
> * Criar um projeto maven e dependências
> * Adicionar uma base de dados e uma tabela
> * Executar a aplicação

## <a name="prerequisites"></a>Pré-requisitos 

* Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar. 

* Obtenha a versão mais recente do [Java Development Kit (JDK)](/java/azure/jdk/?view=azure-java-stable). 

* [Descarregue](https://maven.apache.org/download.cgi) e [instale](https://maven.apache.org/install.html) o arquivo binário [Maven.](https://maven.apache.org/) 
  - No Ubuntu, pode executar `apt-get install maven` para instalar o Maven. 

## <a name="create-a-database-account"></a>Criar uma conta de base de dados 

1. Inicie sessão no [portal do Azure](https://portal.azure.com/). 

2. Selecione **Criar um recurso** > **Bases de** > dados**Azure Cosmos DB**. 

3. No novo painel de **contas,** introduza as definições para a nova conta Azure Cosmos. 

   |Definição   |Valor sugerido  |Descrição  |
   |---------|---------|---------|
   |ID   |   Introduza um nome exclusivo    | Insira um nome único para identificar esta conta Azure Cosmos. <br/><br/>Uma vez que cassandra.cosmosdb.azure.com é anexado ao ID que indica para criar o seu ponto de contacto, utilize um ID exclusivo, mas identificável.         |
   |API    |  Cassandra   |  A API determina o tipo de conta a criar. <br/> Selecione **Cassandra,** porque neste artigo você vai criar uma base de dados de coluna ampla que pode ser consultada usando a sintaxe cassandra query language (CQL).  |
   |Subscrição    |  A sua subscrição        |  Selecione a subscrição Azure que pretende utilizar para esta conta Azure Cosmos.        |
   |Grupo de Recursos   | Introduzir um nome    |  Selecione **Criar Novo** e, em seguida, introduza um novo nome de grupo de recursos para a sua conta. Para simplicidade, pode utilizar o mesmo nome do ID.    |
   |Localização    |  Selecione a região mais próxima dos seus utilizadores    |  Selecione a localização geográfica para hospedar a sua conta Azure Cosmos. Utilize a localização mais próxima dos seus utilizadores, para lhes dar o acesso mais rápido aos dados.    |

   ![Criar conta com o portal](./media/create-cassandra-api-account-java/create-account.png)

4. Selecione **Criar**. <br/>A criação da conta demora alguns minutos. Após a criação do recurso, pode ver que o **Deployment conseguiu** a notificação no lado direito do portal.

## <a name="get-the-connection-details-of-your-account"></a>Obter os detalhes de ligação da conta  

Obtenha as informações de cadeia de ligação do portal Azure e copie-as no ficheiro de configuração Java. A cadeia de ligação permite que a aplicação comunique com a base de dados alojada. 

1. A partir do [portal Azure,](https://portal.azure.com/)vá à sua conta Azure Cosmos. 

2. Abra o painel de cordas de **ligação.**  

3. Copie os valores de **PONTO DE CONTACTO**, **PORTA**, **NOME DE UTILIZADOR** e ** PALAVRA-PASSE PRINCIPAL** para utilizá-los nos passos seguintes.

## <a name="create-the-project-and-the-dependencies"></a>Criar o projeto e as dependências 

O projeto de amostra java que você usa neste artigo está hospedado no GitHub. Você pode executar os passos neste doc ou baixar a amostra do repositório [azure-cosmos-db-cassandra-java-getting-start.](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started) 

Depois de descarregar os ficheiros, atualize as informações de cadeia de ligação dentro do ficheiro e execute-as. `java-examples\src\main\resources\config.properties`  

```java
cassandra_host=<FILLME_with_CONTACT POINT> 
cassandra_port = 10350 
cassandra_username=<FILLME_with_USERNAME> 
cassandra_password=<FILLME_with_PRIMARY PASSWORD> 
```

Utilize os seguintes passos para construir a amostra de raiz: 

1. No terminal ou na linha de comandos, crie um novo projeto Maven com o nome Cassandra-demo. 

   ```bash
   mvn archetype:generate -DgroupId=com.azure.cosmosdb.cassandra -DartifactId=cassandra-demo -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false 
   ```
 
2. Localize a pasta `cassandra-demo`. Com um editor de texto, abra o ficheiro `pom.xml` que foi gerado. 

   Adicione as dependências de Cassandra e construa plugins exigidos pelo seu projeto, como mostra o ficheiro [pom.xml.](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/pom.xml)  

3. Na pasta `cassandra-demo\src\main`, crie uma nova pasta com o nome `resources`.  Na pasta de recursos, adicione os ficheiros config.properties e log4j.properties:

   - O ficheiro [config.properties](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/src/main/resources/config.properties) armazena o ponto final de ligação e os valores-chave da conta Cassandra API. 
   
   - O ficheiro [log4j.properties](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/src/main/resources/log4j.properties) define o nível de exploração de madeira necessário para interagir com a API Cassandra.  

4. Navegue na `src/main/java/com/azure/cosmosdb/cassandra/` pasta. Na pasta cassandra, crie outra pasta com o nome `utils`. A nova pasta armazena as classes de utilitários necessárias para ligar à conta da API para Cassandra. 

   Adicione a classe [CassandraUtils](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/src/main/java/com/azure/cosmosdb/cassandra/util/CassandraUtils.java) para criar o cluster e abrir e fechar as sessões de Cassandra. O cluster liga-se à conta Cassandra API em Azure Cosmos DB e devolve uma sessão de acesso. Utilize a classe [Configurações](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/src/main/java/com/azure/cosmosdb/cassandra/util/Configurations.java) para ler as informações da cadeia de ligação do ficheiro config.properties. 

5. A amostra Java cria uma base de dados com informações do utilizador, tais como o nome do utilizador, o ID do utilizador e a cidade utilizadora. Terá de definir os métodos obter e definir, para aceder aos detalhes do utilizador na função principal.
 
   Crie uma aula [user.java](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/src/main/java/com/azure/cosmosdb/cassandra/examples/UserProfile.java) sob a `src/main/java/com/azure/cosmosdb/cassandra/` pasta com métodos de get e set. 

## <a name="add-a-database-and-a-table"></a>Adicionar uma base de dados e uma tabela  

Esta secção descreve como adicionar uma base de dados (keyspace) e uma tabela, utilizando o CQL.

1. Na pasta `src\main\java\com\azure\cosmosdb\cassandra`, crie uma nova pasta com o nome `repository`. 

2. Crie `UserRepository` a classe Java e adicione-lhe o seguinte código: 

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

4. Crie `UserProfile` a classe Java. Esta classe contém o método principal que chama os métodos createKeyspace e createTable definidos anteriormente: 

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

   Este código altera o diretório (cd) para o caminho da pasta onde criou o projeto. Em seguida, executa o comando `mvn clean install` para gerar o ficheiro `cosmosdb-cassandra-examples.jar` na pasta de destino. Por fim, executa a aplicação Java.

   ```bash
   cd cassandra-demo

   mvn clean install 

   java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile 
   ```

   A janela de terminal apresenta notificações quando o keyspace e a tabela estão criados. 
   
2. Agora, no portal do Azure, abra o **Data Explorer** para confirmar que o keyspace e a tabela foram criados.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a criar uma conta Cassandra API em Azure Cosmos DB, uma base de dados e uma tabela usando uma aplicação Java. Pode agora avançar para o artigo seguinte:

> [!div class="nextstepaction"]
> [carregar dados de exemplo para a tabela da API para Cassandra](cassandra-api-load-data.md).
