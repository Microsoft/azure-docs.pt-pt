---
title: Escalar uma cache Azure para a instância Redis
description: Saiba como escalar o seu Azure Cache para instâncias Redis usando o portal Azure, e ferramentas como Azure PowerShell, e Azure CLI
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 43db8d4c094ec1b08a24c29fdaccf97f63ef29b9
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833981"
---
# <a name="scale-an-azure-cache-for-redis-instance"></a>Escalar uma cache Azure para a instância Redis
A Azure Cache para Redis tem diferentes ofertas de cache, que proporcionam flexibilidade na escolha do tamanho e características da cache. Para uma cache Basic, Standard ou Premium, pode alterar o seu tamanho e nível depois de ter sido criado para acompanhar as necessidades da sua aplicação. Este artigo mostra-lhe como escalar a sua cache usando o portal Azure, e ferramentas como Azure PowerShell e Azure CLI.

## <a name="when-to-scale"></a>Quando dimensionar
Pode utilizar as características de [monitorização](cache-how-to-monitor.md) do Azure Cache para redis para monitorizar a saúde e o desempenho da sua cache e ajudar a determinar quando escalar a cache. 

Pode monitorizar as seguintes métricas para ajudar a determinar se precisa de escalar.

* Carga do servidor Redis
* Utilização de Memória
* Largura de banda de rede
* Utilização da CPU

