---
title: 'Início rápido: Cassandra API com Java – Azure Cosmos DB'
description: Este guia de introdução mostra como utilizar a Cassandra API do Azure Cosmos DB para criar uma aplicação de perfil com o portal do Azure e o Java
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.subservice: cosmosdb-cassandra
ms.devlang: java
ms.topic: quickstart
ms.date: 09/24/2018
ms.openlocfilehash: 7f6108a5d1e8ee386641c6d1f7c09ea96e12458c
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56587605"
---
# <a name="quickstart-build-a-cassandra-app-with-java-sdk-and-azure-cosmos-db"></a>Início rápido: Criar uma aplicação do Cassandra com o Java SDK e o Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [Java](create-cassandra-java.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
>  

Este guia de introdução mostra como utilizar o Java e a [Cassandra API](cassandra-introduction.md) do Azure Cosmos DB para criar um perfil de aplicação através da clonagem de um exemplo do GitHub. Este início rápido também mostra como pode utilizar o portal do Azure baseado na Web para criar uma conta do Azure Cosmos DB.

O Azure Cosmos DB é um serviço de bases de dados com vários modelos e distribuído globalmente da Microsoft. Pode criar e consultar rapidamente o documento, a tabela, a chave/valor e as bases de dados de gráficos que beneficiam de capacidades de escalamento horizontal e distribuição global no centro do Azure Cosmos DB. 

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] Em alternativa, pode [Experimentar o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma subscrição do Azure, sem encargos e compromissos.

Além disso, necessita:

* [Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks)
    * No Ubuntu, execute `apt-get install default-jdk` para instalar o JDK.
    * Certifique-se de que define a variável de ambiente JAVA_HOME para apontar para a pasta onde está instalado o JDK.
* [Transferir](https://maven.apache.org/download.cgi) e [instalar](https://maven.apache.org/install.html) um arquivo binário [Maven](https://maven.apache.org/)
    * No Ubuntu, pode executar `apt-get install maven` para instalar o Maven.
* [Git](https://www.git-scm.com/)
    * No Ubuntu, pode executar `sudo apt-get install git` para instalar o Git.

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

Este passo é opcional. Se estiver interessado em saber de que forma o código cria os recursos da base de dados, pode rever os fragmentos seguintes. Caso contrário, pode avançar diretamente para [Update your connection string (Atualizar a cadeia de ligação)](#update-your-connection-string). Estes fragmentos são retirados do ficheiro `src/main/java/com/azure/cosmosdb/cassandra/util/CassandraUtils.java`.  

* As opções anfitrião de Cassandra, porta, nome de utilizador, palavra-passe e SSL estão definidas. As informações de cadeia de ligação provêm da página da cadeia de ligação no portal do Azure.

   ```java
   cluster = Cluster.builder().addContactPoint(cassandraHost).withPort(cassandraPort).withCredentials(cassandraUsername, cassandraPassword).withSSL(sslOptions).build();
   ```

* O `cluster` liga à Cassandra API do Azure Cosmos DB e devolve uma sessão para acesso.

    ```java
    return cluster.connect();
    ```

Os fragmentos seguintes são do ficheiro `src/main/java/com/azure/cosmosdb/cassandra/repository/UserRepository.java`.

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

1. No [portal do Azure](https://portal.azure.com/), clique em **Cadeia de Ligação**. 

    ![Ver e copiar um nome de utilizador no portal do Azure, página Cadeia de ligação](./media/create-cassandra-java/keys.png)

2. Utilize o ![botão Copiar](./media/create-cassandra-java/copy.png) botão à direita do ecrã, para copiar o PONTO DE CONTACTO.

3. Abra o ficheiro `config.properties` a partir da pasta `C:\git-samples\azure-cosmosdb-cassandra-java-getting-started\java-examples\src\main\resources`. 

3. Cole o valor do PONTO DE CONTACTO do portal em `<Cassandra endpoint host>` na linha 2.

    A linha 2 de config.properties deve agora ter um aspeto semelhante a 

    `cassandra_host=cosmos-db-quickstart.cassandra.cosmosdb.azure.com`

3. Volte ao portal e copie o valor do NOME DE UTILIZADOR. Cole o valor do NOME DE UTILIZADOR do portal em `<cassandra endpoint username>` na linha 4.

    A linha 4 de config.properties deve agora ter um aspeto semelhante a 

    `cassandra_username=cosmos-db-quickstart`

4. Volte ao portal e copie o valor do NOME DE UTILIZADOR. Cole o valor da PALAVRA-PASSE do portal em `<cassandra endpoint password>` na linha 5.

    A linha 5 de config.properties deve agora ter um aspeto semelhante a 

    `cassandra_password=2Ggkr662ifxz2Mg...==`

5. Na linha 6, se pretender utilizar um certificado SSL específico, em seguida, substitua `<SSL key store file location>` com a localização do certificado SSL. Se não for fornecido um valor, o certificado JDK instalado em <JAVA_HOME>/jre/lib/segurança/cacerts é utilizado. 

6. Se tiver alterado a linha 6 para utilizar um certificado SSL específico, atualize a linha 7 para utilizar a palavra-passe para esse certificado. 

7. Guarde o ficheiro `config.properties`.

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

    Prima CTRL + C para interromper a execução do programa e feche a janela da consola.

4. No portal do Azure, abra o **Data Explorer** para consultar, modificar e trabalhar com estes dados novos. 

    ![Ver os dados no Data Explorer](./media/create-cassandra-java/data-explorer.png)

## <a name="review-slas-in-the-azure-portal"></a>Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, aprendeu a criar uma conta do Azure Cosmos DB, uma base de dados Cassandra e um contentor com o Data Explorer, bem como a executar uma aplicação para fazer o mesmo programaticamente. Agora, pode importar dados adicionais para a sua coleção do Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Importar dados do Cassandra para o Azure Cosmos DB](cassandra-import-data.md)
