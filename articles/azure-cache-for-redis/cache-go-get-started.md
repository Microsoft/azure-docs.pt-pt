---
title: Use cache Azure para Redis com Go
description: Neste quickstart, aprende-se a criar uma aplicação Go que utiliza a Azure Cache para Redis.
author: abhirockzz
ms.author: abhishgu
ms.service: cache
ms.devlang: go
ms.topic: quickstart
ms.date: 01/08/2021
ms.openlocfilehash: 04b582b5ef31e61039c5513ea2a4aa60f1c638e7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102121342"
---
# <a name="quickstart-use-azure-cache-for-redis-with-go"></a>Quickstart: Use Azure Cache para Redis com Go

Neste artigo, você aprenderá a construir uma API REST em Go que armazenará e recuperará informações do utilizador apoiadas por uma estrutura de dados [HASH](https://redis.io/topics/data-types-intro#redis-hashes) em [Azure Cache para Redis.](./cache-overview.md) 

## <a name="skip-to-the-code-on-github"></a>Salte para o código no GitHub

Se quiser saltar diretamente para o código, consulte o [quickstart Go](https://github.com/Azure-Samples/azure-redis-cache-go-quickstart/) no GitHub.

## <a name="prerequisites"></a>Pré-requisitos

- Azure subscrição - [crie uma gratuitamente](https://azure.microsoft.com/free/)
- [Ir](https://golang.org/doc/install) (de preferência a versão 1.13 ou superior)
- [Git](https://git-scm.com/downloads)
- Um cliente HTTP tal [caracóis](https://curl.se/)

## <a name="create-an-azure-cache-for-redis-instance"></a>Criar uma Cache Azure para a instância Redis
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="review-the-code-optional"></a>Rever o código (Opcional)

Se estiver interessado em saber como funciona o código, pode rever os seguintes cortes. Caso contrário, sinta-se à vontade para avançar para [executar a aplicação](#run-the-application).

A biblioteca open source [go-redis](https://github.com/go-redis/redis) é usada para interagir com Azure Cache para Redis.

A `main` função começa por ler o nome do anfitrião e a palavra-passe (Chave de Acesso) para a Cache Azure para a instância Redis.

```go
func main() {
    redisHost := os.Getenv("REDIS_HOST")
    redisPassword := os.Getenv("REDIS_PASSWORD")
...
```

Depois, estabelecemos ligação com Azure Cache para Redis. Note que [tls.Config](https://golang.org/pkg/crypto/tls/#Config) está a ser utilizado - A Azure Cache para Redis só aceita ligações seguras com [TLS 1.2 como a versão mínima exigida](cache-remove-tls-10-11.md).

```go
...
op := &redis.Options{Addr: redisHost, Password: redisPassword, TLSConfig: &tls.Config{MinVersion: tls.VersionTLS12}}
client := redis.NewClient(op)

ctx := context.Background()
err := client.Ping(ctx).Err()
if err != nil {
    log.Fatalf("failed to connect with redis instance at %s - %v", redisHost, err)
}
...
```

Se a ligação for bem sucedida, [os manipuladores HTTP](https://golang.org/pkg/net/http/#HandleFunc) são configurados para manusear `POST` e `GET` operações e o servidor HTTP é iniciado. 

> [!NOTE] 
> [Gorilla mux library](https://github.com/gorilla/mux) é usado para encaminhamento (embora não seja estritamente necessário e poderíamos ter escapado usando a biblioteca padrão para esta aplicação de amostra).
>

```go
uh := userHandler{client: client}

router := mux.NewRouter()
router.HandleFunc("/users/", uh.createUser).Methods(http.MethodPost)
router.HandleFunc("/users/{userid}", uh.getUser).Methods(http.MethodGet)

log.Fatal(http.ListenAndServe(":8080", router))
```

`userHandler` estrutura encapsula um [redis. O cliente](https://pkg.go.dev/github.com/go-redis/redis/v8#Client), que é utilizado pelos `createUser` `getUser` métodos , código para estes métodos não foi incluído por uma questão de brevidade. 

- `createUser`: aceita uma carga útil JSON (contendo informações do utilizador) e guarda-a como uma `HASH` cache em Azure para redis.
- `getUser`: recolhe informações do utilizador `HASH` ou devolve uma resposta HTTP se não for `404` encontrada.

```go
type userHandler struct {
    client *redis.Client
}
...

func (uh userHandler) createUser(rw http.ResponseWriter, r *http.Request) {
    // details omitted
}
...

func (uh userHandler) getUser(rw http.ResponseWriter, r *http.Request) {
    // details omitted
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
    git clone https://github.com/Azure-Samples/azure-redis-cache-go-quickstart.git
    ```

## <a name="run-the-application"></a>Executar a aplicação

A aplicação aceita conectividade e credenciais sob a forma de variáveis ambientais. 

1. Pegue o **nome do anfitrião** e **as teclas de acesso** (disponíveis através de Teclas de acesso) para Azure Cache para a instância Redis no portal [Azure](https://portal.azure.com/)

1. Defini-las para as respetivas variáveis ambientais:

    ```shell
    set REDIS_HOST=<Host name>:<port> (e.g. <name of cache>.redis.cache.windows.net:6380)
    set REDIS_PASSWORD=<Primary Access Key>
    ```

1. Na janela do terminal, mude para a pasta correta. Por exemplo:

    ```shell
    cd "C:\git-samples\azure-redis-cache-go-quickstart"
    ```

1. No terminal, executar o seguinte comando para iniciar a aplicação.

    ```shell
    go run main.go
    ```

O servidor HTTP começará na `8080` porta.

## <a name="test-the-application"></a>Testar a aplicação

1. Crie algumas entradas de utilizador. O exemplo abaixo usa caracóis:

    ```bash
    curl -i -X POST -d '{"id":"1","name":"foo1", "email":"foo1@baz.com"}' localhost:8080/users/
    curl -i -X POST -d '{"id":"2","name":"foo2", "email":"foo2@baz.com"}' localhost:8080/users/
    curl -i -X POST -d '{"id":"3","name":"foo3", "email":"foo3@baz.com"}' localhost:8080/users/
    ```

1. Buscar um utilizador existente com o `id` seu:

    ```bash
    curl -i localhost:8080/users/1
    ```

    Deve obter a resposta do JSON como tal:
    
    ```json
    {
        "email": "foo1@bar",
        "id": "1",
        "name": "foo1"
    }
    ```

1. Se tentar obter um utilizador que não exista, obterá um HTTP `404` . Por exemplo:

    ```bash
    curl -i localhost:8080/users/100
    
    #response

    HTTP/1.1 404 Not Found
    Date: Fri, 08 Jan 2021 13:43:39 GMT
    Content-Length: 0
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

Neste quickstart, aprendeu a usar Go with Azure Cache para Redis. Configura e executou uma aplicação simples baseada em API rest para criar e obter informações do utilizador apoiadas por uma estrutura de `HASH` dados Redis.

> [!div class="nextstepaction"]
> [Crie uma simples aplicação web ASP.NET que utilize um Cache Azure para Redis.](./cache-web-app-howto.md)
