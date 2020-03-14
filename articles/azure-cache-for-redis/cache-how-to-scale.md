---
title: Como escalar o Cache Azure para Redis
description: Aprenda a escalar o seu Azure Cache para os casos Redis utilizando o portal Azure, e ferramentas como o Azure PowerShell e o Azure CLI.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 04/11/2017
ms.openlocfilehash: 68c668561123aee943f54e6fdcbad7c6450957f4
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79278002"
---
# <a name="how-to-scale-azure-cache-for-redis"></a>Como escalar o Cache Azure para Redis
O Azure Cache for Redis tem diferentes ofertas de cache, que proporcionam flexibilidade na escolha do tamanho e características do cache. Depois de criar uma cache, pode escalar o tamanho e o nível de preços da cache se os requisitos da sua aplicação mudarem. Este artigo mostra-lhe como escalar a sua cache usando o portal Azure, e ferramentas como o Azure PowerShell e o Azure CLI.

## <a name="when-to-scale"></a>Quando dimensionar
Pode utilizar as características de [monitorização](cache-how-to-monitor.md) do Azure Cache para redis para monitorizar a saúde e o desempenho da sua cache e ajudar a determinar quando escalar a cache. 

Pode monitorizar as seguintes métricas para ajudar a determinar se precisa de escala.

* Carga do servidor Redis
* Utilização de Memória
* Largura de banda de rede
* Utilização do CPU

Se determinar que o seu cache já não satisfaz os requisitos da sua aplicação, pode escalar para um nível de preços de cache maior ou menor que é adequado para a sua aplicação. Para obter mais informações sobre a determinação do nível de preços da cache a utilizar, consulte [o que azure cache para a oferta e tamanho do Redis devo utilizar.](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)

