---
title: Resolver problemas de perda de dados da Cache do Azure para Redis
description: Aprenda a resolver problemas de perda de dados com o Azure Cache para redis, tais como perda parcial de chaves, expiração da chave ou perda completa de chaves.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: d54506b94f076f0a3d967f88bd4e2960a1ca6396
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75530906"
---
# <a name="troubleshoot-data-loss-in-azure-cache-for-redis"></a>Resolver problemas de perda de dados da Cache do Azure para Redis

Este artigo discute como diagnosticar perdas de dados reais ou percebidas que podem ocorrer em Azure Cache para Redis.

> [!NOTE]
> Várias das etapas de resolução de problemas neste guia incluem instruções para executar comandos Redis e monitorizar várias métricas de desempenho. Para mais informações e instruções, consulte os artigos na secção [informação adicional.](#additional-information)
>

## <a name="partial-loss-of-keys"></a>Perda parcial de chaves

O Azure Cache para o Redis não apaga as chaves aleatoriamente depois de terem sido guardadas na memória. No entanto, remove as chaves em resposta a políticas de expiração ou despejo e a comandos explícitos de eliminação de chaves. As chaves que foram escritas ao nó principal num Premium ou Standard Azure Cache para redis também podem não estar disponíveis numa réplica imediatamente. Os dados são replicados do mestre para a réplica de forma assíncrona e não bloqueada.

Se descobrir que as chaves desapareceram da sua cache, verifique as seguintes causas possíveis:

| Causa | Descrição |
|---|---|
| [Expiração da chave](#key-expiration) | As chaves são removidas devido aos intervalos de tempo definidos. |
| [Despejo chave](#key-eviction) | As teclas são removidas sob pressão de memória. |
| [Eliminação da chave](#key-deletion) | As teclas são removidas por comandos de eliminação explícitos. |
| [Replicação asincronizada](#async-replication) | As chaves não estão disponíveis numa réplica devido a atrasos na replicação de dados. |

### <a name="key-expiration"></a>Expiração da chave

O Azure Cache para Redis remove automaticamente uma chave se a chave for atribuída uma saída e esse período tiver passado. Para obter mais informações sobre a expiração da chave Redis, consulte a documentação do comando [EXPIRE.](https://redis.io/commands/expire) Os valores de tempo-out também podem ser definidos utilizando os comandos [SET,](https://redis.io/commands/set) [SETEX,](https://redis.io/commands/setex) [GETSET](https://redis.io/commands/getset)e outros ** \*comandos STORE.**

Para obter estatísticas sobre quantas chaves expiraram, use o comando [INFO.](https://redis.io/commands/info) A `Stats` secção mostra o número total de chaves expiradas. A `Keyspace` secção fornece mais informações sobre o número de chaves com intervalos e o valor médio do tempo de descanso.

```
# Stats

expired_keys:46583

# Keyspace

db0:keys=3450,expires=2,avg_ttl=91861015336
```

Você também pode olhar para as métricas de diagnóstico para a sua cache, para ver se há uma correlação entre quando a chave desapareceu e um pico em chaves expiradas. Consulte o Apêndice de [Debugging Redis Keyspace Misses](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) para obter informações sobre a utilização de notificações do espaço-chave ou **monitor** para depurar este tipo de problemas.

### <a name="key-eviction"></a>Despejo chave

Azure Cache para Redis requer espaço de memória para armazenar dados. Purga as chaves para libertar a memória disponível quando necessário. Quando os valores **used_memory** ou **used_memory_rss** no comando [INFO](https://redis.io/commands/info) se aproximam da definição de **memória máxima** configurada, o Azure Cache para Redis começa a despejar chaves da memória com base na política de [cache](https://redis.io/topics/lru-cache).

Pode monitorizar o número de chaves despejadas utilizando o comando [INFO:](https://redis.io/commands/info)

```
# Stats

evicted_keys:13224
```

Você também pode olhar para as métricas de diagnóstico para a sua cache, para ver se há uma correlação entre quando a chave desapareceu e um pico em chaves despejadas. Consulte o Apêndice de [Debugging Redis Keyspace Misses](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) para obter informações sobre a utilização de notificações do espaço-chave ou **monitor** para depurar este tipo de problemas.

### <a name="key-deletion"></a>Eliminação da chave

Os clientes redis podem emitir o comando [DEL](https://redis.io/commands/del) ou [HDEL](https://redis.io/commands/hdel) para remover explicitamente as chaves do Azure Cache para redis. Pode rastrear o número de operações de eliminação utilizando o comando [INFO.](https://redis.io/commands/info) Se os comandos **DEL** ou **HDEL** tiverem sido `Commandstats` chamados, serão listados na secção.

```
# Commandstats

cmdstat_del:calls=2,usec=90,usec_per_call=45.00

cmdstat_hdel:calls=1,usec=47,usec_per_call=47.00
```

### <a name="async-replication"></a>Replicação asincronizada

Qualquer cache azure para redis no nível Standard ou Premium é configurado com um nó principal e pelo menos uma réplica. Os dados são copiados do mestre para uma réplica assíncrona usando um processo de fundo. O site [redis.io](https://redis.io/topics/replication) descreve como a replicação de dados da Redis funciona em geral. Para cenários em que os clientes escrevem frequentemente à Redis, a perda parcial de dados pode ocorrer porque esta replicação é garantidamente instantânea. Por exemplo, se o mestre cair *depois* de um cliente escrever uma chave para ele, mas *antes* que o processo de fundo tenha a oportunidade de enviar essa chave para a réplica, a chave perde-se quando a réplica assume o cargo de novo mestre.

## <a name="major-or-complete-loss-of-keys"></a>Perda de chaves maior ou completa

Se a maioria ou todas as chaves desapareceram da sua cache, verifique as seguintes causas possíveis:

| Causa | Descrição |
|---|---|
| [Autoclismo de chaves](#key-flushing) | As chaves foram purgadas manualmente. |
| [Seleção de bases de dados incorretas](#incorrect-database-selection) | O Azure Cache for Redis está programado para utilizar uma base de dados não predefinida. |
| [Falha de instância redis](#redis-instance-failure) | O servidor Redis não está disponível. |

### <a name="key-flushing"></a>Autoclismo de chaves

Os clientes podem ligar para o comando [FLUSHDB](https://redis.io/commands/flushdb) para remover todas as teclas numa *única* base de dados ou [FLUSHALL](https://redis.io/commands/flushall) para remover todas as chaves de *todas as* bases de dados numa cache Redis. Para saber se as chaves foram lavadas, utilize o comando [INFO.](https://redis.io/commands/info) A `Commandstats` secção mostra se ou o comando **FLUSH** foi chamado:

```
# Commandstats

cmdstat_flushall:calls=2,usec=112,usec_per_call=56.00

cmdstat_flushdb:calls=1,usec=110,usec_per_call=52.00
```

### <a name="incorrect-database-selection"></a>Seleção de bases de dados incorretas

O Azure Cache for Redis utiliza a base de dados **db0** por padrão. Se mudar para outra base de dados (por exemplo, **db1)** e tentar ler as chaves da mesmas, o Azure Cache para redis não as encontrará lá. Cada base de dados é uma unidade logicamente separada e detém um conjunto de dados diferente. Utilize o comando [SELECT](https://redis.io/commands/select) para utilizar outras bases de dados disponíveis e procure chaves em cada uma delas.

### <a name="redis-instance-failure"></a>Falha de instância redis

Redis é uma loja de dados na memória. Os dados são mantidos nas máquinas físicas ou virtuais que acolhem a cache Redis. Um cache azure para redis no nível Básico funciona apenas numa única máquina virtual (VM). Se o VM estiver em baixo, todos os dados que armazenou na cache estão perdidos. 

Os caches nos níveis Standard e Premium oferecem uma resiliência muito maior contra a perda de dados utilizando dois VMs numa configuração replicada. Quando o nó principal em tal cache falha, o nó de réplica assume-se para servir os dados automaticamente. Estes VMs estão localizados em domínios separados para falhas e atualizações, para minimizar a possibilidade de ambos ficarem indisponíveis simultaneamente. No entanto, se uma grande falha no datacenter ocorrer, os VMs podem ainda cair juntos. Os seus dados perder-se-ão nestes casos raros.

Considere utilizar a persistência de [dados redis](https://redis.io/topics/persistence) e [a geo-replicação](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-geo-replication) para melhorar a proteção dos seus dados contra estas falhas de infraestrutura.

## <a name="additional-information"></a>Informações adicionais

- [Resolver problemas do lado do servidor da Cache do Azure para Redis](cache-troubleshoot-server.md)
- [Que Azure Cache para redis oferecendo e tamanho devo usar?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [Como monitorizar o Azure Cache para redis](cache-how-to-monitor.md)
- [Como posso executar os comandos redis?](cache-faq.md#how-can-i-run-redis-commands)
