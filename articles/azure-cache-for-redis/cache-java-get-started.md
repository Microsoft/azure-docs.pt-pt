---
title: Início rápido para aprender a usar o cache do Azure para Redis com Java | Microsoft Docs
description: Neste guia de início rápido, você criará um novo aplicativo Java que usa o cache do Azure para Redis
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 29275a5e-2e39-4ef2-804f-7ecc5161eab9
ms.service: cache
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: cache
ms.workload: tbd
ms.date: 05/23/2018
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 01fb6f66ce71599a70a127a2b73eba6f848bf365
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68324131"
---
# <a name="quickstart-use-azure-cache-for-redis-with-java"></a>Início rápido: Usar o cache do Azure para Redis com Java

Neste guia de início rápido, você incorpora o cache do Azure para Redis em um aplicativo Java usando o cliente [Jedis](https://github.com/xetorthio/jedis) Redis para ter acesso a um cache seguro e dedicado que pode ser acessado de qualquer aplicativo no Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
- [Apache Maven](https://maven.apache.org/download.cgi)

## <a name="create-an-azure-cache-for-redis"></a>Criar uma Cache do Azure para Redis

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](../../includes/redis-cache-access-keys.md)]

Adicione variáveis de ambiente ao **NOME DO ANFITRIÃO** e à chave de acesso **Primária**. Vai utilizar estas variáveis do seu código em vez de incluir informações confidenciais diretamente no código.

```CMD 
set REDISCACHEHOSTNAME=contosoCache.redis.cache.windows.net
set REDISCACHEKEY=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
```

## <a name="create-a-new-java-app"></a>Criar uma nova aplicação Java

Com o Maven, gera uma nova aplicação de início rápido:

```CMD
mvn archetype:generate -DarchetypeGroupId=org.apache.maven.archetypes -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.3 -DgroupId=example.demo -DartifactId=redistest -Dversion=1.0
```

Mude para o novo diretório de projeto *redistest*.

Abra o ficheiro *pom.xml* e adicione uma dependência para [Jedis](https://github.com/xetorthio/jedis):

```xml
    <dependency>
        <groupId>redis.clients</groupId>
        <artifactId>jedis</artifactId>
        <version>2.9.0</version>
        <type>jar</type>
        <scope>compile</scope>
    </dependency>
```

Guarde o ficheiro *pom.xml*.

Abra *App.java* e substitua o código pelo seguinte código:

```java
package example.demo;

import redis.clients.jedis.Jedis;
import redis.clients.jedis.JedisShardInfo;

/**
 * Redis test
 *
 */
public class App 
{
    public static void main( String[] args )
    {

        boolean useSsl = true;
        String cacheHostname = System.getenv("REDISCACHEHOSTNAME");
        String cachekey = System.getenv("REDISCACHEKEY");

        // Connect to the Azure Cache for Redis over the SSL port using the key.
        JedisShardInfo shardInfo = new JedisShardInfo(cacheHostname, 6380, useSsl);
        shardInfo.setPassword(cachekey); /* Use your access key. */
        Jedis jedis = new Jedis(shardInfo);      

        // Perform cache operations using the cache connection object...

        // Simple PING command        
        System.out.println( "\nCache Command  : Ping" );
        System.out.println( "Cache Response : " + jedis.ping());

        // Simple get and put of integral data types into the cache
        System.out.println( "\nCache Command  : GET Message" );
        System.out.println( "Cache Response : " + jedis.get("Message"));

        System.out.println( "\nCache Command  : SET Message" );
        System.out.println( "Cache Response : " + jedis.set("Message", "Hello! The cache is working from Java!"));

        // Demonstrate "SET Message" executed as expected...
        System.out.println( "\nCache Command  : GET Message" );
        System.out.println( "Cache Response : " + jedis.get("Message"));

        // Get the client list, useful to see if connection list is growing...
        System.out.println( "\nCache Command  : CLIENT LIST" );
        System.out.println( "Cache Response : " + jedis.clientList());

        jedis.close();
    }
}
```

Este código mostra como se conectar a um cache do Azure para a instância do Redis usando o nome de host do cache e as variáveis de ambiente de chave. O código também armazena e obtém um valor de cadeia na cache. Os comandos `PING` e `CLIENT LIST` também são executados. 

Save *App.java*.

## <a name="build-and-run-the-app"></a>Compilar e executar a aplicação

Execute o seguinte comando Maven para criar e executar a aplicação:

```CMD
mvn compile
mvn exec:java -D exec.mainClass=example.demo.App
```

No exemplo abaixo, pode ver que a chave `Message` tinha anteriormente um valor em cache, o qual foi definido com a Consola Redis no portal do Azure. A aplicação atualizou esse valor em cache. A aplicação também executou os comandos `PING` e `CLIENT LIST`.

![Aplicação de cache concluída](./media/cache-java-get-started/cache-app-complete.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se avançar para o próximo tutorial, pode manter os recursos que criou neste início rápido e reutilizá-los.

Caso contrário, se tiver concluído a aplicação de exemplo do início rápido, poderá eliminar os recursos do Azure criados neste início rápido, para evitar encargos. 

> [!IMPORTANT]
> A eliminação de um grupo de recursos é irreversível e o grupo de recursos e todos os recursos nele contidos serão permanentemente eliminados. Confirme que não elimina acidentalmente o grupo de recursos ou recursos errados. Se tiver criado os recursos para alojar este exemplo num grupo de recursos existente que contém os recursos que pretende manter, poderá eliminar cada recurso individualmente nos respetivos painéis em vez de eliminar o grupo de recursos.
>

Inicie sessão no [Portal do Azure](https://portal.azure.com) e clique em **Grupos de recursos**.

Na caixa de texto **Filtrar por nome...** , escreva o nome do grupo de recursos. As instruções neste artigo utilizaram um grupo de recursos denominado *TestResources*. No grupo de recursos na lista de resultados, clique em **...** e em **Eliminar grupo de recursos**.

![Eliminar](./media/cache-java-get-started/cache-delete-resource-group.png)

É-lhe pedido que confirme a eliminação do grupo de recursos. Escreva o nome do grupo de recursos para confirmar e clique em **Eliminar**.

Após alguns instantes, o grupo de recursos e todos os recursos contidos no mesmo são eliminados.

## <a name="next-steps"></a>Passos Seguintes

Neste guia de início rápido, você aprendeu a usar o cache do Azure para Redis de um aplicativo Java. Continue para o próximo início rápido para usar o cache do Azure para Redis com um aplicativo Web ASP.NET.

> [!div class="nextstepaction"]
> [Crie um aplicativo Web ASP.NET que usa um cache do Azure para Redis.](./cache-web-app-howto.md)