Se determinar que o seu cache já não está a cumprir os requisitos da sua aplicação, pode escalar para um nível de preços de cache maior ou menor que seja adequado para a sua aplicação. Para obter mais informações sobre qual o nível de preços da cache a utilizar, consulte [escolher o nível certo](cache-overview.md#choosing-the-right-tier).

## <a name="scale-a-cache"></a>Escalar uma cache
Para escalar a sua cache, [navegue até à cache](cache-configure.md#configure-azure-cache-for-redis-settings) no [portal Azure](https://portal.azure.com) e clique em **Escala** a partir do **menu Recursos**.

![Escala](./media/cache-how-to-scale/redis-cache-scale-menu.png)

Selecione o nível de preços pretendido a partir da lâmina de **nível de preços Select** e clique em **Select**.

![Escalão de preço][redis-cache-pricing-tier-blade]


Pode escalar para um nível de preços diferente com as seguintes restrições:

* Não se pode escalar de um nível de preços mais elevado para um nível de preços mais baixo.
  * Não se pode escalar de uma cache **Premium** até um **Standard** ou uma cache **Básica.**
  * Não se pode escalar de uma cache **standard** até uma cache **básica.**
* Pode escalar de uma cache **Basic** para uma cache **Standard,** mas não pode mudar o tamanho ao mesmo tempo. Se precisar de um tamanho diferente, pode fazer uma operação de escala subsequente ao tamanho pretendido.
* Não se pode escalar de uma cache **básica** diretamente para uma cache **Premium.** Primeiro, escalone de **Base** para **Standard** numa operação de escala, e depois de **Standard** para **Premium** numa operação de escala subsequente.
* Não é possível escalar de um tamanho maior até ao tamanho **C0 (250 MB).** No entanto, pode reduzir-se a qualquer outro tamanho dentro do mesmo nível de preços. Por exemplo, pode descer de C5 Standard para C1 Standard.
 
Enquanto a cache está a escalonar para o novo nível de preços, é apresentado um estado **de escala** na Cache Azure para lâmina **Redis.**

![Dimensionamento][redis-cache-scaling]

Quando o escalonamento está completo, o estado muda de **Escala para** **Running**.

## <a name="how-to-automate-a-scaling-operation"></a>Como automatizar uma operação de escala
Além de escalar as suas instâncias de cache no portal Azure, pode escalar usando cmdlets PowerShell, Azure CLI, e utilizando as Bibliotecas de Gestão do Azure da Microsoft (MAML). 

* [Escala usando PowerShell](#scale-using-powershell)
* [Escala usando Azure CLI](#scale-using-azure-cli)
* [Escala usando MAML](#scale-using-maml)

### <a name="scale-using-powershell"></a>Escala usando PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pode escalar o seu Cache Azure para instâncias Redis com PowerShell utilizando o [cmdlet Set-AzRedisCache](/powershell/module/az.rediscache/set-azrediscache) quando o `Size` , ou propriedades são `Sku` `ShardCount` modificados. O exemplo a seguir mostra como escalar uma cache chamada `myCache` para uma cache de 2,5 GB. 

```powershell
   Set-AzRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB
```

Para obter mais informações sobre a escala com PowerShell, consulte [Para escalar uma cache Azure para Redis utilizando PowerShell](cache-how-to-manage-redis-cache-powershell.md#scale).

### <a name="scale-using-azure-cli"></a>Escala usando Azure CLI
Para escalar o seu Cache Azure para instâncias Redis usando O Azure CLI, ligue para o `azure rediscache set` comando e passe nas alterações de configuração desejadas que incluam um novo tamanho, sku ou tamanho do cluster, dependendo da operação de escala pretendida.

Para obter mais informações sobre o dimensionamento com o Azure CLI, consulte [as definições de alteração de uma cache Azure existente para Redis](cache-manage-cli.md#scale).

### <a name="scale-using-maml"></a>Escala usando MAML
Para escalar o seu Azure Cache para instâncias Redis utilizando as [Bibliotecas de Gestão microsoft Azure (MAML),](https://azure.microsoft.com/updates/management-libraries-for-net-release-announcement/)ligue para o `IRedisOperations.CreateOrUpdate` método e passe no novo tamanho para o `RedisProperties.SKU.Capacity` .

```csharp
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
```

Para obter mais informações, consulte a [Cache Manage Azure para Redis utilizando a amostra MAML.](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML)

## <a name="scaling-faq"></a>FAQ de escala
A lista que se segue contém respostas para perguntas comumente feitas sobre Azure Cache para o escalonamento de Redis.

* [Posso escalar para, de, ou dentro de uma cache Premium?](#can-i-scale-to-from-or-within-a-premium-cache)
* [Depois de escalonar, tenho de mudar o nome do cache ou as chaves de acesso?](#after-scaling-do-i-have-to-change-my-cache-name-or-access-keys)
* [Como funciona o escalonamento?](#how-does-scaling-work)
* [Vou perder dados da minha cache durante o escalonamento?](#will-i-lose-data-from-my-cache-during-scaling)
* [A minha definição de bases de dados personalizadas é afetada durante o escalonamento?](#is-my-custom-databases-setting-affected-during-scaling)
* [O meu cache estará disponível durante o escalonamento?](#will-my-cache-be-available-during-scaling)
* Com a geo-replicação configurada, por que não sou capaz de escalar a minha cache ou mudar os fragmentos num aglomerado?
* [Operações que não são apoiadas](#operations-that-are-not-supported)
* [Quanto tempo demora a escalonar?](#how-long-does-scaling-take)
* [Como posso saber quando a escala está completa?](#how-can-i-tell-when-scaling-is-complete)

### <a name="can-i-scale-to-from-or-within-a-premium-cache"></a>Posso escalar para, de, ou dentro de uma cache Premium?
* Não se pode escalar de uma cache **Premium** até um nível de preços **básico** ou **padrão.**
* Pode escalar de um nível de preços de cache **Premium** para outro.
* Não se pode escalar de uma cache **básica** diretamente para uma cache **Premium.** Primeiro, escalone de **Base** para **Standard** numa operação de escala, e depois de **Standard** para **Premium** numa operação de escala subsequente.
* Se ativar o agrupamento quando criou o seu cache **Premium,** pode [alterar o tamanho do cluster](cache-how-to-premium-clustering.md#cluster-size). Se a sua cache foi criada sem o agrupamento ativado, pode configurar o agrupamento mais tarde.
  
  Para obter mais informações, consulte [Como configurar o agrupamento para uma Cache Premium Azure para Redis.](cache-how-to-premium-clustering.md)

### <a name="after-scaling-do-i-have-to-change-my-cache-name-or-access-keys"></a>Depois de escalonar, tenho de mudar o nome do cache ou as chaves de acesso?
Não, o nome do cache e as chaves não se alteraram durante uma operação de escalonamento.

### <a name="how-does-scaling-work"></a>Como funciona o escalonamento?
* Quando uma cache **Basic** é dimensionada para um tamanho diferente, é desligada e uma nova cache é a provisionada usando o novo tamanho. Durante este tempo, a cache está indisponível e todos os dados da cache estão perdidos.
* Quando uma cache **Basic** é dimensionada para uma cache **Standard,** uma cache de réplica é a provisionada e os dados são copiados da cache primária para a cache de réplica. A cache permanece disponível durante o processo de escala.
* Quando uma cache **Standard** é dimensionada para um tamanho diferente ou para uma cache **Premium,** uma das réplicas é desligada e reprovisionada para o novo tamanho e os dados transferidos, e então a outra réplica executa uma falha antes de ser reprovisionada, semelhante ao processo que ocorre durante uma falha de um dos nós de cache.

### <a name="will-i-lose-data-from-my-cache-during-scaling"></a>Vou perder dados da minha cache durante o escalonamento?
* Quando uma cache **Basic** é dimensionada para um novo tamanho, todos os dados são perdidos e a cache não está disponível durante a operação de dimensionamento.
* Quando uma cache **Basic** é dimensionada para uma cache **Standard,** os dados na cache são tipicamente preservados.
* Quando uma cache **Standard** é dimensionada para um tamanho ou nível maior, ou uma cache **Premium** é dimensionada para um tamanho maior, todos os dados são tipicamente preservados. Ao escalonar uma cache **Standard** ou **Premium** para um tamanho menor, os dados podem ser perdidos dependendo da quantidade de dados que está na cache relacionada com o novo tamanho quando é dimensionado. Se os dados forem perdidos ao escalonar, as chaves são despejadas usando a política de despejo [allkeys-lru.](https://redis.io/topics/lru-cache) 

### <a name="is-my-custom-databases-setting-affected-during-scaling"></a>A minha definição de bases de dados personalizadas é afetada durante o escalonamento?
Se configurar um valor personalizado para a definição durante a `databases` criação de cache, tenha em mente que alguns níveis de preços têm [diferentes limites de bases de dados](cache-configure.md#databases). Eis algumas considerações ao escalonar neste cenário:

* Ao escalonar para um nível de preços com um limite inferior `databases` ao atual:
  * Se estiver a utilizar o número padrão de `databases` , que é 16 para todos os níveis de preços, nenhum dado é perdido.
  * Se estiver a utilizar um número personalizado `databases` que se enquadra nos limites para o nível a que está a escalonar, esta `databases` definição é retida e não se perdem dados.
  * Se estiver a utilizar um número personalizado `databases` que exceda os limites do novo nível, a `databases` definição é reduzida para os limites do novo nível e todos os dados nas bases de dados removidas são perdidos.
* Ao escalar para um nível de preços com o mesmo limite ou superior `databases` ao nível atual, a sua `databases` definição é retida e nenhum dado é perdido.

Enquanto os caches Standard e Premium têm um SLA de 99,9% para disponibilidade, não existe SLA para perda de dados.

### <a name="will-my-cache-be-available-during-scaling"></a>O meu cache estará disponível durante o escalonamento?
* **Os** caches Standard e **Premium** permanecem disponíveis durante a operação de dimensionamento. No entanto, podem ocorrer blips de ligação durante a escala de caches Standard e Premium, e também enquanto escala de caches Básicos para Standard. Espera-se que estas ligações sejam pequenas e os clientes redis devem ser capazes de restabelecer a sua ligação instantaneamente.
* **Caches básicos** estão offline durante as operações de escala para um tamanho diferente. Caches básicos permanecem disponíveis ao escalar de **Básico** para **Standard,** mas, pode experimentar um pequeno blip de conexão. Se ocorrer um blip de ligação, os clientes redis devem ser capazes de restabelecer a sua ligação instantaneamente.


### <a name="scaling-limitations-with-geo-replication"></a>Limitações de escala com geo-replicação

Uma vez adicionado uma ligação de geo-replicação entre dois caches, deixará de ser capaz de iniciar uma operação de escala ou alterar o número de fragmentos num cluster. Tem de desvincular a cache para emitir estes comandos. Para obter mais informações, consulte a [configuração da geo-replicação.](cache-how-to-geo-replication.md)


### <a name="operations-that-are-not-supported"></a>Operações que não são apoiadas
* Não se pode escalar de um nível de preços mais elevado para um nível de preços mais baixo.
  * Não se pode escalar de uma cache **Premium** até um **Standard** ou uma cache **Básica.**
  * Não se pode escalar de uma cache **standard** até uma cache **básica.**
* Pode escalar de uma cache **Basic** para uma cache **Standard,** mas não pode mudar o tamanho ao mesmo tempo. Se precisar de um tamanho diferente, pode fazer uma operação de escala subsequente ao tamanho pretendido.
* Não se pode escalar de uma cache **básica** diretamente para uma cache **Premium.** Primeira escala de **Base** para **Standard** numa operação de escala e, em seguida, escala de **Standard** para **Premium** numa operação subsequente.
* Não é possível escalar de um tamanho maior até ao tamanho **C0 (250 MB).**

Se uma operação de escala falhar, o serviço tenta reverter a operação e a cache voltará ao tamanho original.


### <a name="how-long-does-scaling-take"></a>Quanto tempo demora a escalonar?
O tempo de escala depende da quantidade de dados que existe na cache, com maiores quantidades de dados a demorarem mais tempo a ser concluídos. A escala leva aproximadamente 20 minutos. Para caches agrupados, a escala leva aproximadamente 20 minutos por fragmento.

### <a name="how-can-i-tell-when-scaling-is-complete"></a>Como posso saber quando a escala está completa?
No portal Azure, pode ver a operação de escala em andamento. Quando o escalonamento estiver completo, o estado da cache muda para **executar**.

<!-- IMAGES -->

[redis-cache-pricing-tier-blade]: ./media/cache-how-to-scale/redis-cache-pricing-tier-blade.png

[redis-cache-scaling]: ./media/cache-how-to-scale/redis-cache-scaling.png
