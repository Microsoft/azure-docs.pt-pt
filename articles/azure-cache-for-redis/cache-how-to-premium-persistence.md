---
title: Configurar a persistência de dados-cache Premium do Azure para Redis
description: Saiba como configurar e gerenciar a persistência de dados seu cache do Azure da camada Premium para instâncias Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/24/2017
ms.openlocfilehash: 40cd3467c7a4377427bb8db437e1047382933b1c
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76714868"
---
# <a name="how-to-configure-data-persistence-for-a-premium-azure-cache-for-redis"></a>Como configurar a persistência de dados para um cache do Azure Premium para Redis
O cache do Azure para Redis tem diferentes ofertas de cache que fornecem flexibilidade na escolha do tamanho e dos recursos do cache, incluindo recursos da camada Premium, como o suporte a clustering, persistência e rede virtual. Este artigo descreve como configurar a persistência em um cache do Azure Premium para a instância do Redis.

Para obter informações sobre outras funcionalidades de cache premium, consulte [Introdução ao Cache Azure para o nível Redis Premium](cache-premium-tier-intro.md).

## <a name="what-is-data-persistence"></a>O que é persistência de dados?
A persistência do [Redis](https://redis.io/topics/persistence) permite-lhe persistir os dados armazenados em Redis. Você também pode tirar instantâneos e fazer backup dos dados, que podem ser carregados no caso de uma falha de hardware. Essa é uma enorme vantagem em relação à camada básica ou Standard em que todos os dados são armazenados na memória e pode haver possível perda de dados em caso de falha em que os nós de cache estejam inativos. 

O cache do Azure para Redis oferece persistência Redis usando os seguintes modelos:

* **Persistência RDB** - Quando a persistência do RDB (base de dados Redis) é configurada, o Azure Cache for Redis persiste uma imagem do Azure Cache for Redis num formato binário Redis para o disco com base numa frequência de backup configurável. Se ocorrer um evento catastrófico que desabilite o cache primário e de réplica, o cache será reconstruído usando o instantâneo mais recente. Saiba mais sobre as vantagens e [desvantagens](https://redis.io/topics/persistence#rdb-disadvantages) da persistência do RDB. [](https://redis.io/topics/persistence#rdb-advantages)
* **Persistência AOF** - Quando a persistência da AOF (apêndice apenas ficheiro) é configurada, o Azure Cache para Redis guarda todas as operações de escrita para um registo que é guardado pelo menos uma vez por segundo numa conta de Armazenamento Azure. Se ocorrer um evento catastrófico que desabilite o cache primário e de réplica, o cache será reconstruído usando as operações de gravação armazenadas. Saiba mais sobre as vantagens e [desvantagens](https://redis.io/topics/persistence#aof-disadvantages) da persistência da AOF. [](https://redis.io/topics/persistence#aof-advantages)

A persistência escreve os dados da Redis numa conta de Armazenamento Azure que possui e gere. Pode configurar a partir do **Novo Azure Cache para** lâmina Redis durante a criação de cache e no menu **Recurso** para caches premium existentes.

> [!NOTE]
> 
> O Azure Storage encripta automaticamente os dados quando é persistido. Podes usar as tuas próprias chaves para a encriptação. Para mais informações, consulte [as chaves geridas pelo Cliente com](/azure/storage/common/storage-service-encryption?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#customer-managed-keys-with-azure-key-vault)o Cofre de Chaves Azure .
> 
> 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Uma vez selecionado um nível de preços premium, clique na **persistência do Redis**.

![Persistência de Redis][redis-cache-persistence]

As etapas na próxima seção descrevem como configurar a persistência do Redis em seu novo cache Premium. Assim que a persistência do Redis estiver configurada, clique em **Criar** para criar a sua nova cache premium com persistência redis.

## <a name="enable-redis-persistence"></a>Habilitar persistência Redis

A persistência do redis é ativada na lâmina de persistência de **Dados,** escolhendo a persistência de **RDB** ou **AOF.** Para novos caches, essa folha é acessada durante o processo de criação de cache, conforme descrito na seção anterior. Para caches existentes, a lâmina de **persistência de Dados** é acedida a partir do menu **Recurso** para a sua cache.

![Configurações de Redis][redis-cache-settings]


## <a name="configure-rdb-persistence"></a>Configurar persistência de RDB

Para ativar a persistência do RDB, clique em **RDB**. Para desativar a persistência do RDB numa cache premium previamente ativada, clique em **Desativar**.

![Persistência do RDB do Redis][redis-cache-rdb-persistence]

Para configurar o intervalo de cópia de segurança, selecione uma Frequência de **Backup** da lista de drop-down. As escolhas incluem **15 Minutos,** **30 minutos,** **60 minutos,** **6 horas,** **12 horas**e **24 horas.** Esse intervalo começa a contagem vertical depois que a operação de backup anterior é concluída com êxito e quando decorre um novo backup é iniciado.

Clique na **Conta de Armazenamento** para selecionar a conta de armazenamento para utilizar e escolha a chave **primária** ou a **chave secundária** para utilizar a partir da chave de **armazenamento.** Você deve escolher uma conta de armazenamento na mesma região que a cache, e uma conta **de Armazenamento Premium** é recomendada porque o armazenamento premium tem uma maior entrada. 

> [!IMPORTANT]
> Se a chave de armazenamento para a sua conta de persistência for regenerada, deve reconfigurar a chave desejada a partir da queda da chave de **armazenamento.**
> 
> 

Clique **em OK** para salvar a configuração de persistência.

O próximo backup (ou primeiro backup para novos caches) é iniciado quando o intervalo de frequência de backup expira.

## <a name="configure-aof-persistence"></a>Configurar persistência do AOF

Para permitir a persistência da AOF, clique em **AOF**. Para desativar a persistência da AOF numa cache premium previamente ativada, clique em **Desativar**.

![Persistência Redis AOF][redis-cache-aof-persistence]

Para configurar a persistência da AOF, especifique uma **Primeira Conta de Armazenamento**. Esta conta de armazenamento deve estar na mesma região que a cache, e uma conta **de Armazenamento Premium** é recomendada porque o armazenamento premium tem uma maior entrada. Pode configurar opcionalmente uma conta de armazenamento adicional chamada **Segunda Conta de Armazenamento.** Se uma segunda conta de armazenamento estiver configurada, as gravações no cache de réplica serão gravadas nessa segunda conta de armazenamento. Para cada conta de armazenamento configurada, escolha a **chave primária** ou a **chave secundária** para utilizar a partir da chave de **armazenamento.** 

> [!IMPORTANT]
> Se a chave de armazenamento para a sua conta de persistência for regenerada, deve reconfigurar a chave desejada a partir da queda da chave de **armazenamento.**
> 
> 

Quando a persistência de AOF estiver habilitada, as operações de gravação no cache serão salvas na conta de armazenamento designada (ou nas contas se você tiver configurado uma segunda conta de armazenamento). No caso de uma falha catastrófica que desative o cache primário e de réplica, o log de AOF armazenado será usado para recriar o cache.

## <a name="persistence-faq"></a>Perguntas frequentes sobre persistência
A lista a seguir contém respostas para perguntas frequentes sobre o cache do Azure para persistência Redis.

* [Posso permitir a persistência numa cache previamente criada?](#can-i-enable-persistence-on-a-previously-created-cache)
* [Posso permitir a persistência da AOF e do RDB ao mesmo tempo?](#can-i-enable-aof-and-rdb-persistence-at-the-same-time)
* [Que modelo de persistência devo escolher?](#which-persistence-model-should-i-choose)
* [O que acontece se eu tiver escalado para um tamanho diferente e um reforço for restaurado que foi feito antes da operação de escala?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)


### <a name="rdb-persistence"></a>Persistência de RDB
* [Posso mudar a frequência de cópia de segurança RDB depois de criar a cache?](#can-i-change-the-rdb-backup-frequency-after-i-create-the-cache)
* [Por que se eu tiver uma frequência de reserva RDB de 60 minutos há mais de 60 minutos entre cópias de segurança?](#why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups)
* [O que acontece com os antigos reforços do RDB quando um novo reforço é feito?](#what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made)

### <a name="aof-persistence"></a>Persistência de AOF
* [Quando devo usar uma segunda conta de armazenamento?](#when-should-i-use-a-second-storage-account)
* [A persistência da AOF afeta toda a latência ou desempenho da minha cache?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)
* [Como posso remover a segunda conta de armazenamento?](#how-can-i-remove-the-second-storage-account)
* [O que é uma reescrita e como isso afeta a minha cache?](#what-is-a-rewrite-and-how-does-it-affect-my-cache)
* [O que devo esperar quando escalonar uma cache com AOF ativada?](#what-should-i-expect-when-scaling-a-cache-with-aof-enabled)
* [Como é que os meus dados da AOF estão organizados no armazenamento?](#how-is-my-aof-data-organized-in-storage)


### <a name="can-i-enable-persistence-on-a-previously-created-cache"></a>Posso habilitar a persistência em um cache criado anteriormente?
Sim, a persistência Redis pode ser configurada na criação do cache e nos caches Premium existentes.

### <a name="can-i-enable-aof-and-rdb-persistence-at-the-same-time"></a>Posso habilitar a persistência de AOF e RDB ao mesmo tempo?

Não, você pode habilitar somente RDB ou AOF, mas não ambos ao mesmo tempo.

### <a name="which-persistence-model-should-i-choose"></a>Qual modelo de persistência devo escolher?

A persistência de AOF salva cada gravação em um log, que tem algum impacto na taxa de transferência, em comparação com a persistência de RDB, que salva os backups com base no intervalo de backup configurado, com impacto mínimo sobre o desempenho. Escolha persistência AOF se sua meta principal for minimizar a perda de dados e você puder lidar com uma redução na taxa de transferência para o cache. Escolha persistência de RDB se desejar manter a taxa de transferência ideal em seu cache, mas ainda quiser um mecanismo de recuperação de dados.

* Saiba mais sobre as vantagens e [desvantagens](https://redis.io/topics/persistence#rdb-disadvantages) da persistência do RDB. [](https://redis.io/topics/persistence#rdb-advantages)
* Saiba mais sobre as vantagens e [desvantagens](https://redis.io/topics/persistence#aof-disadvantages) da persistência da AOF. [](https://redis.io/topics/persistence#aof-advantages)

Para obter mais informações sobre o desempenho ao utilizar a persistência da AOF, ver [AOF persistência afeta toda a latência ou desempenho da minha cache?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)

### <a name="what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation"></a>O que acontece se eu tiver escalado para um tamanho diferente e um backup for restaurado que foi feito antes da operação de dimensionamento?

Para persistência de RDB e AOF:

* Se você tiver escalado para um tamanho maior, não haverá nenhum impacto.
* Se tiver escalado para um tamanho menor, e tiver uma definição de bases de [dados](cache-configure.md#databases) personalizada que é maior do que o limite de bases de [dados](cache-configure.md#databases) para o seu novo tamanho, os dados nessas bases de dados não são restaurados. Para mais informações, consulte a definição de bases de [dados personalizadas durante a escala?](cache-how-to-scale.md#is-my-custom-databases-setting-affected-during-scaling)
* Se tiver escalado para um tamanho menor, e não houver espaço suficiente no tamanho mais pequeno para reter todos os dados da última cópia de segurança, as chaves serão despejadas durante o processo de restauro, normalmente usando a política de despejo [allkeys-lru.](https://redis.io/topics/lru-cache)

### <a name="can-i-change-the-rdb-backup-frequency-after-i-create-the-cache"></a>Posso alterar a frequência de backup do RDB depois de criar o cache?
Sim, pode alterar a frequência de backup para a persistência de RDB na lâmina de **persistência de Dados.** Para obter instruções, consulte Configurar persistência Redis.

### <a name="why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups"></a>Por que se eu tiver uma frequência de backup de RDB de 60 minutos, haverá mais de 60 minutos entre os backups?
O intervalo de frequência de backup de persistência de RDB não é iniciado até que o processo de backup anterior seja concluído com êxito. Se a frequência de backup for de 60 minutos e levar um processo de backup de 15 minutos para ser concluída com êxito, o próximo backup não será iniciado até 75 minutos após a hora de início do backup anterior.

### <a name="what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made"></a>O que acontece com os antigos backups de RDB quando um novo backup é feito?
Todos os backups de persistência do RDB, exceto os mais recentes, são excluídos automaticamente. Essa exclusão pode não acontecer imediatamente, mas os backups mais antigos não são persistidos indefinidamente.


### <a name="when-should-i-use-a-second-storage-account"></a>Quando devo usar uma segunda conta de armazenamento?

Você deve usar uma segunda conta de armazenamento para persistência AOF quando acreditar que tem mais do que as operações de conjunto esperadas no cache.  A configuração da conta de armazenamento secundária ajuda a garantir que o cache não alcance os limites de largura de banda de armazenamento.

### <a name="does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache"></a>A persistência do AOF afeta em longo, latência ou desempenho do meu cache?

A persistência da AOF afeta a entrada em cerca de 15% – 20% quando a cache está abaixo da carga máxima (CPU e Server Load ambos abaixo de 90%). Não deve haver problemas de latência quando o cache está dentro desses limites. No entanto, o cache atingirá esses limites mais cedo com o AOF habilitado.

### <a name="how-can-i-remove-the-second-storage-account"></a>Como posso remover a segunda conta de armazenamento?

Você pode remover a conta de armazenamento secundário da persistência AOF definindo a segunda conta de armazenamento como a mesma que a primeira conta de armazenamento. Para obter instruções, consulte a [persistência da Configuração AOF](#configure-aof-persistence).

### <a name="what-is-a-rewrite-and-how-does-it-affect-my-cache"></a>O que é uma reescrita e como ela afeta meu cache?

Quando o arquivo AOF se torna grande o suficiente, uma regravação é automaticamente enfileirada no cache. A regravação redimensiona o arquivo AOF com o conjunto mínimo de operações necessárias para criar o conjunto de dados atual. Durante as regravações, espere atingir os limites de desempenho mais cedo, especialmente ao lidar com grandes conjuntos de altos. As regravações ocorrem com menos frequência, pois o arquivo AOF se torna maior, mas levará uma quantidade significativa de tempo quando ocorrer.

### <a name="what-should-i-expect-when-scaling-a-cache-with-aof-enabled"></a>O que devo esperar ao dimensionar um cache com o AOF habilitado?

Se o arquivo AOF no momento do dimensionamento for significativamente grande, espere que a operação de dimensionamento demore mais do que o esperado, já que ele recarregará o arquivo após a conclusão do dimensionamento.

Para mais informações sobre escala, veja [o que acontece se eu tiver escalado para um tamanho diferente e um backup é restaurado que foi feito antes da operação de escala?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)

### <a name="how-is-my-aof-data-organized-in-storage"></a>Como meus dados do AOF são organizados no armazenamento?

Os dados armazenados em arquivos AOF são divididos em vários BLOBs de páginas por nó para aumentar o desempenho de salvar os dados no armazenamento. A tabela a seguir exibe quantos blobs de páginas são usados para cada tipo de preço:

| Escalão Premium | Blobs |
|--------------|-------|
| P1           | 4 por fragmento    |
| P2           | 8 por fragmento    |
| P3           | 16 por fragmento   |
| P4           | 20 por fragmento   |

Quando o clustering está habilitado, cada fragmento no cache tem seu próprio conjunto de blobs de páginas, conforme indicado na tabela anterior. Por exemplo, um cache P2 com três fragmentos distribui seu arquivo AOF em 24 blobs de página (8 blobs por fragmento, com 3 fragmentos).

Após uma regravação, dois conjuntos de arquivos AOF existem no armazenamento. As regravações ocorrem em segundo plano e são acrescentadas ao primeiro conjunto de arquivos, enquanto as operações de conjunto que são enviadas para o cache durante a regravação acrescentam ao segundo conjunto. Um backup é temporariamente armazenado durante as regravações em caso de falha, mas é excluído imediatamente após a conclusão de uma regravação.


## <a name="next-steps"></a>Passos Seguintes
Saiba como usar mais recursos de cache Premium.

* [Introdução ao Cache Azure para o nível Redis Premium](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-rdb-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-rdb-persistence.png

[redis-cache-aof-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-aof-persistence.png

[redis-cache-settings]: ./media/cache-how-to-premium-persistence/redis-cache-settings.png
