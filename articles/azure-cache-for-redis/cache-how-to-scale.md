---
title: Como dimensionar o cache do Azure para Redis
description: Saiba como dimensionar seu cache do Azure para instâncias Redis usando o portal do Azure e ferramentas como Azure PowerShell e CLI do Azure.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 04/11/2017
ms.openlocfilehash: 68c668561123aee943f54e6fdcbad7c6450957f4
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76714447"
---
# <a name="how-to-scale-azure-cache-for-redis"></a>Como dimensionar o cache do Azure para Redis
O cache do Azure para Redis tem diferentes ofertas de cache, que fornecem flexibilidade na escolha do tamanho e dos recursos do cache. Depois que um cache é criado, você pode dimensionar o tamanho e o tipo de preço do cache se os requisitos do aplicativo forem alterados. Este artigo mostra como dimensionar seu cache usando o portal do Azure e ferramentas como Azure PowerShell e CLI do Azure.

## <a name="when-to-scale"></a>Quando dimensionar
Você pode usar os recursos de [monitoramento](cache-how-to-monitor.md) do cache do Azure para Redis para monitorar a integridade e o desempenho do seu cache e ajudar a determinar quando dimensionar o cache. 

Você pode monitorar as seguintes métricas para ajudar a determinar se você precisa dimensionar.

* Carga do servidor Redis
* Utilização de Memória
* Largura de banda da rede
* Utilização do CPU

Se você determinar que o cache não está mais atendendo aos requisitos do seu aplicativo, poderá dimensionar para um tipo de preço de cache maior ou menor que seja adequado para seu aplicativo. Para obter mais informações sobre como determinar qual tipo de preço de cache usar, consulte [qual cache do Azure para oferta e tamanho do Redis devo usar](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use).

