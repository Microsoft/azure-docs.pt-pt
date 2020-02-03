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
Pode utilizar as características de [monitorização](cache-how-to-monitor.md) do Azure Cache para redis para monitorizar a saúde e o desempenho da sua cache e ajudar a determinar quando escalar a cache. 

Você pode monitorar as seguintes métricas para ajudar a determinar se você precisa dimensionar.

* Carga do servidor Redis
* Utilização de Memória
* Largura de banda da rede
* Utilização do CPU

Se você determinar que o cache não está mais atendendo aos requisitos do seu aplicativo, poderá dimensionar para um tipo de preço de cache maior ou menor que seja adequado para seu aplicativo. Para obter mais informações sobre a determinação do nível de preços da cache a utilizar, consulte [o que azure cache para a oferta e tamanho do Redis devo utilizar.](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)

## <a name="scale-a-cache"></a>Dimensionar um cache
Para escalar a sua cache, [navegue até à cache](cache-configure.md#configure-azure-cache-for-redis-settings) no [portal Azure](https://portal.azure.com) e clique em **Escala** a partir do **menu Recurso**.

![Escala](./media/cache-how-to-scale/redis-cache-scale-menu.png)

Selecione o nível de preços desejado a partir da lâmina seleta de **nível** de preços e clique **em Selecionar**.

![Escalão de preço][redis-cache-pricing-tier-blade]


Você pode dimensionar para um tipo de preço diferente com as seguintes restrições:

* Não é possível dimensionar de um tipo de preço mais alto para um tipo de preço mais baixo.
  * Não pode escalar de uma cache **Premium** até um **Standard** ou um cache **Básico.**
  * Não se pode escalar de uma cache **padrão** até uma cache **básica.**
* Pode escalar de uma cache **Básica** para uma cache **Standard,** mas não pode alterar o tamanho ao mesmo tempo. Se você precisar de um tamanho diferente, poderá fazer uma operação de dimensionamento subsequente para o tamanho desejado.
* Não se pode escalar de uma cache **básica** diretamente para uma cache **Premium.** Primeiro, escala de **Basic** para **Standard** numa operação de escala, e depois da **Standard** para **Premium** numa operação de escala subsequente.
* Não se pode escalar de um tamanho maior até ao tamanho **C0 (250 MB).**
 
Enquanto a cache está a escalonar para o novo nível de preços, é apresentado um estado de **escala** no Azure Cache para a lâmina **Redis.**

![Dimensionamento][redis-cache-scaling]

Quando a escala estiver completa, o estado muda de **Escala para** **Execução**.

## <a name="how-to-automate-a-scaling-operation"></a>Como automatizar uma operação de dimensionamento
Além de dimensionar suas instâncias de cache no portal do Azure, você pode dimensionar usando cmdlets do PowerShell, CLI do Azure e usando as bibliotecas de gerenciamento de Microsoft Azure (MAML). 

* [Escala usando PowerShell](#scale-using-powershell)
* [Escala usando Azure CLI](#scale-using-azure-cli)
* [Escala usando MAML](#scale-using-maml)

### <a name="scale-using-powershell"></a>Dimensionar usando o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pode escalar o seu Cache Azure para os casos Redis com a PowerShell utilizando o cmdlet [Set-AzRedisCache](https://docs.microsoft.com/powershell/module/az.rediscache/set-azrediscache) quando as propriedades `Size`, `Sku`ou `ShardCount` forem modificadas. O exemplo seguinte mostra como escalar uma cache chamada `myCache` para uma cache de 2,5 GB. 

    Set-AzRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB

Para obter mais informações sobre a escala com powerShell, consulte [Para escalar um Cache Azure para Redis utilizando powershell](cache-how-to-manage-redis-cache-powershell.md#scale).

### <a name="scale-using-azure-cli"></a>Dimensionar usando CLI do Azure
Para escalar o seu Cache Azure para os casos Redis utilizando o Azure CLI, ligue para o comando `azure rediscache set` e passe nas alterações de configuração desejadas que incluam um novo tamanho, sku ou tamanho de cluster, dependendo da operação de escalagem desejada.

Para obter mais informações sobre escala com o Azure CLI, consulte [as definições de alteração de um Azure Cache existente para Redis](cache-manage-cli.md#scale).

### <a name="scale-using-maml"></a>Dimensionar usando MAML
Para escalar o seu Cache Azure para os casos Redis utilizando as Bibliotecas de [Gestão Microsoft Azure (MAML),](https://azure.microsoft.com/updates/management-libraries-for-net-release-announcement/)ligue para o método `IRedisOperations.CreateOrUpdate` e passe o novo tamanho para o `RedisProperties.SKU.Capacity`.

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

Para mais informações, consulte o Cache Manage Azure para Redis utilizando a amostra [MAML.](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML)

## <a name="scaling-faq"></a>Perguntas frequentes sobre dimensionamento
A lista a seguir contém respostas para perguntas frequentes sobre o cache do Azure para o dimensionamento de Redis.

* [Posso escalar para, de ou dentro de uma cache Premium?](#can-i-scale-to-from-or-within-a-premium-cache)
* [Depois do escalonamento, tenho de mudar o meu nome de cache ou as chaves de acesso?](#after-scaling-do-i-have-to-change-my-cache-name-or-access-keys)
* [Como funciona a escala?](#how-does-scaling-work)
* [Vou perder dados da minha cache durante a escala?](#will-i-lose-data-from-my-cache-during-scaling)
* [As minhas bases de dados personalizadas estão a ser afetadas durante a escala?](#is-my-custom-databases-setting-affected-during-scaling)
* [A minha cache estará disponível durante o escalonamento?](#will-my-cache-be-available-during-scaling)
* Com a replicação geográfica configurada, por que não posso dimensionar o cache ou alterar os fragmentos em um cluster?
* [Operações que não são apoiadas](#operations-that-are-not-supported)
* [Quanto tempo demora a escalar?](#how-long-does-scaling-take)
* [Como posso saber quando a escala está completa?](#how-can-i-tell-when-scaling-is-complete)

### <a name="can-i-scale-to-from-or-within-a-premium-cache"></a>Posso dimensionar, de ou em um cache Premium?
* Não é possível escalar de uma cache **Premium** para um nível de preços **Básico** ou **Standard.**
* Pode escalar de um nível de preços de cache **Premium** para outro.
* Não se pode escalar de uma cache **básica** diretamente para uma cache **Premium.** Primeiro, escala de **Basic** para **Standard** numa operação de escala, e depois da **Standard** para **Premium** numa operação de escala subsequente.
* Se ativar o agrupamento quando criou a sua cache **Premium,** pode [alterar o tamanho do cluster](cache-how-to-premium-clustering.md#cluster-size). Se o cache tiver sido criado sem o clustering habilitado, você poderá configurar o clustering posteriormente.
  
  Para mais informações, consulte [Como configurar o agrupamento para um Cache Premium Azure para Redis](cache-how-to-premium-clustering.md).

### <a name="after-scaling-do-i-have-to-change-my-cache-name-or-access-keys"></a>Após o dimensionamento, preciso alterar o nome do cache ou as chaves de acesso?
Não, o nome do cache e as chaves não são alterados durante uma operação de dimensionamento.

### <a name="how-does-scaling-work"></a>Como funciona o dimensionamento?
* Quando uma cache **Basic** é dimensionada para um tamanho diferente, é desligada e uma nova cache é aprovisionada usando o novo tamanho. Durante esse tempo, o cache fica indisponível e todos os dados no cache são perdidos.
* Quando uma cache **básica** é dimensionada para uma cache **Standard,** uma cache de réplica é aprovisionada e os dados são copiados da cache primária para a cache da réplica. O cache permanece disponível durante o processo de dimensionamento.
* Quando uma cache **Standard** é dimensionada para um tamanho diferente ou para uma cache **Premium,** uma das réplicas é desligada e reprovisionada para o novo tamanho e os dados transferidos, e então a outra réplica realiza uma falha antes de ser reprovisionada, semelhante ao processo que ocorre durante uma falha de um dos nós da cache.

### <a name="will-i-lose-data-from-my-cache-during-scaling"></a>Perderei dados do meu cache durante o dimensionamento?
* Quando uma cache **Basic** é dimensionada para um novo tamanho, todos os dados são perdidos e a cache não está disponível durante a operação de escala.
* Quando uma cache **Basic** é dimensionada para uma cache **Standard,** os dados na cache são tipicamente preservados.
* Quando uma cache **Standard** é dimensionada para um tamanho ou nível maior, ou uma cache **Premium** é dimensionada para um tamanho maior, todos os dados são normalmente preservados. Ao escalonar uma cache **Standard** ou **Premium** para um tamanho menor, os dados podem ser perdidos dependendo da quantidade de dados na cache relacionada com o novo tamanho quando é escalado. Se os dados forem perdidos ao escalonar, as chaves são despejadas usando a política de despejo [allkeys-lru.](https://redis.io/topics/lru-cache) 

### <a name="is-my-custom-databases-setting-affected-during-scaling"></a>Minha configuração de bancos de dados personalizados é afetada durante o dimensionamento?
Se configurar um valor personalizado para a definição de `databases` durante a criação de cache, lembre-se que alguns níveis de preços têm [diferentes limites](cache-configure.md#databases)de bases de dados . Aqui estão algumas considerações ao dimensionar neste cenário:

* Ao escalonar para um nível de preços com um limite de `databases` inferior ao atual nível:
  * Se estiver a utilizar o número padrão de `databases`, que é 16 para todos os níveis de preços, não se perdem dados.
  * Se estiver a utilizar um número personalizado de `databases` que se insere dentro dos limites para o nível a que está a escalonar, esta configuração `databases` é mantida e não se perdem dados.
  * Se estiver a utilizar um número personalizado de `databases` que exceda os limites do novo nível, a definição de `databases` é reduzida para os limites do novo nível e todos os dados nas bases de dados removidas são perdidos.
* Ao escalonar para um nível de preços com o mesmo limite de `databases` ou superior ao nível atual, a sua definição `databases` é mantida e não se perdem dados.

Embora os caches Standard e Premium tenham um SLA de 99,9% para disponibilidade, não existe SLA para perda de dados.

### <a name="will-my-cache-be-available-during-scaling"></a>Meu cache estará disponível durante o dimensionamento?
* **Os** caches Standard e **Premium** permanecem disponíveis durante a operação de escala. No entanto, a conexão blips pode ocorrer durante o dimensionamento de caches padrão e Premium e também durante o dimensionamento de caches básico para Standard. Espera-se que esses blips de conexão sejam pequenos e os clientes Redis devem ser capazes de restabelecer sua conexão instantaneamente.
* Os caches **básicos** estão offline durante as operações de escala ção para um tamanho diferente. Os caches básicos permanecem disponíveis ao escalonar de **Basic** a **Standard,** mas podem experimentar um pequeno blip de ligação. Se ocorrer uma Blip de conexão, os clientes do Redis deverão ser capazes de restabelecer sua conexão instantaneamente.


### <a name="scaling-limitations-with-geo-replication"></a>Limitações de dimensionamento com replicação geográfica

Depois de adicionar um link de replicação geográfica entre dois caches, você não poderá mais iniciar uma operação de dimensionamento ou alterar o número de fragmentos em um cluster. Você deve desvincular o cache para emitir esses comandos. Para mais informações, consulte [A Geo-replicação configure](cache-how-to-geo-replication.md).


### <a name="operations-that-are-not-supported"></a>Operações que não têm suporte
* Não é possível dimensionar de um tipo de preço mais alto para um tipo de preço mais baixo.
  * Não pode escalar de uma cache **Premium** até um **Standard** ou um cache **Básico.**
  * Não se pode escalar de uma cache **padrão** até uma cache **básica.**
* Pode escalar de uma cache **Básica** para uma cache **Standard,** mas não pode alterar o tamanho ao mesmo tempo. Se você precisar de um tamanho diferente, poderá fazer uma operação de dimensionamento subsequente para o tamanho desejado.
* Não se pode escalar de uma cache **básica** diretamente para uma cache **Premium.** Primeira escala de **Basic** a **Standard** numa operação de escala, e depois escala de **Standard** para **Premium** numa operação subsequente.
* Não se pode escalar de um tamanho maior até ao tamanho **C0 (250 MB).**

Se uma operação de dimensionamento falhar, o serviço tentará reverter a operação e o cache será revertido para o tamanho original.


### <a name="how-long-does-scaling-take"></a>Quanto tempo o dimensionamento leva?
O tempo de escala depende da quantidade de dados na cache, com maiores quantidades de dados a demorarem mais tempo a ser concluídos. O escalonamento demora aproximadamente 20 minutos. Para caches agrupados, a escala leva aproximadamente 20 minutos por fragmento.

### <a name="how-can-i-tell-when-scaling-is-complete"></a>Como posso saber quando o dimensionamento é concluído?
Na portal do Azure, você pode ver a operação de dimensionamento em andamento. Quando a escala estiver completa, o estado da cache muda para **Executar**.

<!-- IMAGES -->

[redis-cache-pricing-tier-blade]: ./media/cache-how-to-scale/redis-cache-pricing-tier-blade.png

[redis-cache-scaling]: ./media/cache-how-to-scale/redis-cache-scaling.png
