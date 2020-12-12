---
title: 'Tutorial: Construa app Java para criar conta Azure Cosmos DB Cassandra API'
description: Este tutorial mostra como criar uma conta API cassandra, adicionar uma base de dados (também chamada de espaço-chave) e adicionar uma tabela a essa conta usando uma aplicação Java.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 12/06/2018
ms.custom: seodec18, devx-track-java
ms.openlocfilehash: fe452f61d17f0b2014957e3b458ef1ad1b3c539d
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/12/2020
ms.locfileid: "97357638"
---
# <a name="tutorial-create-a-cassandra-api-account-in-azure-cosmos-db-by-using-a-java-application-to-store-keyvalue-data"></a>Tutorial: Criar uma conta API cassandra em Azure Cosmos DB utilizando uma aplicação Java para armazenar dados chave/valor
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Como desenvolvedor, pode ter aplicações que usam pares chave/valor. Pode utilizar uma conta API da Cassandra na Azure Cosmos DB para armazenar os dados chave/valor. Este tutorial descreve como usar uma aplicação Java para criar uma conta API Cassandra em Azure Cosmos DB, adicionar uma base de dados (também chamada de espaço-chave) e adicionar uma tabela. A aplicação Java utiliza o [controlador Java](https://github.com/datastax/java-driver) para criar uma base de dados de utilizadores que contenha detalhes como iD do utilizador, nome de utilizador e cidade do utilizador.  

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Criar uma conta de base de dados Cassandra
> * Obter a cadeia de ligação da conta
> * Criar um projeto Maven e dependências
> * Adicionar uma base de dados e uma tabela
> * Executar a aplicação

## <a name="prerequisites"></a>Pré-requisitos 

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar. 

* Obtenha a versão mais recente do [Java Development Kit (JDK)](/java/azure/jdk/?view=azure-java-stable&preserve-view=true). 

* [Faça o download](https://maven.apache.org/download.cgi) e [instale](https://maven.apache.org/install.html) o arquivo binário [Maven.](https://maven.apache.org/) 
  - No Ubuntu, pode executar `apt-get install maven` para instalar o Maven. 

## <a name="create-a-database-account"></a>Criar uma conta de base de dados 

1. Inicie sessão no [portal do Azure](https://portal.azure.com/). 

2. Selecione **Criar uma** base de  >  **dados de** recursos  >  **Azure Cosmos DB**. 

3. No painel de **conta Novo,** insira as definições para a nova conta Azure Cosmos. 

   |Definição   |Valor sugerido  |Descrição  |
   |---------|---------|---------|
   |ID   |   Introduza um nome exclusivo    | Insira um nome único para identificar esta conta Azure Cosmos. <br/><br/>Uma vez que cassandra.cosmosdb.azure.com é anexado ao ID que indica para criar o seu ponto de contacto, utilize um ID exclusivo, mas identificável.         |
   |API    |  Cassandra   |  A API determina o tipo de conta a criar. <br/> Selecione **Cassandra**, porque neste artigo você vai criar uma base de dados de colunas largas que pode ser consultada usando a sintaxe da Língua Desídua de Cassandra (CQL).  |
   |Subscrição    |  A sua subscrição        |  Selecione a subscrição Azure que pretende utilizar para esta conta Azure Cosmos.        |
   |Grupo de Recursos   | Introduza um nome    |  Selecione **Criar Novo** e, em seguida, introduza um novo nome de grupo de recursos para a sua conta. Para simplicidade, pode utilizar o mesmo nome do ID.    |
   |Localização    |  Selecione a região mais próxima dos seus utilizadores    |  Selecione a localização geográfica para hospedar a sua conta Azure Cosmos. Utilize a localização mais próxima dos seus utilizadores, para lhes dar o acesso mais rápido aos dados.    |

   :::image type="content" source="./media/create-cassandra-api-account-java/create-account.png" alt-text="Criar conta com o portal":::

4. Selecione **Criar**. <br/>A criação da conta demora alguns minutos. Após a criação do recurso, pode ver a notificação **da Implementação no** lado direito do portal.

## <a name="get-the-connection-details-of-your-account"></a>Obter os detalhes de ligação da conta  

Obtenha a informação do fio de ligação do portal Azure e copie-as no ficheiro de configuração Java. A cadeia de ligação permite que a aplicação comunique com a base de dados alojada. 

1. A partir do [portal Azure,](https://portal.azure.com/)aceda à sua conta Azure Cosmos. 

2. Abra o painel **de cordas de ligação.**  

3. Copie os valores de **PONTO DE CONTACTO**, **PORTA**, **NOME DE UTILIZADOR** e **PALAVRA-PASSE PRINCIPAL** para utilizá-los nos passos seguintes.

## <a name="create-the-project-and-the-dependencies"></a>Criar o projeto e as dependências 

O projeto de amostra de Java que você usa neste artigo está hospedado no GitHub. Você pode executar os passos neste doc ou baixar a amostra do [repositório azure-cosmos-db-cassandra-java-getting-start-start-tory.](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started) 

Depois de descarregar os ficheiros, atualize as informações de cadeia de ligação dentro do `java-examples\src\main\resources\config.properties` ficheiro e execute-as.  

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

   Adicione as dependências de Cassandra e construa plugins exigidos pelo seu projeto, como mostra o [ficheiropom.xml.](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/main/pom.xml)  

3. Na pasta `cassandra-demo\src\main`, crie uma nova pasta com o nome `resources`.  Na pasta de recursos, adicione os ficheiros config.properties e log4j.properties:

   - O ficheiro [config.properties](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/main/src/main/resources/config.properties) armazena o ponto final de ligação e os valores-chave da conta API cassandra. 
   
   - O ficheiro [log4j.properties](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/main/src/main/resources/log4j.properties) define o nível de registo necessário para interagir com a API de Cassandra.  

4. Navegue na `src/main/java/com/azure/cosmosdb/cassandra/` pasta. Na pasta cassandra, crie outra pasta com o nome `utils`. A nova pasta armazena as classes de utilitários necessárias para ligar à conta da API para Cassandra. 

   Adicione a classe [CassandraUtils](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/main/src/main/java/com/azure/cosmosdb/cassandra/util/CassandraUtils.java) para criar o cluster e abrir e fechar as sessões de Cassandra. O cluster conecta-se à conta da API cassandra em Azure Cosmos DB e devolve uma sessão de acesso. Utilize a classe [Configurações](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/main/src/main/java/com/azure/cosmosdb/cassandra/util/Configurations.java) para ler as informações da cadeia de ligação do ficheiro config.properties. 

5. A amostra de Java cria uma base de dados com informações do utilizador, tais como nome de utilizador, ID do utilizador e cidade utilizadora. Terá de definir os métodos obter e definir, para aceder aos detalhes do utilizador na função principal.
 
   Crie uma classe [.java utilizador](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/main/src/main/java/com/azure/cosmosdb/cassandra/examples/UserProfile.java) sob a pasta `src/main/java/com/azure/cosmosdb/cassandra/` com métodos de obter e definir. 

## <a name="add-a-database-and-a-table"></a>Adicionar uma base de dados e uma tabela  

Esta secção descreve como adicionar uma base de dados (espaço-chave) e uma tabela, utilizando o CQL.

1. Na pasta `src\main\java\com\azure\cosmosdb\cassandra`, crie uma nova pasta com o nome `repository`. 

2. Crie a `UserRepository` classe Java e adicione-lhe o seguinte código: 

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

4. Crie a `UserProfile` aula de Java. Esta classe contém o método principal que chama os métodos createKeyspace e createTable definidos anteriormente: 

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

Neste tutorial, aprendeu a criar uma conta API da Cassandra em Azure Cosmos DB, uma base de dados e uma tabela usando uma aplicação Java. Pode agora avançar para o artigo seguinte:

> [!div class="nextstepaction"]
> [carregar dados de exemplo para a tabela da API para Cassandra](cassandra-api-load-data.md).
