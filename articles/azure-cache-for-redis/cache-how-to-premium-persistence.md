---
title: Como configurar a persistência de dados para uma Cache do Azure Premium para Redis
description: Saiba como configurar e gerenciar a persistência de dados do escalão Premium Cache do Azure para instâncias de Redis
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: b01cf279-60a0-4711-8c5f-af22d9540d38
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 08/24/2017
ms.author: yegu
ms.openlocfilehash: de0b2e3ef7b0268540ef4896ade132a297ee88ff
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60543507"
---
# <a name="how-to-configure-data-persistence-for-a-premium-azure-cache-for-redis"></a>Como configurar a persistência de dados para uma Cache do Azure Premium para Redis
A Cache de Redis do Azure tem ofertas de cache diferente que fornecem flexibilidade na escolha de tamanho de cache e funcionalidades, incluindo as funcionalidades do escalão Premium, tais como clustering, persistência e suporte de rede virtual. Este artigo descreve como configurar a persistência na versão premium Cache do Azure para a instância de Redis.

Para obter informações sobre outras funcionalidades de cache premium, consulte [introdução à Cache do Azure para o escalão Premium da Redis](cache-premium-tier-intro.md).

## <a name="what-is-data-persistence"></a>O que é a persistência de dados?
[Persistência de redis](https://redis.io/topics/persistence) permite-lhe manter os dados armazenados no Redis. Também pode criar instantâneos e fazer backup dos dados, o que poderá carregar em caso de falha de hardware. Esta é uma enorme vantagem sobre o escalão básico ou Standard, onde todos os dados são armazenados na memória e pode haver potencial perda de dados em caso de falha em que nós de Cache estão indisponíveis. 

A Cache de Redis do Azure oferece a persistência de Redis com os seguintes modelos:

* **Persistência de RDB** -persistência quando RDB (base de dados de Redis) estiver configurada, a Cache de Redis do Azure persistir um instantâneo da Cache do Azure para Redis num formato binário para o disco com base numa frequência de cópia de segurança configurável de Redis. Se um evento catastrófico ocorrer que desativa a cache principal e de réplica, o cache é reconstruído com o instantâneo mais recente. Saiba mais sobre o [vantagens](https://redis.io/topics/persistence#rdb-advantages) e [desvantagens](https://redis.io/topics/persistence#rdb-disadvantages) de persistência de RDB.
* **Persistência do AOF** -persistência quando AOF (ficheiro só de acréscimo) estiver configurada, a Cache de Redis do Azure salva cada operação de escrita num log que está guardado, pelo menos, uma vez por segundo para uma conta de armazenamento do Azure. Se um evento catastrófico ocorrer que desativa a cache principal e de réplica, o cache é reconstruído com as operações de escrita armazenado. Saiba mais sobre o [vantagens](https://redis.io/topics/persistence#aof-advantages) e [desvantagens](https://redis.io/topics/persistence#aof-disadvantages) de persistência do AOF.

Persistência é configurada do **novo Azure Cache de Redis** painel durante a criação da cache e, no **menu recursos** Premium existentes coloca em cache.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Depois de um escalão de preço premium é selecionado, clique em **persistência de Redis**.

![Persistência de redis][redis-cache-persistence]

Os passos na secção seguinte descrevem como configurar a persistência de Redis a nova cache premium. Quando a persistência de Redis estiver configurada, clique em **criar** para criar a nova cache premium com persistência de Redis.

## <a name="enable-redis-persistence"></a>Ativar a persistência de Redis

Redis persistência está ativada no **persistência de dados de Redis** painel escolhendo um **RDB** ou **AOF** persistência. Para as novas caches, este painel é acessado durante o processo de criação de cache, conforme descrito na secção anterior. Para as caches já existentes, o **persistência de dados de Redis** painel é acessado a partir do **menu recursos** para a sua cache.

![Definições de redis][redis-cache-settings]


## <a name="configure-rdb-persistence"></a>Configurar a persistência de RDB

Para ativar a persistência de RDB, clique em **RDB**. Para desativar a persistência de RDB numa cache premium anteriormente ativado, clique em **desativado**.

![RDB persistência de redis][redis-cache-rdb-persistence]

Para configurar o intervalo de cópia de segurança, selecione um **frequência de cópia de segurança** na lista pendente. As opções são **15 minutos**, **30 minutos**, **60 minutos**, **6 horas**, **12 horas**e **24 horas**. Este intervalo começa a contagem para baixo depois da operação de cópia de segurança anterior seja concluída com êxito e quando ele decorrida uma nova cópia de segurança é iniciada.

Clique em **conta de armazenamento** para selecionar a conta de armazenamento a utilizar e escolha o o **chave primária** ou **chave secundária** para utilizar a partir do **achavedearmazenamento** lista pendente. Tem de escolher uma conta de armazenamento na mesma região que a cache e um **o armazenamento Premium** conta é recomendada porque o armazenamento premium tem um débito mais elevado. 

> [!IMPORTANT]
> Se a chave de armazenamento para a sua conta de persistência é regenerada, tem de reconfigurar a chave pretendida a **chave de armazenamento** pendente.
> 
> 

Clique em **OK** para guardar a configuração de persistência.

A próxima cópia de segurança (ou a primeira cópia de segurança para as novas caches) é iniciada depois de decorrido o intervalo de frequência de cópia de segurança.

## <a name="configure-aof-persistence"></a>Configurar a persistência de AOF

Para ativar a persistência de AOF, clique em **AOF**. Para desativar a persistência de AOF numa cache premium anteriormente ativado, clique em **desativado**.

![AOF persistência de redis][redis-cache-aof-persistence]

Para configurar a persistência de AOF, especifique um **primeira conta de armazenamento**. Esta conta de armazenamento tem de ser na mesma região que a cache e um **o armazenamento Premium** conta é recomendada porque o armazenamento premium tem um débito mais elevado. Opcionalmente, pode configurar uma conta de armazenamento adicional denominada **segunda conta de armazenamento**. Se estiver configurada uma segunda conta de armazenamento, as gravações para a cache de réplica são escritas para esta segunda conta de armazenamento. Para cada conta de armazenamento configurados, escolha o **chave primária** ou **chave secundária** para utilizar a partir do **chave de armazenamento** pendente. 

> [!IMPORTANT]
> Se a chave de armazenamento para a sua conta de persistência é regenerada, tem de reconfigurar a chave pretendida a **chave de armazenamento** pendente.
> 
> 

Quando a persistência de AOF está ativada, escreva operações para a cache são guardadas para a conta de armazenamento designada (ou contas, se tiver configurado uma segunda conta de armazenamento). Se ocorrer uma falha catastrófica, que desativa a cache principal e de réplica, o registo AOF armazenado é utilizado para recriar a cache.

## <a name="persistence-faq"></a>FAQ de persistência
A lista seguinte contém respostas para perguntas freqüentes sobre a Cache do Azure para a persistência de Redis.

* [Pode ativar persistência numa cache criada anteriormente?](#can-i-enable-persistence-on-a-previously-created-cache)
* [Pode ativar persistência AOF e RDB ao mesmo tempo?](#can-i-enable-aof-and-rdb-persistence-at-the-same-time)
* [Qual modelo de persistência devo escolher?](#which-persistence-model-should-i-choose)
* [O que acontece se eu tiver dimensionado para um tamanho diferente e é restaurar uma cópia de segurança que foi feita antes da operação de dimensionamento?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)


### <a name="rdb-persistence"></a>Persistência de RDB
* [Posso alterar a frequência de cópia de segurança de RDB depois de criar a cache?](#can-i-change-the-rdb-backup-frequency-after-i-create-the-cache)
* [Por que se eu tiver uma frequência de cópia de segurança de RDB de 60 minutos há mais de 60 minutos entre as cópias de segurança?](#why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups)
* [O que acontece com as cópias de segurança RDB antigas quando é feita uma nova cópia de segurança?](#what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made)

### <a name="aof-persistence"></a>Persistência do AOF
* [Quando devo utilizar uma segunda conta de armazenamento?](#when-should-i-use-a-second-storage-account)
* [É o efeito de persistência do AOF ao longo, latência ou desempenho de minha cache?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)
* [Como posso remover a segunda conta de armazenamento?](#how-can-i-remove-the-second-storage-account)
* [O que é uma reescrita e como ele afeta a minha cache?](#what-is-a-rewrite-and-how-does-it-affect-my-cache)
* [O que posso esperar quando a escala de uma cache com AOF ativado?](#what-should-i-expect-when-scaling-a-cache-with-aof-enabled)
* [Como os meus dados AOF são organizados no armazenamento?](#how-is-my-aof-data-organized-in-storage)


### <a name="can-i-enable-persistence-on-a-previously-created-cache"></a>Pode ativar persistência numa cache criada anteriormente?
Sim, a persistência de Redis pode ser configurada durante a criação de cache e nos caches de premium já existentes.

### <a name="can-i-enable-aof-and-rdb-persistence-at-the-same-time"></a>Pode ativar persistência AOF e RDB ao mesmo tempo?

Não, pode habilitar apenas RDB ou AOF, mas não ambos simultaneamente.

### <a name="which-persistence-model-should-i-choose"></a>Qual modelo de persistência devo escolher?

Persistência do AOF salva cada gravação num log, que terá algum impacto no débito, em comparação com a persistência de RDB que salva cópias de segurança com base no intervalo de cópia de segurança configurado com um impacto mínimo no desempenho. Escolha a persistência de AOF se seu principal objetivo é minimizar a perda de dados, e pode manipular uma queda na taxa de transferência para a sua cache. Escolha a persistência de RDB se pretender manter o débito ideal em seu cache, mas ainda pretendem um mecanismo de recuperação de dados.

* Saiba mais sobre o [vantagens](https://redis.io/topics/persistence#rdb-advantages) e [desvantagens](https://redis.io/topics/persistence#rdb-disadvantages) de persistência de RDB.
* Saiba mais sobre o [vantagens](https://redis.io/topics/persistence#aof-advantages) e [desvantagens](https://redis.io/topics/persistence#aof-disadvantages) de persistência do AOF.

Para obter mais informações sobre o desempenho ao utilizar a persistência de AOF, consulte [efeito de persistência do AOF faz ao longo, latência ou desempenho de minha cache?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)

### <a name="what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation"></a>O que acontece se eu tiver dimensionado para um tamanho diferente e é restaurar uma cópia de segurança que foi feita antes da operação de dimensionamento?

Para a persistência de RDB e AOF:

* Se tiver dimensionado para um tamanho superior, não é afetado.
* Se tiver dimensionado para um tamanho mais pequeno e tem um personalizado [bases de dados](cache-configure.md#databases) definição que for maior do que o [limite de bases de dados](cache-configure.md#databases) para o novo tamanho, dados essas bases de dados não são restaurados. Para obter mais informações, consulte [é minha definição afetados durante o dimensionamento de bancos de dados personalizados?](cache-how-to-scale.md#is-my-custom-databases-setting-affected-during-scaling)
* Se tiver dimensionado para um tamanho mais pequeno, e não existir espaço suficiente no tamanho mais pequeno para conter todos os dados da última cópia de segurança, serão expulso chaves durante o processo de restauro, normalmente, a utilização a [allkeys lru](https://redis.io/topics/lru-cache) política de expulsão.

### <a name="can-i-change-the-rdb-backup-frequency-after-i-create-the-cache"></a>Posso alterar a frequência de cópia de segurança de RDB depois de criar a cache?
Sim, pode alterar a frequência de cópia de segurança para a persistência de RDB no **persistência de dados de Redis** painel. Para obter instruções, consulte Configurar Redis persistência.

### <a name="why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups"></a>Por que se eu tiver uma frequência de cópia de segurança de RDB de 60 minutos há mais de 60 minutos entre as cópias de segurança?
O intervalo de frequência de cópia de segurança de persistência de RDB não inicia até que o processo de cópia de segurança anterior seja concluída com êxito. Se a frequência de cópia de segurança é 60 minutos e demora um processo de cópia de segurança 15 minutos para concluir com êxito, a próxima cópia de segurança não inicia até 75 minutos após a hora de início da cópia de segurança anterior.

### <a name="what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made"></a>O que acontece com as cópias de segurança RDB antigas quando é feita uma nova cópia de segurança?
Todos os backups de persistência de RDB, exceto daquele mais recente são automaticamente eliminados. Esta exclusão pode não ocorrer imediatamente, mas as cópias de segurança mais antigas não são mantidas indefinidamente.


### <a name="when-should-i-use-a-second-storage-account"></a>Quando devo utilizar uma segunda conta de armazenamento?

Deve utilizar uma segunda conta de armazenamento para a persistência de AOF quando considerar que a tiver superior do que as operações de conjunto esperado na cache.  Como configurar a conta de armazenamento secundário ajuda a garantir a que sua cache não atinge os limites de largura de banda de armazenamento.

### <a name="does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache"></a>É o efeito de persistência do AOF ao longo, latência ou desempenho de minha cache?

Persistência do AOF afetará débito por aproximadamente 15% – 20% quando o cache é inferior carga máxima (CPU e o servidor carregar em 90%). Deve não haver problemas de latência quando o cache é dentro destes limites. No entanto, a cache será contactado estes limites mais cedo com AOF ativado.

### <a name="how-can-i-remove-the-second-storage-account"></a>Como posso remover a segunda conta de armazenamento?

Pode remover a conta de armazenamento secundário da persistência de AOF definindo a segunda conta de armazenamento para ser o mesmo que a primeira conta de armazenamento. Para obter instruções, consulte [persistência configurar AOF](#configure-aof-persistence).

### <a name="what-is-a-rewrite-and-how-does-it-affect-my-cache"></a>O que é uma reescrita e como ele afeta a minha cache?

Quando o ficheiro AOF for grande o suficiente, uma reescrita automaticamente em fila na cache. A reescrita redimensiona o ficheiro AOF com o conjunto mínimo de operações necessárias para criar o conjunto de dados atual. Durante a reescritas, espere para atingir os limites de desempenho mais cedo especialmente ao lidar com grandes conjuntos de dados. Reescritas ocorrerem menor, muitas vezes, como o ficheiro AOF se torna maior, mas irá demorar uma quantidade significativa de tempo quando isso ocorre.

### <a name="what-should-i-expect-when-scaling-a-cache-with-aof-enabled"></a>O que posso esperar quando a escala de uma cache com AOF ativado?

Se o ficheiro AOF no momento do dimensionamento é significativamente grande, em seguida, esperar que a operação de dimensionamento demorar mais tempo do que o esperado, uma vez que ele irá ser recarregar o ficheiro depois de dimensionamento foi concluída.

Para obter mais informações sobre o dimensionamento, veja [o que acontece se eu tiver dimensionado para um tamanho diferente e é restaurar uma cópia de segurança que foi feita antes da operação de dimensionamento?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)

### <a name="how-is-my-aof-data-organized-in-storage"></a>Como os meus dados AOF são organizados no armazenamento?

Dados armazenados em ficheiros do AOF estão divididos em vários blobs de páginas por nó para melhorar o desempenho de salvar os dados para o armazenamento. A tabela seguinte apresenta o número de blobs de páginas são utilizados para cada escalão de preço:

| Escalão Premium | Blobs |
|--------------|-------|
| P1           | 4 por shard    |
| P2           | 8 por shard    |
| P3           | 16 por shard   |
| P4           | 20 por shard   |

Quando o clustering é ativado, cada partição horizontal na cache tem seu próprio conjunto de blobs de páginas, conforme indicado na tabela anterior. Por exemplo, uma cache de P2 com partições três horizontais distribui o seu arquivo AOF 24 blobs de páginas (8 blobs por partição horizontal, com partições horizontais de 3).

Após uma reescrita, dois conjuntos de ficheiros do AOF existem no armazenamento. Reescritas ocorrerem em segundo plano e de acréscimo para o primeiro conjunto de arquivos, enquanto as operações de conjunto que são enviadas para a cache durante a reescrita acrescentar para o segundo conjunto. Uma cópia de segurança é armazenada temporariamente durante a reescritas em caso de falha, mas é eliminada imediatamente após a conclusão de uma reescrita.


## <a name="next-steps"></a>Passos Seguintes
Saiba como utilizar as funcionalidades de cache premium mais.

* [Introdução à Cache do Azure para o escalão Premium da Redis](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-rdb-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-rdb-persistence.png

[redis-cache-aof-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-aof-persistence.png

[redis-cache-settings]: ./media/cache-how-to-premium-persistence/redis-cache-settings.png
