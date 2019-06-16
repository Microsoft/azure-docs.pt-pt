---
title: Como dimensionar a Cache do Azure para Redis | Documentos da Microsoft
description: Saiba como dimensionar a sua Cache do Azure para instâncias de Redis
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 350db214-3b7c-4877-bd43-fef6df2db96c
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 04/11/2017
ms.author: yegu
ms.openlocfilehash: 495fc031150d04f253279606baebb5d64d52bce7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66132940"
---
# <a name="how-to-scale-azure-cache-for-redis"></a>Como dimensionar a Cache do Azure para Redis
A Cache de Redis do Azure tem ofertas de cache diferente, que fornecem flexibilidade na escolha de tamanho de cache e as funcionalidades. Depois de criar uma cache, pode aumentar o tamanho e o escalão de preço da cache se alteram os requisitos da sua aplicação. Este artigo mostra-lhe como dimensionar a sua cache com o portal do Azure e ferramentas como o Azure PowerShell e CLI do Azure.

## <a name="when-to-scale"></a>Quando dimensionar
Pode utilizar o [monitorização](cache-how-to-monitor.md) recursos do Azure Cache de Redis monitorizar o estado de funcionamento e o desempenho da cache e ajudar a determinar quando deve dimensionar a cache. 

Pode monitorizar as métricas seguintes para ajudar a determinar se precisar de aumentar.

* Carga do servidor redis
* Utilização da memória
* Largura de banda de rede
* Utilização da CPU

Se determinar a sua cache já não está a cumprir os requisitos da aplicação, pode dimensionar para uma cache, maior ou menor, preços mais adequado para a sua aplicação. Para obter mais informações sobre como determinar qual cache de escalão de preço para utilizar, consulte [que a Cache do Azure para o tamanho e oferta de Redis devo utilizar](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use).

