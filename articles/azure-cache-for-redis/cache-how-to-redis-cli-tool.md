---
title: Como usar o Redis-CLI com o cache do Azure para Redis
description: Saiba como usar o *Redis-CLI. exe* como uma ferramenta de linha de comando para interagir com um cache do Azure para Redis como um cliente.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 03/22/2018
ms.openlocfilehash: a48e69f19db88c7823365964c2fe9c0629a078bc
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75412677"
---
# <a name="how-to-use-the-redis-command-line-tool-with-azure-cache-for-redis"></a>Como usar a ferramenta de linha de comando Redis com o cache do Azure para Redis

o *Redis-CLI. exe* é uma ferramenta de linha de comando popular para interagir com um cache do Azure para Redis como um cliente. Essa ferramenta também está disponível para uso com o cache do Azure para Redis.

A ferramenta está disponível para plataformas Windows baixando as [ferramentas de linha de comando Redis para Windows](https://github.com/MSOpenTech/redis/releases/). 

Se você quiser executar a ferramenta de linha de comando em outra plataforma, baixe o cache do Azure para Redis de [https://redis.io/download](https://redis.io/download).

## <a name="gather-cache-access-information"></a>Coletar informações de acesso ao cache

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Você pode coletar as informações necessárias para acessar o cache usando três métodos:

1. CLI do Azure usando [AZ Redis List-Keys](https://docs.microsoft.com/cli/azure/redis?view=azure-cli-latest#az-redis-list-keys)
2. Azure PowerShell usando [Get-AzRedisCacheKey](https://docs.microsoft.com/powershell/module/az.rediscache/Get-AzRedisCacheKey)
3. No portal do Azure.

Nesta seção, você irá recuperar as chaves do portal do Azure.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## <a name="enable-access-for-redis-cliexe"></a>Habilitar o acesso para Redis-CLI. exe

Com o cache do Azure para Redis, somente a porta SSL (6380) é habilitada por padrão. A ferramenta de linha de comando `redis-cli.exe` não dá suporte a SSL. Você tem duas opções de configuração para usá-la:

1. [Habilitar a porta não SSL (6379)](cache-configure.md#access-ports) - **essa configuração não é recomendada** porque, nessa configuração, as chaves de acesso são enviadas via TCP em texto não criptografado. Essa alteração pode comprometer o acesso ao seu cache. O único cenário em que você pode considerar essa configuração é quando você está acessando apenas um cache de teste.

2. Baixe e instale o [stunnel](https://www.stunnel.org/downloads.html).

    Execute **STUNNEL GUI Start** para iniciar o servidor.

    Clique com o botão direito do mouse no ícone da barra de tarefas do servidor stunnel e clique em **Mostrar janela de log**.

    No menu da janela de log do stunnel, clique em **configuração** > **Editar configuração** para abrir o arquivo de configuração atual.

    Adicione a seguinte entrada para *Redis-CLI. exe* na seção **definições de serviço** . Insira seu nome de cache real no lugar de `yourcachename`. 

    ```
    [redis-cli]
    client = yes
    accept = 127.0.0.1:6380
    connect = yourcachename.redis.cache.windows.net:6380
    ```

    Salve e feche o arquivo de configuração. 
  
    No menu da janela de log do stunnel, clique em **configuração** > **recarregar configuração**.


## <a name="connect-using-the-redis-command-line-tool"></a>Conecte-se usando a ferramenta de linha de comando Redis.

Ao usar o stunnel, execute *Redis-CLI. exe*e passe apenas sua *porta*e a *chave de acesso* (primária ou secundária) para se conectar ao cache.

```
redis-cli.exe -p 6380 -a YourAccessKey
```

![stunnel com Redis-CLI](media/cache-how-to-redis-cli-tool/cache-redis-cli-stunnel.png)

Se você estiver usando um cache de teste com a porta não SSL não **segura** , execute `redis-cli.exe` e passe seu *nome de host*, *porta*e *chave de acesso* (primário ou secundário) para se conectar ao cache de teste.

```
redis-cli.exe -h yourcachename.redis.cache.windows.net -p 6379 -a YourAccessKey
```

![stunnel com Redis-CLI](media/cache-how-to-redis-cli-tool/cache-redis-cli-non-ssl.png)




## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como usar o [console do Redis](cache-configure.md#redis-console) para emitir comandos.

