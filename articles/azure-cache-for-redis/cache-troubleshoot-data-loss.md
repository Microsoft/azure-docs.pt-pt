---
title: Solucionar problemas do cache do Azure para perda de dados do Redis | Microsoft Docs
description: Saiba como resolver problemas de perda de dados com o cache do Azure para Redis
services: cache
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 10/17/2019
ms.author: yegu
ms.openlocfilehash: 523f4a302eb1f4679eb34bc959efc895fa5408ec
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72821038"
---
# <a name="troubleshoot-azure-cache-for-redis-data-loss"></a>Solucionar problemas do cache do Azure para perda de dados Redis

Esta seção discute como diagnosticar perdas de dados reais ou percebidas que podem ocorrer no cache do Azure para Redis.

- [Perda parcial de chaves](#partial-loss-of-keys)
- [Perda principal ou completa de chaves](#major-or-complete-loss-of-keys)

> [!NOTE]
> Várias das etapas de solução de problemas neste guia incluem instruções para executar comandos do Redis e monitorar várias métricas de desempenho. Para obter mais informações e instruções, consulte os artigos na seção [informações adicionais](#additional-information) .
>

## <a name="partial-loss-of-keys"></a>Perda parcial de chaves

O Redis não exclui as chaves aleatoriamente depois de serem armazenadas na memória. No entanto, ele removerá as chaves, em resposta às políticas de expiração ou remoção, bem como aos comandos de exclusão de chave explícita. Além disso, as chaves que foram gravadas no nó mestre em um cache do Azure Premium ou Standard para Redis podem não estar disponíveis em uma réplica imediatamente. Os dados são replicados do mestre para a réplica de maneira assíncrona e sem bloqueio.

Se você achar que as chaves desapareceram do cache, poderá verificar o seguinte para ver qual pode ser a causa:

| Causa | Descrição |
|---|---|
| [Expiração da chave](#key-expiration) | As chaves são removidas devido aos tempos limite definidos neles |
| [Remoção de chave](#key-eviction) | As chaves são removidas sob pressão de memória |
| [Exclusão de chave](#key-deletion) | As chaves são removidas por comandos delete explícitos |
| [Replicação assíncrona](#async-replication) | As chaves não estão disponíveis em uma réplica devido a atrasos de replicação de dados |

### <a name="key-expiration"></a>Expiração da chave

O Redis removerá uma chave automaticamente se for atribuído um tempo limite e esse período tiver passado. Você pode encontrar mais detalhes sobre a expiração de chave Redis na documentação de comando de [expiração](http://redis.io/commands/expire) . Os valores de tempo limite também podem ser definidos por meio de [set](http://redis.io/commands/set), [setex](https://redis.io/commands/setex), [GETSET](https://redis.io/commands/getset)e outros comandos \*STORE.

Você pode usar o comando [info](http://redis.io/commands/info) para obter estatísticas de quantas chaves expiraram. A seção *estatísticas* mostra o número total de chaves expiradas. A seção *keyspace* fornece informações adicionais sobre o número de chaves com tempos limite e o valor de tempo limite médio.

```
# Stats

expired_keys:46583

# Keyspace

db0:keys=3450,expires=2,avg_ttl=91861015336
```

Além disso, você pode examinar as métricas de diagnóstico do cache para ver se há uma correlação entre quando a chave ficou ausente e um pico nas chaves expiradas. Consulte o [Apêndice](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) para obter informações sobre como usar notificações de keyspace ou monitor para depurar esses tipos de problemas.

### <a name="key-eviction"></a>Remoção de chave

Redis requer espaço de memória para armazenar dados. Ele limpará as chaves para liberar a memória disponível quando necessário. Quando os valores **used_memory** ou **Used_memory_rss** no comando [info](http://redis.io/commands/info) se aproximam da configuração **MaxMemory** definida, Redis começará a remover chaves da memória com base na política de [cache](http://redis.io/topics/lru-cache).

Você pode monitorar o número de chaves removidas usando o comando [info](http://redis.io/commands/info) .

```
# Stats

evicted_keys:13224
```

Além disso, você pode examinar as métricas de diagnóstico do cache para ver se há uma correlação entre quando a chave ficou ausente e um pico nas chaves removidas. Consulte o [Apêndice](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) para obter informações sobre como usar notificações de keyspace ou monitor para depurar esses tipos de problemas.

### <a name="key-deletion"></a>Exclusão de chave

Os clientes do Redis podem emitir o comando [del](http://redis.io/commands/del) ou [HDEL](http://redis.io/commands/hdel) para remover explicitamente as chaves de Redis. Você pode acompanhar o número de operações de exclusão usando o comando [info](http://redis.io/commands/info) . Se os comandos DEL ou HDEL tiverem sido chamados, eles serão listados na seção *Commandstats* .

```
# Commandstats

cmdstat_del:calls=2,usec=90,usec_per_call=45.00

cmdstat_hdel:calls=1,usec=47,usec_per_call=47.00
```

### <a name="async-replication"></a>Replicação assíncrona

Qualquer cache do Azure para Redis na camada Standard ou Premium é configurado com um nó mestre e pelo menos uma réplica. Os dados são copiados do mestre para uma réplica de forma assíncrona usando um processo em segundo plano. O site do [Redis.Io](http://redis.io/topics/replication) descreve como a replicação de dados do Redis funciona em geral. Para cenários em que os clientes estão gravando em Redis frequentemente, a perda de dados parcial pode ocorrer devido ao fato de que essa replicação é garantida para ser instantânea. Por exemplo, se o mestre falhar _depois_ que um cliente gravar uma chave nele, mas _antes_ de o processo em segundo plano ter a oportunidade de enviar essa chave para a réplica, a chave será perdida quando a réplica assumir o controle como o novo mestre.

## <a name="major-or-complete-loss-of-keys"></a>Perda principal ou completa de chaves

Se você achar que a maior parte ou todas as chaves desapareceram do seu cache, poderá verificar o seguinte para ver qual pode ser a causa:

| Causa | Descrição |
|---|---|
| [Liberação de chave](#key-flushing) | As chaves foram limpas manualmente |
| [Seleção de banco de dados incorreta](#incorrect-database-selection) | Redis está definido para usar um banco de dados não padrão |
| [Falha na instância de Redis](#redis-instance-failure) | As chaves são removidas por comandos delete explícitos |

### <a name="key-flushing"></a>Liberação de chave

Os clientes podem chamar o comando [FLUSHDB](http://redis.io/commands/flushdb) para remover todas as chaves em um **único** banco de dados ou [FLUSHALL](http://redis.io/commands/flushall) para remover todas as chaves de **todos os** bancos em um cache Redis. Você pode descobrir se as chaves foram liberadas usando o comando [info](http://redis.io/commands/info) . Ele mostrará se o comando FLUSH foi chamado na seção *Commandstats* .

```
# Commandstats

cmdstat_flushall:calls=2,usec=112,usec_per_call=56.00

cmdstat_flushdb:calls=1,usec=110,usec_per_call=52.00
```

### <a name="incorrect-database-selection"></a>Seleção de banco de dados incorreta

O cache do Azure para Redis usa o banco de dados **db0** por padrão. Se você alternar para outro banco de dados (por exemplo, DB1) e tentar ler chaves a partir dele, o Redis não os encontrará porque cada banco de dados é uma unidade separada logicamente e mantém um conjunto diferente. Use o comando [selecionar](http://redis.io/commands/select) para usar outros bancos de dados disponíveis e procurar chaves em cada um deles.

### <a name="redis-instance-failure"></a>Falha na instância de Redis

Redis é um armazenamento de dados na memória. Os dados são mantidos nas máquinas físicas ou virtuais que hospedam o Redis. Um cache do Azure para instância Redis na camada básica é executado somente em uma única máquina virtual (VM). Se essa VM estiver inativa, todos os dados que você armazenou no cache serão perdidos. Os caches nas camadas Standard e Premium oferecem resiliência muito maior contra perda de dados usando duas VMs em uma configuração replicada. Quando o nó mestre nesse cache falha, o nó da réplica assumirá o fornecimento dos dados automaticamente. Essas VMs estão localizadas em domínios de falha e de atualização separados para minimizar a chance de ambos ficarem indisponíveis simultaneamente. No caso de uma grande interrupção do datacenter, no entanto, as VMs ainda podem ficar juntas. Seus dados serão perdidos nesses casos raros.

Você deve considerar o uso da [persistência de dados Redis](http://redis.io/topics/persistence) e da [replicação geográfica](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-geo-replication) para melhorar a proteção de seus dados contra essas falhas de infraestrutura.

## <a name="additional-information"></a>Informação adicional

- [Solucionar problemas do servidor de cache do Azure para Redis](cache-troubleshoot-server.md)
- [Qual o cache do Azure para oferta e tamanho do Redis devo usar?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [Como monitorar o cache do Azure para Redis](cache-how-to-monitor.md)
- [Como posso executar comandos do Redis?](cache-faq.md#how-can-i-run-redis-commands)