## <a name="scale-a-cache"></a>Escalar uma cache
Para escalar a sua cache, [navegue até à cache](cache-configure.md#configure-azure-cache-for-redis-settings) no [portal Azure](https://portal.azure.com) e clique em **Escala** a partir do **menu Recurso**.

![Escala](./media/cache-how-to-scale/redis-cache-scale-menu.png)

Selecione o nível de preços desejado a partir da lâmina seleta de **nível** de preços e clique **em Selecionar**.

![Escalão de preço][redis-cache-pricing-tier-blade]


Pode escalar para um nível de preços diferente com as seguintes restrições:

* Não se pode escalar de um nível de preços mais alto para um nível de preços mais baixo.
  * Não pode escalar de uma cache **Premium** até um **Standard** ou um cache **Básico.**
  * Não se pode escalar de uma cache **padrão** até uma cache **básica.**
* Pode escalar de uma cache **Básica** para uma cache **Standard,** mas não pode alterar o tamanho ao mesmo tempo. Se precisar de um tamanho diferente, pode fazer uma operação de escala subsequente ao tamanho desejado.
* Não se pode escalar de uma cache **básica** diretamente para uma cache **Premium.** Primeiro, escala de **Basic** para **Standard** numa operação de escala, e depois da **Standard** para **Premium** numa operação de escala subsequente.
* Não se pode escalar de um tamanho maior até ao tamanho **C0 (250 MB).**
 
Enquanto a cache está a escalonar para o novo nível de preços, é apresentado um estado de **escala** no Azure Cache para a lâmina **Redis.**

![Dimensionamento][redis-cache-scaling]

Quando a escala estiver completa, o estado muda de **Escala para** **Execução**.

## <a name="how-to-automate-a-scaling-operation"></a>Como automatizar uma operação de escala
Além de escalonar as suas instâncias de cache no portal Azure, pode escalar utilizando cmdlets PowerShell, Azure CLI e utilizando as Bibliotecas de Gestão Microsoft Azure (MAML). 

* [Escala usando PowerShell](#scale-using-powershell)
* [Escala usando Azure CLI](#scale-using-azure-cli)
* [Escala usando MAML](#scale-using-maml)

### <a name="scale-using-powershell"></a>Escala usando PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pode escalar o seu Cache Azure para os casos Redis com a PowerShell utilizando o cmdlet [Set-AzRedisCache](https://docs.microsoft.com/powershell/module/az.rediscache/set-azrediscache) quando as propriedades `Size`, `Sku`ou `ShardCount` forem modificadas. O exemplo seguinte mostra como escalar uma cache chamada `myCache` para uma cache de 2,5 GB. 

    Set-AzRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB

Para obter mais informações sobre a escala com powerShell, consulte [Para escalar um Cache Azure para Redis utilizando powershell](cache-how-to-manage-redis-cache-powershell.md#scale).

### <a name="scale-using-azure-cli"></a>Escala usando Azure CLI
Para escalar o seu Cache Azure para os casos Redis utilizando o Azure CLI, ligue para o comando `azure rediscache set` e passe nas alterações de configuração desejadas que incluam um novo tamanho, sku ou tamanho de cluster, dependendo da operação de escalagem desejada.

Para obter mais informações sobre escala com o Azure CLI, consulte [as definições de alteração de um Azure Cache existente para Redis](cache-manage-cli.md#scale).

### <a name="scale-using-maml"></a>Escala usando MAML
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

## <a name="scaling-faq"></a>FaQ de escala
A lista que se segue contém respostas a perguntas comumente feitas sobre o Azure Cache para escala redis.

* [Posso escalar para, de ou dentro de uma cache Premium?](#can-i-scale-to-from-or-within-a-premium-cache)
* [Depois do escalonamento, tenho de mudar o meu nome de cache ou as chaves de acesso?](#after-scaling-do-i-have-to-change-my-cache-name-or-access-keys)
* [Como funciona a escala?](#how-does-scaling-work)
* [Vou perder dados da minha cache durante a escala?](#will-i-lose-data-from-my-cache-during-scaling)
* [As minhas bases de dados personalizadas estão a ser afetadas durante a escala?](#is-my-custom-databases-setting-affected-during-scaling)
* [A minha cache estará disponível durante o escalonamento?](#will-my-cache-be-available-during-scaling)
* Com a Geo-replicação configurada, por que não sou capaz de escalar a minha cache ou mudar os fragmentos num aglomerado?
* [Operações que não são apoiadas](#operations-that-are-not-supported)
* [Quanto tempo demora a escalar?](#how-long-does-scaling-take)
* [Como posso saber quando a escala está completa?](#how-can-i-tell-when-scaling-is-complete)

### <a name="can-i-scale-to-from-or-within-a-premium-cache"></a>Posso escalar para, de ou dentro de uma cache Premium?
* Não é possível escalar de uma cache **Premium** para um nível de preços **Básico** ou **Standard.**
* Pode escalar de um nível de preços de cache **Premium** para outro.
* Não se pode escalar de uma cache **básica** diretamente para uma cache **Premium.** Primeiro, escala de **Basic** para **Standard** numa operação de escala, e depois da **Standard** para **Premium** numa operação de escala subsequente.
* Se ativar o agrupamento quando criou a sua cache **Premium,** pode [alterar o tamanho do cluster](cache-how-to-premium-clustering.md#cluster-size). Se a sua cache foi criada sem agrupar ativada, pode configurar o agrupamento mais tarde.
  
  Para mais informações, consulte [Como configurar o agrupamento para um Cache Premium Azure para Redis](cache-how-to-premium-clustering.md).

### <a name="after-scaling-do-i-have-to-change-my-cache-name-or-access-keys"></a>Depois do escalonamento, tenho de mudar o meu nome de cache ou as chaves de acesso?
Não, o seu nome de cache e as chaves permanecem inalterados durante uma operação de escala.

### <a name="how-does-scaling-work"></a>Como funciona a escala?
* Quando uma cache **Basic** é dimensionada para um tamanho diferente, é desligada e uma nova cache é aprovisionada usando o novo tamanho. Durante este tempo, a cache não está disponível e todos os dados na cache são perdidos.
* Quando uma cache **básica** é dimensionada para uma cache **Standard,** uma cache de réplica é aprovisionada e os dados são copiados da cache primária para a cache da réplica. A cache permanece disponível durante o processo de escala.
* Quando uma cache **Standard** é dimensionada para um tamanho diferente ou para uma cache **Premium,** uma das réplicas é desligada e reprovisionada para o novo tamanho e os dados transferidos, e então a outra réplica realiza uma falha antes de ser reprovisionada, semelhante ao processo que ocorre durante uma falha de um dos nós da cache.

### <a name="will-i-lose-data-from-my-cache-during-scaling"></a>Vou perder dados da minha cache durante a escala?
* Quando uma cache **Basic** é dimensionada para um novo tamanho, todos os dados são perdidos e a cache não está disponível durante a operação de escala.
* Quando uma cache **Basic** é dimensionada para uma cache **Standard,** os dados na cache são tipicamente preservados.
* Quando uma cache **Standard** é dimensionada para um tamanho ou nível maior, ou uma cache **Premium** é dimensionada para um tamanho maior, todos os dados são normalmente preservados. Ao escalonar uma cache **Standard** ou **Premium** para um tamanho menor, os dados podem ser perdidos dependendo da quantidade de dados na cache relacionada com o novo tamanho quando é escalado. Se os dados forem perdidos ao escalonar, as chaves são despejadas usando a política de despejo [allkeys-lru.](https://redis.io/topics/lru-cache) 

### <a name="is-my-custom-databases-setting-affected-during-scaling"></a>As minhas bases de dados personalizadas estão a ser afetadas durante a escala?
Se configurar um valor personalizado para a definição de `databases` durante a criação de cache, lembre-se que alguns níveis de preços têm [diferentes limites](cache-configure.md#databases)de bases de dados . Aqui estão algumas considerações ao escalonar neste cenário:

* Ao escalonar para um nível de preços com um limite de `databases` inferior ao atual nível:
  * Se estiver a utilizar o número padrão de `databases`, que é 16 para todos os níveis de preços, não se perdem dados.
  * Se estiver a utilizar um número personalizado de `databases` que se insere dentro dos limites para o nível a que está a escalonar, esta configuração `databases` é mantida e não se perdem dados.
  * Se estiver a utilizar um número personalizado de `databases` que exceda os limites do novo nível, a definição de `databases` é reduzida para os limites do novo nível e todos os dados nas bases de dados removidas são perdidos.
* Ao escalonar para um nível de preços com o mesmo limite de `databases` ou superior ao nível atual, a sua definição `databases` é mantida e não se perdem dados.

Embora os caches Standard e Premium tenham um SLA de 99,9% para disponibilidade, não existe SLA para perda de dados.

### <a name="will-my-cache-be-available-during-scaling"></a>A minha cache estará disponível durante o escalonamento?
* **Os** caches Standard e **Premium** permanecem disponíveis durante a operação de escala. No entanto, podem ocorrer blips de ligação durante a escala de caches Standard e Premium, e também ao escalar de caches Básicos para Standard. Espera-se que estes blips de ligação sejam pequenos e os clientes redis devem ser capazes de restabelecer a sua ligação instantaneamente.
* Os caches **básicos** estão offline durante as operações de escala ção para um tamanho diferente. Os caches básicos permanecem disponíveis ao escalonar de **Basic** a **Standard,** mas podem experimentar um pequeno blip de ligação. Se ocorrer um blip de ligação, os clientes redis devem ser capazes de restabelecer a sua ligação instantaneamente.


### <a name="scaling-limitations-with-geo-replication"></a>Limitações de escala com geo-replicação

Depois de ter adicionado uma ligação de geo-replicação entre dois caches, deixará de ser capaz de iniciar uma operação de escala ou alterar o número de fragmentos num cluster. Deve desligar a cache para emitir estes comandos. Para mais informações, consulte [A Geo-replicação configure](cache-how-to-geo-replication.md).


### <a name="operations-that-are-not-supported"></a>Operações que não são apoiadas
* Não se pode escalar de um nível de preços mais alto para um nível de preços mais baixo.
  * Não pode escalar de uma cache **Premium** até um **Standard** ou um cache **Básico.**
  * Não se pode escalar de uma cache **padrão** até uma cache **básica.**
* Pode escalar de uma cache **Básica** para uma cache **Standard,** mas não pode alterar o tamanho ao mesmo tempo. Se precisar de um tamanho diferente, pode fazer uma operação de escala subsequente ao tamanho desejado.
* Não se pode escalar de uma cache **básica** diretamente para uma cache **Premium.** Primeira escala de **Basic** a **Standard** numa operação de escala, e depois escala de **Standard** para **Premium** numa operação subsequente.
* Não se pode escalar de um tamanho maior até ao tamanho **C0 (250 MB).**

Se uma operação de escala falhar, o serviço tenta reverter a operação e a cache reverterá para o tamanho original.


### <a name="how-long-does-scaling-take"></a>Quanto tempo demora a escalar?
O tempo de escala depende da quantidade de dados na cache, com maiores quantidades de dados a demorarem mais tempo a ser concluídos. O escalonamento demora aproximadamente 20 minutos. Para caches agrupados, a escala leva aproximadamente 20 minutos por fragmento.

### <a name="how-can-i-tell-when-scaling-is-complete"></a>Como posso saber quando a escala está completa?
No portal Azure, pode ver-se a operação de escala ção em curso. Quando a escala estiver completa, o estado da cache muda para **Executar**.

<!-- IMAGES -->

[redis-cache-pricing-tier-blade]: ./media/cache-how-to-scale/redis-cache-pricing-tier-blade.png

[redis-cache-scaling]: ./media/cache-how-to-scale/redis-cache-scaling.png
