---
title: Como usar redis-cli com Azure Cache para Redis
description: Aprenda a usar *redis-cli.exe* como uma ferramenta de linha de comando para interagir com um Azure Cache para Redis como cliente.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 03/22/2018
ms.openlocfilehash: bd2da798cae92a7e47bd879b69dd108618463402
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010771"
---
# <a name="how-to-use-the-redis-command-line-tool-with-azure-cache-for-redis"></a>Como utilizar a ferramenta de linha de comando Redis com Azure Cache para Redis

*redis-cli.exe* é uma ferramenta popular de linha de comando para interagir com um Azure Cache para Redis como cliente. Esta ferramenta também está disponível para utilização com Azure Cache para Redis.

A ferramenta está disponível para plataformas Windows, baixando as [ferramentas de linha de comando Redis para Windows](https://github.com/MSOpenTech/redis/releases/). 

Se quiser executar a ferramenta de linha de comando noutra plataforma, [https://redis.io/download](https://redis.io/download)baixe o Azure Cache para Redis a partir de .

## <a name="gather-cache-access-information"></a>Recolher informações de acesso a cache

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pode recolher as informações necessárias para aceder à cache utilizando três métodos:

1. Azure CLI usando [az redis lista-keys](https://docs.microsoft.com/cli/azure/redis?view=azure-cli-latest#az-redis-list-keys)
2. Azure PowerShell usando [Get-AzRedisCacheKey](https://docs.microsoft.com/powershell/module/az.rediscache/Get-AzRedisCacheKey)
3. Utilizar o portal do Azure.

Nesta secção, você vai recuperar as chaves do portal Azure.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## <a name="enable-access-for-redis-cliexe"></a>Ativar o acesso para redis-cli.exe

Com O Cache Azure para Redis, apenas a porta TLS (6380) é ativada por defeito. A `redis-cli.exe` ferramenta de linha de comando não suporta TLS. Tem duas opções de configuração para usá-lo:

1. [Ativar a porta não-TLS (6379)](cache-configure.md#access-ports) - **Esta configuração não é recomendada** porque nesta configuração, as teclas de acesso são enviadas via TCP em texto claro. Esta alteração pode comprometer o acesso à sua cache. O único cenário em que se pode considerar esta configuração é quando está apenas a aceder a uma cache de teste.

2. Descarregue e instale [o túnel.](https://www.stunnel.org/downloads.html)

    Executar **o túnel GUI Comece** a ligar o servidor.

    Clique no ícone da barra de tarefas para o servidor de túnel e clique na Janela de **Registo do Show**.

    No menu stunnel Log Window, clique na **configuração** > de**configuração** para abrir o ficheiro de configuração atual.

    Adicione a seguinte entrada para *redis-cli.exe* na secção definições de **serviço.** Insira o seu `yourcachename`nome real de cache no lugar de . 

    ```
    [redis-cli]
    client = yes
    accept = 127.0.0.1:6380
    connect = yourcachename.redis.cache.windows.net:6380
    ```

    Guarde e feche o ficheiro de configuração. 
  
    No menu da janela de log stunnel, clique na configuração de **configuração** > **de recarga**.


## <a name="connect-using-the-redis-command-line-tool"></a>Ligue-se utilizando a ferramenta de linha de comando Redis.

Quando utilizar o túnel, corra *redis-cli.exe*, e passe apenas a sua *porta*, e chave de *acesso* (primária ou secundária) para ligar à cache.

```
redis-cli.exe -p 6380 -a YourAccessKey
```

![túnel com redis-cli](media/cache-how-to-redis-cli-tool/cache-redis-cli-stunnel.png)

Se estiver a utilizar uma cache de teste com a `redis-cli.exe` porta não-TLS não **segura,** corra e passe o nome do *anfitrião,* *a porta*e a chave de *acesso* (primária ou secundária) para se ligar à cache de teste.

```
redis-cli.exe -h yourcachename.redis.cache.windows.net -p 6379 -a YourAccessKey
```

![túnel com redis-cli](media/cache-how-to-redis-cli-tool/cache-redis-cli-non-ssl.png)




## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a utilização da [Consola Redis](cache-configure.md#redis-console) para emitir comandos.

