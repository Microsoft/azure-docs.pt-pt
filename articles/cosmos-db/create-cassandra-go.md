---
title: Construa uma app Go com a Azure Cosmos DB Cassandra API usando o cliente gocql
description: Este quickstart mostra como usar um cliente Go para interagir com a Azure Cosmos DB Cassandra API
ms.service: cosmos-db
author: abhirockzz
ms.author: abhishgu
ms.subservice: cosmosdb-cassandra
ms.devlang: go
ms.topic: quickstart
ms.date: 07/14/2020
ms.openlocfilehash: 595ec1aaa4aedc3916d1b4d46986dcabae887aaf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93076406"
---
# <a name="quickstart-build-a-go-app-with-the-gocql-client-to-manage-azure-cosmos-db-cassandra-api-data"></a>Quickstart: Construa uma app Go com o `gocql` cliente para gerir os dados da Azure Cosmos DB Cassandra API
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [.NET Core](create-cassandra-dotnet-core.md)
> * [Java v3](create-cassandra-java.md)
> * [Java v4](create-cassandra-java-v4.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
> * [Golang](create-cassandra-go.md)
>

Azure Cosmos DB é um serviço de base de dados multi-modelo que permite criar e consultar rapidamente documentos, tabelas, valor-chave e bases de dados de gráficos com capacidades de distribuição global e escala horizontal. Neste arranque rápido, você começará por criar uma conta Azure Cosmos DB Cassandra API. Em seguida, executará uma aplicação Go para criar um espaço-chave Cassandra, mesa e executar algumas operações. Esta aplicação Go usa [gocql](https://github.com/gocql/gocql), que é um cliente Cassandra para o idioma Go. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie um de graça.](https://azure.microsoft.com/free/?WT.mc_id=cassandrago-docs-abhishgu) Ou [experimente a Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/?WT.mc_id=cassandrago-docs-abhishgu) sem uma subscrição do Azure.
- [Vá](https://golang.org/) instalado no seu computador, e um conhecimento funcional do Go.
- [Git.](https://git-scm.com/downloads)

## <a name="create-a-database-account"></a>Criar uma conta de base de dados

Antes de criar uma base de dados, precisa de criar uma conta Cassandra com a Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Comece por clonar a aplicação do GitHub.

1. Abra um pedido de comando e crie uma nova pasta chamada `git-samples` .

    ```bash
    md "C:\git-samples"
    ```

2. Abra uma janela de terminal do Git, como git bash. Utilize o `cd` comando para alterar a nova pasta e instalar a aplicação da amostra.

    ```bash
    cd "C:\git-samples"
    ```

3. Execute o seguinte comando para clonar o repositório de exemplo. Este comando cria uma cópia da aplicação de exemplo no seu computador.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-go-getting-started.git
    ```

## <a name="review-the-code"></a>Rever o código

Este passo é opcional. Se estiver interessado em saber como o código cria os recursos da base de dados, pode rever os seguintes códigos. Caso contrário, pode adiantar-se para [executar a aplicação](#run-the-application)

A `GetSession` função (parte de `utils\utils.go` ) devolve uma que é usada para executar [`*gocql.Session`](https://godoc.org/github.com/gocql/gocql#Session) operações de cluster tais como inserir, encontrar etc.

```go
func GetSession(cosmosCassandraContactPoint, cosmosCassandraPort, cosmosCassandraUser, cosmosCassandraPassword string) *gocql.Session {
    clusterConfig := gocql.NewCluster(cosmosCassandraContactPoint)
    port, err := strconv.Atoi(cosmosCassandraPort)
    
    clusterConfig.Authenticator = gocql.PasswordAuthenticator{Username: cosmosCassandraUser, Password: cosmosCassandraPassword}
    clusterConfig.Port = port
    clusterConfig.SslOpts = &gocql.SslOptions{Config: &tls.Config{MinVersion: tls.VersionTLS12}}
    clusterConfig.ProtoVersion = 4
    
    session, err := clusterConfig.CreateSession()
    ...
    return session
}
```

O anfitrião Azure Cosmos DB Cassandra é passado para a [`gocql.NewCluster`](https://godoc.org/github.com/gocql/gocql#NewCluster) função para obter uma [`*gocql.ClusterConfig`](https://godoc.org/github.com/gocql/gocql#ClusterConfig) estrutura que é então configurada para usar o nome de utilizador, palavra-passe, porta e versão TLS apropriada[(requisito de segurança de encriptação HTTPS/SSL/TLS)](./database-security.md?WT.mc_id=cassandrago-docs-abhishgu#how-does-azure-cosmos-db-secure-my-database)

A `GetSession` função é então chamada da `main` função `main.go` ().

```go
func main() {
    session := utils.GetSession(cosmosCassandraContactPoint, cosmosCassandraPort, cosmosCassandraUser, cosmosCassandraPassword)
    defer session.Close()
    ...
}
```

As informações e credenciais de conectividade são aceites sob a forma de variáveis ambientais (resolvidas no `init` método)

```go
func init() {
    cosmosCassandraContactPoint = os.Getenv("COSMOSDB_CASSANDRA_CONTACT_POINT")
    cosmosCassandraPort = os.Getenv("COSMOSDB_CASSANDRA_PORT")
    cosmosCassandraUser = os.Getenv("COSMOSDB_CASSANDRA_USER")
    cosmosCassandraPassword = os.Getenv("COSMOSDB_CASSANDRA_PASSWORD")

    if cosmosCassandraContactPoint == "" || cosmosCassandraUser == "" || cosmosCassandraPassword == "" {
        log.Fatal("missing mandatory environment variables")
    }
}
```

É então usado para executar várias operações (parte de `operations\setup.go` ) em Azure Cosmos DB começando com `keyspace` e `table` criação.

Como o nome sugere, a `DropKeySpaceIfExists` função cai `keyspace` apenas se existir.

```go
const dropKeyspace = "DROP KEYSPACE IF EXISTS %s"

func DropKeySpaceIfExists(keyspace string, session *gocql.Session) {
    err := utils.ExecuteQuery(fmt.Sprintf(dropKeyspace, keyspace), session)
    if err != nil {
        log.Fatal("Failed to drop keyspace", err)
    }
    log.Println("Keyspace dropped")
}
```

`CreateKeySpace` função é usada para criar o `keyspace` ( `user_profile` )

```go
const createKeyspace = "CREATE KEYSPACE %s WITH REPLICATION = { 'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1 }"

func CreateKeySpace(keyspace string, session *gocql.Session) {
    err := utils.ExecuteQuery(fmt.Sprintf(createKeyspace, keyspace), session)
    if err != nil {
        log.Fatal("Failed to create keyspace", err)
    }
    log.Println("Keyspace created")
}
```

Isto é seguido pela criação de mesas ( `user` ) que é cuidada de `CreateUserTable` função

```go
const createTable = "CREATE TABLE %s.%s (user_id int PRIMARY KEY, user_name text, user_bcity text)"

func CreateUserTable(keyspace, table string, session *gocql.Session) {
    err := session.Query(fmt.Sprintf(createTable, keyspace, table)).Exec()
    if err != nil {
        log.Fatal("failed to create table ", err)
    }
    log.Println("Table created")
}
```

Uma vez criado o espaço-chave e a tabela, invocamos operações CRUD (parte de `operations\crud.go` ). 

`InsertUser` é usado para criar um `User` . Define a informação do utilizador (ID, nome e cidade) como os argumentos de consulta usando [`Bind`](https://godoc.org/github.com/gocql/gocql#Query.Bind)

```go
const createQuery = "INSERT INTO %s.%s (user_id, user_name , user_bcity) VALUES (?,?,?)"

func InsertUser(keyspace, table string, session *gocql.Session, user model.User) {
    err := session.Query(fmt.Sprintf(createQuery, keyspace, table)).Bind(user.ID, user.Name, user.City).Exec()
    if err != nil {
        log.Fatal("Failed to create user", err)
    }
    log.Println("User created")
}
```

`FindUser` é usado para procurar um utilizador `model\user.go` () usando um ID de utilizador específico enquanto [`Scan`](https://godoc.org/github.com/gocql/gocql#Iter.Scan) liga os atributos do utilizador (devolvido por Cassandra) a variáveis individuais `userid` (, , , - é `name` `city` apenas uma das formas em que você pode usar o resultado obtido como resultado da consulta de pesquisa

```go
const selectQuery = "SELECT * FROM %s.%s where user_id = ?"

func FindUser(keyspace, table string, id int, session *gocql.Session) model.User {
    var userid int
    var name, city string
    err := session.Query(fmt.Sprintf(selectQuery, keyspace, table)).Bind(id).Scan(&userid, &name, &city)

    if err != nil {
        if err == gocql.ErrNotFound {
            log.Printf("User with id %v does not exist\n", id)
        } else {
            log.Printf("Failed to find user with id %v - %v\n", id, err)
        }
    }
    return model.User{ID: userid, Name: name, City: city}
}
```

`FindAllUsers` é usado para buscar todos os utilizadores. [`SliceMap`](https://godoc.org/github.com/gocql/gocql#Iter.SliceMap) é usado como abreviatura para obter todas as informações do utilizador sob a forma de uma fatia de `map` s. Pense em cada um `map` como pares de valor-chave onde o nome da coluna (por exemplo, `user_id` ) é a chave juntamente com o seu respetivo valor.

```go
const findAllUsersQuery = "SELECT * FROM %s.%s"

func FindAllUsers(keyspace, table string, session *gocql.Session) []model.User {
    var users []model.User
    results, _ := session.Query(fmt.Sprintf(findAllUsersQuery, keyspace, table)).Iter().SliceMap()

    for _, u := range results {
        users = append(users, mapToUser(u))
    }
    return users
}
```

Cada `map` informação do utilizador é convertida para uma `User` função de utilização `mapToUser` que simplesmente extrai o valor da respetiva coluna e a utiliza para criar uma instância da `User` estrutura

```go
func mapToUser(m map[string]interface{}) model.User {
    id, _ := m["user_id"].(int)
    name, _ := m["user_name"].(string)
    city, _ := m["user_bcity"].(string)

    return model.User{ID: id, Name: name, City: city}
}
```

## <a name="run-the-application"></a>Executar a aplicação

Como mencionado anteriormente, a aplicação aceita conectividade e credenciais na forma das variáveis ambientais. 

1. Na sua conta DB Azure Cosmos no [portal Azure,](https://portal.azure.com/)selecione **Connection String**. 

    :::image type="content" source="./media/create-cassandra-go/copy-username-connection-string-azure-portal.png" alt-text="Ver e copiar detalhes da página de string de ligação no portal Azure":::

Copie os valores para os seguintes atributos `CONTACT POINT` `PORT` (, `USERNAME` `PRIMARY PASSWORD` e) e dedi-os às respetivas variáveis ambientais

```shell
set COSMOSDB_CASSANDRA_CONTACT_POINT=<value for "CONTACT POINT">
set COSMOSDB_CASSANDRA_PORT=<value for "PORT">
set COSMOSDB_CASSANDRA_USER=<value for "USERNAME">
set COSMOSDB_CASSANDRA_PASSWORD=<value for "PRIMARY PASSWORD">
```

Na janela do terminal, mude para a pasta correta. Por exemplo:

```shell
cd "C:\git-samples\azure-cosmosdb-cassandra-go-getting-started"
```

2. No terminal, executar o seguinte comando para iniciar a aplicação.

```shell
go run main.go
```

3. A janela do terminal apresenta notificações para as várias operações, incluindo o espaço-chave e a configuração da tabela, a criação do utilizador, etc.

4. No portal do Azure, abra o **Data Explorer** para consultar, modificar e trabalhar com estes dados novos. 

    :::image type="content" source="./media/create-cassandra-go/view-data-explorer-go-app.png" alt-text="Ver os dados no Data Explorer - Azure Cosmos DB":::

## <a name="review-slas-in-the-azure-portal"></a>Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, aprendeu a criar uma conta DB Azure Cosmos com a Cassandra API, e executou uma aplicação Go que cria uma base de dados e um recipiente Cassandra. Pode agora importar dados adicionais na sua conta DB Azure Cosmos. 

> [!div class="nextstepaction"]
> [Importar dados do Cassandra para o Azure Cosmos DB](cassandra-import-data.md)