---
title: Criar um aplicativo de console usando a API do Azure Cosmos DB para MongoDB e o SDK do Golang
description: Apresenta um exemplo de código Golang que você pode usar para se conectar e consultar usando a API do Azure Cosmos DB para MongoDB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: quickstart
ms.date: 12/26/2018
ms.openlocfilehash: e16b9b7e591fcc089d74794c98ddfc951cbdced9
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755120"
---
# <a name="quickstart-build-a-console-app-using-azure-cosmos-dbs-api-for-mongodb-and-golang-sdk"></a>Início rápido: criar um aplicativo de console usando a API do Azure Cosmos DB para o MongoDB e o SDK do Golang

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

O Azure Cosmos DB é um serviço de base de dados com vários modelos e de distribuição global da Microsoft. Você pode criar e consultar rapidamente documentos, chave/valor e bancos de dados de grafo, todos beneficiando-se dos recursos de escala horizontal e distribuição global no núcleo de Cosmos DB.

Este guia de início rápido demonstra como pegar um aplicativo MongoDB existente escrito em [Golang](https://golang.org/) e conectá-lo ao seu banco de dados Cosmos usando a API do Azure Cosmos DB para MongoDB.

Em outras palavras, seu aplicativo Golang só sabe que está se conectando usando um cliente MongoDB. É transparente para o aplicativo que os dados são armazenados em um banco de Cosmos.

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar. 

  [!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

- O [Go](https://golang.org/dl/) e conhecimento básico da linguagem [Go](https://golang.org/).
- Um IDE — [GoLand](https://www.jetbrains.com/go/) by Jetbrains, [Visual Studio Code](https://code.visualstudio.com/) pela Microsoft ou [Atom](https://atom.io/). Neste tutorial, estou usando GoLand.

<a id="create-account"></a>
## <a name="create-a-database-account"></a>Criar uma conta de base de dados

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Clone a aplicação de exemplo e instale os pacotes necessários.

1. Crie uma pasta chamada CosmosDBSample dentro da pasta GOROOT\src, que é C:\Go\ por predefinição.
2. Execute o comando seguinte com uma janela de terminal do git, como bash git, para clonar o repositório de exemplo para a pasta CosmosDBSample. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-golang-getting-started.git
    ```
3.  Execute o seguinte comando para obter o pacote mgo. 

    ```
    go get gopkg.in/mgo.v2
    ```

O driver [MgO](https://labix.org/mgo) é um driver do [MongoDB](https://www.mongodb.com/) para a [linguagem go](https://golang.org/) que implementa uma seleção rica e bem testada de recursos em uma API muito simples, seguindo os idiomas go padrão.

<a id="connection-string"></a>

## <a name="update-your-connection-string"></a>Atualizar a cadeia de ligação

Agora, regresse ao portal do Azure para obter as informações da cadeia de ligação e copie-as para a aplicação.

1. Clique em **Início rápido** no menu de navegação à esquerda e clique em **Outros** para ver as informações da cadeia de ligação exigidas pela aplicação Go.

2. No Goglang, abra o ficheiro main.go no diretório GOROOT\CosmosDBSample e atualize as seguintes linhas de código com as informações da cadeia de ligação do portal do Azure, conforme mostrado na seguinte captura de ecrã. 

    O nome da Base de Dados é o prefixo do valor do **Anfitrião** no painel da cadeia de ligação do portal do Azure. Para a conta mostrada na imagem abaixo, o nome da Base de Dados é golang-coach.

    ```go
    Database: "The prefix of the Host value in the Azure portal",
    Username: "The Username in the Azure portal",
    Password: "The Password in the Azure portal",
    ```

    ![Painel Início rápido, separador Outros no portal do Azure, que mostra as informações da cadeia de ligação](./media/create-mongodb-golang/cosmos-db-golang-connection-string.png)

3. Guarde o ficheiro main.go.

## <a name="review-the-code"></a>Rever o código

Este passo é opcional. Se estiver interessado em aprender de que forma os recursos da base de dados são criados no código, pode consultar os seguintes fragmentos. Caso contrário, pode avançar para [Executar a aplicação](#run-the-app). 

Os seguintes fragmentos são retirados do ficheiro main.go.

### <a name="connecting-the-go-app-to-cosmos-db"></a>Conectando o aplicativo Go ao Cosmos DB

A API do Azure Cosmos DB para MongoDB dá suporte à conexão habilitada para SSL. Para se conectar, você precisa definir a função **DialServer** em [MgO. DialInfo](https://godoc.org/gopkg.in/mgo.v2#DialInfo)e fazer uso do [TLS. *Função de discagem* ](https://golang.org/pkg/crypto/tls#Dial) para executar a conexão.

O trecho de código Golang a seguir conecta o aplicativo go à API do Azure Cosmos DB para MongoDB. A classe *DialInfo* contém opções para estabelecer uma sessão.

```go
// DialInfo holds options for establishing a session.
dialInfo := &mgo.DialInfo{
    Addrs:    []string{"golang-couch.documents.azure.com:10255"}, // Get HOST + PORT
    Timeout:  60 * time.Second,
    Database: "database", // It can be anything
    Username: "username", // Username
    Password: "Azure database connect password from Azure Portal", // PASSWORD
    DialServer: func(addr *mgo.ServerAddr) (net.Conn, error) {
        return tls.Dial("tcp", addr.String(), &tls.Config{})
    },
}

// Create a session which maintains a pool of socket connections
// to Cosmos database (using Azure Cosmos DB's API for MongoDB).
session, err := mgo.DialWithInfo(dialInfo)

if err != nil {
    fmt.Printf("Can't connect, go error %v\n", err)
    os.Exit(1)
}

defer session.Close()

// SetSafe changes the session safety mode.
// If the safe parameter is nil, the session is put in unsafe mode, 
// and writes become fire-and-forget,
// without error checking. The unsafe mode is faster since operations won't hold on waiting for a confirmation.
// 
session.SetSafe(&mgo.Safe{})
```

O método **mgo.Dial()** é utilizado quando não existir uma ligação SSL. Para uma ligação SSL, o método **mgo.DialWithInfo()** é necessário.

Uma instância do objeto **DialWIthInfo{}** é utilizada para criar o objeto de sessão. Quando a sessão é estabelecida, pode aceder à coleção com o seguinte fragmento de código:

```go
collection := session.DB("database").C("package")
```

<a id="create-document"></a>

### <a name="create-a-document"></a>Criar um documento

```go
// Model
type Package struct {
    Id bson.ObjectId  `bson:"_id,omitempty"`
    FullName      string
    Description   string
    StarsCount    int
    ForksCount    int
    LastUpdatedBy string
}

// insert Document in collection
err = collection.Insert(&Package{
    FullName:"react",
    Description:"A framework for building native apps with React.",
    ForksCount: 11392,
    StarsCount:48794,
    LastUpdatedBy:"shergin",

})

if err != nil {
    log.Fatal("Problem inserting data: ", err)
    return
}
```

### <a name="query-or-read-a-document"></a>Consultar ou ler um documento

Cosmos DB dá suporte a consultas avançadas em dados armazenados em cada coleção. O seguinte código de exemplo mostra uma consulta que pode executar nos documentos da sua coleção.

```go
// Get a Document from the collection
result := Package{}
err = collection.Find(bson.M{"fullname": "react"}).One(&result)
if err != nil {
    log.Fatal("Error finding record: ", err)
    return
}

fmt.Println("Description:", result.Description)
```


### <a name="update-a-document"></a>Atualizar um documento

```go
// Update a document
updateQuery := bson.M{"_id": result.Id}
change := bson.M{"$set": bson.M{"fullname": "react-native"}}
err = collection.Update(updateQuery, change)
if err != nil {
    log.Fatal("Error updating record: ", err)
    return
}
```

### <a name="delete-a-document"></a>Eliminar um documento

Cosmos DB dá suporte à exclusão de documentos.

```go
// Delete a document
query := bson.M{"_id": result.Id}
err = collection.Remove(query)
if err != nil {
   log.Fatal("Error deleting record: ", err)
   return
}
```
    
## <a name="run-the-app"></a>Executar a aplicação

1. No Golang, verifique se o GOPATH (disponível em **arquivo**, **configurações**, **go**, **GOPATH**) inclui o local em que o gopkg foi instalado, que é USERPROFILE\go por padrão. 
2. Comente as linhas que eliminam o documento, as linhas 103-107, para conseguir ver o documento depois de executar a aplicação.
3. Em Golang, clique em **executar**e, em seguida, clique em **Executar ' Compilar principal. ir e executar '** .

    A aplicação termina e apresenta a descrição do documento criado em [Criar um documento](#create-document).
    
    ```
    Description: A framework for building native apps with React.
    
    Process finished with exit code 0
    ```

    ![Golang mostrando a saída do aplicativo](./media/create-mongodb-golang/goglang-cosmos-db.png)
    
## <a name="review-your-document-in-data-explorer"></a>Rever o documento no Data Explorer

Regresse ao portal do Azure para ver o documento no Data Explorer.

1. Clique em **Data Explorer (Pré-visualização)** no menu de navegação à esquerda, expanda **golang-coach**, **pacote**e, em seguida, clique em **Documentos**. No separador **Documentos**, clique no \_id para exibir o documento no painel direito. 

    ![O Data Explorer a mostrar o documento recentemente criado](./media/create-mongodb-golang/golang-cosmos-db-data-explorer.png)
    
2. Pode então trabalhar com o documento inline e clicar em **Atualizar** para guardá-lo. Também pode eliminar o documento ou criar novos documentos ou consultas.

## <a name="review-slas-in-the-azure-portal"></a>Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você aprendeu como criar uma conta do cosmos e executar um aplicativo Golang. Agora você pode importar dados adicionais para o banco de Cosmos. 

> [!div class="nextstepaction"]
> [Import MongoDB data into Azure Cosmos DB](mongodb-migrate.md) (Importar dados do MongoDB para o Azure Cosmos DB)
