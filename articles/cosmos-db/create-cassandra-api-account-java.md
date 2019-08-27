---
title: 'Tutorial: Criar uma conta de API do Cassandra usando um aplicativo Java-Azure Cosmos DB'
description: Este tutorial mostra como criar uma conta de API do Cassandra, adicionar um banco de dados (também chamado de keyspace) e adicionar uma tabela a essa conta usando um aplicativo Java.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 12/06/2018
ms.custom: seodec18
Customer intent: As a developer, I want to build a Java application to access and manage Azure Cosmos DB resources so that customers can store key/value data and utilize the global distribution, elastic scaling, multi-master, and other capabilities offered by Azure Cosmos DB.
ms.openlocfilehash: d2d4d568f53c426b063f3285cc8d3d510c3db440
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034610"
---
# <a name="tutorial-create-a-cassandra-api-account-in-azure-cosmos-db-by-using-a-java-application-to-store-keyvalue-data"></a>Tutorial: Criar uma conta de API do Cassandra no Azure Cosmos DB usando um aplicativo Java para armazenar dados de chave/valor

Como desenvolvedor, você pode ter aplicativos que usam pares chave/valor. Você pode usar uma conta de API do Cassandra no Azure Cosmos DB para armazenar os dados de chave/valor. Este tutorial descreve como usar um aplicativo Java para criar uma conta de API do Cassandra no Azure Cosmos DB, adicionar um banco de dados (também chamado de keyspace) e adicionar uma tabela. O aplicativo Java usa o [Driver Java](https://github.com/datastax/java-driver) para criar um banco de dados de usuário que contém detalhes como ID de usuário, nome de usuário e cidade do usuário.  

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Criar uma conta de base de dados Cassandra
> * Obter a cadeia de ligação da conta
> * Criar um projeto Maven e dependências
> * Adicionar uma base de dados e uma tabela
> * Executar a aplicação

## <a name="prerequisites"></a>Pré-requisitos 

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar. 

* Obtenha a versão mais recente do [Java Development Kit (JDK)](https://aka.ms/azure-jdks). 

* [Baixe](https://maven.apache.org/download.cgi) e [Instale](https://maven.apache.org/install.html) o arquivo binário [Maven](https://maven.apache.org/) . 
  - No Ubuntu, pode executar `apt-get install maven` para instalar o Maven. 

## <a name="create-a-database-account"></a>Criar uma conta de base de dados 

1. Inicie sessão no [portal do Azure](https://portal.azure.com/). 

2. Selecione **Criar um recurso** > **Bases de dados** > **Azure Cosmos DB**. 

3. No painel **nova conta** , insira as configurações para a nova conta do Azure Cosmos. 

   |Definição   |Valor sugerido  |Descrição  |
   |---------|---------|---------|
   |ID   |   Introduza um nome exclusivo    | Insira um nome exclusivo para identificar esta conta do Azure Cosmos. <br/><br/>Como cassandra.cosmosdb.azure.com é acrescentado à ID que você fornece para criar seu ponto de contato, use uma ID exclusiva, mas identificável.         |
   |API    |  Cassandra   |  A API determina o tipo de conta a criar. <br/> Selecione **Cassandra**, porque neste artigo você criará um banco de dados de coluna larga que pode ser consultado usando a sintaxe CQL (Cassandra Query Language).  |
   |Subscription    |  A sua subscrição        |  Selecione a assinatura do Azure que você deseja usar para esta conta do Azure Cosmos.        |
   |Grupo de Recursos   | Introduzir um nome    |  Selecione **criar novo**e insira um novo nome de grupo de recursos para sua conta. Para simplicidade, pode utilizar o mesmo nome do ID.    |
   |Localização    |  Selecione a região mais próxima dos seus utilizadores    |  Selecione a localização geográfica na qual hospedar sua conta do Azure Cosmos. Use o local mais próximo de seus usuários, para dar a eles o acesso mais rápido aos dados.    |

   ![Criar conta com o portal](./media/create-cassandra-api-account-java/create-account.png)

4. Selecione **Criar**. <br/>A criação da conta demora alguns minutos. Depois que o recurso for criado, você poderá ver a notificação de **implantação bem-sucedida** no lado direito do Portal.

## <a name="get-the-connection-details-of-your-account"></a>Obter os detalhes de ligação da conta  

Obtenha as informações da cadeia de conexão do portal do Azure e copie-as no arquivo de configuração do Java. A cadeia de ligação permite que a aplicação comunique com a base de dados alojada. 

1. Na [portal do Azure](https://portal.azure.com/), vá para sua conta do cosmos do Azure. 

2. Abra o painel **cadeia de conexão** .  

3. Copie os valores de **PONTO DE CONTACTO**, **PORTA**, **NOME DE UTILIZADOR** e  **PALAVRA-PASSE PRINCIPAL** para utilizá-los nos passos seguintes.

## <a name="create-the-project-and-the-dependencies"></a>Criar o projeto e as dependências 

O projeto de exemplo Java que você usa neste artigo está hospedado no GitHub. Você pode executar as etapas neste documento ou baixar o exemplo do repositório [Azure-Cosmos-DB-Cassandra-Java-Getting-Started](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started) . 

Depois de baixar os arquivos, atualize as informações da cadeia de conexão `java-examples\src\main\resources\config.properties` dentro do arquivo e execute-o.  

```java
cassandra_host=<FILLME_with_CONTACT POINT> 
cassandra_port = 10350 
cassandra_username=<FILLME_with_USERNAME> 
cassandra_password=<FILLME_with_PRIMARY PASSWORD> 
```

Use as seguintes etapas para criar o exemplo do zero: 

1. No terminal ou na linha de comandos, crie um novo projeto Maven com o nome Cassandra-demo. 

   ```bash
   mvn archetype:generate -DgroupId=com.azure.cosmosdb.cassandra -DartifactId=cassandra-demo -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false 
   ```
 
2. Localize a pasta `cassandra-demo`. Com um editor de texto, abra o ficheiro `pom.xml` que foi gerado. 

   Adicione as dependências de Cassandra e os plug-ins de compilação exigidos pelo seu projeto, conforme mostrado no arquivo [pom. xml](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/pom.xml) .  

3. Na pasta `cassandra-demo\src\main`, crie uma nova pasta com o nome `resources`.  Na pasta de recursos, adicione os ficheiros config.properties e log4j.properties:

   - O arquivo [config. Properties](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/resources/config.properties) armazena o ponto de extremidade de conexão e os valores de chave da conta de API do Cassandra. 
   
   - O arquivo [Log4J. Properties](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/resources/log4j.properties) define o nível de registro em log necessário para interagir com o API do Cassandra.  

4. Navegue até a `src/main/java/com/azure/cosmosdb/cassandra/` pasta. Na pasta cassandra, crie outra pasta com o nome `utils`. A nova pasta armazena as classes de utilitários necessárias para ligar à conta da API para Cassandra. 

   Adicione a classe [CassandraUtils](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/java/com/azure/cosmosdb/cassandra/util/CassandraUtils.java) para criar o cluster e abrir e fechar as sessões de Cassandra. O cluster se conecta à conta de API do Cassandra no Azure Cosmos DB e retorna uma sessão a ser acessada. Utilize a classe [Configurações](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/java/com/azure/cosmosdb/cassandra/util/Configurations.java) para ler as informações da cadeia de ligação do ficheiro config.properties. 

5. O exemplo de Java cria um banco de dados com informações de usuário, como nome de usuário, ID de usuário e cidade do usuário. Terá de definir os métodos obter e definir, para aceder aos detalhes do utilizador na função principal.
 
   Crie uma classe [User. java](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/java/com/azure/cosmosdb/cassandra/User.java) sob a `src/main/java/com/azure/cosmosdb/cassandra/` pasta com os métodos get e Set. 

## <a name="add-a-database-and-a-table"></a>Adicionar uma base de dados e uma tabela  

Esta seção descreve como adicionar um banco de dados (keyspace) e uma tabela, usando CQL.

1. Na pasta `src\main\java\com\azure\cosmosdb\cassandra`, crie uma nova pasta com o nome `repository`. 

2. Crie a `UserRepository` classe Java e adicione o seguinte código a ela: 

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

4. Crie a `UserProfile` classe Java. Esta classe contém o método principal que chama os métodos createKeyspace e createTable definidos anteriormente: 

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

   Esse código altera o diretório (CD) para o caminho da pasta em que você criou o projeto. Em seguida, executa o comando `mvn clean install` para gerar o ficheiro `cosmosdb-cassandra-examples.jar` na pasta de destino. Por fim, executa a aplicação Java.

   ```bash
   cd cassandra-demo

   mvn clean install 

   java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile 
   ```

   A janela de terminal apresenta notificações quando o keyspace e a tabela estão criados. 
   
2. Agora, no portal do Azure, abra o **Data Explorer** para confirmar que o keyspace e a tabela foram criados.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, você aprendeu a criar uma conta de API do Cassandra no Azure Cosmos DB, um banco de dados e uma tabela usando um aplicativo Java. Pode agora avançar para o artigo seguinte:

> [!div class="nextstepaction"]
> [carregar dados de exemplo para a tabela da API para Cassandra](cassandra-api-load-data.md).