## <a name="scale-a-cache"></a>Dimensionar um cache
Para dimensionar o cache, [navegue até o cache](cache-configure.md#configure-azure-cache-for-redis-settings) no [portal do Azure](https://portal.azure.com) e clique em **escala** no **menu de recursos**.

![Escalabilidade](./media/cache-how-to-scale/redis-cache-scale-menu.png)

Selecione o tipo de preço desejado na folha **Selecionar tipo de preço** e clique em **selecionar**.

![Escalão de preço][redis-cache-pricing-tier-blade]


Você pode dimensionar para um tipo de preço diferente com as seguintes restrições:

* Não é possível dimensionar de um tipo de preço mais alto para um tipo de preço mais baixo.
  * Não é possível dimensionar de um cache **Premium** para um cache **padrão** ou **básico** .
  * Não é possível dimensionar de um cache **padrão** para um cache **básico** .
* Você pode dimensionar de um cache **básico** para um cache **padrão** , mas não pode alterar o tamanho ao mesmo tempo. Se você precisar de um tamanho diferente, poderá fazer uma operação de dimensionamento subsequente para o tamanho desejado.
* Não é possível dimensionar de um cache **básico** diretamente para um cache **Premium** . Primeiro, dimensione de **básico** para **Standard** em uma operação de dimensionamento e, em seguida, de **Standard** para **Premium** em uma operação de dimensionamento subsequente.
* Não é possível dimensionar de um tamanho maior para o tamanho de **C0 (250 MB)** .
 
Enquanto o cache está sendo dimensionado para o novo tipo de preço, um status de **dimensionamento** é exibido na folha **cache do Azure para Redis** .

![Dimensionamento][redis-cache-scaling]

Quando o dimensionamento for concluído, o status será alterado de **dimensionamento** para **em execução**.

## <a name="how-to-automate-a-scaling-operation"></a>Como automatizar uma operação de dimensionamento
Além de dimensionar suas instâncias de cache no portal do Azure, você pode dimensionar usando cmdlets do PowerShell, CLI do Azure e usando as bibliotecas de gerenciamento de Microsoft Azure (MAML). 

* [Dimensionar usando o PowerShell](#scale-using-powershell)
* [Dimensionar usando CLI do Azure](#scale-using-azure-cli)
* [Dimensionar usando MAML](#scale-using-maml)

### <a name="scale-using-powershell"></a>Dimensionar usando o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Você pode dimensionar o cache do Azure para instâncias Redis com o PowerShell usando o cmdlet [set-AzRedisCache](https://docs.microsoft.com/powershell/module/az.rediscache/set-azrediscache) quando as propriedades `Size`, `Sku`ou `ShardCount` são modificadas. O exemplo a seguir mostra como dimensionar um cache chamado `myCache` para um cache de 2,5 GB. 

    Set-AzRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB

Para obter mais informações sobre o dimensionamento com o PowerShell, consulte [para dimensionar um cache do Azure para Redis usando o PowerShell](cache-how-to-manage-redis-cache-powershell.md#scale).

### <a name="scale-using-azure-cli"></a>Dimensionar usando CLI do Azure
Para dimensionar o cache do Azure para instâncias Redis usando CLI do Azure, chame o comando `azure rediscache set` e passe as alterações de configuração desejadas que incluem um novo tamanho, SKU ou tamanho de cluster, dependendo da operação de dimensionamento desejada.

Para obter mais informações sobre como dimensionar com CLI do Azure, consulte [alterar as configurações de um cache do Azure existente para Redis](cache-manage-cli.md#scale).

### <a name="scale-using-maml"></a>Dimensionar usando MAML
Para dimensionar o cache do Azure para instâncias Redis usando as [bibliotecas de gerenciamento de Microsoft Azure (MAML)](https://azure.microsoft.com/updates/management-libraries-for-net-release-announcement/), chame o método `IRedisOperations.CreateOrUpdate` e passe o novo tamanho para o `RedisProperties.SKU.Capacity`.

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

Para obter mais informações, consulte o exemplo [gerenciar o cache do Azure para Redis usando MAML](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) .

## <a name="scaling-faq"></a>Perguntas frequentes sobre dimensionamento
A lista a seguir contém respostas para perguntas frequentes sobre o cache do Azure para o dimensionamento de Redis.

* [Posso dimensionar, de ou em um cache Premium?](#can-i-scale-to-from-or-within-a-premium-cache)
* [Após o dimensionamento, preciso alterar o nome do cache ou as chaves de acesso?](#after-scaling-do-i-have-to-change-my-cache-name-or-access-keys)
* [Como funciona o dimensionamento?](#how-does-scaling-work)
* [Perderei dados do meu cache durante o dimensionamento?](#will-i-lose-data-from-my-cache-during-scaling)
* [Minha configuração de bancos de dados personalizados é afetada durante o dimensionamento?](#is-my-custom-databases-setting-affected-during-scaling)
* [Meu cache estará disponível durante o dimensionamento?](#will-my-cache-be-available-during-scaling)
* Com a replicação geográfica configurada, por que não posso dimensionar o cache ou alterar os fragmentos em um cluster?
* [Operações que não têm suporte](#operations-that-are-not-supported)
* [Quanto tempo o dimensionamento leva?](#how-long-does-scaling-take)
* [Como posso saber quando o dimensionamento é concluído?](#how-can-i-tell-when-scaling-is-complete)

### <a name="can-i-scale-to-from-or-within-a-premium-cache"></a>Posso dimensionar, de ou em um cache Premium?
* Não é possível dimensionar de um cache **Premium** para um tipo de preço **básico** ou **Standard** .
* Você pode dimensionar de um tipo de preço de cache **Premium** para outro.
* Não é possível dimensionar de um cache **básico** diretamente para um cache **Premium** . Primeiro, dimensione de **básico** para **Standard** em uma operação de dimensionamento e, em seguida, de **Standard** para **Premium** em uma operação de dimensionamento subsequente.
* Se você habilitou o clustering quando criou o cache **Premium** , poderá [alterar o tamanho do cluster](cache-how-to-premium-clustering.md#cluster-size). Se o cache tiver sido criado sem o clustering habilitado, você poderá configurar o clustering posteriormente.
  
  Para obter mais informações, consulte [como configurar o clustering para um cache Premium do Azure para Redis](cache-how-to-premium-clustering.md).

### <a name="after-scaling-do-i-have-to-change-my-cache-name-or-access-keys"></a>Após o dimensionamento, preciso alterar o nome do cache ou as chaves de acesso?
Não, o nome do cache e as chaves não são alterados durante uma operação de dimensionamento.

### <a name="how-does-scaling-work"></a>Como funciona o dimensionamento?
* Quando um cache **básico** é dimensionado para um tamanho diferente, ele é desligado e um novo cache é provisionado usando o novo tamanho. Durante esse tempo, o cache fica indisponível e todos os dados no cache são perdidos.
* Quando um cache **básico** é dimensionado para um cache **padrão** , um cache de réplica é provisionado e os dados são copiados do cache primário para o cache de réplica. O cache permanece disponível durante o processo de dimensionamento.
* Quando um cache **padrão** é dimensionado para um tamanho diferente ou para um cache **Premium** , uma das réplicas é desligada e reprovisionada para o novo tamanho e os dados transferidos e, em seguida, a outra réplica executa um failover antes de ser reprovisionada, semelhante ao processo que ocorre durante uma falha de um dos nós de cache.

### <a name="will-i-lose-data-from-my-cache-during-scaling"></a>Perderei dados do meu cache durante o dimensionamento?
* Quando um cache **básico** é dimensionado para um novo tamanho, todos os dados são perdidos e o cache fica indisponível durante a operação de dimensionamento.
* Quando um cache **básico** é dimensionado para um cache **padrão** , os dados no cache são normalmente preservados.
* Quando um cache **padrão** é dimensionado para um tamanho ou camada maior, ou um cache **Premium** é dimensionado para um tamanho maior, todos os dados normalmente são preservados. Ao dimensionar um cache **Standard** ou **Premium** para um tamanho menor, os dados podem ser perdidos, dependendo da quantidade de dados no cache relacionada ao novo tamanho quando ele é dimensionado. Se os dados forem perdidos ao reduzir verticalmente, as chaves serão removidas usando a política de remoção [AllKeys-LRU](https://redis.io/topics/lru-cache) . 

### <a name="is-my-custom-databases-setting-affected-during-scaling"></a>Minha configuração de bancos de dados personalizados é afetada durante o dimensionamento?
Se você tiver configurado um valor personalizado para a configuração de `databases` durante a criação do cache, tenha em mente que alguns tipos de preço têm [limites de bancos de dados](cache-configure.md#databases)diferentes. Aqui estão algumas considerações ao dimensionar neste cenário:

* Ao Dimensionar para um tipo de preço com um limite de `databases` inferior à camada atual:
  * Se você estiver usando o número padrão de `databases`, que é 16 para todos os tipos de preço, nenhum dado será perdido.
  * Se você estiver usando um número personalizado de `databases` que esteja dentro dos limites da camada para a qual você está dimensionando, essa configuração de `databases` será mantida e nenhum dado será perdido.
  * Se você estiver usando um número personalizado de `databases` que exceda os limites da nova camada, a configuração de `databases` será diminuída para os limites da nova camada e todos os dados nos bancos removidos serão perdidos.
* Ao Dimensionar para um tipo de preço com o mesmo limite de `databases` ou maior do que a camada atual, sua configuração de `databases` é mantida e nenhum dado é perdido.

Embora os caches Standard e Premium tenham um SLA de 99,9% para disponibilidade, não existe SLA para perda de dados.

### <a name="will-my-cache-be-available-during-scaling"></a>Meu cache estará disponível durante o dimensionamento?
* Os caches **Standard** e **Premium** permanecem disponíveis durante a operação de dimensionamento. No entanto, a conexão blips pode ocorrer durante o dimensionamento de caches padrão e Premium e também durante o dimensionamento de caches básico para Standard. Espera-se que esses blips de conexão sejam pequenos e os clientes Redis devem ser capazes de restabelecer sua conexão instantaneamente.
* Os caches **básicos** ficam offline durante as operações de dimensionamento para um tamanho diferente. Os caches básicos permanecem disponíveis ao Dimensionar do **básico** para o **Standard** , mas podem ocorrer uma pequena Blip de conexão. Se ocorrer uma Blip de conexão, os clientes do Redis deverão ser capazes de restabelecer sua conexão instantaneamente.


### <a name="scaling-limitations-with-geo-replication"></a>Limitações de dimensionamento com replicação geográfica

Depois de adicionar um link de replicação geográfica entre dois caches, você não poderá mais iniciar uma operação de dimensionamento ou alterar o número de fragmentos em um cluster. Você deve desvincular o cache para emitir esses comandos. Para obter mais informações, consulte [Configurar a replicação geográfica](cache-how-to-geo-replication.md).


### <a name="operations-that-are-not-supported"></a>Operações que não têm suporte
* Não é possível dimensionar de um tipo de preço mais alto para um tipo de preço mais baixo.
  * Não é possível dimensionar de um cache **Premium** para um cache **padrão** ou **básico** .
  * Não é possível dimensionar de um cache **padrão** para um cache **básico** .
* Você pode dimensionar de um cache **básico** para um cache **padrão** , mas não pode alterar o tamanho ao mesmo tempo. Se você precisar de um tamanho diferente, poderá fazer uma operação de dimensionamento subsequente para o tamanho desejado.
* Não é possível dimensionar de um cache **básico** diretamente para um cache **Premium** . Primeiro, dimensione de **básico** para **Standard** em uma operação de dimensionamento e, em seguida, dimensione de **Standard** para **Premium** em uma operação subsequente.
* Não é possível dimensionar de um tamanho maior para o tamanho de **C0 (250 MB)** .

Se uma operação de dimensionamento falhar, o serviço tentará reverter a operação e o cache será revertido para o tamanho original.


### <a name="how-long-does-scaling-take"></a>Quanto tempo o dimensionamento leva?
O tempo de escala depende da quantidade de dados na cache, com maiores quantidades de dados a demorarem mais tempo a ser concluídos. O escalonamento demora aproximadamente 20 minutos. Para caches agrupados, a escala leva aproximadamente 20 minutos por fragmento.

### <a name="how-can-i-tell-when-scaling-is-complete"></a>Como posso saber quando o dimensionamento é concluído?
Na portal do Azure, você pode ver a operação de dimensionamento em andamento. Quando o dimensionamento for concluído, o status do cache será alterado para **em execução**.

<!-- IMAGES -->

[redis-cache-pricing-tier-blade]: ./media/cache-how-to-scale/redis-cache-pricing-tier-blade.png

[redis-cache-scaling]: ./media/cache-how-to-scale/redis-cache-scaling.png
