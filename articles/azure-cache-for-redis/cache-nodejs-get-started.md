---
title: Início rápido para aprender a usar o cache do Azure para Redis com node. js | Microsoft Docs
description: Neste início rápido, você aprenderá a usar o cache do Azure para Redis com node. js e node_redis.
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: v-lincan
ms.assetid: 06fddc95-8029-4a8d-83f5-ebd5016891d9
ms.service: cache
ms.devlang: nodejs
ms.topic: quickstart
ms.tgt_pltfrm: cache
ms.workload: tbd
ms.date: 05/21/2018
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: dc629133a393c4ff8f9193b02e9080d7f08f4958
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326467"
---
# <a name="quickstart-use-azure-cache-for-redis-with-nodejs"></a>Início rápido: Usar o cache do Azure para Redis com node. js

Neste guia de início rápido, você incorpora o cache do Azure para Redis em um aplicativo node. js para ter acesso a um cache seguro e dedicado que pode ser acessado de qualquer aplicativo no Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
- [node_redis](https://github.com/mranney/node_redis), que você pode instalar com o comando `npm install redis`. 

Para obter exemplos de utilização de outros clientes Node.js, consulte a documentação individual para os clientes Node.js listados em [Clientes Node.js Redis](https://redis.io/clients#nodejs).

## <a name="create-a-cache"></a>Criar uma cache
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](../../includes/redis-cache-access-keys.md)]


Adicione variáveis de ambiente ao **NOME DO ANFITRIÃO** e à chave de acesso **Primária**. Vai utilizar estas variáveis do seu código em vez de incluir informações confidenciais diretamente no código.

```
set REDISCACHEHOSTNAME=contosoCache.redis.cache.windows.net
set REDISCACHEKEY=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
```

## <a name="connect-to-the-cache"></a>Ligar à cache

As compilações mais recentes do [node_redis](https://github.com/mranney/node_redis) fornecem suporte para conexão com o cache do Azure para REDIS usando SSL. O exemplo a seguir mostra como se conectar ao cache do Azure para Redis usando o ponto de extremidade SSL de 6380. 

```js
var redis = require("redis");

// Add your cache name and access key.
var client = redis.createClient(6380, process.env.REDISCACHEHOSTNAME,
    {auth_pass: process.env.REDISCACHEKEY, tls: {servername: process.env.REDISCACHEHOSTNAME}});
```

Não crie uma ligação nova para cada operação no código. Em vez disso, reutilize ligações tanto quanto possível. 

## <a name="create-a-new-nodejs-app"></a>Criar uma aplicação nova em Node.js

Crie um novo ficheiro de script com o nome *redistest.js*.

Adicione o seguinte exemplo de JavaScript ao ficheiro. Este código mostra como se conectar a um cache do Azure para a instância do Redis usando o nome de host do cache e as variáveis de ambiente de chave. O código também armazena e obtém um valor de cadeia na cache. Os comandos `PING` e `CLIENT LIST` também são executados. Para obter mais exemplos de utilização de Redis com o cliente [node_redis](https://github.com/mranney/node_redis), veja [https://redis.js.org/](https://redis.js.org/).

```js
var redis = require("redis");
var bluebird = require("bluebird");

bluebird.promisifyAll(redis.RedisClient.prototype);
bluebird.promisifyAll(redis.Multi.prototype);

async function testCache() {

    // Connect to the Azure Cache for Redis over the SSL port using the key.
    var cacheConnection = redis.createClient(6380, process.env.REDISCACHEHOSTNAME, 
        {auth_pass: process.env.REDISCACHEKEY, tls: {servername: process.env.REDISCACHEHOSTNAME}});
        
    // Perform cache operations using the cache connection object...

    // Simple PING command
    console.log("\nCache command: PING");
    console.log("Cache response : " + await cacheConnection.pingAsync());

    // Simple get and put of integral data types into the cache
    console.log("\nCache command: GET Message");
    console.log("Cache response : " + await cacheConnection.getAsync("Message"));    

    console.log("\nCache command: SET Message");
    console.log("Cache response : " + await cacheConnection.setAsync("Message",
        "Hello! The cache is working from Node.js!"));    

    // Demonstrate "SET Message" executed as expected...
    console.log("\nCache command: GET Message");
    console.log("Cache response : " + await cacheConnection.getAsync("Message"));    

    // Get the client list, useful to see if connection list is growing...
    console.log("\nCache command: CLIENT LIST");
    console.log("Cache response : " + await cacheConnection.clientAsync("LIST"));    
}

testCache();
```

Execute o script com o Node.js.

```
node redistest.js
```

No exemplo abaixo, pode ver que a chave `Message` tinha anteriormente um valor em cache, o qual foi definido com a Consola Redis no portal do Azure. A aplicação atualizou esse valor em cache. A aplicação também executou os comandos `PING` e `CLIENT LIST`.

![Aplicação de cache concluída](./media/cache-nodejs-get-started/cache-app-complete.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se avançar para o próximo tutorial, pode manter os recursos que criou neste início rápido e reutilizá-los.

Caso contrário, se tiver concluído a aplicação de exemplo do início rápido, poderá eliminar os recursos do Azure criados neste início rápido, para evitar encargos. 

> [!IMPORTANT]
> A eliminação de um grupo de recursos é irreversível e o grupo de recursos e todos os recursos nele contidos serão permanentemente eliminados. Confirme que não elimina acidentalmente o grupo de recursos ou recursos errados. Se tiver criado os recursos para alojar este exemplo num grupo de recursos existente que contém os recursos que pretende manter, poderá eliminar cada recurso individualmente nos respetivos painéis em vez de eliminar o grupo de recursos.
>

Inicie sessão no [Portal do Azure](https://portal.azure.com) e clique em **Grupos de recursos**.

Na caixa de texto **Filtrar por nome...** , escreva o nome do grupo de recursos. As instruções neste artigo utilizaram um grupo de recursos denominado *TestResources*. No grupo de recursos na lista de resultados, clique em **...** e em **Eliminar grupo de recursos**.

![Eliminar](./media/cache-nodejs-get-started/cache-delete-resource-group.png)

É-lhe pedido que confirme a eliminação do grupo de recursos. Escreva o nome do grupo de recursos para confirmar e clique em **Eliminar**.

Após alguns instantes, o grupo de recursos e todos os recursos contidos no mesmo são eliminados.

## <a name="next-steps"></a>Passos Seguintes

Neste guia de início rápido, você aprendeu a usar o cache do Azure para Redis de um aplicativo node. js. Continue para o próximo início rápido para usar o cache do Azure para Redis com um aplicativo Web ASP.NET.

> [!div class="nextstepaction"]
> [Crie um aplicativo Web ASP.NET que usa um cache do Azure para Redis.](./cache-web-app-howto.md)