## <a name="scale-a-cache"></a>Dimensione uma cache
Para dimensionar a sua cache [navegue para a cache](cache-configure.md#configure-azure-cache-for-redis-settings) no [portal do Azure](https://portal.azure.com) e clique em **dimensionamento** do **menu recursos**.

![Escala](./media/cache-how-to-scale/redis-cache-scale-menu.png)

Selecione o escalão de preço pretendido do **selecione o escalão de preço** painel e clique em **selecione**.

![Escalão de preço][redis-cache-pricing-tier-blade]


Pode dimensionar para um escalão de preço diferente com as seguintes restrições:

* Não é possível dimensionar a de um escalão de preço mais elevado para um escalão de preço mais baixo.
  * Não é possível dimensionar de um **Premium** colocar em cache para um **padrão** ou uma **básica** cache.
  * Não é possível dimensionar de um **padrão** colocar em cache para um **básica** cache.
* Pode dimensionar a partir de um **básica** colocar em cache para um **padrão** cache, mas não é possível alterar o tamanho, ao mesmo tempo. Se precisar de um tamanho diferente, pode fazer uma operação de dimensionamento subsequente para o tamanho pretendido.
* Não é possível dimensionar de um **básica** colocar em cache diretamente para um **Premium** cache. Em primeiro lugar, dimensione desde **básica** para **padrão** numa única operação de dimensionamento e, em seguida, **padrão** para **Premium** num dimensionamento subsequentes operação.
* Não é possível dimensionar a partir de um tamanho maior para o **C0 (250 MB)** tamanho.
 
Enquanto a cache está a dimensionar para o novo escalão de preço, uma **Scaling** estado é apresentado no **a Cache de Redis do Azure** painel.

![Dimensionamento][redis-cache-scaling]

Quando o dimensionamento é concluída, o estado é alterado de **Scaling** ao **em execução**.

## <a name="how-to-automate-a-scaling-operation"></a>Como automatizar uma operação de dimensionamento
Para além de dimensionar as instâncias de cache no portal do Azure, pode dimensionar com cmdlets do PowerShell, CLI do Azure e ao utilizar as bibliotecas de gestão do Azure de Microsoft (MAML). 

* [Dimensionamento com o PowerShell](#scale-using-powershell)
* [Dimensionamento com a CLI do Azure](#scale-using-azure-cli)
* [Escala usando MAML](#scale-using-maml)

### <a name="scale-using-powershell"></a>Dimensionamento com o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pode dimensionar a Cache do Azure para instâncias de Redis com o PowerShell, utilizando o [Set-AzRedisCache](https://docs.microsoft.com/powershell/module/az.rediscache/set-azrediscache) cmdlet quando o `Size`, `Sku`, ou `ShardCount` propriedades são modificadas. O exemplo seguinte mostra como dimensionar uma cache com o nome `myCache` a uma cache de 2,5 GB. 

    Set-AzRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB

Para obter mais informações sobre o dimensionamento com o PowerShell, consulte [dimensionar uma Cache do Azure para Redis com o Powershell](cache-howto-manage-redis-cache-powershell.md#scale).

### <a name="scale-using-azure-cli"></a>Dimensionamento com a CLI do Azure
Para dimensionar a sua Cache do Azure para instâncias de Redis com a CLI do Azure, chame o `azure rediscache set` de comandos e introduzir as alterações de configuração pretendida que incluem um novo tamanho, sku ou tamanho do cluster, dependendo da operação de dimensionamento pretendido.

Para obter mais informações sobre o dimensionamento com a CLI do Azure, consulte [alterar as definições de um Cache do Azure existente para Redis](cache-manage-cli.md#scale).

### <a name="scale-using-maml"></a>Escala usando MAML
Para dimensionar a sua Cache do Azure para instâncias de Redis com o [bibliotecas de gestão do Azure (MAML) da Microsoft](https://azure.microsoft.com/updates/management-libraries-for-net-release-announcement/), chamar os `IRedisOperations.CreateOrUpdate` método e passe o novo tamanho do `RedisProperties.SKU.Capacity`.

    static void Main(string[] args)
    {
        // For instructions on getting the access token, see
        // https://azure.microsoft.com/documentation/articles/cache-configure/#access-keys
        string token = GetAuthorizationHeader();

        TokenCloudCredentials creds = new TokenCloudCredentials(subscriptionId,token);

        RedisManagementClient client = new RedisManagementClient(creds);
        var redisProperties = new RedisProperties();

        // To scale, set a new size for the redisSKUCapacity parameter.
        redisProperties.Sku = new Sku(redisSKUName,redisSKUFamily,redisSKUCapacity);
        redisProperties.RedisVersion = redisVersion;
        var redisParams = new RedisCreateOrUpdateParameters(redisProperties, redisCacheRegion);
        client.Redis.CreateOrUpdate(resourceGroupName,cacheName, redisParams);
    }

Para obter mais informações, consulte a [gerir a Cache do Azure para Redis utilizando MAML](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) exemplo.

## <a name="scaling-faq"></a>FAQ de dimensionamento
A lista seguinte contém respostas para perguntas freqüentes sobre a Cache do Azure para dimensionar de Redis.

* [Posso Dimensionar para, a partir ou dentro de uma cache Premium?](#can-i-scale-to-from-or-within-a-premium-cache)
* [Depois de dimensionamento, é necessário que alterar as minhas chaves de acesso ou o nome da cache?](#after-scaling-do-i-have-to-change-my-cache-name-or-access-keys)
* [Como funciona o dimensionamento?](#how-does-scaling-work)
* [Irei perder dados da minha cache durante o dimensionamento?](#will-i-lose-data-from-my-cache-during-scaling)
* [Meus bancos de dados personalizados é definir afetados durante o dimensionamento?](#is-my-custom-databases-setting-affected-during-scaling)
* [Minha cache estará disponível durante dimensionamento?](#will-my-cache-be-available-during-scaling)
* Com a georreplicação configurada, por que motivo não consigo dimensionar a minha cache ou alterar as partições horizontais num cluster?
* [Operações que não são suportadas](#operations-that-are-not-supported)
* [O intervalo de tempo de dimensionamento demora?](#how-long-does-scaling-take)
* [Como posso saber quando o dimensionamento está completo?](#how-can-i-tell-when-scaling-is-complete)

### <a name="can-i-scale-to-from-or-within-a-premium-cache"></a>Posso Dimensionar para, a partir ou dentro de uma cache Premium?
* Não é possível dimensionar de um **Premium** colocar em cache para um **básica** ou **padrão** escalão de preço.
* Pode dimensionar a partir de um **Premium** cache de escalão de preço para outro.
* Não é possível dimensionar de um **básica** colocar em cache diretamente para um **Premium** cache. Em primeiro lugar, dimensione desde **básica** para **padrão** numa única operação de dimensionamento e, em seguida, **padrão** para **Premium** num dimensionamento subsequentes operação.
* Se tiver ativado o clustering quando criou sua **Premium** cache, pode [alterar o tamanho de cluster](cache-how-to-premium-clustering.md#cluster-size). Se a sua cache foi criada sem clustering ativado, pode configurar o clustering num momento posterior.
  
  Para obter mais informações, consulte [como configurar o clustering para uma Cache do Azure Premium para Redis](cache-how-to-premium-clustering.md).

### <a name="after-scaling-do-i-have-to-change-my-cache-name-or-access-keys"></a>Depois de dimensionamento, é necessário que alterar as minhas chaves de acesso ou o nome da cache?
Não, o nome de cache e as chaves são inalteradas durante uma operação de dimensionamento.

### <a name="how-does-scaling-work"></a>Como funciona o dimensionamento?
* Quando um **básica** cache for aumentado para um tamanho diferente, é desligado e uma nova cache é aprovisionada com o novo tamanho. Durante este período, o cache não estiver disponível e todos os dados na cache é perdido.
* Quando um **básica** cache for aumentado para um **padrão** cache, uma cache de réplica é aprovisionada e os dados são copiados da cache primária para a cache de réplica. A cache permanece disponível durante o processo de dimensionamento.
* Quando um **padrão** cache for aumentado para um tamanho diferente ou a um **Premium** cache, uma das réplicas é encerrado e reaprovisionada para o novo tamanho e os dados transferidos por e, em seguida, a outra réplica executa uma ativação pós-falha antes de ser reaprovisionada, semelhante ao processo que ocorre durante uma falha de um de nós de cache.

### <a name="will-i-lose-data-from-my-cache-during-scaling"></a>Irei perder dados da minha cache durante o dimensionamento?
* Quando um **básica** cache for aumentado para um novo tamanho, todos os dados são perdidos e o cache não está disponível durante a operação de dimensionamento.
* Quando um **básica** cache for aumentado para um **padrão** cache, os dados na cache normalmente é preservado.
* Quando um **padrão** cache for aumentado para uma maior tamanho ou camada, ou uma **Premium** cache for aumentado para um tamanho maior, todos os dados normalmente são preservados. Quando dimensionar um **padrão** ou **Premium** cache para baixo para um tamanho mais pequeno, os dados pode ser perdida consoante a quantidade de dados está no cache relacionados com o novo tamanho quando que ela foi dimensionada. Se os dados são perdidos ao reduzir verticalmente, as chaves são expulsos utilizando o [allkeys lru](https://redis.io/topics/lru-cache) política de expulsão. 

### <a name="is-my-custom-databases-setting-affected-during-scaling"></a>Meus bancos de dados personalizados é definir afetados durante o dimensionamento?
Se tiver configurado um valor personalizado para o `databases` definir durante a criação de cache, tenha em atenção que alguns preços camadas possuem diferentes [limites de bases de dados](cache-configure.md#databases). Seguem-se algumas considerações sobre quando o dimensionamento neste cenário:

* Quando dimensionar para um escalão de preço com um menor `databases` limite que o escalão atual:
  * Se estiver a utilizar o número predefinido de `databases`, que é 16 para todos os escalões de preço, nenhum dado seja perdido.
  * Se estiver a utilizar vários personalizado `databases` que está dentro dos limites para a camada para o qual são dimensionamento, isso `databases` definição é mantida, e não são perdidos dados.
  * Se estiver a utilizar vários personalizado `databases` que exceder os limites do novo escalão, o `databases` definição é baixada para os limites da nova camada e todos os dados nas bases de dados removidos se perder.
* Quando dimensionar para um escalão de preço com igual ou superior `databases` limite que o escalão atual, sua `databases` definição é mantida, e não são perdidos dados.

Enquanto as caches Standard e Premium têm um SLA de 99,9% de disponibilidade, não existe nenhum SLA para a perda de dados.

### <a name="will-my-cache-be-available-during-scaling"></a>Minha cache estará disponível durante dimensionamento?
* **Standard** e **Premium** caches permanecem disponíveis durante a operação de dimensionamento. No entanto, blips de ligação podem ocorrer ao dimensionamento caches Standard e Premium e também ao dimensionar de básico para caches padrão. Estes blips de ligação devem ser pequeno e clientes redis devem ser capazes de restabelecer sua conexão imediatamente.
* **Básico** caches estão offline durante operações para um tamanho diferente de dimensionamento. Caches básicas permaneçam disponíveis quando dimensionar de **básica** ao **padrão** mas poderá experienciar uma blip ligação pequeno. Se ocorrer um blip de ligação, os clientes da redis poderá restabelecer sua conexão imediatamente.


### <a name="scaling-limitations-with-geo-replication"></a>Limitações de dimensionamento com georreplicação

Depois de adicionar uma ligação de georreplicação entre dois caches, já não será capaz de iniciar uma operação de dimensionamento ou alterar o número de partições horizontais num cluster. Deve desassociar o cache para emitir estes comandos. Para obter mais informações, consulte [configurar a georreplicação](cache-how-to-geo-replication.md).


### <a name="operations-that-are-not-supported"></a>Operações que não são suportadas
* Não é possível dimensionar a de um escalão de preço mais elevado para um escalão de preço mais baixo.
  * Não é possível dimensionar de um **Premium** colocar em cache para um **padrão** ou uma **básica** cache.
  * Não é possível dimensionar de um **padrão** colocar em cache para um **básica** cache.
* Pode dimensionar a partir de um **básica** colocar em cache para um **padrão** cache, mas não é possível alterar o tamanho, ao mesmo tempo. Se precisar de um tamanho diferente, pode fazer uma operação de dimensionamento subsequente para o tamanho pretendido.
* Não é possível dimensionar de um **básica** colocar em cache diretamente para um **Premium** cache. Primeiro dimensionar de **básica** para **padrão** numa operação de dimensionamento e, em seguida, escala a partir **padrão** para **Premium** num subsequentes operação.
* Não é possível dimensionar a partir de um tamanho maior para o **C0 (250 MB)** tamanho.

Se uma operação de dimensionamento falhar, o serviço tentará reverter a operação e o cache irá reverter para o tamanho original.


### <a name="how-long-does-scaling-take"></a>O intervalo de tempo de dimensionamento demora?
Dimensionamento demora cerca de 20 minutos, dependendo da quantidade de dados está no cache.

### <a name="how-can-i-tell-when-scaling-is-complete"></a>Como posso saber quando o dimensionamento está completo?
No portal do Azure, pode ver a operação de dimensionamento em curso. Quando o dimensionamento é concluída, o estado da cache é alterado para **em execução**.

<!-- IMAGES -->

[redis-cache-pricing-tier-blade]: ./media/cache-how-to-scale/redis-cache-pricing-tier-blade.png

[redis-cache-scaling]: ./media/cache-how-to-scale/redis-cache-scaling.png



