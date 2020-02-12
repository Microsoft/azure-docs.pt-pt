---
title: Construa uma app Java com a Azure Cosmos DB Cassandra API
description: Este guia de introdução mostra como utilizar a Cassandra API do Azure Cosmos DB para criar uma aplicação de perfil com o portal do Azure e o Java
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.subservice: cosmosdb-cassandra
ms.devlang: java
ms.topic: quickstart
ms.date: 09/24/2018
ms.custom: seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 5a21f36136c6f1d77a2e9cb9108f539c9fb39334
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77134869"
---
# <a name="quickstart-build-a-java-app-to-manage-azure-cosmos-db-cassandra-api-data"></a>Quickstart: Construa uma app Java para gerir dados da API da Azure Cosmos DB Cassandra

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [Java](create-cassandra-java.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [python](create-cassandra-python.md)
>  

Neste arranque rápido, cria-se uma conta API Da API da Azure Cosmos DB Cassandra, e usa-se uma aplicação Cassandra Java clonada do GitHub para criar uma base de dados e um contentor da Cassandra. Azure Cosmos DB é um serviço de base de dados multi-modelo que permite criar e consultar rapidamente documentos, tabelas, basede-chaves e bases de dados de gráficos com capacidades de distribuição global e escala horizontal.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie um de graça.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Ou [experimente o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma subscrição Azure.
- Kit de [Desenvolvimento Java (JDK) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). Aponte a sua variável ambiente `JAVA_HOME` para a pasta onde o JDK está instalado.
- Um [arquivo binário Maven.](https://maven.apache.org/download.cgi) Em Ubuntu, corra `apt-get install maven` para instalar Maven.
- [Git](https://www.git-scm.com/downloads). Em Ubuntu, executar `sudo apt-get install git` para instalar Git.

## <a name="create-a-database-account"></a>Criar uma conta de base de dados

Antes de poder criar uma base de dados de documentos, tem de criar uma conta do Cassandra com o Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Agora, vamos trabalhar com código. Vamos clonar uma aplicação do Cassandra a partir do GitHub, definir a cadeia de ligação e executá-la. Vai ver como é fácil trabalhar com dados programaticamente. 

1. Abra uma linha de comandos. Crie uma nova pasta com o nome `git-samples`. Em seguida, feche a linha de comandos.

    ```bash
    md "C:\git-samples"
    ```

2. Abra uma janela de terminal do git, como o git bash e utilize o comando `cd` para alterar para uma nova pasta e instalar a aplicação de exemplo.

    ```bash
    cd "C:\git-samples"
    ```

3. Execute o seguinte comando para clonar o repositório de exemplo. Este comando cria uma cópia da aplicação de exemplo no seu computador.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started.git
    ```

## <a name="review-the-code"></a>Rever o código

Este passo é opcional. Se estiver interessado em saber de que forma o código cria os recursos da base de dados, pode rever os fragmentos seguintes. Caso contrário, pode avançar diretamente para [Update your connection string (Atualizar a cadeia de ligação)](#update-your-connection-string). Estes snippets são todos retirados do ficheiro *sRC/main/java/com/azure/cosmosdb/cassandra/util/CassandraUtils.java.*  

* As opções anfitrião de Cassandra, porta, nome de utilizador, palavra-passe e SSL estão definidas. As informações de cadeia de ligação provêm da página da cadeia de ligação no portal do Azure.

   ```java
   cluster = Cluster.builder().addContactPoint(cassandraHost).withPort(cassandraPort).withCredentials(cassandraUsername, cassandraPassword).withSSL(sslOptions).build();
   ```

* O `cluster` liga à Cassandra API do Azure Cosmos DB e devolve uma sessão para acesso.

    ```java
    return cluster.connect();
    ```

Os seguintes cortes são do ficheiro *sRC/main/java/com/azure/cosmosdb/cassandra/repository/UserRepository.java.*

* Criar um keyspace novo.

    ```java
    public void createKeyspace() {
        final String query = "CREATE KEYSPACE IF NOT EXISTS uprofile WITH replication = {'class': 'SimpleStrategy', 'replication_factor': '3' } ";
        session.execute(query);
        LOGGER.info("Created keyspace 'uprofile'");
    }
    ```

* Crie uma nova tabela.

   ```java
   public void createTable() {
        final String query = "CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)";
        session.execute(query);
        LOGGER.info("Created table 'user'");
   }
   ```

* Insira entidades de utilizador utilizando um objeto de instrução preparado.

    ```java
    public PreparedStatement prepareInsertStatement() {
        final String insertStatement = "INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (?,?,?)";
        return session.prepare(insertStatement);
    }

    public void insertUser(PreparedStatement statement, int id, String name, String city) {
        BoundStatement boundStatement = new BoundStatement(statement);
        session.execute(boundStatement.bind(id, name, city));
    }
    ```

* Efetue uma consulta para obter as informações de todos os utilizadores.

    ```java
   public void selectAllUsers() {
        final String query = "SELECT * FROM uprofile.user";
        List<Row> rows = session.execute(query).all();

        for (Row row : rows) {
            LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
        }
    }
    ```

* Efetue uma consulta para obter as informações de um único utilizador.

    ```java
    public void selectUser(int id) {
        final String query = "SELECT * FROM uprofile.user where user_id = 3";
        Row row = session.execute(query).one();

        LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
    }
    ```

## <a name="update-your-connection-string"></a>Atualizar a cadeia de ligação

Agora, regresse ao portal do Azure para obter as informações da cadeia de ligação e copie-as para a aplicação. Os detalhes da cadeia de ligação permitem que a aplicação comunique com a base de dados alojada.

1. Na sua conta Azure Cosmos DB no [portal Azure,](https://portal.azure.com/)selecione **Connection String**. 

    ![Ver e copiar um nome de utilizador no portal do Azure, página Cadeia de ligação](./media/create-cassandra-java/copy-username-connection-string-azure-portal.png)

2. Utilize o ![botão Copiar](./media/create-cassandra-java/copy-button-azure-portal.png) botão à direita do ecrã, para copiar o PONTO DE CONTACTO.

3. Abra o ficheiro *config.properties* a partir da pasta *C:\git-samples\azure-cosmosdb-cassandra-java-getting-start\java-examples\src\main\resources.* 

3. Cole o valor do PONTO DE CONTACTO do portal em `<Cassandra endpoint host>` na linha 2.

    Linha 2 de *config.propriedades* deve agora parecer semelhante a 

    `cassandra_host=cosmos-db-quickstart.cassandra.cosmosdb.azure.com`

3. Volte ao portal e copie o valor USERNAME. Cole o valor do NOME DE UTILIZADOR do portal em `<cassandra endpoint username>` na linha 4.

    Linha 4 de *config.propriedades* deve agora parecer semelhante a 

    `cassandra_username=cosmos-db-quickstart`

4. Volte ao portal e copie o valor password. Cole o valor da PALAVRA-PASSE do portal em `<cassandra endpoint password>` na linha 5.

    Linha 5 de *config.propriedades* deve agora parecer semelhante a 

    `cassandra_password=2Ggkr662ifxz2Mg...==`

5. Na linha 6, se pretender utilizar um certificado SSL específico, em seguida, substitua `<SSL key store file location>` com a localização do certificado SSL. Se não for fornecido um valor, o certificado JDK instalado em <JAVA_HOME>/jre/lib/segurança/cacerts é utilizado. 

6. Se tiver alterado a linha 6 para utilizar um certificado SSL específico, atualize a linha 7 para utilizar a palavra-passe para esse certificado. 

7. Guarde o ficheiro *config.properties.*

## <a name="run-the-java-app"></a>Executar a aplicação Java

1. Na janela de terminal do git, `cd` para a pasta `azure-cosmosdb-cassandra-java-getting-started\java-examples`.

    ```git
    cd "C:\git-samples\azure-cosmosdb-cassandra-java-getting-started\java-examples"
    ```

2. Na janela de terminal do git, utilize o comando seguinte para gerar o ficheiro `cosmosdb-cassandra-examples.jar`.

    ```git
    mvn clean install
    ```

3. Na janela de terminal do git, execute o seguinte comando para iniciar a aplicação do Java.

    ```git
    java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
    ```

    A janela de terminal apresenta notificações quando o keyspace e a tabela estão criados. A seguir, seleciona e devolve todos os utilizadores da tabela e apresenta a saída e, em seguida, seleciona uma linha por ID e apresenta o valor.  

    Prima Ctrl+C para parar a execução do programa e fechar a janela da consola.

4. No portal do Azure, abra o **Data Explorer** para consultar, modificar e trabalhar com estes dados novos. 

    ![Ver os dados no Data Explorer - Azure Cosmos DB](./media/create-cassandra-java/view-data-explorer-java-app.png)

## <a name="review-slas-in-the-azure-portal"></a>Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a criar uma conta Azure Cosmos DB com a Cassandra API, e a executar uma app Cassandra Java que cria uma base de dados e um contentor de Cassandra. Agora pode importar dados adicionais para a sua conta Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Importar dados do Cassandra para o Azure Cosmos DB](cassandra-import-data.md)
