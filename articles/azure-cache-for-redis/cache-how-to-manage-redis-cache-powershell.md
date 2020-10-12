---
title: Gerir a cache Azure para Redis com Azure PowerShell
description: Aprenda a executar tarefas administrativas para Azure Cache para Redis usando Azure PowerShell.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/13/2017
ms.author: yegu
ms.openlocfilehash: 327505dfbaf5f6f35b065f4f3941053c5114aa33
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89019220"
---
# <a name="manage-azure-cache-for-redis-with-azure-powershell"></a>Gerir a cache Azure para Redis com Azure PowerShell
> [!div class="op_single_selector"]
> * [PowerShell](cache-how-to-manage-redis-cache-powershell.md)
> * [CLI do Azure](cache-manage-cli.md)
> 
> 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Este tópico mostra-lhe como executar tarefas comuns como criar, atualizar e escalar o seu Cache Azure para instâncias Redis, como regenerar as teclas de acesso e como visualizar informações sobre os seus caches. Para obter uma lista completa de Cache Azure para cmdlets Redis PowerShell, consulte [Azure Cache para cmdlets Redis](https://docs.microsoft.com/powershell/module/az.rediscache).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

Para obter mais informações sobre o modelo de implementação clássico, consulte [Azure Resource Manager vs. design clássico: Compreenda os modelos de implantação e o estado dos seus recursos.](../azure-resource-manager/management/deployment-models.md)

## <a name="prerequisites"></a>Pré-requisitos
Se já instalou a Azure PowerShell, tem de ter a versão 1.0.0 ou mais tarde do Azure PowerShell. Pode verificar a versão do Azure PowerShell que instalou com este comando na visou prompt do Comando Azure PowerShell.

```azurepowershell
    Get-Module Az | format-table version
```

Primeiro, tens de entrar em Azure com este comando.

```azurepowershell
    Connect-AzAccount
```

Especifique o endereço de e-mail da sua conta Azure e a sua palavra-passe no diálogo de entrada de assinatura do Microsoft Azure.

Em seguida, se tiver várias subscrições do Azure, tem de definir a sua subscrição Azure. Para ver uma lista das suas subscrições atuais, execute este comando.

```azurepowershell
    Get-AzSubscription | sort SubscriptionName | Select SubscriptionName
```

Para especificar a subscrição, executar o seguinte comando. No exemplo seguinte, o nome de subscrição é `ContosoSubscription` .

```azurepowershell
    Select-AzSubscription -SubscriptionName ContosoSubscription
```

Antes de poder utilizar o Windows PowerShell com o Azure Resource Manager, precisa do seguinte:

* Windows PowerShell, Versão 3.0 ou 4.0. Para encontrar a versão do Windows PowerShell, `$PSVersionTable` escreva: e verifique o valor de `PSVersion` 3.0 ou 4.0. Para instalar uma versão compatível, consulte [o Quadro de Gestão do Windows 3.0](https://www.microsoft.com/download/details.aspx?id=34595).

Para obter ajuda detalhada para qualquer cmdlet que você vê neste tutorial, use o Get-Help cmdlet.

```azurepowershell
    Get-Help <cmdlet-name> -Detailed
```

Por exemplo, para obter ajuda para o `New-AzRedisCache` cmdlet, escreva:

```azurepowershell
    Get-Help New-AzRedisCache -Detailed
```

### <a name="how-to-connect-to-other-clouds"></a>Como ligar-se a outras nuvens
Por defeito, o ambiente Azure é `AzureCloud` , que representa o exemplo global da nuvem Azure. Para ligar a um caso diferente, utilize o `Connect-AzAccount` comando com o ou - interruptor de linha de comando com o ambiente ou nome ambiente `-Environment` `EnvironmentName` pretendido.

Para ver a lista de ambientes disponíveis, execute o `Get-AzEnvironment` cmdlet.

### <a name="to-connect-to-the-azure-government-cloud"></a>Para ligar à Nuvem do Governo de Azure
Para ligar à Nuvem do Governo de Azure, utilize um dos seguintes comandos.

```azurepowershell
    Connect-AzAccount -EnvironmentName AzureUSGovernment
```

ou

```azurepowershell
    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureUSGovernment)
```

Para criar uma cache na Nuvem do Governo de Azure, use um dos seguintes locais.

* USGov Virginia
* USGov Iowa

Para obter mais informações sobre a Cloud do Governo Azure, consulte o [Microsoft Azure Government](https://azure.microsoft.com/features/gov/) e [o Microsoft Azure Government Developer Guide](../azure-government-developer-guide.md).

### <a name="to-connect-to-the-azure-china-cloud"></a>Para ligar à Nuvem Azure China
Para ligar à Nuvem Azure China, utilize um dos seguintes comandos.

```azurepowershell
    Connect-AzAccount -EnvironmentName AzureChinaCloud
```

ou

```azurepowershell
    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureChinaCloud)
```

Para criar uma cache na Nuvem Azure China, use um dos seguintes locais.

* Leste da China
* Norte da China

Para mais informações sobre a Nuvem Azure China, consulte [a AzureChinaCloud para a Azure operada pela 21Vianet na China.](https://www.windowsazure.cn/)

### <a name="to-connect-to-microsoft-azure-germany"></a>Para ligar à Microsoft Azure Germany
Para ligar à Microsoft Azure Germany, utilize um dos seguintes comandos.

```azurepowershell
    Connect-AzAccount -EnvironmentName AzureGermanCloud
```

ou

```azurepowershell
    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureGermanCloud)
```

Para criar uma cache no Microsoft Azure Germany, utilize um dos seguintes locais.

* Alemanha Central
* Nordeste da Alemanha

Para mais informações sobre o Microsoft Azure Germany, consulte [o Microsoft Azure Germany](https://azure.microsoft.com/overview/clouds/germany/).

### <a name="properties-used-for-azure-cache-for-redis-powershell"></a>Propriedades utilizadas para Azure Cache para Redis PowerShell
A tabela seguinte contém propriedades e descrições para parâmetros comumente usados ao criar e gerir o seu Cache Azure para instâncias Redis usando Azure PowerShell.

| Parâmetro | Descrição | Predefinição |
| --- | --- | --- |
| Nome |Nome da cache | |
| Localização |Localização da cache | |
| ResourceGroupName |Nome do grupo de recursos para criar a cache | |
| Tamanho |Do tamanho da cache. Os valores válidos são: P1, P2, P3, P4, C0, C1, C2, C3, C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB |1GB |
| ShardCount |O número de fragmentos para criar ao criar uma cache premium com clustering ativado. Valores válidos são: 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 | |
| SKU |Especifica o SKU da cache. Valores válidos são: Básico, Standard, Premium |Standard |
| RedisConfiguration |Especifica as definições de configuração do Redis. Para obter mais informações sobre cada definição, consulte a seguinte tabela [de propriedades RedisConfiguration.](#redisconfiguration-properties) | |
| EnableNonSslPort |Indica se a porta não-SSL está ativada. |Falso |
| MaxMemoryPolicy |Este parâmetro foi depreciado - use redisConfiguration em vez disso. | |
| StaticIP |Ao hospedar o seu cache num VNET, especifica um endereço IP único na sub-rede para a cache. Se não for fornecido, um é escolhido para si a partir da sub-rede. | |
| Sub-rede |Ao hospedar o seu cache num VNET, especifica o nome da sub-rede em que deve colocar a cache. | |
| VirtualNetwork |Ao hospedar a sua cache num VNET, especifica o ID de recurso do VNET no qual deve implantar a cache. | |
| Teclas |Especifica qual a chave de acesso para regenerar ao renovar as teclas de acesso. Valores válidos são: Primário, Secundário | |

### <a name="redisconfiguration-properties"></a>Propriedades de RedisConfiguration
| Propriedade | Descrição | Escalões de preço |
| --- | --- | --- |
| rdb-backup-habilitado |Se [a persistência de dados do Redis](cache-how-to-premium-persistence.md) está ativada |Premium apenas |
| rdb-armazenamento-ligação-cadeia |A cadeia de ligação à conta de armazenamento para [a persistência de dados do Redis](cache-how-to-premium-persistence.md) |Premium apenas |
| rdb-backup-frequência |A frequência de backup para [a persistência de dados do Redis](cache-how-to-premium-persistence.md) |Premium apenas |
| maxmemory reservado |Configura a [memória reservada](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) para processos não cache |Standard e Premium |
| maxmemory-política |Configura a [política de despejo](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) para a cache |Todos os níveis de preços |
| notificar-key-keyspace-eventos |Configura [notificações de keyspace](cache-configure.md#keyspace-notifications-advanced-settings) |Standard e Premium |
| entradas hash-max-ziplist |Configura a [otimização da memória](https://redis.io/topics/memory-optimization) para pequenos tipos de dados agregados |Standard e Premium |
| hash-max-ziplist-valor |Configura a [otimização da memória](https://redis.io/topics/memory-optimization) para pequenos tipos de dados agregados |Standard e Premium |
| set-max-intset-entradas |Configura a [otimização da memória](https://redis.io/topics/memory-optimization) para pequenos tipos de dados agregados |Standard e Premium |
| zset-max-ziplist-entradas |Configura a [otimização da memória](https://redis.io/topics/memory-optimization) para pequenos tipos de dados agregados |Standard e Premium |
| zset-max-ziplist-valor |Configura a [otimização da memória](https://redis.io/topics/memory-optimization) para pequenos tipos de dados agregados |Standard e Premium |
| Bases de dados |Configura o número de bases de dados. Esta propriedade só pode ser configurada na criação de cache. |Standard e Premium |

## <a name="to-create-an-azure-cache-for-redis"></a>Para criar um Cache Azure para Redis
A nova cache Azure para instâncias Redis são criadas usando o [cmdlet New-AzRedisCache.](https://docs.microsoft.com/powershell/module/az.rediscache/new-azrediscache)

> [!IMPORTANT]
> A primeira vez que cria um Azure Cache para Redis numa subscrição utilizando o portal Azure, o portal regista o `Microsoft.Cache` espaço de nome para essa subscrição. Se tentar criar a primeira Cache Azure para Redis numa subscrição utilizando o PowerShell, deve primeiro registar esse espaço com o seguinte comando; caso contrário, os cmdlets, tais como `New-AzRedisCache` e `Get-AzRedisCache` falham.
> 
> `Register-AzResourceProvider -ProviderNamespace "Microsoft.Cache"`
> 
> 

Para ver uma lista dos parâmetros disponíveis e suas descrições `New-AzRedisCache` para, executar o seguinte comando.

```azurepowershell
    PS C:\> Get-Help New-AzRedisCache -detailed

    NAME
        New-AzRedisCache

    SYNOPSIS
        Creates a new Azure Cache for Redis.


    SYNTAX
        New-AzRedisCache -Name <String> -ResourceGroupName <String> -Location <String> [-RedisVersion <String>]
        [-Size <String>] [-Sku <String>] [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort
        <Boolean>] [-ShardCount <Integer>] [-VirtualNetwork <String>] [-Subnet <String>] [-StaticIP <String>]
        [<CommonParameters>]


    DESCRIPTION
        The New-AzRedisCache cmdlet creates a new Azure Cache for Redis.


    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis to create.

        -ResourceGroupName <String>
            Name of resource group in which to create the Azure Cache for Redis.

        -Location <String>
            Location in which to create the Azure Cache for Redis.

        -RedisVersion <String>
            RedisVersion is deprecated and will be removed in future release.

        -Size <String>
            Size of the Azure Cache for Redis. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
            C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.

        -Sku <String>
            Sku of Azure Cache for Redis. The default value is Standard. Possible values are Basic, Standard and Premium.

        -MaxMemoryPolicy <String>
            The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
            MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}

        -RedisConfiguration <Hashtable>
            All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
            rdb-backup-frequency, maxmemory-reserved, maxmemory-policy, notify-keyspace-events, hash-max-ziplist-entries,
            hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries, zset-max-ziplist-value, databases.

        -EnableNonSslPort <Boolean>
            EnableNonSslPort is used by Azure Cache for Redis. If no value is provided, the default value is false and the
            non-SSL port will be disabled. Possible values are true and false.

        -ShardCount <Integer>
            The number of shards to create on a Premium Cluster Cache.

        -VirtualNetwork <String>
            The exact ARM resource ID of the virtual network to deploy the Azure Cache for Redis in. Example format: /subscriptions/{
            subid}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicNetwork/VirtualNetworks/{vnetName}

        -Subnet <String>
            Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network.

        -StaticIP <String>
            Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```

Para criar uma cache com parâmetros predefinidos, executar o seguinte comando.

```azurepowershell
    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US"
```

`ResourceGroupName`, `Name` e `Location` são necessários parâmetros, mas os restantes são opcionais e têm valores predefinidos. Executando o comando anterior cria uma Cache Standard SKU Azure para redis com o nome especificado, localização e grupo de recursos, que é de 1 GB de tamanho com a porta não-SSL desativada.

Para criar uma cache premium, especifique um tamanho de P1 (6 GB - 60 GB), P2 (13 GB - 130 GB), P3 (26 GB - 260 GB) ou P4 (53 GB - 530 GB). Para ativar o agrupamento, especifique uma contagem de fragmentos utilizando o `ShardCount` parâmetro. O exemplo a seguir cria uma cache premium P1 com 3 fragmentos. Uma cache premium P1 tem 6 GB de tamanho, e uma vez que especificamos três fragmentos o tamanho total é de 18 GB (3 x 6 GB).

```azurepowershell
    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P1 -ShardCount 3
```

Para especificar valores para o `RedisConfiguration` parâmetro, comparfique os valores no interior `{}` como pares de chaves/valor como `@{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}` . O exemplo a seguir cria uma cache padrão de 1 GB com `allkeys-random` política maxmemory e notificações keyspace configuradas com `KEA` . Para obter mais informações, consulte [as notificações keyspace (definições avançadas)](cache-configure.md#keyspace-notifications-advanced-settings) e [as políticas de Memória](cache-configure.md#memory-policies).

```azurepowershell
    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}
```

<a name="databases"></a>

## <a name="to-configure-the-databases-setting-during-cache-creation"></a>Para configurar a definição das bases de dados durante a criação de cache
A `databases` regulação só pode ser configurada durante a criação da cache. O exemplo a seguir cria uma cache P3 (26 GB) premium com 48 bases de dados utilizando o cmdlet [New-AzRedisCache.](https://docs.microsoft.com/powershell/module/az.rediscache/New-azRedisCache)

```azurepowershell
    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P3 -RedisConfiguration @{"databases" = "48"}
```

Para obter mais informações sobre a `databases` propriedade, consulte [a Cache Azure padrão para a configuração do servidor Redis](cache-configure.md#default-redis-server-configuration). Para obter mais informações sobre a criação de uma cache utilizando o cmdlet [New-AzRedisCache,](https://docs.microsoft.com/powershell/module/az.rediscache/new-azrediscache) consulte o anterior Para criar uma cache Azure para a secção Redis.

## <a name="to-update-an-azure-cache-for-redis"></a>Para atualizar um Cache Azure para Redis
A azure cache para instâncias Redis são atualizados usando o [cmdlet Set-AzRedisCache.](https://docs.microsoft.com/powershell/module/az.rediscache/Set-azRedisCache)

Para ver uma lista dos parâmetros disponíveis e suas descrições `Set-AzRedisCache` para, executar o seguinte comando.

```azurepowershell
    PS C:\> Get-Help Set-AzRedisCache -detailed

    NAME
        Set-AzRedisCache

    SYNOPSIS
        Set Azure Cache for Redis updatable parameters.

    SYNTAX
        Set-AzRedisCache -Name <String> -ResourceGroupName <String> [-Size <String>] [-Sku <String>]
        [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort <Boolean>] [-ShardCount
        <Integer>] [<CommonParameters>]

    DESCRIPTION
        The Set-AzRedisCache cmdlet sets Azure Cache for Redis parameters.

    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis to update.

        -ResourceGroupName <String>
            Name of the resource group for the cache.

        -Size <String>
            Size of the Azure Cache for Redis. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
            C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.

        -Sku <String>
            Sku of Azure Cache for Redis. The default value is Standard. Possible values are Basic, Standard and Premium.

        -MaxMemoryPolicy <String>
            The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
            MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}

        -RedisConfiguration <Hashtable>
            All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
            rdb-backup-frequency, maxmemory-reserved, maxmemory-policy, notify-keyspace-events, hash-max-ziplist-entries,
            hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries, zset-max-ziplist-value.

        -EnableNonSslPort <Boolean>
            EnableNonSslPort is used by Azure Cache for Redis. The default value is null and no change will be made to the
            currently configured value. Possible values are true and false.

        -ShardCount <Integer>
            The number of shards to create on a Premium Cluster Cache.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```

O `Set-AzRedisCache` cmdlet pode ser usado para atualizar propriedades `Size` `Sku` como, , `EnableNonSslPort` , e os `RedisConfiguration` valores. 

O comando seguinte atualiza a política maxmemory para a Cache Azure para Redis chamada myCache.

```azurepowershell
    Set-AzRedisCache -ResourceGroupName "myGroup" -Name "myCache" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random"}
```

<a name="scale"></a>

## <a name="to-scale-an-azure-cache-for-redis"></a>Para escalar uma Cache Azure para Redis
`Set-AzRedisCache` pode ser usado para escalar uma Cache Azure para a instância Redis quando o `Size` , ou propriedades são `Sku` `ShardCount` modificados. 

> [!NOTE]
> A escala de uma cache utilizando o PowerShell está sujeita aos mesmos limites e diretrizes que a escalonamento de uma cache a partir do portal Azure. Pode escalar para um nível de preços diferente com as seguintes restrições.
> 
> * Não se pode escalar de um nível de preços mais elevado para um nível de preços mais baixo.
> * Não se pode escalar de uma cache **Premium** até um **Standard** ou uma cache **Básica.**
> * Não se pode escalar de uma cache **standard** até uma cache **básica.**
> * Pode escalar de uma cache **Basic** para uma cache **Standard,** mas não pode mudar o tamanho ao mesmo tempo. Se precisar de um tamanho diferente, pode fazer uma operação de escala subsequente ao tamanho pretendido.
> * Não se pode escalar de uma cache **básica** diretamente para uma cache **Premium.** Você deve escalar de **Básico** para **Standard** em uma operação de escala e, em seguida, de **Standard** para **Premium** em uma operação de escala subsequente.
> * Não é possível escalar de um tamanho maior até ao tamanho **C0 (250 MB).**
> 
> Para obter mais informações, consulte [Como Escalar Azure Cache para Redis](cache-how-to-scale.md).
> 
> 

O exemplo a seguir mostra como escalar uma cache chamada `myCache` para uma cache de 2,5 GB. Note que este comando funciona tanto para uma cache Basic ou standard.

```azurepowershell
    Set-AzRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB
```

Após a emissão deste comando, o estado da cache é devolvido (semelhante à `Get-AzRedisCache` chamada). Note que `ProvisioningState` o é `Scaling` .

```azurepowershell
    PS C:\> Set-AzRedisCache -Name myCache -ResourceGroupName myGroup -Size 2.5GB


    Name               : mycache
    Id                 : /subscriptions/12ad12bd-abdc-2231-a2ed-a2b8b246bbad4/resourceGroups/mygroup/providers/Mi
                         crosoft.Cache/Redis/mycache
    Location           : South Central US
    Type               : Microsoft.Cache/Redis
    HostName           : mycache.redis.cache.windows.net
    Port               : 6379
    ProvisioningState  : Scaling
    SslPort            : 6380
    RedisConfiguration : {[maxmemory-policy, volatile-lru], [maxmemory-reserved, 150], [notify-keyspace-events, KEA],
                         [maxmemory-delta, 150]...}
    EnableNonSslPort   : False
    RedisVersion       : 3.0
    Size               : 1GB
    Sku                : Standard
    ResourceGroupName  : mygroup
    PrimaryKey         : ....
    SecondaryKey       : ....
    VirtualNetwork     :
    Subnet             :
    StaticIP           :
    TenantSettings     : {}
    ShardCount         :
```

Quando a operação de escala está concluída, a `ProvisioningState` operação muda para `Succeeded` . Se precisar de fazer uma operação de escala subsequente, como mudar de Base para Standard e depois alterar o tamanho, deve esperar até que a operação anterior esteja concluída ou receber um erro semelhante ao seguinte.

```azurepowershell
    Set-AzRedisCache : Conflict: The resource '...' is not in a stable state, and is currently unable to accept the update request.
```

## <a name="to-get-information-about-an-azure-cache-for-redis"></a>Para obter informações sobre um Cache Azure para Redis
Pode obter informações sobre uma cache utilizando o [cmdlet Get-AzRedisCache.](https://docs.microsoft.com/powershell/module/az.rediscache/get-azrediscache)

Para ver uma lista dos parâmetros disponíveis e suas descrições `Get-AzRedisCache` para, executar o seguinte comando.

```azurepowershell
    PS C:\> Get-Help Get-AzRedisCache -detailed

    NAME
        Get-AzRedisCache

    SYNOPSIS
        Gets details about a single cache or all caches in the specified resource group or all caches in the current
        subscription.

    SYNTAX
        Get-AzRedisCache [-Name <String>] [-ResourceGroupName <String>] [<CommonParameters>]

    DESCRIPTION
        The Get-AzRedisCache cmdlet gets the details about a cache or caches depending on input parameters. If both
        ResourceGroupName and Name parameters are provided then Get-AzRedisCache will return details about the
        specific cache name provided.

        If only ResourceGroupName is provided than it will return details about all caches in the specified resource group.

        If no parameters are given than it will return details about all caches the current subscription.

    PARAMETERS
        -Name <String>
            The name of the cache. When this parameter is provided along with ResourceGroupName, Get-AzRedisCache
            returns the details for the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache or caches. If ResourceGroupName is provided with Name
            then Get-AzRedisCache returns the details of the cache specified by Name. If only the ResourceGroup
            parameter is provided, then details for all caches in the resource group are returned.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```

Para devolver informações sobre todos os caches na subscrição atual, corra `Get-AzRedisCache` sem parâmetros.

```azurepowershell
    Get-AzRedisCache
```

Para devolver informações sobre todas as caches de um grupo de recursos específicos, corra `Get-AzRedisCache` com o `ResourceGroupName` parâmetro.

```azurepowershell
    Get-AzRedisCache -ResourceGroupName myGroup
```

Para devolver informações sobre uma cache específica, corra `Get-AzRedisCache` com o parâmetro que contém o nome da `Name` cache, e o parâmetro com o `ResourceGroupName` grupo de recursos que contém essa cache.

```azurepowershell
    PS C:\> Get-AzRedisCache -Name myCache -ResourceGroupName myGroup

    Name               : mycache
    Id                 : /subscriptions/12ad12bd-abdc-2231-a2ed-a2b8b246bbad4/resourceGroups/myGroup/providers/Mi
                         crosoft.Cache/Redis/mycache
    Location           : South Central US
    Type               : Microsoft.Cache/Redis
    HostName           : mycache.redis.cache.windows.net
    Port               : 6379
    ProvisioningState  : Succeeded
    SslPort            : 6380
    RedisConfiguration : {[maxmemory-policy, volatile-lru], [maxmemory-reserved, 62], [notify-keyspace-events, KEA],
                         [maxclients, 1000]...}
    EnableNonSslPort   : False
    RedisVersion       : 3.0
    Size               : 1GB
    Sku                : Standard
    ResourceGroupName  : myGroup
    VirtualNetwork     :
    Subnet             :
    StaticIP           :
    TenantSettings     : {}
    ShardCount         :
```

## <a name="to-retrieve-the-access-keys-for-an-azure-cache-for-redis"></a>Para recuperar as chaves de acesso de um Cache Azure para Redis
Para recuperar as teclas de acesso para o seu cache, pode utilizar o cmdlet [Get-AzRedisCacheKey.](https://docs.microsoft.com/powershell/module/az.rediscache/Get-azRedisCacheKey)

Para ver uma lista dos parâmetros disponíveis e suas descrições `Get-AzRedisCacheKey` para, executar o seguinte comando.

```azurepowershell
    PS C:\> Get-Help Get-AzRedisCacheKey -detailed

    NAME
        Get-AzRedisCacheKey

    SYNOPSIS
        Gets the accesskeys for the specified Azure Cache for Redis.


    SYNTAX
        Get-AzRedisCacheKey -Name <String> -ResourceGroupName <String> [<CommonParameters>]

    DESCRIPTION
        The Get-AzRedisCacheKey cmdlet gets the access keys for the specified cache.

    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis.

        -ResourceGroupName <String>
            Name of the resource group for the cache.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```

Para recuperar as chaves do seu cache, ligue para o `Get-AzRedisCacheKey` cmdlet e passe em nome da sua cache o nome do grupo de recursos que contém a cache.

```azurepowershell
    PS C:\> Get-AzRedisCacheKey -Name myCache -ResourceGroupName myGroup

    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : ABhfB757JgjIgt785JgKH9865eifmekfnn649303JKL=
```

## <a name="to-regenerate-access-keys-for-your-azure-cache-for-redis"></a>Para regenerar as chaves de acesso para o seu Azure Cache para Redis
Para regenerar as teclas de acesso para o seu cache, pode utilizar o cmdlet [New-AzRedisCacheKey.](https://docs.microsoft.com/powershell/module/az.rediscache/New-azRedisCacheKey)

Para ver uma lista dos parâmetros disponíveis e suas descrições `New-AzRedisCacheKey` para, executar o seguinte comando.

```azurepowershell
    PS C:\> Get-Help New-AzRedisCacheKey -detailed

    NAME
        New-AzRedisCacheKey

    SYNOPSIS
        Regenerates the access key of an Azure Cache for Redis.

    SYNTAX
        New-AzRedisCacheKey -Name <String> -ResourceGroupName <String> -KeyType <String> [-Force] [<CommonParameters>]

    DESCRIPTION
        The New-AzRedisCacheKey cmdlet regenerate the access key of an Azure Cache for Redis.

    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis.

        -ResourceGroupName <String>
            Name of the resource group for the cache.

        -KeyType <String>
            Specifies whether to regenerate the primary or secondary access key. Possible values are Primary or Secondary.

        -Force
            When the Force parameter is provided, the specified access key is regenerated without any confirmation prompts.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```

Para regenerar a chave primária ou secundária para a sua cache, ligue para o `New-AzRedisCacheKey` cmdlet e passe no nome, grupo de recursos e especifique ou `Primary` `Secondary` para o `KeyType` parâmetro. No exemplo seguinte, a chave de acesso secundário para uma cache é regenerada.

```azurepowershell
    PS C:\> New-AzRedisCacheKey -Name myCache -ResourceGroupName myGroup -KeyType Secondary

    Confirm
    Are you sure you want to regenerate Secondary key for Azure Cache for Redis 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : c53hj3kh4jhHjPJk8l0jji785JgKH9865eifmekfnn6=
```

## <a name="to-delete-an-azure-cache-for-redis"></a>Para apagar uma cache Azure para Redis
Para eliminar uma cache Azure para Redis, utilize o [cmdlet Remove-AzRedisCache.](https://docs.microsoft.com/powershell/module/az.rediscache/remove-azrediscache)

Para ver uma lista dos parâmetros disponíveis e suas descrições `Remove-AzRedisCache` para, executar o seguinte comando.

```azurepowershell
    PS C:\> Get-Help Remove-AzRedisCache -detailed

    NAME
        Remove-AzRedisCache

    SYNOPSIS
        Remove Azure Cache for Redis if exists.

    SYNTAX
        Remove-AzRedisCache -Name <String> -ResourceGroupName <String> [-Force] [-PassThru] [<CommonParameters>

    DESCRIPTION
        The Remove-AzRedisCache cmdlet removes an Azure Cache for Redis if it exists.

    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis to remove.

        -ResourceGroupName <String>
            Name of the resource group of the cache to remove.

        -Force
            When the Force parameter is provided, the cache is removed without any confirmation prompts.

        -PassThru
            By default Remove-AzRedisCache removes the cache and does not return any value. If the PassThru par
            is provided then Remove-AzRedisCache returns a boolean value indicating the success of the operatio

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```

No exemplo seguinte, a cache nomeada `myCache` é removida.

```azurepowershell
    PS C:\> Remove-AzRedisCache -Name myCache -ResourceGroupName myGroup

    Confirm
    Are you sure you want to remove Azure Cache for Redis 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
```


## <a name="to-import-an-azure-cache-for-redis"></a>Para importar um Cache Azure para Redis
Pode importar dados para uma cache Azure para o caso Redis usando o `Import-AzRedisCache` cmdlet.

> [!IMPORTANT]
> A importação/exportação só está disponível para caches [de nível Premium.](cache-overview.md#service-tiers) Para obter mais informações sobre importação/exportação, consulte [os dados relativos à importação e exportação em Azure Cache para Redis](cache-how-to-import-export-data.md).
> 
> 

Para ver uma lista dos parâmetros disponíveis e suas descrições `Import-AzRedisCache` para, executar o seguinte comando.

```azurepowershell
    PS C:\> Get-Help Import-AzRedisCache -detailed

    NAME
        Import-AzRedisCache

    SYNOPSIS
        Import data from blobs to Azure Cache for Redis.


    SYNTAX
        Import-AzRedisCache -Name <String> -ResourceGroupName <String> -Files <String[]> [-Format <String>] [-Force]
        [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Import-AzRedisCache cmdlet imports data from the specified blobs into Azure Cache for Redis.


    PARAMETERS
        -Name <String>
            The name of the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache.

        -Files <String[]>
            SAS urls of blobs whose content should be imported into the cache.

        -Format <String>
            Format for the blob.  Currently "rdb" is the only supported, with other formats expected in the future.

        -Force
            When the Force parameter is provided, import will be performed without any confirmation prompts.

        -PassThru
            By default Import-AzRedisCache imports data in cache and does not return any value. If the PassThru
            parameter is provided then Import-AzRedisCache returns a boolean value indicating the success of the
            operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```


O comando seguinte importa dados da bolha especificada pelo SAS uri para Azure Cache para Redis.

```azurepowershell
    PS C:\>Import-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Files @("https://mystorageaccount.blob.core.windows.net/mycontainername/blobname?sv=2015-04-05&sr=b&sig=caIwutG2uDa0NZ8mjdNJdgOY8%2F8mhwRuGNdICU%2B0pI4%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwd") -Force
```

## <a name="to-export-an-azure-cache-for-redis"></a>Para exportar um Cache Azure para Redis
Pode exportar dados de uma cache Azure para a instância Redis utilizando o `Export-AzRedisCache` cmdlet.

> [!IMPORTANT]
> A importação/exportação só está disponível para caches [de nível Premium.](cache-overview.md#service-tiers) Para obter mais informações sobre importação/exportação, consulte [os dados relativos à importação e exportação em Azure Cache para Redis](cache-how-to-import-export-data.md).
> 
> 

Para ver uma lista dos parâmetros disponíveis e suas descrições `Export-AzRedisCache` para, executar o seguinte comando.

```azurepowershell
    PS C:\> Get-Help Export-AzRedisCache -detailed

    NAME
        Export-AzRedisCache

    SYNOPSIS
        Exports data from Azure Cache for Redis to a specified container.


    SYNTAX
        Export-AzRedisCache -Name <String> -ResourceGroupName <String> -Prefix <String> -Container <String> [-Format
        <String>] [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Export-AzRedisCache cmdlet exports data from Azure Cache for Redis to a specified container.


    PARAMETERS
        -Name <String>
            The name of the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache.

        -Prefix <String>
            Prefix to use for blob names.

        -Container <String>
            SAS url of container where data should be exported.

        -Format <String>
            Format for the blob.  Currently "rdb" is the only supported, with other formats expected in the future.

        -PassThru
            By default Export-AzRedisCache does not return any value. If the PassThru parameter is provided
            then Export-AzRedisCache returns a boolean value indicating the success of the operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```


O comando seguinte exporta dados de uma cache Azure para a instância Redis para o recipiente especificado pelo SAS uri.

```azurepowershell
    PS C:\>Export-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Prefix "blobprefix"
    -Container "https://mystorageaccount.blob.core.windows.net/mycontainer?sv=2015-04-05&sr=c&sig=HezZtBZ3DURmEGDduauE7
    pvETY4kqlPI8JCNa8ATmaw%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwdl"
```

## <a name="to-reboot-an-azure-cache-for-redis"></a>Para reiniciar um Cache Azure para Redis
Pode reiniciar o seu Cache Azure para a instância Redis utilizando o `Reset-AzRedisCache` cmdlet.

> [!IMPORTANT]
> O reboot só está disponível para caches [de nível Premium.](cache-overview.md#service-tiers) Para obter mais informações sobre o reinício do seu cache, consulte [a administração Cache - reinicie](cache-administration.md#reboot).
> 
> 

Para ver uma lista dos parâmetros disponíveis e suas descrições `Reset-AzRedisCache` para, executar o seguinte comando.

```azurepowershell
    PS C:\> Get-Help Reset-AzRedisCache -detailed

    NAME
        Reset-AzRedisCache

    SYNOPSIS
        Reboot specified node(s) of an Azure Cache for Redis instance.


    SYNTAX
        Reset-AzRedisCache -Name <String> -ResourceGroupName <String> -RebootType <String> [-ShardId <Integer>]
        [-Force] [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Reset-AzRedisCache cmdlet reboots the specified node(s) of an Azure Cache for Redis instance.


    PARAMETERS
        -Name <String>
            The name of the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache.

        -RebootType <String>
            Which node to reboot. Possible values are "PrimaryNode", "SecondaryNode", "AllNodes".

        -ShardId <Integer>
            Which shard to reboot when rebooting a premium cache with clustering enabled.

        -Force
            When the Force parameter is provided, reset will be performed without any confirmation prompts.

        -PassThru
            By default Reset-AzRedisCache does not return any value. If the PassThru parameter is provided
            then Reset-AzRedisCache returns a boolean value indicating the success of the operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```


O comando a seguir reinicia os dois nós da cache especificada.

```azurepowershell
    PS C:\>Reset-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -RebootType "AllNodes"
    -Force
```


## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre a utilização do Windows PowerShell com o Azure, consulte os seguintes recursos:

* [Cache Azure para documentação de cmdlet Redis sobre MSDN](https://docs.microsoft.com/powershell/module/az.rediscache)
* [Cmdlets Azure Resource Manager](https://go.microsoft.com/fwlink/?LinkID=394765): Aprenda a utilizar os cmdlets no módulo Azure Resource Manager.
* [Utilizar grupos de recursos para gerir os seus recursos Azure](../azure-resource-manager/templates/deploy-portal.md): Saiba como criar e gerir grupos de recursos no portal Azure.
* [Blog Azure](https://azure.microsoft.com/blog/): Conheça as novidades em Azure.
* [Blog Windows PowerShell](https://devblogs.microsoft.com/powershell/): Conheça as novas funcionalidades no Windows PowerShell.
* ["Ei, tipo dos guiões!" Blog](https://blogs.technet.microsoft.com/heyscriptingguy/author/the-scripting-guys/): Obtenha dicas e truques do mundo real da comunidade Windows PowerShell.

