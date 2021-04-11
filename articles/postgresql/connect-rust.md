---
title: 'Quickstart: Connect with Rust - Azure Database for PostgreSQL - Single Server'
description: Este quickstart fornece amostras de código Rust que pode usar para ligar e consultar dados da Base de Dados Azure para PostgreSQL - Servidor Único.
author: abhirockzz
ms.author: abhishgu
ms.service: postgresql
ms.devlang: rust
ms.topic: quickstart
ms.date: 03/26/2021
ms.openlocfilehash: 00adc50ac14e627eb356a3e62ce0faa5a9716aa3
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106505855"
---
# <a name="quickstart-use-rust-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>Quickstart: Use Ferrugem para ligar e consultar dados na Base de Dados Azure para PostgreSQL - Servidor Único

Neste artigo, você aprenderá a usar o [controlador PostgreSQL para a Rust](https://github.com/sfackler/rust-postgres) para interagir com a Base de Dados Azure para PostgreSQL explorando as operações CRUD (criar, ler, atualizar, excluir) operações implementadas no código de amostra. Finalmente, pode executar a aplicação localmente para vê-la em ação.

## <a name="prerequisites"></a>Pré-requisitos
Para este arranque rápido você precisa:

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free)
- Uma versão recente de [Rust](https://www.rust-lang.org/tools/install) instalada.
- Uma base de dados Azure para um servidor único postgreSQL - crie um usando [o portal Azure](./quickstart-create-server-database-portal.md) <br/> ou [Azure CLI](./quickstart-create-server-database-azure-cli.md).
- Com base no facto de estar a utilizar acesso público ou privado, complete **uma das** ações abaixo para permitir a conectividade.

  |Ação| Método de conectividade|Manual de instruções|
  |:--------- |:--------- |:--------- |
  | **Configurar as regras de firewall** | Público | [Portal](./howto-manage-firewall-using-portal.md) <br/> [CLI](./howto-manage-firewall-using-cli.md)|
  | **Configure Serviço Endpoint** | Público | [Portal](./howto-manage-vnet-using-portal.md) <br/> [CLI](./howto-manage-vnet-using-cli.md)|
  | **Configure a ligação privada** | Privado | [Portal](./howto-configure-privatelink-portal.md) <br/> [CLI](./howto-configure-privatelink-cli.md) |

- [Git](https://git-scm.com/downloads) instalado.

## <a name="get-database-connection-information"></a>Obtenha informações de ligação de base de dados
A ligação a uma base de dados Azure para base de dados PostgreSQL requer o nome do servidor e credenciais de login totalmente qualificados. Pode obter esta informação do portal Azure.

1. No [portal Azure,](https://portal.azure.com/)procure e selecione a sua Base de Dados Azure para o nome do servidor PostgreSQL.
1. Na **página** geral do servidor, copie o nome de **servidor** totalmente qualificado e o nome de **utilizador Admin**. O **nome de Servidor** totalmente qualificado é sempre do formulário *\<my-server-name> .postgres.database.azure.com*, e o nome de utilizador **Admin** é sempre do formulário *\<my-admin-username>@\<my-server-name>* .

## <a name="review-the-code-optional"></a>Rever o código (opcional)

Se estiver interessado em saber como funciona o código, pode rever os seguintes cortes. Caso contrário, sinta-se à vontade para avançar para [executar a aplicação](#run-the-application).

### <a name="connect"></a>Ligar

A `main` função começa por ligar à Base de Dados Azure para PostgreSQL e depende de seguir variáveis ambientais para informações de `POSTGRES_HOST` `POSTGRES_USER` `POSTGRES_PASSWORD` conectividade, e, `POSTGRES_DBNAME` . Por predefinição, o serviço de base de dados PostgreSQL está configurado para exigir `TLS` ligação. Pode optar por desativar a necessidade `TLS` de a sua aplicação de cliente não suportar `TLS` conectividade. Para mais informações, consulte a [conectividade Configure TLS na Base de Dados Azure para PostgreSQL - Single Server](./concepts-ssl-connection-security.md).

A aplicação da amostra neste artigo utiliza O S.TLS com a [caixa pós-abertura](https://crates.io/crates/postgres-openssl/). [postgres::Cliente::a](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.connect) função de ligação é usada para iniciar a ligação e o programa sai no caso de esta falhar.

```rust
fn main() {
    let pg_host = std::env::var("POSTGRES_HOST").expect("missing environment variable POSTGRES_HOST");
    let pg_user = std::env::var("POSTGRES_USER").expect("missing environment variable POSTGRES_USER");
    let pg_password = std::env::var("POSTGRES_PASSWORD").expect("missing environment variable POSTGRES_PASSWORD");
    let pg_dbname = std::env::var("POSTGRES_DBNAME").unwrap_or("postgres".to_string());

    let builder = SslConnector::builder(SslMethod::tls()).unwrap();
    let tls_connector = MakeTlsConnector::new(builder.build());

    let url = format!(
        "host={} port=5432 user={} password={} dbname={} sslmode=require",
        pg_host, pg_user, pg_password, pg_dbname
    );
    let mut pg_client = postgres::Client::connect(&url, tls_connector).expect("failed to connect to postgres");
...
}
```

### <a name="drop-and-create-table"></a>Largue e crie tabela

A aplicação da amostra utiliza uma tabela simples `inventory` para demonstrar as operações CRUD (criar, ler, atualizar, eliminar).

```sql
CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);
```

A `drop_create_table` função tenta inicialmente `DROP` chegar à mesa antes de criar uma `inventory` nova. Isto facilita a aprendizagem/experimentação, pois começa sempre com um estado conhecido (limpo). O método [de execução](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.execute) é utilizado para criar e largar operações. 

```rust
const CREATE_QUERY: &str =
    "CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);";

const DROP_TABLE: &str = "DROP TABLE inventory";

fn drop_create_table(pg_client: &mut postgres::Client) {
    let res = pg_client.execute(DROP_TABLE, &[]);
    match res {
        Ok(_) => println!("dropped table"),
        Err(e) => println!("failed to drop table {}", e),
    }
    pg_client
        .execute(CREATE_QUERY, &[])
        .expect("failed to create 'inventory' table");
}
```

### <a name="insert-data"></a>Inserir dados

`insert_data` adiciona entradas à `inventory` tabela. Cria uma [declaração preparada](https://docs.rs/postgres/0.19.0/postgres/struct.Statement.html) com função de [preparação.](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.prepare) 


```rust
const INSERT_QUERY: &str = "INSERT INTO inventory (name, quantity) VALUES ($1, $2) RETURNING id;";

fn insert_data(pg_client: &mut postgres::Client) {

    let prep_stmt = pg_client
        .prepare(&INSERT_QUERY)
        .expect("failed to create prepared statement");

    let row = pg_client
        .query_one(&prep_stmt, &[&"item-1", &42])
        .expect("insert failed");

    let id: i32 = row.get(0);
    println!("inserted item with id {}", id);
...
}
```

Note também a utilização de [prepare_typed](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.prepare_typed) método, que permite especificar explicitamente os tipos de parâmetros de consulta.

```rust
...
let typed_prep_stmt = pg_client
        .prepare_typed(&INSERT_QUERY, &[Type::VARCHAR, Type::INT4])
        .expect("failed to create prepared statement");

let row = pg_client
        .query_one(&typed_prep_stmt, &[&"item-2", &43])
        .expect("insert failed");

let id: i32 = row.get(0);
println!("inserted item with id {}", id);
...
```

Finalmente, um `for` loop é usado para adicionar , `item-3` `item-4` e, com quantidade gerada `item-5` aleatoriamente para cada um.

```rust
...
    for n in 3..=5 {
        let row = pg_client
            .query_one(
                &typed_prep_stmt,
                &[
                    &("item-".to_owned() + &n.to_string()),
                    &rand::thread_rng().gen_range(10..=50),
                ],
            )
            .expect("insert failed");

        let id: i32 = row.get(0);
        println!("inserted item with id {} ", id);
    }
...
```

### <a name="query-data"></a>Consultar dados

`query_data` função demonstra como recuperar dados da `inventory` tabela. O [método query_one](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.query_one) é utilizado para obter um item através do seu `id` . 

```rust
const SELECT_ALL_QUERY: &str = "SELECT * FROM inventory;";
const SELECT_BY_ID: &str = "SELECT name, quantity FROM inventory where id=$1;";

fn query_data(pg_client: &mut postgres::Client) {

    let prep_stmt = pg_client
        .prepare_typed(&SELECT_BY_ID, &[Type::INT4])
        .expect("failed to create prepared statement");

    let item_id = 1;

    let c = pg_client
        .query_one(&prep_stmt, &[&item_id])
        .expect("failed to query item");

    let name: String = c.get(0);
    let quantity: i32 = c.get(1);
    println!("quantity for item {} = {}", name, quantity);
...
}
```

Todas as linhas da tabela de inventário são recolhidas utilizando uma `select * from` consulta com o método de [consulta.](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.query) As linhas devolvidas são iteradas para extrair o valor de cada coluna utilizando [obter](https://docs.rs/postgres/0.19.0/postgres/row/struct.Row.html#method.get). 

> [!TIP]
> Note como `get` permite especificar a coluna pelo seu índice numérico na linha ou pelo seu nome de coluna.

```rust
...
    let items = pg_client
        .query(SELECT_ALL_QUERY, &[])
        .expect("select all failed");

    println!("listing items...");

    for item in items {
        let id: i32 = item.get("id");
        let name: String = item.get("name");
        let quantity: i32 = item.get("quantity");
        println!(
            "item info: id = {}, name = {}, quantity = {} ",
            id, name, quantity
        );
    }
...
```

### <a name="update-data"></a>Atualizar dados

A `update_date` função atualiza aleatoriamente a quantidade de todos os itens. Uma vez que a `insert_data` função tinha adicionado `5` linhas, o mesmo é tomado em consideração no `for` loop - `for n in 1..=5`

> [!TIP]
> Note que utilizamos `query` em vez `execute` de, uma vez que pretendemos recuperar `id` o e o recém-gerado `quantity` (usando [a cláusula RETURNING).](https://www.postgresql.org/docs/current/dml-returning.html)

```rust
const UPDATE_QUERY: &str = "UPDATE inventory SET quantity = $1 WHERE name = $2 RETURNING quantity;";

fn update_data(pg_client: &mut postgres::Client) {
    let stmt = pg_client
        .prepare_typed(&UPDATE_QUERY, &[Type::INT4, Type::VARCHAR])
        .expect("failed to create prepared statement");

    for id in 1..=5 {
        let row = pg_client
            .query_one(
                &stmt,
                &[
                    &rand::thread_rng().gen_range(10..=50),
                    &("item-".to_owned() + &id.to_string()),
                ],
            )
            .expect("update failed");
        
        let quantity: i32 = row.get("quantity");
        println!("updated item id {} to quantity = {}", id, quantity);
    }
}
```

### <a name="delete-data"></a>Eliminar dados

Finalmente, a `delete` função demonstra como remover um item da `inventory` tabela pela sua `id` . O `id` é escolhido aleatoriamente - é um inteiro aleatório entre `1` `5` (inclusive) uma vez que a `5` `insert_data` função tinha adicionado `5` linhas para começar. 

> [!TIP]
> Note que utilizamos `query` em vez `execute` de, uma vez que pretendemos recuperar a informação sobre o item que acabamos de eliminar (usando [a cláusula RETURNING).](https://www.postgresql.org/docs/current/dml-returning.html)

```rust
const DELETE_QUERY: &str = "DELETE FROM inventory WHERE id = $1 RETURNING id, name, quantity;";

fn delete(pg_client: &mut postgres::Client) {
    let stmt = pg_client
        .prepare_typed(&DELETE_QUERY, &[Type::INT4])
        .expect("failed to create prepared statement");

    let item = pg_client
        .query_one(&stmt, &[&rand::thread_rng().gen_range(1..=5)])
        .expect("delete failed");

    let id: i32 = item.get(0);
    let name: String = item.get(1);
    let quantity: i32 = item.get(2);
    println!(
        "deleted item info: id = {}, name = {}, quantity = {} ",
        id, name, quantity
    );
}
```

## <a name="run-the-application"></a>Executar a aplicação

1. Para começar, executar o seguinte comando para clonar o repositório de amostras:

    ```bash
    git clone https://github.com/Azure-Samples/azure-postgresql-rust-quickstart.git
    ```

2. Desaprova as variáveis ambientais necessárias com os valores copiados do portal Azure:

    ```bash
    export POSTGRES_HOST=<server name e.g. my-server.postgres.database.azure.com>
    export POSTGRES_USER=<admin username e.g. my-admin-user@my-server>
    export POSTGRES_PASSWORD=<admin password>
    export POSTGRES_DBNAME=<database name. it is optional and defaults to postgres>
    ```

3. Para executar o requerimento, mude para o diretório onde o clonou e `cargo run` execute:

    ```bash
    cd azure-postgresql-rust-quickstart
    cargo run
    ```

    Deve ver uma saída semelhante a esta:

    ```bash
    dropped 'inventory' table
    inserted item with id 1
    inserted item with id 2
    inserted item with id 3 
    inserted item with id 4 
    inserted item with id 5 
    quantity for item item-1 = 42
    listing items...
    item info: id = 1, name = item-1, quantity = 42 
    item info: id = 2, name = item-2, quantity = 43 
    item info: id = 3, name = item-3, quantity = 11 
    item info: id = 4, name = item-4, quantity = 32 
    item info: id = 5, name = item-5, quantity = 24 
    updated item id 1 to quantity = 27
    updated item id 2 to quantity = 14
    updated item id 3 to quantity = 31
    updated item id 4 to quantity = 16
    updated item id 5 to quantity = 10
    deleted item info: id = 4, name = item-4, quantity = 16 
    ```

4. Para confirmar, também pode ligar à Base de Dados Azure para PostgreSQL [utilizando psql](./quickstart-create-server-database-portal.md#connect-to-the-server-with-psql) e executar consultas na base de dados, por exemplo:

    ```sql
    select * from inventory;
    ```

[Tendo problemas? Deixe-nos saber](https://aka.ms/postgres-doc-feedback)

## <a name="clean-up-resources"></a>Limpar os recursos

Para limpar todos os recursos utilizados durante este arranque rápido, elimine o grupo de recursos utilizando o seguinte comando:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Gerir a base de dados do Azure para servidor postgresQL utilizando o Portal](./howto-create-manage-server-portal.md)<br/>

> [!div class="nextstepaction"]
> [Gerir a base de dados do Azure para servidor PostgreSQL utilizando o CLI](./how-to-manage-server-cli.md)<br/>

[Não consegue encontrar o que procura? Deixe-nos saber.](https://aka.ms/postgres-doc-feedback)
