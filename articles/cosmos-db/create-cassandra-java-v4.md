---
title: Aplicativo Java com Azure Cosmos DB Cassandra API usando Java 4.0 SDK
description: Este quickstart mostra como usar a API API AZure Cosmos DB Cassandra para criar uma aplicação de perfil com o portal Azure e Java 4.0 SDK.
ms.service: cosmos-db
author: TheovanKraay
ms.author: thvankra
ms.subservice: cosmosdb-cassandra
ms.devlang: java
ms.topic: quickstart
ms.date: 05/18/2020
ms.custom: seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 076cb892740b99971400fbc34f60dc1083554555
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90532210"
---
# <a name="quickstart-build-a-java-app-to-manage-azure-cosmos-db-cassandra-api-data-v4-driver"></a>Quickstart: Construa uma app Java para gerir dados da Azure Cosmos DB Cassandra API (v4 Driver)

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [.NET Core](create-cassandra-dotnet-core.md)
> * [Java v3](create-cassandra-java.md)
> * [Java v4](create-cassandra-java-v4.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
> 

Neste quickstart, você cria uma conta Azure Cosmos DB Cassandra API, e use uma aplicação Cassandra Java clonada do GitHub para criar uma base de dados e um recipiente Cassandra usando os [motoristas de Apache Cassandra v4.x](https://github.com/datastax/java-driver/tree/4.x) para Java. Azure Cosmos DB é um serviço de base de dados multi-modelo que permite criar e consultar rapidamente documentos, tabelas, valor-chave e bases de dados de gráficos com capacidades de distribuição global e escala horizontal.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie um de graça.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Ou [experimente a Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma subscrição do Azure.
- [Kit de Desenvolvimento de Java (JDK) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). Aponte a `JAVA_HOME` variável ambiente para a pasta onde o JDK está instalado.
- Um [arquivo binário de Maven.](https://maven.apache.org/download.cgi) Em Ubuntu, corra `apt-get install maven` para instalar Maven.
- [Git.](https://www.git-scm.com/downloads) Em Ubuntu, corra `sudo apt-get install git` para instalar Git.

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
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started-v4.git
    ```

## <a name="review-the-code"></a>Rever o código

Este passo é opcional. Se estiver interessado em saber de que forma o código cria os recursos da base de dados, pode rever os fragmentos seguintes. Caso contrário, pode avançar diretamente para [Update your connection string (Atualizar a cadeia de ligação)](#update-your-connection-string). Estes snippets são todos retirados do *ficheiro src/main/java/com/azure/cosmosdb/cassandra/util/CassandraUtils.java.*  

* A `CqlSession` ligação à Azure Cosmos DB Cassandra API e devolve uma sessão de acesso `Cluster` (objeto do condutor V3 está agora obsoleto). Cassandra Host, Port, User name e password é definida usando a página de cadeia de ligação no portal Azure.

    ```java
        this.session = CqlSession.builder().withSslContext(sc)
                .addContactPoint(new InetSocketAddress(cassandraHost, cassandraPort)).withLocalDatacenter(region)
                .withAuthCredentials(cassandraUsername, cassandraPassword).build();
    ```


Os seguintes snippets são do ficheiro *src/main/java/com/azure/cosmosdb/cassandra/repositório/UserRepository.java.*

* Largue o espaço-chave se já existir de uma execução anterior.

    ```java
    public void dropKeyspace() {
        String query = "DROP KEYSPACE IF EXISTS "+keyspace+"";
        session.execute(query);
        LOGGER.info("dropped keyspace '"+keyspace+"'");
    } 
    ```
* É criado um novo keyspace.

    ```java
    public void createKeyspace() {
        String query = "CREATE KEYSPACE "+keyspace+" WITH REPLICATION = { 'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1 }";
        session.execute(query);
        LOGGER.info("Created keyspace '"+keyspace+"'");
    }
    ```

* É criada uma nova tabela.

   ```java
    public void createTable() {
        String query = "CREATE TABLE "+keyspace+"."+table+" (user_id int PRIMARY KEY, user_name text, user_bcity text)";
        session.execute(query);
        LOGGER.info("Created table '"+table+"'");
    }
   ```

* As entidades utilizadoras são inseridas com um objeto de declaração preparado.

    ```java
    public String prepareInsertStatement() {
        final String insertStatement = "INSERT INTO  "+keyspace+"."+table+" (user_id, user_name , user_bcity) VALUES (?,?,?)";
        return insertStatement;
    }

    public void insertUser(String preparedStatement, int id, String name, String city) {
        PreparedStatement prepared = session.prepare(preparedStatement);
        BoundStatement bound = prepared.bind(id, city, name).setIdempotent(true);
        session.execute(bound);
    }
    ```

* Consulta para obter todas as informações do Utilizador.

    ```java
    public void selectAllUsers() {
        final String query = "SELECT * FROM "+keyspace+"."+table+"";
        List<Row> rows = session.execute(query).all();

        for (Row row : rows) {
            LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
        }
    }
    ```

 * Consulta para obter uma única informação do Utilizador.

    ```java
    public void selectUser(int id) {
        final String query = "SELECT * FROM "+keyspace+"."+table+" where user_id = 3";
        Row row = session.execute(query).one();

        LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
    }
    ```

## <a name="update-your-connection-string"></a>Atualizar a cadeia de ligação

Agora, regresse ao portal do Azure para obter as informações da cadeia de ligação e copie-as para a aplicação. Os detalhes da cadeia de ligação permitem que a aplicação comunique com a base de dados alojada.

1. Na sua conta DB Azure Cosmos no [portal Azure,](https://portal.azure.com/)selecione **Connection String**. 

    :::image type="content" source="./media/create-cassandra-java/copy-username-connection-string-azure-portal.png" alt-text="Ver e copiar um nome de utilizador no portal do Azure, página Cadeia de ligação":::

2. Utilize o :::image type="icon" source="./media/create-cassandra-java/copy-button-azure-portal.png"::: botão no lado direito do ecrã para copiar o valor CONTACT POINT.

3. Abra o ficheiro *config.properties* a partir da pasta *C:\git-samples\azure-cosmosdb-cassandra-java-start-start-start\java-exemplos\src\main\resources.* 

3. Cole o valor do PONTO DE CONTACTO do portal em `<Cassandra endpoint host>` na linha 2.

    Linha 2 de *config.propriedades* devem agora parecer semelhantes a 

    `cassandra_host=cosmos-db-quickstart.cassandra.cosmosdb.azure.com`

3. Volte ao portal e copie o valor USERNAME. Cole o valor do NOME DE UTILIZADOR do portal em `<cassandra endpoint username>` na linha 4.

    Linha 4 de *config.propriedades* devem agora parecer semelhantes a 

    `cassandra_username=cosmos-db-quickstart`

4. Volte ao portal e copie o valor PASSWORD. Cole o valor da PALAVRA-PASSE do portal em `<cassandra endpoint password>` na linha 5.

    Linha 5 de *config.propriedades* devem agora parecer semelhantes a 

    `cassandra_password=2Ggkr662ifxz2Mg...==`

5. Na linha 6, se pretender utilizar um certificado TLS/SSL específico, substitua-o `<SSL key store file location>` pela localização do certificado TLS/SSL. Se não for fornecido um valor, o certificado JDK instalado em <JAVA_HOME>/jre/lib/segurança/cacerts é utilizado. 

6. Se alterou a linha 6 para utilizar um certificado TLS/SSL específico, atualize a linha 7 para utilizar a palavra-passe para esse certificado. 

7. Note que terá de adicionar a região predefinido `West US` (por exemplo) para o ponto de contacto, por exemplo.

    `region=West US`

    Isto porque o controlador v.4x só permite que um DC local seja emparelhado com o ponto de contacto. Se quiser adicionar uma região que não seja o padrão (que é a região que foi dada quando a conta Cosmos DB foi criada pela primeira vez), terá de utilizar o sufixo regional ao adicionar o ponto de contacto, por `host-westus.cassandra.cosmos.azure.com` exemplo.

8. Guarde o ficheiro *config.properties.*

## <a name="run-the-java-app"></a>Executar a aplicação Java

1. Na janela de terminal do git, `cd` para a pasta `azure-cosmosdb-cassandra-java-getting-started-v4`.

    ```git
    cd "C:\git-samples\azure-cosmosdb-cassandra-java-getting-started-v4"
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

    Prima Ctrl+C para parar a execução do programa e feche a janela da consola.

4. No portal do Azure, abra o **Data Explorer** para consultar, modificar e trabalhar com estes dados novos. 

    :::image type="content" source="./media/create-cassandra-java/view-data-explorer-java-app.png" alt-text="Ver os dados no Data Explorer - Azure Cosmos DB":::

## <a name="review-slas-in-the-azure-portal"></a>Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, aprendeu a criar uma conta DB Azure Cosmos com a Cassandra API, e executou uma aplicação Cassandra Java que cria uma base de dados e um recipiente Cassandra. Pode agora importar dados adicionais na sua conta DB Azure Cosmos. 

> [!div class="nextstepaction"]
> [Importar dados do Cassandra para o Azure Cosmos DB](cassandra-import-data.md)
