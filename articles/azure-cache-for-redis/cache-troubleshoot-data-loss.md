---
title: Resolver problemas de perda de dados da Cache do Azure para Redis
description: Saiba como resolver problemas de perda de dados com a Azure Cache para Redis, como perda parcial de chaves, expiração da chave ou perda total de chaves.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: 6db036752bab7b84b72a37b148eaec7aa5765ef3
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92538600"
---
# <a name="troubleshoot-data-loss-in-azure-cache-for-redis"></a>Resolver problemas de perda de dados da Cache do Azure para Redis

Este artigo discute como diagnosticar perdas de dados reais ou percebidas que possam ocorrer em Azure Cache para Redis.

> [!NOTE]
> Várias das etapas de resolução de problemas neste guia incluem instruções para executar comandos Redis e monitorizar várias métricas de desempenho. Para obter mais informações e instruções, consulte os artigos na secção [informações adicionais.](#additional-information)
>

## <a name="partial-loss-of-keys"></a>Perda parcial de chaves

O Azure Cache para Redis não apaga as chaves aleatoriamente depois de terem sido guardadas na memória. No entanto, remove as chaves em resposta às políticas de caducidade ou de despejo e aos comandos explícitos de eliminação de chaves. As teclas que tenham sido escritas para o nó primário num Cache Premium ou Standard Azure para redis também podem não estar disponíveis numa réplica imediatamente. Os dados são replicados desde o primário até à réplica de forma assíncronea e não bloqueada.

Se descobrir que as chaves desapareceram do seu cache, verifique as seguintes causas possíveis:

| Causa | Descrição |
|---|---|
| [Expiração da chave](#key-expiration) | As chaves são removidas devido aos intervalos de tempo definidos. |
| [Despejo chave](#key-eviction) | As teclas são removidas sob pressão de memória. |
| [Eliminação de chaves](#key-deletion) | As teclas são removidas por comandos de eliminação explícitos. |
| [Replicação assínc](#async-replication) | As chaves não estão disponíveis numa réplica devido a atrasos na replicação de dados. |

### <a name="key-expiration"></a>Expiração da chave

A azure Cache para Redis remove automaticamente uma chave se a chave for atribuída a um intervalo e esse período tiver passado. Para obter mais informações sobre a expiração da chave Redis, consulte a documentação do comando [EXPIR.](https://redis.io/commands/expire) Os valores de intervalo também podem ser definidos utilizando os [comandos SET](https://redis.io/commands/set), [SETEX,](https://redis.io/commands/setex) [GETSET](https://redis.io/commands/getset)e outros comandos **\* STORE.**

Para obter estatísticas sobre quantas teclas já expiraram, utilize o comando [INFO.](https://redis.io/commands/info) A `Stats` secção mostra o número total de chaves caducadas. A `Keyspace` secção fornece mais informações sobre o número de chaves com intervalos de tempo e o valor médio de tempo de desconto.

```
# Stats

expired_keys:46583

# Keyspace

db0:keys=3450,expires=2,avg_ttl=91861015336
```

Também pode olhar para as métricas de diagnóstico do seu cache, para ver se há uma correlação entre quando a chave desapareceu e um pico em chaves expiradas. Consulte o apêndice de [Debugging Redis Keyspace Misses](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) para obter informações sobre a utilização de notificações de keyspace ou **MONITOR** para depurar este tipo de problemas.

### <a name="key-eviction"></a>Despejo chave

A azure Cache para Redis requer espaço de memória para armazenar dados. Limpa as chaves para libertar a memória disponível quando necessário. Quando os valores **used_memory** ou **used_memory_rss** no comando [INFO](https://redis.io/commands/info) se aproximam da configuração **maxmemory** configurada, a Cache Azure para Redis começa a despejar as teclas da memória com base na [política de cache](https://redis.io/topics/lru-cache).

Pode monitorizar o número de chaves despejadas utilizando o comando [INFO:](https://redis.io/commands/info)

```
# Stats

evicted_keys:13224
```

Também pode olhar para as métricas de diagnóstico do seu cache, para ver se há uma correlação entre quando a chave desapareceu e um pico em chaves despejadas. Consulte o apêndice de [Debugging Redis Keyspace Misses](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) para obter informações sobre a utilização de notificações de keyspace ou **MONITOR** para depurar este tipo de problemas.

### <a name="key-deletion"></a>Eliminação de chaves

Os clientes Redis podem emitir o comando [DEL](https://redis.io/commands/del) ou [HDEL](https://redis.io/commands/hdel) para remover explicitamente as chaves da Cache Azure para Redis. Pode rastrear o número de operações de eliminação utilizando o comando [INFO.](https://redis.io/commands/info) Se os comandos **DEL** ou **HDEL** tiverem sido chamados, serão listados na `Commandstats` secção.

```
# Commandstats

cmdstat_del:calls=2,usec=90,usec_per_call=45.00

cmdstat_hdel:calls=1,usec=47,usec_per_call=47.00
```

### <a name="async-replication"></a>Replicação assínc

Qualquer cache Azure para a instância Redis no nível Standard ou Premium está configurado com um nó primário e pelo menos uma réplica. Os dados são copiados do primário para uma réplica assíncronea usando um processo de fundo. O [site redis.io](https://redis.io/topics/replication) descreve como a replicação de dados do Redis funciona em geral. Para cenários em que os clientes escrevem ao Redis com frequência, a perda parcial de dados pode ocorrer porque esta replicação não é garantida como instantânea. Por exemplo, se o primário cair *depois de* um cliente escrever uma chave para ele, mas *antes* que o processo de fundo tenha a oportunidade de enviar essa chave para a réplica, a chave perde-se quando a réplica assume o cargo de nova primária.

## <a name="major-or-complete-loss-of-keys"></a>Perda total ou completa de chaves

Se a maioria ou todas as chaves desaparecerem do seu cache, verifique as seguintes causas possíveis:

| Causa | Descrição |
|---|---|
| [Lavagem de chaves](#key-flushing) | As chaves foram purgadas manualmente. |
| [Seleção de bases de dados incorreta](#incorrect-database-selection) | O Azure Cache para Redis está programado para utilizar uma base de dados não padrão. |
| [Falha de exemplo de Redis](#redis-instance-failure) | O servidor Redis não está disponível. |

### <a name="key-flushing"></a>Lavagem de chaves

Os clientes podem ligar para o comando [FLUSHDB](https://redis.io/commands/flushdb) para remover todas as chaves de uma *única* base de dados ou [FLUSHALL](https://redis.io/commands/flushall) para remover todas as chaves de *todas as* bases de dados de uma cache Redis. Para saber se as chaves foram lavadas, utilize o comando [INFO.](https://redis.io/commands/info) A `Commandstats` secção mostra se o comando **FLUSH** foi chamado:

```
# Commandstats

cmdstat_flushall:calls=2,usec=112,usec_per_call=56.00

cmdstat_flushdb:calls=1,usec=110,usec_per_call=52.00
```

### <a name="incorrect-database-selection"></a>Seleção de bases de dados incorreta

A azure Cache para Redis utiliza a base de **dados db0** por predefinição. Se mudar para outra base de dados (por exemplo, **db1** ) e tentar ler as chaves, a Azure Cache para Redis não as encontrará lá. Cada base de dados é uma unidade logicamente separada e contém um conjunto de dados diferente. Utilize o comando [SELECT](https://redis.io/commands/select) para utilizar outras bases de dados disponíveis e procure chaves em cada uma delas.

### <a name="redis-instance-failure"></a>Falha de exemplo de Redis

Redis é uma loja de dados na memória. Os dados são mantidos nas máquinas físicas ou virtuais que acolhem a cache Redis. Uma cache Azure para a instância Redis no nível básico funciona apenas numa única máquina virtual (VM). Se o VM estiver em baixo, todos os dados que armazenaste na cache estão perdidos. 

Caches nos níveis Standard e Premium oferecem uma resiliência muito maior contra a perda de dados usando dois VMs numa configuração replicada. Quando o nó primário em tal cache falha, o nó réplica assume-se para servir automaticamente os dados. Estes VMs estão localizados em domínios separados para falhas e atualizações, para minimizar a possibilidade de ambos ficarem indisponíveis simultaneamente. No entanto, se uma grande interrupção do datacenter acontecer, os VMs ainda podem descer juntos. Os seus dados perder-se-ão nestes casos raros.

Considere usar a persistência de [dados redis](https://redis.io/topics/persistence) e [a geo-replicação](./cache-how-to-geo-replication.md) para melhorar a proteção dos seus dados contra estas falhas de infraestrutura.

## <a name="additional-information"></a>Informações adicionais

- [Resolver problemas do lado do servidor da Cache do Azure para Redis](cache-troubleshoot-server.md)
- [Escolher o escalão certo](cache-overview.md#choosing-the-right-tier)
- [Como monitorizar a Cache do Azure para Redis](cache-how-to-monitor.md)
- [Como posso comandar o Redis?](cache-development-faq.md#how-can-i-run-redis-commands)