---
title: Ligue uma aplicação Rust à API da Azure Cosmos DB para a MongoDB
description: Este quickstart demonstra como construir uma aplicação Rust apoiada pela API da Azure Cosmos DB para o MongoDB.
author: abhirockzz
ms.author: abhishgu
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: rust
ms.topic: quickstart
ms.date: 01/12/2021
ms.openlocfilehash: 4b7e7258664aed3b171166bb392406cd5d826b3f
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98792554"
---
# <a name="quickstart-connect-a-rust-application-to-azure-cosmos-dbs-api-for-mongodb"></a>Quickstart: Ligue uma aplicação rust à API da Azure Cosmos DB para a MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
> * [Rust](create-mongodb-rust.md)
>

Azure Cosmos DB é um serviço de base de dados multi-modelo que permite criar e consultar rapidamente documentos, tabelas, valor-chave e bases de dados de gráficos com capacidades de distribuição global e escala horizontal. A amostra apresentada neste artigo é uma simples aplicação baseada em linha de comando que utiliza o [controlador Rust para a MongoDB](https://github.com/mongodb/mongo-rust-driver). Uma vez que a API da Azure Cosmos DB para a MongoDB é [compatível com o protocolo de fio MongoDB,](./mongodb-introduction.md#wire-protocol-compatibility)é possível que qualquer condutor cliente da MongoDB se conecte a ele.

Você aprenderá a usar o controlador MongoDB Rust para interagir com a API da Azure Cosmos DB para a MongoDB explorando as operações CRUD (criar, ler, atualizar, excluir) operações implementadas no código de amostra. Finalmente, pode executar a aplicação localmente para vê-la em ação.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie um de graça.](https://azure.microsoft.com/free) Ou [experimente a Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma subscrição do Azure. Também pode utilizar o [Emulador Azure Cosmos DB](https://aka.ms/cosmosdb-emulator) com a cadeia de ligação `.mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true` .
- [Ferrugem](https://www.rust-lang.org/tools/install) (versão 1.39 ou superior)
- [Git](https://git-scm.com/downloads)

## <a name="set-up-azure-cosmos-db"></a>Configurar Azure Cosmos DB

Para criar uma conta DB Azure Cosmos, siga as [instruções aqui.](create-mongodb-dotnet.md) A aplicação necessitará da cadeia de ligação MongoDB que pode obter através do portal Azure. Para mais detalhes, consulte [a cadeia de ligação MongoDB para personalizar](connect-mongodb-account.md#get-the-mongodb-connection-string-to-customize).

## <a name="run-the-application"></a>Executar a aplicação

### <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Execute os seguintes comandos para clonar o repositório de exemplo.

1. Abra um pedido de comando, crie uma nova pasta chamada `git-samples` e, em seguida, feche o pedido de comando.

    ```bash
    mkdir "C:\git-samples"
    ```

1. Abra uma janela de terminal do git, como o git bash e utilize o comando `cd` para alterar para uma nova pasta e instalar a aplicação de exemplo.

    ```bash
    cd "C:\git-samples"
    ```

1. Execute o seguinte comando para clonar o repositório de exemplo. Este comando cria uma cópia da aplicação de exemplo no seu computador. 

    ```bash
    git clone https://github.com/Azure-Samples/cosmosdb-rust-mongodb-quickstart
    ```

### <a name="build-the-application"></a>Criar a aplicação

Para construir o binário:

```bash
cargo build --release
```

### <a name="configure-the-application"></a>Configurar a aplicação 

Exporte a cadeia de ligação, a base de dados MongoDB e os nomes de recolha como variáveis ambientais. 

```bash
export MONGODB_URL="mongodb://<COSMOSDB_ACCOUNT_NAME>:<COSMOSDB_PASSWORD>@<COSMOSDB_ACCOUNT_NAME>.mongo.cosmos.azure.com:10255/?ssl=true&replicaSet=globaldb&maxIdleTimeMS=120000&appName=@<COSMOSDB_ACCOUNT_NAME>@"
```

> [!NOTE] 
> A `ssl=true` opção é importante devido aos requisitos da Cosmos DB. Para obter mais informações, consulte [os requisitos de cadeia de ligação](connect-mongodb-account.md#connection-string-requirements).
>

Para a `MONGODB_URL` variável ambiente, substitua os espaços reservados `<COSMOSDB_ACCOUNT_NAME>` para e `<COSMOSDB_PASSWORD>`

- `<COSMOSDB_ACCOUNT_NAME>`: O nome da conta DB Azure Cosmos que criou
- `<COSMOSDB_PASSWORD>`: A chave da base de dados extraída no passo anterior

```bash
export MONGODB_DATABASE=todos_db
export MONGODB_COLLECTION=todos
```

Pode escolher os seus valores preferidos `MONGODB_DATABASE` para e `MONGODB_COLLECTION` ou deixá-los como está.

Para executar a aplicação, mude para a pasta correta (onde existe o binário de aplicação):

```bash
cd target/release
```

Para criar uma `todo`

```bash
./todo create "Create an Azure Cosmos DB database account"
```

Se for bem sucedido, deverá ver uma saída com o MongoDB `_id` do documento recém-criado:

```bash
inserted todo with id = ObjectId("5ffd1ca3004cc935004a0959")
```

Criar outro `todo`

```bash
./todo create "Get the MongoDB connection string using the Azure CLI"
```

Listar todos os `todo` s

```bash
./todo list all
```

Devias ver aqueles que acabaste de adicionar:

```bash
todo_id: 5ffd1ca3004cc935004a0959 | description: Create an Azure Cosmos DB database account | status: pending
todo_id: 5ffd1cbe003bcec40022c81c | description: Get the MongoDB connection string using the Azure CLI | status: pending
```

Para atualizar o estado de um `todo` (por exemplo, alterá-lo para `completed` estado), utilize o `todo` ID como tal:

```bash
./todo update 5ffd1ca3004cc935004a0959 completed

#output
updating todo_id 5ffd1ca3004cc935004a0959 status to completed
updated status for todo id 5ffd1ca3004cc935004a0959
```

Listar apenas os `todo` s completos

```bash
./todo list completed
```

Devia ver o que acabou de atualizar.

```bash
listing 'completed' todos

todo_id: 5ffd1ca3004cc935004a0959 | description: Create an Azure Cosmos DB database account | status: completed
```

Excluir um `todo` uso é ID

```bash
./todo delete 5ffd1ca3004cc935004a0959
```

Listar os `todo` s para confirmar

```bash
./todo list all
```

O `todo` que acabaste de apagar não devia estar presente.

### <a name="view-data-in-data-explorer"></a>Ver dados no Data Explorer

Os dados armazenados no Azure Cosmos DB estão disponíveis para visualização e consulta no portal Azure.

Para ver, consultar e trabalhar com os dados do utilizador criados no passo anterior, inicie a sessão no [portal do Azure](https://portal.azure.com) no browser.

Na caixa de pesquisa superior, **insira Azure Cosmos DB**. Quando a sua folha de conta Cosmos abrir, selecione a sua conta Cosmos. Na navegação à esquerda, selecione **Data Explorer**. Expanda a coleção no painel Coleções e, em seguida, pode ver os documentos na coleção, consultar os dados e, ainda, criar e executar UDFs, acionadores e procedimentos armazenados.

## <a name="review-the-code-optional"></a>Rever o código (opcional)

Se estiver interessado em saber como funciona a aplicação, pode rever os códigos nesta secção. Os seguintes cortes são retirados do `src/main.rs` ficheiro.

A `main` função é o ponto de entrada para a `todo` aplicação. Espera que o URL de ligação para a API da Azure Cosmos DB para a MongoDB seja fornecido pela `MONGODB_URL` variável ambiental. É criado um novo `TodoManager` exemplo, seguido de uma [ `match` expressão](https://doc.rust-lang.org/book/ch06-02-match.html) que delega no método adequado `TodoManager` com base na operação escolhida pelo utilizador - `create` , , ou `update` `list` `delete` .

```rust
fn main() {
    let conn_string = std::env::var_os("MONGODB_URL").expect("missing environment variable MONGODB_URL").to_str().expect("failed to get MONGODB_URL").to_owned();
    let todos_db_name = std::env::var_os("MONGODB_DATABASE").expect("missing environment variable MONGODB_DATABASE").to_str().expect("failed to get MONGODB_DATABASE").to_owned();
    let todos_collection_name = std::env::var_os("MONGODB_COLLECTION").expect("missing environment variable MONGODB_COLLECTION").to_str().expect("failed to get MONGODB_COLLECTION").to_owned();

    let tm = TodoManager::new(conn_string,todos_db_name.as_str(), todos_collection_name.as_str());
      
    let ops: Vec<String> = std::env::args().collect();
    let op = ops[1].as_str();

    match op {
        CREATE_OPERATION_NAME => tm.add_todo(ops[2].as_str()),
        LIST_OPERATION_NAME => tm.list_todos(ops[2].as_str()),
        UPDATE_OPERATION_NAME => tm.update_todo_status(ops[2].as_str(), ops[3].as_str()),
        DELETE_OPERATION_NAME => tm.delete_todo(ops[2].as_str()),
        _ => panic!(INVALID_OP_ERR_MSG)
    }
}
```

`TodoManager` é um `struct` que encapsula um [mongodb::sync::Collection](https://docs.rs/mongodb/1.1.1/mongodb/sync/struct.Collection.html). Quando tenta instantaneamente `TodoManager` utilizar a `new` função, inicia uma ligação à API da Azure Cosmos DB para o MongoDB.

```rust
struct TodoManager {
    coll: Collection
}
....
impl TodoManager{
    fn new(conn_string: String, db_name: &str, coll_name: &str) -> Self{
        let mongo_client = Client::with_uri_str(&*conn_string).expect("failed to create client");
        let todo_coll = mongo_client.database(db_name).collection(coll_name);
            
        TodoManager{coll: todo_coll}
    }
....
```

O mais importante é `TodoManager` que tem métodos para ajudar a gerir `todo` s. Vamos passar por cima deles um por um.

O `add_todo` método requer uma `todo` descrição fornecida pelo utilizador e cria uma instância de `Todo` estrutura, que se parece com abaixo. A estrutura [do serde](https://github.com/serde-rs/serde) é usada para mapear (serializar/des-serializar) dados de BSON em casos de `Todo` structs. Note como `serde` os atributos de campo são usados para personalizar o processo de serialização/des-serialzation. Por exemplo, `todo_id` o campo no Todo é um e está armazenado em `struct` `ObjectId` MongoDB como `_id` .

```rust
#[derive(Serialize, Deserialize)]
struct Todo {
    #[serde(rename = "_id", skip_serializing_if = "Option::is_none")]
    todo_id: Option<bson::oid::ObjectId>,
    #[serde(rename = "description")]
    desc: String,
    status: String,
}
```

[Collection.insert_one](https://docs.rs/mongodb/1.1.1/mongodb/struct.Collection.html#method.insert_one) aceita um [documento](https://docs.rs/bson/1.1.0/bson/document/struct.Document.html) que represente os `todo` detalhes a adicionar. Note que a conversão de `Todo` um é um processo em `Document` duas etapas, conseguido com uma combinação de [to_bson](https://docs.rs/bson/1.1.0/bson/ser/fn.to_bson.html) e [as_document](https://docs.rs/bson/1.1.0/bson/enum.Bson.html#method.as_document).

```rust
fn add_todo(self, desc: &str) {
    let new_todo = Todo {
        todo_id: None,
        desc: String::from(desc),
        status: String::from(TODO_PENDING_STATUS),
    };
    
    let todo_doc = mongodb::bson::to_bson(&new_todo).expect("struct to BSON conversion failed").as_document().expect("BSON to Document conversion failed").to_owned();
        
    let r = self.coll.insert_one(todo_doc, None).expect("failed to add todo");    
    println!("inserted todo with id = {}", r.inserted_id);
}
```

[O collection.find](https://docs.rs/mongodb/1.1.1/mongodb/struct.Collection.html#method.find) é utilizado para obter *todos os* s `todo` ou filtros com base no estado fornecido pelo utilizador `pending` (ou `completed` . Note como, no `while` loop, cada `Document` um obtido como resultado da pesquisa é convertido em uma `Todo` estrutura usando [bson::from_bson](https://docs.rs/bson/1.1.0/bson/de/fn.from_bson.html). Isto é o oposto do que foi feito no `add_todo` método.

```rust
fn list_todos(self, status_filter: &str) {
    let mut filter = doc!{};
    if status_filter == TODO_PENDING_STATUS ||  status_filter == TODO_COMPLETED_STATUS{
        println!("listing '{}' todos",status_filter);
        filter = doc!{"status": status_filter}
    } else if status_filter != "all" {
        panic!(INVALID_FILTER_ERR_MSG)
    }

    let mut todos = self.coll.find(filter, None).expect("failed to find todos");
    
    while let Some(result) = todos.next() {
        let todo_doc = result.expect("todo not present");
        let todo: Todo = bson::from_bson(Bson::Document(todo_doc)).expect("BSON to struct conversion failed");
        println!("todo_id: {} | description: {} | status: {}", todo.todo_id.expect("todo id missing"), todo.desc, todo.status);
    }
}
```

Um `todo` estado pode ser atualizado `pending` (de ou `completed` vice-versa) usando. O `todo` método é convertido para [bson::oid:::ObjectId](https://docs.rs/bson/1.1.0/bson/oid/struct.ObjectId.html) que então usado pelo método[Collection.update_one](https://docs.rs/mongodb/1.1.1/mongodb/struct.Collection.html#method.update_one) para localizar o documento que precisa de ser atualizado.

```rust
fn update_todo_status(self, todo_id: &str, status: &str) {

    if status != TODO_COMPLETED_STATUS && status != TODO_PENDING_STATUS {
        panic!(INVALID_FILTER_ERR_MSG)
    }

    println!("updating todo_id {} status to {}", todo_id, status);
    
    let id_filter = doc! {"_id": bson::oid::ObjectId::with_string(todo_id).expect("todo_id is not valid ObjectID")};

    let r = self.coll.update_one(id_filter, doc! {"$set": { "status": status }}, None).expect("update failed");
    if r.modified_count == 1 {
        println!("updated status for todo id {}",todo_id);
    } else if r.matched_count == 0 {
        println!("could not update. check todo id {}",todo_id);
    }
}
```

Eliminar um `todo` é simples usando o método [Collection.delete_one.](https://docs.rs/mongodb/1.1.1/mongodb/struct.Collection.html#method.delete_one)


```rust
fn delete_todo(self, todo_id: &str) {
    println!("deleting todo {}", todo_id);
    
    let id_filter = doc! {"_id": bson::oid::ObjectId::with_string(todo_id).expect("todo_id is not valid ObjectID")};

    self.coll.delete_one(id_filter, None).expect("delete failed").deleted_count;
}
``` 

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Próximos passos

Neste quickstart, aprendeu a criar uma conta API Azure Cosmos DB MongoDB usando a Azure Cloud Shell, e criar e executar uma aplicação de linha de comando Rust para gerir `todo` s. Agora, pode importar dados adicionais para a sua conta do Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Import MongoDB data into Azure Cosmos DB](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json) (Importar dados do MongoDB para o Azure Cosmos DB)
