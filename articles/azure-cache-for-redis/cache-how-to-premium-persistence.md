---
title: Como configurar a persistência de dados para um cache do Azure Premium para Redis
description: Saiba como configurar e gerenciar a persistência de dados seu cache do Azure da camada Premium para instâncias Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 08/24/2017
ms.author: yegu
ms.openlocfilehash: b74a16735b44d081a79b17716bdbc72357a36013
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122733"
---
# <a name="how-to-configure-data-persistence-for-a-premium-azure-cache-for-redis"></a>Como configurar a persistência de dados para um cache do Azure Premium para Redis
O cache do Azure para Redis tem diferentes ofertas de cache que fornecem flexibilidade na escolha do tamanho e dos recursos do cache, incluindo recursos da camada Premium, como o suporte a clustering, persistência e rede virtual. Este artigo descreve como configurar a persistência em um cache do Azure Premium para a instância do Redis.

Para obter informações sobre outros recursos de cache Premium, consulte [introdução ao cache do Azure para a camada Premium do Redis](cache-premium-tier-intro.md).

## <a name="what-is-data-persistence"></a>O que é persistência de dados?
A [persistência de Redis](https://redis.io/topics/persistence) permite que você persista os dados armazenados no Redis. Você também pode tirar instantâneos e fazer backup dos dados, que podem ser carregados no caso de uma falha de hardware. Essa é uma enorme vantagem em relação à camada básica ou Standard em que todos os dados são armazenados na memória e pode haver possível perda de dados em caso de falha em que os nós de cache estejam inativos. 

O cache do Azure para Redis oferece persistência Redis usando os seguintes modelos:

* **Persistência de RDB** – quando a persistência de RDB (banco de dados Redis) é configurada, o cache do Azure para Redis persiste um instantâneo do cache do Azure para Redis em um formato binário Redis para o disco com base em uma frequência de backup configurável. Se ocorrer um evento catastrófico que desabilite o cache primário e de réplica, o cache será reconstruído usando o instantâneo mais recente. Saiba mais sobre as [vantagens](https://redis.io/topics/persistence#rdb-advantages) e [desvantagens](https://redis.io/topics/persistence#rdb-disadvantages) da persistência do RDB.
* **Persistência de AOF** – quando a persistência de AOF (acrescentar somente arquivo) está configurada, o cache do Azure para Redis salva cada operação de gravação em um log que é salvo pelo menos uma vez por segundo em uma conta de armazenamento do Azure. Se ocorrer um evento catastrófico que desabilite o cache primário e de réplica, o cache será reconstruído usando as operações de gravação armazenadas. Saiba mais sobre as [vantagens](https://redis.io/topics/persistence#aof-advantages) e [desvantagens](https://redis.io/topics/persistence#aof-disadvantages) da persistência do AoF.

A persistência é configurada na folha **novo cache do Azure para Redis** durante a criação do cache e no **menu de recursos** para os caches Premium existentes.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Quando um tipo de preço premium for selecionado, clique em **persistência de Redis**.

![Persistência de Redis][redis-cache-persistence]

As etapas na próxima seção descrevem como configurar a persistência do Redis em seu novo cache Premium. Quando a persistência do Redis estiver configurada, clique em **criar** para criar seu novo cache Premium com a persistência do Redis.

## <a name="enable-redis-persistence"></a>Habilitar persistência Redis

A persistência Redis é habilitada na folha **persistência de dados do Redis** , escolhendo a persistência de **RDB** ou de **AoF** . Para novos caches, essa folha é acessada durante o processo de criação de cache, conforme descrito na seção anterior. Para os caches existentes, a folha **persistência de dados Redis** é acessada no **menu de recursos** do seu cache.

![Configurações de Redis][redis-cache-settings]


## <a name="configure-rdb-persistence"></a>Configurar persistência de RDB

Para habilitar a persistência de RDB, clique em **RDB**. Para desabilitar a persistência de RDB em um cache Premium habilitado anteriormente, clique em **desabilitado**.

![Persistência do RDB do Redis][redis-cache-rdb-persistence]

Para configurar o intervalo de backup, selecione uma **frequência de backup** na lista suspensa. As opções incluem **15 minutos**, **30 minutos**, **60 minutos**, **6 horas**, **12 horas**e **24 horas**. Esse intervalo começa a contagem vertical depois que a operação de backup anterior é concluída com êxito e quando decorre um novo backup é iniciado.

Clique em **conta de armazenamento** para selecionar a conta de armazenamento a ser usada e escolha a chave **primária** ou a **chave secundária** a ser usada na lista suspensa **chave de armazenamento** . Você deve escolher uma conta de armazenamento na mesma região que o cache e uma conta de **armazenamento Premium** é recomendada porque o armazenamento Premium tem maior taxa de transferência. 

> [!IMPORTANT]
> Se a chave de armazenamento da sua conta de persistência for regenerada, você deverá reconfigurar a chave desejada na lista suspensa **chave de armazenamento** .
> 
> 

Clique em **OK** para salvar a configuração de persistência.

O próximo backup (ou primeiro backup para novos caches) é iniciado quando o intervalo de frequência de backup expira.

## <a name="configure-aof-persistence"></a>Configurar persistência do AOF

Para habilitar a persistência do AOF, clique em **AoF**. Para desabilitar a persistência do AOF em um cache Premium habilitado anteriormente, clique em **desabilitado**.

![Persistência Redis AOF][redis-cache-aof-persistence]

Para configurar a persistência do AOF, especifique uma **primeira conta de armazenamento**. Essa conta de armazenamento deve estar na mesma região que o cache e uma conta de **armazenamento Premium** é recomendada porque o armazenamento Premium tem maior taxa de transferência. Opcionalmente, você pode configurar uma conta de armazenamento adicional denominada **segunda conta de armazenamento**. Se uma segunda conta de armazenamento estiver configurada, as gravações no cache de réplica serão gravadas nessa segunda conta de armazenamento. Para cada conta de armazenamento configurada, escolha a chave **primária** ou a **chave secundária** a ser usada na lista suspensa **chave de armazenamento** . 

> [!IMPORTANT]
> Se a chave de armazenamento da sua conta de persistência for regenerada, você deverá reconfigurar a chave desejada na lista suspensa **chave de armazenamento** .
> 
> 

Quando a persistência de AOF estiver habilitada, as operações de gravação no cache serão salvas na conta de armazenamento designada (ou nas contas se você tiver configurado uma segunda conta de armazenamento). No caso de uma falha catastrófica que desative o cache primário e de réplica, o log de AOF armazenado será usado para recriar o cache.

## <a name="persistence-faq"></a>Perguntas frequentes sobre persistência
A lista a seguir contém respostas para perguntas frequentes sobre o cache do Azure para persistência Redis.

* [Posso habilitar a persistência em um cache criado anteriormente?](#can-i-enable-persistence-on-a-previously-created-cache)
* [Posso habilitar a persistência de AOF e RDB ao mesmo tempo?](#can-i-enable-aof-and-rdb-persistence-at-the-same-time)
* [Qual modelo de persistência devo escolher?](#which-persistence-model-should-i-choose)
* [O que acontece se eu tiver escalado para um tamanho diferente e um backup for restaurado que foi feito antes da operação de dimensionamento?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)


### <a name="rdb-persistence"></a>Persistência de RDB
* [Posso alterar a frequência de backup do RDB depois de criar o cache?](#can-i-change-the-rdb-backup-frequency-after-i-create-the-cache)
* [Por que se eu tiver uma frequência de backup de RDB de 60 minutos, haverá mais de 60 minutos entre os backups?](#why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups)
* [O que acontece com os antigos backups de RDB quando um novo backup é feito?](#what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made)

### <a name="aof-persistence"></a>Persistência de AOF
* [Quando devo usar uma segunda conta de armazenamento?](#when-should-i-use-a-second-storage-account)
* [A persistência do AOF afeta em longo, latência ou desempenho do meu cache?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)
* [Como posso remover a segunda conta de armazenamento?](#how-can-i-remove-the-second-storage-account)
* [O que é uma reescrita e como ela afeta meu cache?](#what-is-a-rewrite-and-how-does-it-affect-my-cache)
* [O que devo esperar ao dimensionar um cache com o AOF habilitado?](#what-should-i-expect-when-scaling-a-cache-with-aof-enabled)
* [Como meus dados do AOF são organizados no armazenamento?](#how-is-my-aof-data-organized-in-storage)


### <a name="can-i-enable-persistence-on-a-previously-created-cache"></a>Posso habilitar a persistência em um cache criado anteriormente?
Sim, a persistência Redis pode ser configurada na criação do cache e nos caches Premium existentes.

### <a name="can-i-enable-aof-and-rdb-persistence-at-the-same-time"></a>Posso habilitar a persistência de AOF e RDB ao mesmo tempo?

Não, você pode habilitar somente RDB ou AOF, mas não ambos ao mesmo tempo.

### <a name="which-persistence-model-should-i-choose"></a>Qual modelo de persistência devo escolher?

A persistência de AOF salva cada gravação em um log, que tem algum impacto na taxa de transferência, em comparação com a persistência de RDB, que salva os backups com base no intervalo de backup configurado, com impacto mínimo sobre o desempenho. Escolha persistência AOF se sua meta principal for minimizar a perda de dados e você puder lidar com uma redução na taxa de transferência para o cache. Escolha persistência de RDB se desejar manter a taxa de transferência ideal em seu cache, mas ainda quiser um mecanismo de recuperação de dados.

* Saiba mais sobre as [vantagens](https://redis.io/topics/persistence#rdb-advantages) e [desvantagens](https://redis.io/topics/persistence#rdb-disadvantages) da persistência do RDB.
* Saiba mais sobre as [vantagens](https://redis.io/topics/persistence#aof-advantages) e [desvantagens](https://redis.io/topics/persistence#aof-disadvantages) da persistência do AoF.

Para obter mais informações sobre o desempenho ao usar a persistência do AOF, consulte [a persistência do AoF afeta em longo, latência ou desempenho do meu cache?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)

### <a name="what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation"></a>O que acontece se eu tiver escalado para um tamanho diferente e um backup for restaurado que foi feito antes da operação de dimensionamento?

Para persistência de RDB e AOF:

* Se você tiver escalado para um tamanho maior, não haverá nenhum impacto.
* Se você tiver escalado para um tamanho menor, e tiver uma configuração personalizada de [bancos](cache-configure.md#databases) de dados maior do que o [limite dos bancos](cache-configure.md#databases) de dado para o novo tamanho, eles não serão restaurados. Para obter mais informações, consulte [a configuração de meus bancos de dados personalizados é afetada durante o dimensionamento?](cache-how-to-scale.md#is-my-custom-databases-setting-affected-during-scaling)
* Se você tiver escalado para um tamanho menor e não houver espaço suficiente no tamanho menor para manter todos os dados do último backup, as chaves serão removidas durante o processo de restauração, normalmente usando a política de remoção [AllKeys-LRU](https://redis.io/topics/lru-cache) .

### <a name="can-i-change-the-rdb-backup-frequency-after-i-create-the-cache"></a>Posso alterar a frequência de backup do RDB depois de criar o cache?
Sim, você pode alterar a frequência de backup para persistência de RDB na folha **persistência de dados Redis** . Para obter instruções, consulte Configurar persistência Redis.

### <a name="why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups"></a>Por que se eu tiver uma frequência de backup de RDB de 60 minutos, haverá mais de 60 minutos entre os backups?
O intervalo de frequência de backup de persistência de RDB não é iniciado até que o processo de backup anterior seja concluído com êxito. Se a frequência de backup for de 60 minutos e levar um processo de backup de 15 minutos para ser concluída com êxito, o próximo backup não será iniciado até 75 minutos após a hora de início do backup anterior.

### <a name="what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made"></a>O que acontece com os antigos backups de RDB quando um novo backup é feito?
Todos os backups de persistência do RDB, exceto os mais recentes, são excluídos automaticamente. Essa exclusão pode não acontecer imediatamente, mas os backups mais antigos não são persistidos indefinidamente.


### <a name="when-should-i-use-a-second-storage-account"></a>Quando devo usar uma segunda conta de armazenamento?

Você deve usar uma segunda conta de armazenamento para persistência AOF quando acreditar que tem mais do que as operações de conjunto esperadas no cache.  A configuração da conta de armazenamento secundária ajuda a garantir que o cache não alcance os limites de largura de banda de armazenamento.

### <a name="does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache"></a>A persistência do AOF afeta em longo, latência ou desempenho do meu cache?

A persistência de AOF afeta a taxa de transferência em cerca de 15% – 20% quando o cache está abaixo da carga máxima (carga de CPU e servidor em 90%). Não deve haver problemas de latência quando o cache está dentro desses limites. No entanto, o cache atingirá esses limites mais cedo com o AOF habilitado.

### <a name="how-can-i-remove-the-second-storage-account"></a>Como posso remover a segunda conta de armazenamento?

Você pode remover a conta de armazenamento secundário da persistência AOF definindo a segunda conta de armazenamento como a mesma que a primeira conta de armazenamento. Para obter instruções, consulte [Configurar persistência AoF](#configure-aof-persistence).

### <a name="what-is-a-rewrite-and-how-does-it-affect-my-cache"></a>O que é uma reescrita e como ela afeta meu cache?

Quando o arquivo AOF se torna grande o suficiente, uma regravação é automaticamente enfileirada no cache. A regravação redimensiona o arquivo AOF com o conjunto mínimo de operações necessárias para criar o conjunto de dados atual. Durante as regravações, espere atingir os limites de desempenho mais cedo, especialmente ao lidar com grandes conjuntos de altos. As regravações ocorrem com menos frequência, pois o arquivo AOF se torna maior, mas levará uma quantidade significativa de tempo quando ocorrer.

### <a name="what-should-i-expect-when-scaling-a-cache-with-aof-enabled"></a>O que devo esperar ao dimensionar um cache com o AOF habilitado?

Se o arquivo AOF no momento do dimensionamento for significativamente grande, espere que a operação de dimensionamento demore mais do que o esperado, já que ele recarregará o arquivo após a conclusão do dimensionamento.

Para obter mais informações sobre o dimensionamento, consulte [o que acontece se eu tiver escalado para um tamanho diferente e um backup for restaurado que foi feito antes da operação de dimensionamento?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)

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


## <a name="next-steps"></a>Passos seguintes
Saiba como usar mais recursos de cache Premium.

* [Introdução ao cache do Azure para a camada Premium do Redis](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-rdb-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-rdb-persistence.png

[redis-cache-aof-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-aof-persistence.png

[redis-cache-settings]: ./media/cache-how-to-premium-persistence/redis-cache-settings.png
