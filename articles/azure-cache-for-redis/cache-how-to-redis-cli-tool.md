---
title: Como utilizar o redis-cli de Redis cache do Azure | Documentos da Microsoft
description: Saiba como utilizar o redis-cli com o Azure Cache de Redis.
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: yegu
ms.openlocfilehash: 318d02f5da816ae8fe2fe199b9c87b3748d5d1fc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "66133025"
---
# <a name="how-to-use-the-redis-command-line-tool-with-azure-cache-for-redis"></a>Como utilizar a ferramenta de linha de comandos da Redis cache do Azure para Redis

*redis cli.exe* é uma ferramenta de linha de comandos popular para interagir com uma Cache do Azure para Redis como um cliente. Essa ferramenta também está disponível para utilização com a Cache de Redis do Azure.

A ferramenta está disponível para plataformas Windows baixando o [Redis ferramentas da linha de comandos para Windows](https://github.com/MSOpenTech/redis/releases/). 

Se quiser executar a ferramenta da linha de comandos em outra plataforma, Baixe o Cache do Azure para Redis a partir da [ https://redis.io/download ](https://redis.io/download).

## <a name="gather-cache-access-information"></a>Recolher informações de acesso da cache

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pode recolher as informações necessárias para aceder à cache usando três métodos:

1. CLI do Azure com [az redis lista as chaves](https://docs.microsoft.com/cli/azure/redis?view=azure-cli-latest#az-redis-list-keys)
2. Azure PowerShell com [Get AzRedisCacheKey](https://docs.microsoft.com/powershell/module/az.rediscache/Get-AzRedisCacheKey)
3. No portal do Azure.

Nesta secção, irá obter as chaves do portal do Azure.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## <a name="enable-access-for-redis-cliexe"></a>Ativar o acesso redis-cli.exe

Com a Cache de Redis do Azure, apenas o SSL da porta (6380) está ativada por predefinição. O `redis-cli.exe` ferramenta da linha de comandos não suporta SSL. Há duas opções de configuração para utilizá-lo:

1. [Ativar a porta não SSL (6379)](cache-configure.md#access-ports) - **esta configuração não é recomendada** porque esta configuração, as chaves de acesso são enviadas através de TCP em texto não criptografado. Esta alteração pode comprometer o acesso à sua cache. O único cenário onde pode considerar a esta configuração é quando apenas estão a aceder uma cache de teste.

2. Transfira e instale [túnel](https://www.stunnel.org/downloads.html).

    Execute **túnel GUI início** para iniciar o servidor.

    Faça duplo clique no ícone da barra de tarefas para o servidor do túnel e clique em **Mostrar a janela de registo**.

    No menu da janela de registo de túnel, clique em **Configuration** > **Editar configuração** para abrir o ficheiro de configuração atual.

    Adicione a seguinte entrada para *redis cli.exe* sob a **definições do serviço** secção. Inserir o nome da sua cache real em vez de `yourcachename`. 

    ```
    [redis-cli]
    client = yes
    accept = 127.0.0.1:6380
    connect = yourcachename.redis.cache.windows.net:6380
    ```

    Guarde e feche o ficheiro de configuração. 
  
    No menu da janela de registo de túnel, clique em **Configuration** > **recarregar configuração**.


## <a name="connect-using-the-redis-command-line-tool"></a>Ligue-se com a ferramenta de linha de comandos da Redis.

Ao utilizar o túnel, execute *redis cli.exe*e apenas passar seu *porta*, e *chave de acesso* (primário ou secundário) para ligar à cache.

```
redis-cli.exe -p 6380 -a YourAccessKey
```

![túnel com o redis-cli](media/cache-how-to-redis-cli-tool/cache-redis-cli-stunnel.png)

Se estiver a utilizar uma cache de teste com o **inseguras** porta não SSL, execute `redis-cli.exe` e passar seu *nome de anfitrião*, *porta*, e *chave de acesso*(primário ou secundário) para ligar à cache de teste.

```
redis-cli.exe -h yourcachename.redis.cache.windows.net -p 6379 -a YourAccessKey
```

![túnel com o redis-cli](media/cache-how-to-redis-cli-tool/cache-redis-cli-non-ssl.png)




## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre como utilizar o [consola de Redis](cache-configure.md#redis-console) aos comandos do problema.

