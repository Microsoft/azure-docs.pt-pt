---
title: Configure persistência de dados - Premium Azure Cache for Redis
description: Saiba como configurar e gerir a persistência de dados do seu Premium Tier Azure Cache para os casos Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/24/2017
ms.openlocfilehash: 84a5b4784a36fb22ae50a7a1ec4fcb7e5ef5b7c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80245281"
---
# <a name="how-to-configure-data-persistence-for-a-premium-azure-cache-for-redis"></a>Como configurar a persistência de dados para um Premium Azure Cache para Redis
O Azure Cache for Redis tem diferentes ofertas de cache que proporcionam flexibilidade na escolha do tamanho e funcionalidades do cache, incluindo funcionalidades de nível Premium, como clustering, persistência e suporte de rede virtual. Este artigo descreve como configurar a persistência num Azure Cache premium para o caso Redis.

Para obter informações sobre outras funcionalidades de cache premium, consulte [Introdução ao Cache Azure para o nível Redis Premium](cache-premium-tier-intro.md).

## <a name="what-is-data-persistence"></a>O que é a persistência de dados?
A persistência do [Redis](https://redis.io/topics/persistence) permite-lhe persistir os dados armazenados em Redis. Também pode tirar fotografias e fazer o backup dos dados, que pode carregar em caso de falha de hardware. Esta é uma enorme vantagem sobre o nível Basic ou Standard onde todos os dados são armazenados na memória e pode haver perda de dados potencial em caso de falha em que os nós cache estão em baixo. 

O Azure Cache for Redis oferece persistência redis utilizando os seguintes modelos:

* **Persistência RDB** - Quando a persistência do RDB (base de dados Redis) é configurada, o Azure Cache for Redis persiste uma imagem do Azure Cache for Redis num formato binário Redis para o disco com base numa frequência de backup configurável. Se ocorrer um evento catastrófico que desativa a cache primária e a réplica, a cache é reconstruída utilizando o instantâneo mais recente. Saiba mais sobre as vantagens e [desvantagens](https://redis.io/topics/persistence#rdb-disadvantages) da persistência do RDB. [advantages](https://redis.io/topics/persistence#rdb-advantages)
* **Persistência AOF** - Quando a persistência da AOF (apêndice apenas ficheiro) é configurada, o Azure Cache para Redis guarda todas as operações de escrita para um registo que é guardado pelo menos uma vez por segundo numa conta de Armazenamento Azure. Se ocorrer um evento catastrófico que desativa a cache primária e a réplica, a cache é reconstruída utilizando as operações de escrita armazenadas. Saiba mais sobre as vantagens e [desvantagens](https://redis.io/topics/persistence#aof-disadvantages) da persistência da AOF. [advantages](https://redis.io/topics/persistence#aof-advantages)

A persistência escreve os dados da Redis numa conta de Armazenamento Azure que possui e gere. Pode configurar a partir do **Novo Azure Cache para** lâmina Redis durante a criação de cache e no menu **Recurso** para caches premium existentes.

> [!NOTE]
> 
> O Azure Storage encripta automaticamente os dados quando é persistido. Podes usar as tuas próprias chaves para a encriptação. Para mais informações, consulte [as chaves geridas pelo Cliente com](/azure/storage/common/storage-service-encryption)o Cofre de Chaves Azure .
> 
> 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Uma vez selecionado um nível de preços premium, clique na **persistência do Redis**.

![Persistência redis][redis-cache-persistence]

Os passos na secção seguinte descrevem como configurar a persistência do Redis na sua nova cache premium. Assim que a persistência do Redis estiver configurada, clique em **Criar** para criar a sua nova cache premium com persistência redis.

## <a name="enable-redis-persistence"></a>Permitir a persistência dos Redis

A persistência do redis é ativada na lâmina de persistência de **Dados,** escolhendo a persistência de **RDB** ou **AOF.** Para novos caches, esta lâmina é acedida durante o processo de criação de cache, como descrito na secção anterior. Para caches existentes, a lâmina de **persistência de Dados** é acedida a partir do menu **Recurso** para a sua cache.

![Definições de redis][redis-cache-settings]


## <a name="configure-rdb-persistence"></a>Configure a persistência do RDB

Para ativar a persistência do RDB, clique em **RDB**. Para desativar a persistência do RDB numa cache premium previamente ativada, clique em **Desativar**.

![Persistência redis RDB][redis-cache-rdb-persistence]

Para configurar o intervalo de cópia de segurança, selecione uma Frequência de **Backup** da lista de drop-down. As escolhas incluem **15 Minutos,** **30 minutos,** **60 minutos,** **6 horas,** **12 horas**e **24 horas.** Este intervalo começa a contar após a operação de backup anterior concluída com sucesso e quando decorrido uma nova cópia de segurança é iniciada.

Clique na **Conta de Armazenamento** para selecionar a conta de armazenamento para utilizar e escolha a chave **primária** ou a **chave secundária** para utilizar a partir da chave de **armazenamento.** Você deve escolher uma conta de armazenamento na mesma região que a cache, e uma conta **de Armazenamento Premium** é recomendada porque o armazenamento premium tem uma maior entrada. 

> [!IMPORTANT]
> Se a chave de armazenamento para a sua conta de persistência for regenerada, deve reconfigurar a chave desejada a partir da queda da chave de **armazenamento.**
> 
> 

Clique **em OK** para salvar a configuração de persistência.

A próxima cópia de segurança (ou primeira cópia de segurança para novos caches) é iniciada assim que o intervalo de frequência de reserva decorrido.

## <a name="configure-aof-persistence"></a>Configurar a persistência da AOF

Para permitir a persistência da AOF, clique em **AOF**. Para desativar a persistência da AOF numa cache premium previamente ativada, clique em **Desativar**.

![Persistência redis AOF][redis-cache-aof-persistence]

Para configurar a persistência da AOF, especifique uma **Primeira Conta de Armazenamento**. Esta conta de armazenamento deve estar na mesma região que a cache, e uma conta **de Armazenamento Premium** é recomendada porque o armazenamento premium tem uma maior entrada. Pode configurar opcionalmente uma conta de armazenamento adicional chamada **Segunda Conta de Armazenamento.** Se uma segunda conta de armazenamento estiver configurada, as escritas para a cache de réplica são escritas nesta segunda conta de armazenamento. Para cada conta de armazenamento configurada, escolha a **chave primária** ou a **chave secundária** para utilizar a partir da chave de **armazenamento.** 

> [!IMPORTANT]
> Se a chave de armazenamento para a sua conta de persistência for regenerada, deve reconfigurar a chave desejada a partir da queda da chave de **armazenamento.**
> 
> 

Quando a persistência da AOF estiver ativada, as operações de escrita na cache são guardadas na conta de armazenamento designada (ou contas se tiver configurado uma segunda conta de armazenamento). Em caso de falha catastrófica que derrube a cache primária e réplica, o registo AOF armazenado é usado para reconstruir o cache.

## <a name="persistence-faq"></a>Persistência FAQ
A lista que se segue contém respostas a perguntas comumente feitas sobre a persistência de Azure Cache para redis.

* [Posso permitir a persistência numa cache previamente criada?](#can-i-enable-persistence-on-a-previously-created-cache)
* [Posso permitir a persistência da AOF e do RDB ao mesmo tempo?](#can-i-enable-aof-and-rdb-persistence-at-the-same-time)
* [Que modelo de persistência devo escolher?](#which-persistence-model-should-i-choose)
* [O que acontece se eu tiver escalado para um tamanho diferente e um reforço for restaurado que foi feito antes da operação de escala?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)


### <a name="rdb-persistence"></a>Persistência do RDB
* [Posso mudar a frequência de cópia de segurança RDB depois de criar a cache?](#can-i-change-the-rdb-backup-frequency-after-i-create-the-cache)
* [Por que se eu tiver uma frequência de reserva RDB de 60 minutos há mais de 60 minutos entre cópias de segurança?](#why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups)
* [O que acontece com os antigos reforços do RDB quando um novo reforço é feito?](#what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made)

### <a name="aof-persistence"></a>Persistência da AOF
* [Quando devo usar uma segunda conta de armazenamento?](#when-should-i-use-a-second-storage-account)
* [A persistência da AOF afeta toda a latência ou desempenho da minha cache?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)
* [Como posso remover a segunda conta de armazenamento?](#how-can-i-remove-the-second-storage-account)
* [O que é uma reescrita e como isso afeta a minha cache?](#what-is-a-rewrite-and-how-does-it-affect-my-cache)
* [O que devo esperar quando escalonar uma cache com AOF ativada?](#what-should-i-expect-when-scaling-a-cache-with-aof-enabled)
* [Como é que os meus dados da AOF estão organizados no armazenamento?](#how-is-my-aof-data-organized-in-storage)


### <a name="can-i-enable-persistence-on-a-previously-created-cache"></a>Posso permitir a persistência numa cache previamente criada?
Sim, a persistência do Redis pode ser configurada tanto na criação de cache como em caches premium existentes.

### <a name="can-i-enable-aof-and-rdb-persistence-at-the-same-time"></a>Posso permitir a persistência da AOF e do RDB ao mesmo tempo?

Não, só pode ativar RDB ou AOF, mas não ambos ao mesmo tempo.

### <a name="which-persistence-model-should-i-choose"></a>Que modelo de persistência devo escolher?

A persistência da AOF poupa cada escrita para um registo, que tem algum impacto na entrada, em comparação com a persistência do RDB que poupa backups com base no intervalo de backup configurado, com o mínimo impacto no desempenho. Escolha a persistência da AOF se o seu principal objetivo é minimizar a perda de dados e pode lidar com uma diminuição da entrada para a sua cache. Escolha a persistência do RDB se pretender manter a melhor entrada na sua cache, mas ainda assim quer um mecanismo de recuperação de dados.

* Saiba mais sobre as vantagens e [desvantagens](https://redis.io/topics/persistence#rdb-disadvantages) da persistência do RDB. [advantages](https://redis.io/topics/persistence#rdb-advantages)
* Saiba mais sobre as vantagens e [desvantagens](https://redis.io/topics/persistence#aof-disadvantages) da persistência da AOF. [advantages](https://redis.io/topics/persistence#aof-advantages)

Para obter mais informações sobre o desempenho ao utilizar a persistência da AOF, ver [AOF persistência afeta toda a latência ou desempenho da minha cache?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)

### <a name="what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation"></a>O que acontece se eu tiver escalado para um tamanho diferente e um reforço for restaurado que foi feito antes da operação de escala?

Tanto para a persistência de RDB como AOF:

* Se escalou para um tamanho maior, não há impacto.
* Se tiver escalado para um tamanho menor, e tiver uma definição de bases de [dados](cache-configure.md#databases) personalizada que é maior do que o limite de bases de [dados](cache-configure.md#databases) para o seu novo tamanho, os dados nessas bases de dados não são restaurados. Para mais informações, consulte a definição de bases de [dados personalizadas durante a escala?](cache-how-to-scale.md#is-my-custom-databases-setting-affected-during-scaling)
* Se tiver escalado para um tamanho menor, e não houver espaço suficiente no tamanho mais pequeno para reter todos os dados da última cópia de segurança, as chaves serão despejadas durante o processo de restauro, normalmente usando a política de despejo [allkeys-lru.](https://redis.io/topics/lru-cache)

### <a name="can-i-change-the-rdb-backup-frequency-after-i-create-the-cache"></a>Posso mudar a frequência de cópia de segurança RDB depois de criar a cache?
Sim, pode alterar a frequência de backup para a persistência de RDB na lâmina de **persistência de Dados.** Para obter instruções, consulte a persistência do Configure Redis.

### <a name="why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups"></a>Por que se eu tiver uma frequência de reserva RDB de 60 minutos há mais de 60 minutos entre cópias de segurança?
O intervalo de frequência de backup de persistência RDB só começa quando o processo de backup anterior tiver concluído com sucesso. Se a frequência de reserva for de 60 minutos e demorar um processo de backup de 15 minutos para ser concluída com sucesso, a próxima cópia de segurança só começará 75 minutos após a hora de início da cópia de segurança anterior.

### <a name="what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made"></a>O que acontece com os antigos reforços do RDB quando um novo reforço é feito?
Todas as cópias de segurança de persistência RDB, com exceção das mais recentes, são automaticamente eliminadas. Esta eliminação pode não acontecer imediatamente, mas os backups mais antigos não são indeterminados indefinidamente.


### <a name="when-should-i-use-a-second-storage-account"></a>Quando devo usar uma segunda conta de armazenamento?

Deve utilizar uma segunda conta de armazenamento para persistência da AOF quando acredita que tem operações mais altas do que o esperado na cache.  A criação da conta de armazenamento secundário ajuda a garantir que a sua cache não atinge os limites de largura de banda de armazenamento.

### <a name="does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache"></a>A persistência da AOF afeta toda a latência ou desempenho da minha cache?

A persistência da AOF afeta a entrada em cerca de 15% – 20% quando a cache está abaixo da carga máxima (CPU e Server Load ambos abaixo de 90%). Não deve haver problemas de latência quando a cache está dentro destes limites. No entanto, a cache atingirá estes limites mais cedo com a AOF ativada.

### <a name="how-can-i-remove-the-second-storage-account"></a>Como posso remover a segunda conta de armazenamento?

Pode remover a conta de armazenamento secundário de persistência da AOF, definindo a segunda conta de armazenamento como a mesma que a primeira conta de armazenamento. Para obter instruções, consulte a [persistência da Configuração AOF](#configure-aof-persistence).

### <a name="what-is-a-rewrite-and-how-does-it-affect-my-cache"></a>O que é uma reescrita e como isso afeta a minha cache?

Quando o ficheiro AOF se torna suficientemente grande, uma reescrita é automaticamente colocada na cache. A reescrita redimensiona o ficheiro AOF com o conjunto mínimo de operações necessárias para criar o conjunto de dados atual. Durante as reescritas, espere atingir os limites de desempenho mais cedo, especialmente quando lidar com grandes conjuntos de dados. As reescritas ocorrem menos frequentemente à medida que o ficheiro AOF se torna maior, mas levará uma quantidade significativa de tempo quando isso acontece.

### <a name="what-should-i-expect-when-scaling-a-cache-with-aof-enabled"></a>O que devo esperar quando escalonar uma cache com AOF ativada?

Se o ficheiro AOF no momento da escala for significativamente grande, então espere que a operação de escala demore mais tempo do que o esperado, uma vez que será recarregado o ficheiro depois de a escala ter terminado.

Para mais informações sobre escala, veja [o que acontece se eu tiver escalado para um tamanho diferente e um backup é restaurado que foi feito antes da operação de escala?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)

### <a name="how-is-my-aof-data-organized-in-storage"></a>Como é que os meus dados da AOF estão organizados no armazenamento?

Os dados armazenados em ficheiros AOF são divididos em várias páginas por nó para aumentar o desempenho de guardar os dados para armazenamento. A tabela seguinte mostra quantas bolhas de página são usadas para cada nível de preços:

| Escalão Premium | Blobs |
|--------------|-------|
| P1           | 4 por fragmento    |
| P2           | 8 por fragmento    |
| P3           | 16 por fragmento   |
| P4           | 20 por fragmento   |

Quando o agrupamento está ativado, cada fragmento na cache tem o seu próprio conjunto de bolhas de página, conforme indicado na tabela anterior. Por exemplo, uma cache P2 com três fragmentos distribui o seu ficheiro AOF em 24 páginas de bolhas (8 bolhas por fragmento, com 3 fragmentos).

Após uma reescrita, existem dois conjuntos de ficheiros AOF no armazenamento. As reescritas ocorrem em segundo plano e anexam ao primeiro conjunto de ficheiros, enquanto as operações definidas que são enviadas para a cache durante o apêndice de reescrita para o segundo conjunto. Uma cópia de segurança é armazenada temporariamente durante as reescritas em caso de falha, mas é prontamente eliminada após a reescrita.


## <a name="next-steps"></a>Passos seguintes
Saiba como usar mais funcionalidades de cache premium.

* [Introdução ao Cache Azure para o nível Redis Premium](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-rdb-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-rdb-persistence.png

[redis-cache-aof-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-aof-persistence.png

[redis-cache-settings]: ./media/cache-how-to-premium-persistence/redis-cache-settings.png
