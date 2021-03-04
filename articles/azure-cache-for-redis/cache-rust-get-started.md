---
title: Use cache Azure para redis com ferrugem
description: Neste arranque rápido, aprende-se a interagir com a Azure Cache para redis usando Rustis.
author: abhirockzz
ms.author: abhishgu
ms.service: cache
ms.devlang: rust
ms.topic: quickstart
ms.date: 01/08/2021
ms.openlocfilehash: 17f38d79b75179d7a54ca5ed1d20dff18d0a0363
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121104"
---
# <a name="quickstart-use-azure-cache-for-redis-with-rust"></a>Quickstart: Use cache Azure para Redis com Ferrugem

Neste artigo, você aprenderá a usar a [linguagem de programação Rust](https://www.rust-lang.org/) para interagir com [Azure Cache para Redis](./cache-overview.md). Irá demonstrar exemplos de estruturas de dados redis comumente utilizadas, tais como [String,](https://redis.io/topics/data-types-intro#redis-strings) [Hash,](https://redis.io/topics/data-types-intro#redis-hashes) [List,](https://redis.io/topics/data-types-intro#redis-lists) etc. usando a biblioteca [redis-rs](https://github.com/mitsuhiko/redis-rs) para Redis. Este cliente expõe APIs de alto e baixo nível e você verá ambos estes estilos em ação com a ajuda do código de amostra apresentado neste artigo.

## <a name="skip-to-the-code-on-github"></a>Salte para o código no GitHub

Se quiser saltar diretamente para o código, consulte o [quickstart rust](https://github.com/Azure-Samples/azure-redis-cache-rust-quickstart/) no GitHub.

## <a name="prerequisites"></a>Pré-requisitos

- Azure subscrição - [crie uma gratuitamente](https://azure.microsoft.com/free/)
- [Ferrugem](https://www.rust-lang.org/tools/install) (versão 1.39 ou superior)
- [Git](https://git-scm.com/downloads)

## <a name="create-an-azure-cache-for-redis-instance"></a>Criar uma Cache Azure para a instância Redis
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="review-the-code-optional"></a>Rever o código (opcional)

Se estiver interessado em saber como funciona o código, pode rever os seguintes cortes. Caso contrário, sinta-se à vontade para avançar para [executar a aplicação](#run-the-application).

A `connect` função é utilizada para estabelecer uma ligação com a Cache Azure para Redis. Espera que o nome do hospedeiro e a palavra-passe (Chave de Acesso) sejam passados através de variáveis ambientais `REDIS_HOSTNAME` `REDIS_PASSWORD` e, respectivamente. O formato para o URL de ligação é `rediss://<username>:<password>@<hostname>` - Azure Cache para Redis só aceita ligações seguras com [TLS 1.2 como a versão mínima exigida](cache-remove-tls-10-11.md).

A chamada para [redis::Cliente::aberto](https://docs.rs/redis/0.19.0/redis/struct.Client.html#method.open) executa validação básica enquanto [get_connection()](https://docs.rs/redis/0.19.0/redis/struct.Client.html#method.get_connection) realmente inicia a ligação - o programa para se a conectividade falhar devido a qualquer razão, como uma palavra-passe incorreta.

```rust
fn connect() -> redis::Connection {
    let redis_host_name =
        env::var("REDIS_HOSTNAME").expect("missing environment variable REDIS_HOSTNAME");
    let redis_password =
        env::var("REDIS_PASSWORD").expect("missing environment variable REDIS_PASSWORD");
    let redis_conn_url = format!("rediss://:{}@{}", redis_password, redis_host_name);

    redis::Client::open(redis_conn_url)
        .expect("invalid connection URL")
        .get_connection()
        .expect("failed to connect to redis")
}
```

A `basics` função abrange os comandos [SET,](https://redis.io/commands/set) [GET](https://redis.io/commands/get)e [INCR.](https://redis.io/commands/incr) A API de baixo nível é utilizada `SET` para `GET` e, que define e recupera o valor de uma chave chamada `foo` . O `INCRBY` comando é executado usando uma API de alto nível, ou seja, o [incr](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.incr) incrementa o valor de uma chave (nomeada) `counter` seguida de uma chamada para a `2` [recuperar.](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.get)

```rust
fn basics() {
    let mut conn = connect();
    let _: () = redis::cmd("SET")
        .arg("foo")
        .arg("bar")
        .query(&mut conn)
        .expect("failed to execute SET for 'foo'");

    let bar: String = redis::cmd("GET")
        .arg("foo")
        .query(&mut conn)
        .expect("failed to execute GET for 'foo'");
    println!("value for 'foo' = {}", bar);

    let _: () = conn
        .incr("counter", 2)
        .expect("failed to execute INCR for 'counter'");
    let val: i32 = conn
        .get("counter")
        .expect("failed to execute GET for 'counter'");
    println!("counter = {}", val);
}
```

O corte de código abaixo demonstra a funcionalidade de uma estrutura de `HASH` dados Redis. [O HSET](https://redis.io/commands/hset) é invocado utilizando a API de baixo nível para armazenar informações `name` sobre os `version` `repo` condutores da Redis (clientes). Por exemplo, os detalhes para o condutor Rust (um que está sendo usado neste código de amostra!) é capturado sob a forma de um [BTreeMap](https://doc.rust-lang.org/std/collections/struct.BTreeMap.html) e depois passado para a API de baixo nível. É então recuperado usando [HGETALL](https://redis.io/commands/hgetall).

`HSET` também pode ser executado usando uma API de alto nível usando [hset_multiple](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.hset_multiple) que aceita uma variedade de tuples. [hget](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.hget) é então executado para obter o valor de um único atributo (o `repo` neste caso).

```rust
fn hash() {
    let mut conn = connect();

    let mut driver: BTreeMap<String, String> = BTreeMap::new();
    let prefix = "redis-driver";
    driver.insert(String::from("name"), String::from("redis-rs"));
    driver.insert(String::from("version"), String::from("0.19.0"));
    driver.insert(
        String::from("repo"),
        String::from("https://github.com/mitsuhiko/redis-rs"),
    );

    let _: () = redis::cmd("HSET")
        .arg(format!("{}:{}", prefix, "rust"))
        .arg(driver)
        .query(&mut conn)
        .expect("failed to execute HSET");

    let info: BTreeMap<String, String> = redis::cmd("HGETALL")
        .arg(format!("{}:{}", prefix, "rust"))
        .query(&mut conn)
        .expect("failed to execute HGETALL");
    println!("info for rust redis driver: {:?}", info);

    let _: () = conn
        .hset_multiple(
            format!("{}:{}", prefix, "go"),
            &[
                ("name", "go-redis"),
                ("version", "8.4.6"),
                ("repo", "https://github.com/go-redis/redis"),
            ],
        )
        .expect("failed to execute HSET");

    let repo_name: String = conn
        .hget(format!("{}:{}", prefix, "go"), "repo")
        .expect("HGET failed");
    println!("go redis driver repo name: {:?}", repo_name);
}
```

Na função abaixo, pode ver como utilizar uma `LIST` estrutura de dados. [LPUSH](https://redis.io/commands/lpush) é executado (com a API de baixo nível) para adicionar uma entrada na lista e o método [lpop](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.lpop) de alto nível é usado para recuperar isso da lista. Em seguida, o método [rpush](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.rpush) é usado para adicionar um par de entradas na lista que são depois recolhidas usando o método [lrange](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.lrange) de baixo nível.

```rust
fn list() {
    let mut conn = connect();
    let list_name = "items";

    let _: () = redis::cmd("LPUSH")
        .arg(list_name)
        .arg("item-1")
        .query(&mut conn)
        .expect("failed to execute LPUSH for 'items'");

    let item: String = conn
        .lpop(list_name)
        .expect("failed to execute LPOP for 'items'");
    println!("first item: {}", item);

    let _: () = conn.rpush(list_name, "item-2").expect("RPUSH failed");
    let _: () = conn.rpush(list_name, "item-3").expect("RPUSH failed");

    let len: isize = conn
        .llen(list_name)
        .expect("failed to execute LLEN for 'items'");
    println!("no. of items in list = {}", len);

    let items: Vec<String> = conn
        .lrange(list_name, 0, len - 1)
        .expect("failed to execute LRANGE for 'items'");

    println!("listing items in list");
    for item in items {
        println!("item: {}", item)
    }
}
```

Aqui pode ver algumas das `SET` operações. O método [sadd](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.sadd) (API de alto nível) é usado para adicionar algumas entradas a um `SET` nome `users` . [SISMEMBER](https://redis.io/commands/hset) é então executado (API de baixo nível) para verificar se `user1` existe. Finalmente, [os membros do Smembers](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.smembers) são usados para buscar e iterar sobre todas as entradas definidas sob a forma de um[Vec (Vec). <String> ](https://doc.rust-lang.org/std/vec/struct.Vec.html)

```rust
fn set() {
    let mut conn = connect();
    let set_name = "users";

    let _: () = conn
        .sadd(set_name, "user1")
        .expect("failed to execute SADD for 'users'");
    let _: () = conn
        .sadd(set_name, "user2")
        .expect("failed to execute SADD for 'users'");

    let ismember: bool = redis::cmd("SISMEMBER")
        .arg(set_name)
        .arg("user1")
        .query(&mut conn)
        .expect("failed to execute SISMEMBER for 'users'");
    println!("does user1 exist in the set? {}", ismember);

    let users: Vec<String> = conn.smembers(set_name).expect("failed to execute SMEMBERS");
    println!("listing users in set");

    for user in users {
        println!("user: {}", user)
    }
}
```

`sorted_set` função abaixo demonstra a estrutura de dados do Conjunto Classificado. [ZADD](https://redis.io/commands/zadd) é invocado (com a API de baixo nível) para adicionar uma pontuação número inteiro aleatória para um jogador ( `player-1` ). Em seguida, o método [zadd](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.zadd) (API de alto nível) é usado para adicionar mais jogadores `player-2` (para `player-5` ) e suas respetivas pontuações (geradas aleatoriamente). O número de entradas no conjunto classificado é definido usando [o ZCARD](https://redis.io/commands/zcard) e que é usado como limite para o comando [ZRANGE](https://redis.io/commands/zrange) (invocado com a API de baixo nível) para listar os jogadores com as suas pontuações em ordem ascendente.

```rust
fn sorted_set() {
    let mut conn = connect();
    let sorted_set = "leaderboard";

    let _: () = redis::cmd("ZADD")
        .arg(sorted_set)
        .arg(rand::thread_rng().gen_range(1..10))
        .arg("player-1")
        .query(&mut conn)
        .expect("failed to execute ZADD for 'leaderboard'");

    for num in 2..=5 {
        let _: () = conn
            .zadd(
                sorted_set,
                String::from("player-") + &num.to_string(),
                rand::thread_rng().gen_range(1..10),
            )
            .expect("failed to execute ZADD for 'leaderboard'");
    }

    let count: isize = conn
        .zcard(sorted_set)
        .expect("failed to execute ZCARD for 'leaderboard'");

    let leaderboard: Vec<(String, isize)> = conn
        .zrange_withscores(sorted_set, 0, count - 1)
        .expect("ZRANGE failed");

    println!("listing players and scores in ascending order");

    for item in leaderboard {
        println!("{} = {}", item.0, item.1)
    }
}
```

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Comece por clonar a aplicação do GitHub.

1. Abra um pedido de comando e crie uma nova pasta chamada `git-samples` .

    ```bash
    md "C:\git-samples"
    ```

1. Abra uma janela de terminal do Git, como git bash. Utilize o `cd` comando para alterar a nova pasta onde estará a clonar a aplicação da amostra.

    ```bash
    cd "C:\git-samples"
    ```

1. Execute o seguinte comando para clonar o repositório de exemplo. Este comando cria uma cópia da aplicação de exemplo no seu computador.

    ```bash
    git clone https://github.com/Azure-Samples/azure-redis-cache-rust-quickstart.git
    ```

## <a name="run-the-application"></a>Executar a aplicação

A aplicação aceita conectividade e credenciais sob a forma de variáveis ambientais. 

1. Procure o **nome do anfitrião** e **as teclas de acesso** (disponíveis através de Teclas de acesso) para Azure Cache para a instância Redis no portal [Azure](https://portal.azure.com/).

1. Defini-las para as respetivas variáveis ambientais:

    ```shell
    set REDIS_HOSTNAME=<Host name>:<port> (e.g. <name of cache>.redis.cache.windows.net:6380)
    set REDIS_PASSWORD=<Primary Access Key>
    ```

1. Na janela do terminal, mude para a pasta correta. Por exemplo:

    ```shell
    cd "C:\git-samples\azure-redis-cache-rust-quickstart"
    ```

1. No terminal, executar o seguinte comando para iniciar a aplicação.

    ```shell
    cargo run
    ```
    
    Verá uma saída como tal:
    
    ```bash
    ******* Running SET, GET, INCR commands *******
    value for 'foo' = bar
    counter = 2
    ******* Running HASH commands *******
    info for rust redis driver: {"name": "redis-rs", "repo": "https://github.com/mitsuhiko/redis-rs", "version": "0.19.0"}
    go redis driver repo name: "https://github.com/go-redis/redis"
    ******* Running LIST commands *******
    first item: item-1
    no. of items in list = 2
    listing items in list
    item: item-2
    item: item-3
    ******* Running SET commands *******
    does user1 exist in the set? true
    listing users in set
    user: user2
    user: user1
    user: user3
    ******* Running SORTED SET commands *******
    listing players and scores
    player-2 = 2
    player-4 = 4
    player-1 = 7
    player-5 = 6
    player-3 = 8
    ```
    
    Se pretender executar uma função específica, comente outras funções na `main` função:
    
    ```rust
    fn main() {
        basics();
        hash();
        list();
        set();
        sorted_set();
    }
    ```

## <a name="clean-up-resources"></a>Limpar os recursos

Se terminar com o grupo de recursos Azure e os recursos que criou neste quickstart, pode eliminá-los para evitar encargos.

> [!IMPORTANT]
> A supressão de um grupo de recursos é irreversível e o grupo de recursos e todos os recursos nele eliminados. Se criou o seu Azure Cache para a instância Redis num grupo de recursos existente que pretende manter, pode eliminar apenas a cache selecionando **Eliminar** a partir da página **de visão geral** da cache. 

Para eliminar o grupo de recursos e a sua Cache Redis para a instância Azure:

1. A partir do [portal Azure,](https://portal.azure.com)procure e selecione **grupos de Recursos.**
1. Na caixa de texto **Filter por nome,** insira o nome do grupo de recursos que contém a sua instância de cache e, em seguida, selecione-o a partir dos resultados da pesquisa. 
1. Na sua página de grupo de recursos, selecione **Eliminar o grupo de recursos**.
1. Digite o nome do grupo de recursos e, em seguida, **selecione Delete**.
   
   ![Elimine o seu grupo de recursos para Azure Cache para Redis](./media/cache-python-get-started/delete-your-resource-group-for-azure-cache-for-redis.png)

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a usar o controlador Rust para redis para ligar e executar operações em Azure Cache para Redis.

> [!div class="nextstepaction"]
> [Crie uma simples aplicação web ASP.NET que utilize um Cache Azure para Redis.](./cache-web-app-howto.md)
