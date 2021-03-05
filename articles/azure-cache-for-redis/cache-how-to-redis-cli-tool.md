---
title: Use redis-cli com cache Azure para Redis
description: Aprenda a usar *redis-cli.exe* como ferramenta de linha de comando para interagir com um Cache Azure para Redis como cliente
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: e4f5fc7290b45f65067f6711f70476e13a010223
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102183391"
---
# <a name="use-the-redis-command-line-tool-with-azure-cache-for-redis"></a>Utilize a ferramenta redis de linha de comando com cache Azure para Redis

*redis-cli.exe* é uma ferramenta popular de linha de comando para interagir com um Cache Azure para Redis como cliente. Esta ferramenta também está disponível para utilização com cache Azure para Redis.

A ferramenta está disponível para plataformas Windows descarregando as ferramentas da [linha de comando Redis para Windows](https://github.com/MSOpenTech/redis/releases/). 

Se pretender executar a ferramenta de linha de comando noutra plataforma, baixe a cache Azure para Redis a partir de [https://redis.io/download](https://redis.io/download) .

## <a name="gather-cache-access-information"></a>Recolha informações de acesso à cache

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pode recolher as informações necessárias para aceder à cache utilizando três métodos:

1. Azure CLI usando [az redis list-keys](/cli/azure/redis#az-redis-list-keys)
2. Azure PowerShell usando [Get-AzRedisCacheKey](/powershell/module/az.rediscache/Get-AzRedisCacheKey)
3. No portal do Azure.

Nesta secção, irá retirar as chaves do portal Azure.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## <a name="enable-access-for-redis-cliexe"></a>Permitir o acesso a redis-cli.exe

Com a Cache Azure para Redis, apenas a porta TLS (6380) está ativada por padrão. A `redis-cli.exe` ferramenta de linha de comando não suporta TLS. Tem duas opções de configuração para usá-lo:

1. [Ativar a porta não-TLS (6379)](cache-configure.md#access-ports)  -  **Esta configuração não é recomendada** porque nesta configuração, as teclas de acesso são enviadas via TCP em texto claro. Esta alteração pode comprometer o acesso à sua cache. O único cenário em que se pode considerar esta configuração é quando está apenas a aceder a uma cache de teste.

2. Faça o download e instale [atordoado.](https://www.stunnel.org/downloads.html)

    Executar **gui stunnel Iniciar** para iniciar o servidor.

    Clique com o botão direito no ícone da barra de tarefas para o servidor atordoado e clique em **Mostrar Janela de Registo**.

    No menu 'Janela's' stunnel, clique em  >  **Configuração De edição** para abrir o ficheiro de configuração atual.

    Adicione a seguinte entrada para *redis-cli.exe* na secção **definições de Serviço.** Insira o seu nome de cache real no lugar de `yourcachename` . 

    ```
    [redis-cli]
    client = yes
    accept = 127.0.0.1:6380
    connect = yourcachename.redis.cache.windows.net:6380
    ```

    Guarde e feche o ficheiro de configuração. 
  
    No menu 'Janela's' stunnel, clique em  >  **Configuração De Reload Configuration Reload**.


## <a name="connect-using-the-redis-command-line-tool"></a>Ligue-se utilizando a ferramenta da linha de comando Redis.

Quando utilizar atordoado, corra *redis-cli.exe*, e passe apenas a sua *porta*, e *a chave de acesso* (primária ou secundária) para ligar à cache.

```
redis-cli.exe -p 6380 -a YourAccessKey
```

![Screenshot que mostra que a sua ligação à cache é bem sucedida.](media/cache-how-to-redis-cli-tool/cache-redis-cli-stunnel.png)

Se estiver a utilizar uma cache de teste com a porta não-TLS **insegura,** corra `redis-cli.exe` e passe o nome de *anfitrião,* *porta* e chave *de acesso* (primária ou secundária) para ligar à cache de teste.

```
redis-cli.exe -h yourcachename.redis.cache.windows.net -p 6379 -a YourAccessKey
```

![atordoado com redis-cli](media/cache-how-to-redis-cli-tool/cache-redis-cli-non-ssl.png)




## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a utilização da [Consola Redis](cache-configure.md#redis-console) para emitir comandos.