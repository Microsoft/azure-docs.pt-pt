---
title: Ligue uma aplicação Go à API da Azure Cosmos DB para o MongoDB
description: Este quickstart demonstra como ligar uma aplicação Go existente à API da Azure Cosmos DB para o MongoDB.
author: abhirockzz
ms.author: abhishgu
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: go
ms.topic: quickstart
ms.date: 04/24/2020
ms.openlocfilehash: ad7baea087cd6073659929cc41f626b597bbae63
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650339"
---
# <a name="quickstart-connect-a-go-application-to-azure-cosmos-dbs-api-for-mongodb"></a>Quickstart: Ligue uma aplicação Go à API da Azure Cosmos DB para o MongoDB

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

Azure Cosmos DB é um serviço de base de dados multi-modelo que permite criar e consultar rapidamente documentos, tabelas, basede-chaves e bases de dados de gráficos com capacidades de distribuição global e escala horizontal. Neste arranque rápido, cria-se e gere-se uma conta Azure Cosmos DB utilizando a Azure Cloud Shell, clone uma aplicação de amostra existente do GitHub e configure-a para trabalhar com o Azure Cosmos DB. 

A aplicação da amostra é uma ferramenta de gestão baseada em linha de comando `todo` escrita em Go. A API da Azure Cosmos DB para mongoDB é [compatível com o protocolo de arame MongoDB,](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction#wire-protocol-compatibility)permitindo que qualquer condutor de cliente da MongoDB se conectem ao mesmo. Esta aplicação utiliza o [controlador Go para mongoDB](https://github.com/mongodb/mongo-go-driver) de uma forma transparente para a aplicação que os dados são armazenados numa base de dados Azure Cosmos DB.

## <a name="prerequisites"></a>Pré-requisitos
- Uma conta Azure com uma subscrição ativa. [Crie um de graça.](https://azure.microsoft.com/free) Ou [experimente o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma subscrição Azure. Também pode utilizar o [Emulador DB Azure Cosmos](https://aka.ms/cosmosdb-emulator) com a corda de ligação `.mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true` .
- [Vá](https://golang.org/) instalado no seu computador, e um conhecimento de trabalho de Go.
- [Git.](https://git-scm.com/downloads)
- Se não quiser utilizar a Azure Cloud Shell, [Azure CLI 2.0+](/cli/azure/install-azure-cli).

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Execute os seguintes comandos para clonar o repositório de exemplo.

1. Abra um pedido de comando, crie uma nova pasta chamada `git-samples` e feche o pedido de comando.

    ```bash
    mkdir "C:\git-samples"
    ```

2. Abra uma janela de terminal do git, como o git bash e utilize o comando `cd` para alterar para uma nova pasta e instalar a aplicação de exemplo.

    ```bash
    cd "C:\git-samples"
    ```

3. Execute o seguinte comando para clonar o repositório de exemplo. Este comando cria uma cópia da aplicação de exemplo no seu computador. 

    ```bash
    git clone https://github.com/Azure-Samples/cosmosdb-go-mongodb-quickstart
    ```

## <a name="review-the-code"></a>Rever o código

Este passo é opcional. Se estiver interessado em saber como funciona a aplicação, pode rever os seguintes excertos. Caso contrário, pode saltar para a frente para [executar a aplicação](#run-the-application). O layout da aplicação é o seguinte:

```bash
.
├── go.mod
├── go.sum
└── todo.go
```

Os seguintes fragmentos foram todos retirados do ficheiro `todo.go`.

### <a name="connecting-the-go-app-to-azure-cosmos-db"></a>Ligar a aplicação Go ao Azure Cosmos DB

[`clientOptions`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo/options?tab=doc#ClientOptions)encapsula a cadeia de ligação para O Azure Cosmos DB, que é passado usando uma variável ambiental (detalhes na próxima secção). A ligação é inicializada utilizando [`mongo.NewClient`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#NewClient) a qual a instância é `clientOptions` passada. [ `Ping` função](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Client.Ping) é invocada para confirmar conectividade bem sucedida (é uma estratégia de falha rápida)

```go
    ctx, cancel := context.WithTimeout(context.Background(), time.Second*10)
    defer cancel()

    clientOptions := options.Client().ApplyURI(mongoDBConnectionString).SetDirect(true)
    
    c, err := mongo.NewClient(clientOptions)
    err = c.Connect(ctx)
    if err != nil {
        log.Fatalf("unable to initialize connection %v", err)
    }

    err = c.Ping(ctx, nil)
    if err != nil {
        log.Fatalf("unable to connect %v", err)
    }
```

> [!NOTE] 
> A [`SetDirect(true)`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo/options?tab=doc#ClientOptions.SetDirect) utilização da configuração é importante, sem a qual terá o seguinte erro de conectividade:`unable to connect connection(cdb-ms-prod-<azure-region>-cm1.documents.azure.com:10255[-4]) connection is closed`
>

### <a name="create-a-todo-item"></a>Criar um `todo` item

Para criar `todo` um, temos uma pega para um [`mongo.Collection`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection) e invocamos a [`InsertOne`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.InsertOne) função. 

```go
func create(desc string) {
    c := connect()
    ctx := context.Background()
    defer c.Disconnect(ctx)

    todoCollection := c.Database(database).Collection(collection)
    r, err := todoCollection.InsertOne(ctx, Todo{Description: desc, Status: statusPending})
    if err != nil {
        log.Fatalf("failed to add todo %v", err)
    }
```

Passamos por uma `Todo` estrutura que contém a descrição e o estado (que está inicialmente definido `pending` para)

```go
type Todo struct {
    ID          primitive.ObjectID `bson:"_id,omitempty"`
    Description string             `bson:"description"`
    Status      string             `bson:"status"`
}
```
### <a name="list-todo-items"></a>Listar `todo` itens

Podemos listar TODOs com base em critérios. A [`bson.D`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/bson?tab=doc#D) é criado para encapsular os critérios do filtro

```go
func list(status string) {
    .....
    var filter interface{}
    switch status {
    case listAllCriteria:
        filter = bson.D{}
    case statusCompleted:
        filter = bson.D{{statusAttribute, statusCompleted}}
    case statusPending:
        filter = bson.D{{statusAttribute, statusPending}}
    default:
        log.Fatal("invalid criteria for listing todo(s)")
    }
```

[`Find`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.Find)é usado para procurar por documentos com base no filtro e o resultado é convertido em uma fatia de`Todo`

```go
    todoCollection := c.Database(database).Collection(collection)
    rs, err := todoCollection.Find(ctx, filter)
    if err != nil {
        log.Fatalf("failed to list todo(s) %v", err)
    }
    var todos []Todo
    err = rs.All(ctx, &todos)
    if err != nil {
        log.Fatalf("failed to list todo(s) %v", err)
    }
```

Finalmente, a informação é prestada em formato tabular

```go
    todoTable := [][]string{}

    for _, todo := range todos {
        s, _ := todo.ID.MarshalJSON()
        todoTable = append(todoTable, []string{string(s), todo.Description, todo.Status})
    }

    table := tablewriter.NewWriter(os.Stdout)
    table.SetHeader([]string{"ID", "Description", "Status"})

    for _, v := range todoTable {
        table.Append(v)
    }
    table.Render()
```

### <a name="update-a-todo-item"></a>Atualizar um `todo` item

A `todo` pode ser atualizado com base no seu `_id` . Um [`bson.D`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/bson?tab=doc#D) filtro é criado com base no e outro é criado para a `_id` informação atualizada, que é um novo estado ( `completed` ou ) neste `pending` caso. Finalmente, a [`UpdateOne`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.UpdateOne) função é invocada com o filtro e o documento atualizado

```go
func update(todoid, newStatus string) {
....
    todoCollection := c.Database(database).Collection(collection)
    oid, err := primitive.ObjectIDFromHex(todoid)
    if err != nil {
        log.Fatalf("failed to update todo %v", err)
    }
    filter := bson.D{{"_id", oid}}
    update := bson.D{{"$set", bson.D{{statusAttribute, newStatus}}}}
    _, err = todoCollection.UpdateOne(ctx, filter, update)
    if err != nil {
        log.Fatalf("failed to update todo %v", err)
    }
```

### <a name="delete-a-todo"></a>Eliminar um`todo`

A `todo` é eliminado com base no seu e é `_id` encapsulado sob a forma de um [`bson.D`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/bson?tab=doc#D) caso. [`DeleteOne`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.DeleteOne)é invocado para apagar o documento.

```go
func delete(todoid string) {
....
    todoCollection := c.Database(database).Collection(collection)
    oid, err := primitive.ObjectIDFromHex(todoid)
    if err != nil {
        log.Fatalf("invalid todo ID %v", err)
    }
    filter := bson.D{{"_id", oid}}
    _, err = todoCollection.DeleteOne(ctx, filter)
    if err != nil {
        log.Fatalf("failed to delete todo %v", err)
    }
}
```

## <a name="build-the-application"></a>Criar a aplicação

Mude para o diretório onde clonou a aplicação e a construa `go build` (utilizando).

```bash
cd monogdb-go-quickstart
go build -o todo
```

Para confirmar que a aplicação foi construída corretamente.

```bash
./todo --help
```

## <a name="setup-azure-cosmos-db"></a>Configuração Azure Cosmos DB

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Se optar por instalar e usar a CLI localmente, este tópico requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, consulte [Instale o Azure CLI]. 

Se estiver a utilizar um Azure CLI instalado, inicie sessão na subscrição do Azure com o comando [de login az](/cli/azure/reference-index#az-login) e siga as instruções no ecrã. Pode ignorar esta etapa se estiver a utilizar o Azure Cloud Shell.

```azurecli
az login 
``` 
   
### <a name="add-the-azure-cosmos-db-module"></a>Adicionar o módulo Azure Cosmos DB

Se estiver a utilizar uma CLI do Azure instalada, veja se o componente `cosmosdb` já está instalado ao executar o comando `az`. Se `cosmosdb` está na lista de comandos de base, siga para o próximo comando. Pode ignorar esta etapa se estiver a utilizar o Azure Cloud Shell.

Se `cosmosdb` não estiver na lista de comandos de base, reinstale a [CLI do Azure](/cli/azure/install-azure-cli).

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Criar um [grupo de recursos](../azure-resource-manager/management/overview.md) com o grupo [Az criar](/cli/azure/group#az-group-create). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure, como aplicações Web, bases de dados e contas de armazenamento, são implementados e geridos. 

O exemplo seguinte cria um grupo de recursos na região Europa Ocidental. Escolha um nome exclusivo para o grupo de recursos.

Se estiver a utilizar a Casca de Nuvem Azure, selecione **Try It**, siga as instruções no ecrã para iniciar sessão e, em seguida, copie o comando para o pedido de comando.

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

### <a name="create-an-azure-cosmos-db-account"></a>Criar uma conta do Azure Cosmos DB

Crie uma conta Cosmos com o [az cosmosdb criar](/cli/azure/cosmosdb#az-cosmosdb-create) comando.

No seguinte comando, substitua o seu nome único de conta Cosmos onde vê o `<cosmosdb-name>` espaço reservado. Este nome único será usado como parte do seu ponto final cosmos DB , por isso o nome precisa ser único em todas as contas da `https://<cosmosdb-name>.documents.azure.com/` Cosmos em Azure. 

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

O parâmetro `--kind MongoDB` permite ligações de cliente da MongoDB.

Após criar a conta do DB Cosmos Azure, a CLI do Azure mostra informações semelhantes ao exemplo seguinte. 

> [!NOTE]
> Este exemplo utiliza o JSON como formato de saída da CLI do Azure, que é a predefinição. Para utilizar outro formato de saída, veja [Formatos de saída para os comandos da CLI do Azure](https://docs.microsoft.com/cli/azure/format-output-azure-cli).

```json
{
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<cosmosdb-name>.documents.azure.com:443/",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Document
DB/databaseAccounts/<cosmosdb-name>",
  "kind": "MongoDB",
  "location": "West Europe",
  "name": "<cosmosdb-name>",
  "readLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ],
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "writeLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ]
} 
```

### <a name="retrieve-the-database-key"></a>Obter a chave de base de dados

Para se ligar a uma base de dados da Cosmos, precisa da chave de base de dados. Use o comando da lista de [chaves az cosmosdb](/cli/azure/cosmosdb/keys#az-cosmosdb-keys-list) para recuperar a chave primária.

```azurecli-interactive
az cosmosdb keys list --name <cosmosdb-name> --resource-group myResourceGroup --query "primaryMasterKey"
```

A CLI do Azure apresenta informações semelhantes ao exemplo seguinte. 

```json
"RUayjYjixJDWG5xTqIiXjC..."
```

## <a name="configure-the-application"></a>Configurar a aplicação 

<a name="devconfig"></a>
### <a name="export-the-connection-string-mongodb-database-and-collection-names-as-environment-variables"></a>Exportar a cadeia de ligação, a base de dados MongoDB e os nomes de recolha como variáveis ambientais. 

```bash
export MONGODB_CONNECTION_STRING="mongodb://<COSMOSDB_ACCOUNT_NAME>:<COSMOSDB_PASSWORD>@<COSMOSDB_ACCOUNT_NAME>.mongo.cosmos.azure.com:10255/?ssl=true&replicaSet=globaldb&maxIdleTimeMS=120000&appName=@<COSMOSDB_ACCOUNT_NAME>@"
```

> [!NOTE] 
> A `ssl=true` opção é importante devido aos requisitos da Cosmos DB. Para mais informações, consulte [os requisitos](connect-mongodb-account.md#connection-string-requirements)de cadeia de ligação .
>

Para a `MONGODB_CONNECTION_STRING` variável ambiental, substitua os espaços reservados para `<COSMOSDB_ACCOUNT_NAME>` e`<COSMOSDB_PASSWORD>`

1. `<COSMOSDB_ACCOUNT_NAME>`: O nome da conta Azure Cosmos DB que criou
2. `<COSMOSDB_PASSWORD>`: A chave da base de dados extraída no passo anterior

```bash
export MONGODB_DATABASE=todo-db
export MONGODB_COLLECTION=todos
```

Pode escolher os seus valores preferidos e `MONGODB_DATABASE` `MONGODB_COLLECTION` ou deixá-los como estão.

## <a name="run-the-application"></a>Executar a aplicação

Para criar um`todo`

```bash
./todo --create "Create an Azure Cosmos DB database account"
```

Se for bem sucedido, deverá ver uma saída com o MongoDB `_id` do documento recém-criado:

```bash
added todo ObjectID("5e9fd6befd2f076d1f03bd8a")
```

Criar outro`todo`

```bash
./todo --create "Get the MongoDB connection string using the Azure CLI"
```

Enumerar todos os `todo` s

```bash
./todo --list all
```

Você deve ver aqueles que você acabou de adicionar em um formato tabular como tal

```bash
+----------------------------+--------------------------------+-----------+
|             ID             |          DESCRIPTION           |  STATUS   |
+----------------------------+--------------------------------+-----------+
| "5e9fd6b1bcd2fa6bd267d4c4" | Create an Azure Cosmos DB      | pending   |
|                            | database account               |           |
| "5e9fd6befd2f076d1f03bd8a" | Get the MongoDB connection     | pending   |
|                            | string using the Azure CLI     |           |
+----------------------------+--------------------------------+-----------+
```

Para atualizar o estado de um `todo` (por exemplo, alterá-lo para `completed` estado), utilize o `todo` ID

```bash
./todo --update 5e9fd6b1bcd2fa6bd267d4c4,completed
```

Listar apenas os `todo` s concluídos

```bash
./todo --list completed
```

Devia ver o que acabou de atualizar.

```bash
+----------------------------+--------------------------------+-----------+
|             ID             |          DESCRIPTION           |  STATUS   |
+----------------------------+--------------------------------+-----------+
| "5e9fd6b1bcd2fa6bd267d4c4" | Create an Azure Cosmos DB      | completed |
|                            | database account               |           |
+----------------------------+--------------------------------+-----------+
```

### <a name="view-data-in-data-explorer"></a>Ver dados no Data Explorer

Os dados armazenados no Azure Cosmos DB estão disponíveis para visualização e consulta no portal Azure.

Para ver, consultar e trabalhar com os dados do utilizador criados no passo anterior, inicie a sessão no [portal do Azure](https://portal.azure.com) no browser.

Na caixa de pesquisa superior, entre em **Azure Cosmos DB**. Quando a sua lâmina de conta Cosmos abrir, selecione a sua conta Cosmos. Na navegação à esquerda, selecione **Data Explorer**. Expanda a coleção no painel Coleções e, em seguida, pode ver os documentos na coleção, consultar os dados e, ainda, criar e executar UDFs, acionadores e procedimentos armazenados. 

![O Data Explorer a mostrar o documento recentemente criado](./media/create-mongodb-go/go-cosmos-db-data-explorer.jpg)


Eliminar um `todo` uso do seu ID

```bash
./todo --delete 5e9fd6b1bcd2fa6bd267d4c4,completed
```

Listar os `todo` s para confirmar

```bash
./todo --list all
```

O `todo` que acabou de apagar não deve estar presente.

```bash
+----------------------------+--------------------------------+-----------+
|             ID             |          DESCRIPTION           |  STATUS   |
+----------------------------+--------------------------------+-----------+
| "5e9fd6befd2f076d1f03bd8a" | Get the MongoDB connection     | pending   |
|                            | string using the Azure CLI     |           |
+----------------------------+--------------------------------+-----------+
```

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Próximos passos

Neste arranque rápido, aprendeu a criar uma conta API Azure Cosmos DB MongoDB usando a Azure Cloud Shell, e criar e executar uma app de linha de comando Go para gerir `todo` s. Agora, pode importar dados adicionais para a sua conta do Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Import MongoDB data into Azure Cosmos DB](mongodb-migrate.md) (Importar dados do MongoDB para o Azure Cosmos DB)
