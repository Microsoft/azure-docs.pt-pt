---
title: 'Quickstart: Use Azure Cache para Redis em Java'
description: Neste quickstart, você vai criar uma nova app Java que usa Azure Cache para Redis
author: yegu-ms
ms.service: cache
ms.devlang: java
ms.topic: quickstart
ms.date: 05/22/2020
ms.author: yegu
ms.custom: mvc, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: edb80ab3107cbd0a5ddd802d56a60ce77affb2f2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012923"
---
# <a name="quickstart-use-azure-cache-for-redis-in-java"></a>Quickstart: Use Azure Cache para Redis em Java

Neste quickstart, incorpora o Azure Cache para Redis numa aplicação Java utilizando o cliente [Jedis](https://github.com/xetorthio/jedis) Redis para ter acesso a uma cache segura e dedicada que esteja acessível a partir de qualquer aplicação dentro do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Azure subscrição - [crie uma gratuitamente](https://azure.microsoft.com/free/)
- [Apache Maven](https://maven.apache.org/download.cgi)

## <a name="create-an-azure-cache-for-redis"></a>Criar uma Cache do Azure para Redis

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](../../includes/redis-cache-access-keys.md)]

## <a name="setting-up-the-working-environment"></a>Criação do ambiente de trabalho 

Dependendo do seu sistema operativo, adicione variáveis ambientais para o **nome anfitrião** e **a chave de acesso primário**. Abra uma solicitação de comando, ou uma janela de terminal, e configura os seguintes valores:

```CMD 
set REDISCACHEHOSTNAME=<YOUR_HOST_NAME>.redis.cache.windows.net
set REDISCACHEKEY=<YOUR_PRIMARY_ACCESS_KEY>
```

```bash
export REDISCACHEHOSTNAME=<YOUR_HOST_NAME>.redis.cache.windows.net
export REDISCACHEKEY=<YOUR_PRIMARY_ACCESS_KEY>
```

Substitua os espaços reservados pelos seguintes valores:

- `<YOUR_HOST_NAME>`: O nome de anfitrião DNS, obtido a partir da secção *Propriedades* do seu Recurso Azure Cache para Redis no portal Azure.
- `<YOUR_PRIMARY_ACCESS_KEY>`: A chave de acesso primário, obtida a partir da secção *de teclas* de acesso do seu recurso Azure Cache para Redis no portal Azure.

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
        <version>3.2.0</version>
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

        // Connect to the Azure Cache for Redis over the TLS/SSL port using the key.
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

Este código mostra-lhe como ligar a uma Cache Azure para a instância Redis usando o nome do anfitrião da cache e variáveis de ambiente chave. O código também armazena e obtém um valor de cadeia na cache. Os comandos `PING` e `CLIENT LIST` também são executados. 

Save *App.java*.

## <a name="build-and-run-the-app"></a>Compilar e executar a aplicação

Execute o seguinte comando Maven para criar e executar a aplicação:

```CMD
mvn compile
mvn exec:java -D exec.mainClass=example.demo.App
```

No exemplo abaixo, pode ver que a chave `Message` tinha anteriormente um valor em cache, o qual foi definido com a Consola Redis no portal do Azure. A aplicação atualizou esse valor em cache. A aplicação também executou os comandos `PING` e `CLIENT LIST`.

![Azure Cache para a aplicação Redis concluída](./media/cache-java-get-started/azure-cache-redis-complete.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Se avançar para o próximo tutorial, pode manter os recursos que criou neste início rápido e reutilizá-los.

Caso contrário, se tiver concluído a aplicação de exemplo do início rápido, pode eliminar os recursos do Azure criados neste início rápido, para evitar encargos. 

> [!IMPORTANT]
> A eliminação de um grupo de recursos é irreversível e o grupo de recursos e todos os recursos contidos no mesmo serão permanentemente eliminados. Confirme que não elimina acidentalmente o grupo de recursos ou recursos errados. Se tiver criado os recursos para alojar este exemplo num grupo de recursos existente que contém os recursos que pretende manter, poderá eliminar cada recurso individualmente nos respetivos painéis em vez de eliminar o grupo de recursos.
>

1. Inicie sessão no [Portal do Azure](https://portal.azure.com) e selecione **Grupos de recursos**.

1. Na caixa de texto **filter por nome,** digite o nome do seu grupo de recursos. As instruções neste artigo utilizaram um grupo de recursos denominado *TestResources*. No seu grupo de recursos na lista de resultados, selecione **...** e, em seguida, **eliminar o grupo de recursos**.

   ![Grupo de recursos Azure eliminado](./media/cache-java-get-started/azure-cache-redis-delete-resource-group.png)

1. É-lhe pedido que confirme a eliminação do grupo de recursos. Digite o nome do seu grupo de recursos para confirmar e selecione **Delete**.

Após alguns instantes, o grupo de recursos e todos os recursos contidos no mesmo são eliminados.

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, aprendeu a usar a Cache Azure para Redis a partir de uma aplicação Java. Continue até ao próximo quickstart para usar Azure Cache para Redis com uma aplicação web ASP.NET.

> [!div class="nextstepaction"]
> [Crie uma aplicação web ASP.NET que utilize um Cache Azure para Redis.](./cache-web-app-howto.md)
