---
title: Gerenciar o cache do Azure para Redis com o Azure PowerShell
description: Saiba como executar tarefas administrativas para o cache do Azure para Redis usando Azure PowerShell.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/13/2017
ms.author: yegu
ms.openlocfilehash: 6eeabb279f5bd5165ebb249651b8bc926deb92a6
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/28/2019
ms.locfileid: "75530923"
---
# <a name="manage-azure-cache-for-redis-with-azure-powershell"></a>Gerenciar o cache do Azure para Redis com o Azure PowerShell
> [!div class="op_single_selector"]
> * [PowerShell](cache-how-to-manage-redis-cache-powershell.md)
> * [CLI do Azure](cache-manage-cli.md)
> 
> 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Este tópico mostra como executar tarefas comuns, como criar, atualizar e dimensionar o cache do Azure para instâncias Redis, como regenerar chaves de acesso e como exibir informações sobre seus caches. Para obter uma lista completa do cache do Azure para cmdlets do PowerShell do Redis, confira [cache do Azure para cmdlets do Redis](https://docs.microsoft.com/powershell/module/az.rediscache).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

Para obter mais informações sobre o modelo de implantação clássico, consulte [Azure Resource Manager vs. implantação clássica: entender os modelos de implantação e o estado de seus recursos](../azure-resource-manager/resource-manager-deployment-model.md).

## <a name="prerequisites"></a>Pré-requisitos
Se você já tiver instalado o Azure PowerShell, deverá ter Azure PowerShell versão 1.0.0 ou posterior. Você pode verificar a versão do Azure PowerShell que você instalou com esse comando no prompt de comando do Azure PowerShell.

    Get-Module Az | format-table version


Primeiro, você deve fazer logon no Azure com este comando.

    Connect-AzAccount

Especifique o endereço de email da sua conta do Azure e sua senha na caixa de diálogo Microsoft Azure entrar.

Em seguida, se você tiver várias assinaturas do Azure, precisará definir sua assinatura do Azure. Para ver uma lista de suas assinaturas atuais, execute este comando.

    Get-AzSubscription | sort SubscriptionName | Select SubscriptionName

Para especificar a assinatura, execute o comando a seguir. No exemplo a seguir, o nome da assinatura é `ContosoSubscription`.

    Select-AzSubscription -SubscriptionName ContosoSubscription

Para poder usar o Windows PowerShell com Azure Resource Manager, você precisará do seguinte:

* Windows PowerShell, versão 3,0 ou 4,0. Para localizar a versão do Windows PowerShell, digite:`$PSVersionTable` e verifique se o valor de `PSVersion` é 3,0 ou 4,0. Para instalar uma versão compatível, consulte [Windows Management framework 3,0](https://www.microsoft.com/download/details.aspx?id=34595) ou [windows Management Framework 4,0](https://www.microsoft.com/download/details.aspx?id=40855).

Para obter ajuda detalhada para qualquer cmdlet que você vir neste tutorial, use o cmdlet Get-Help.

    Get-Help <cmdlet-name> -Detailed

Por exemplo, para obter ajuda para o cmdlet `New-AzRedisCache`, digite:

    Get-Help New-AzRedisCache -Detailed

### <a name="how-to-connect-to-other-clouds"></a>Como se conectar a outras nuvens
Por padrão, o ambiente do Azure é `AzureCloud`, que representa a instância global da nuvem do Azure. Para se conectar a uma instância diferente, use o comando `Connect-AzAccount` com a opção de linha de comando `-Environment` ou-`EnvironmentName` com o ambiente desejado ou o nome do ambiente.

Para ver a lista de ambientes disponíveis, execute o cmdlet `Get-AzEnvironment`.

### <a name="to-connect-to-the-azure-government-cloud"></a>Para se conectar à nuvem do Azure governamental
Para se conectar à nuvem do Azure governamental, use um dos comandos a seguir.

    Connect-AzAccount -EnvironmentName AzureUSGovernment

ou

    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureUSGovernment)

Para criar um cache na nuvem do Azure governamental, use um dos locais a seguir.

* USGov Virginia
* USGov Iowa

Para obter mais informações sobre a nuvem do Azure governamental, consulte [Guia do desenvolvedor](../azure-government-developer-guide.md)de [Microsoft Azure governamental](https://azure.microsoft.com/features/gov/) e Microsoft Azure governamental.

### <a name="to-connect-to-the-azure-china-cloud"></a>Para se conectar à nuvem do Azure na China
Para se conectar à nuvem do Azure na China, use um dos comandos a seguir.

    Connect-AzAccount -EnvironmentName AzureChinaCloud

ou

    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureChinaCloud)

Para criar um cache na nuvem do Azure na China, use um dos locais a seguir.

* Norte da China
* China Norte

Para obter mais informações sobre a nuvem do Azure na China, consulte [AzureChinaCloud for Azure operado pela 21vianet na China](https://www.windowsazure.cn/).

### <a name="to-connect-to-microsoft-azure-germany"></a>Para se conectar ao Microsoft Azure Alemanha
Para se conectar ao Microsoft Azure Alemanha, use um dos comandos a seguir.

    Connect-AzAccount -EnvironmentName AzureGermanCloud


ou

    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureGermanCloud)

Para criar um cache no Microsoft Azure Alemanha, use um dos seguintes locais.

* Nordeste da Alemanha
* Alemanha Nordeste

Para obter mais informações sobre Microsoft Azure Alemanha, consulte [Microsoft Azure Alemanha](https://azure.microsoft.com/overview/clouds/germany/).

### <a name="properties-used-for-azure-cache-for-redis-powershell"></a>Propriedades usadas para o cache do Azure para Redis PowerShell
A tabela a seguir contém propriedades e descrições para parâmetros usados com frequência ao criar e gerenciar o cache do Azure para instâncias Redis usando Azure PowerShell.

| Parâmetro | Descrição | Predefinição |
| --- | --- | --- |
| Nome |Nome do cache | |
| Localização |Local do cache | |
| ResourceGroupName |Nome do grupo de recursos no qual criar o cache | |
| Tamanho |O tamanho do cache. Os valores válidos são: P1, P2, P3, P4, C0, C1, C2, C3, C4, C5, C6, 250 MB, 1GB, 2,5 GB, 6 GB, 13GB, 26GB, 53GB |1GB |
| ShardCount |O número de fragmentos a serem criados ao criar um cache Premium com clustering habilitado. Os valores válidos são: 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 | |
| SKU |Especifica a SKU do cache. Os valores válidos são: básico, Standard, Premium |Padrão |
| RedisConfiguration |Especifica as definições de configuração do Redis. Para obter detalhes sobre cada configuração, consulte a tabela de [Propriedades RedisConfiguration](#redisconfiguration-properties) a seguir. | |
| EnableNonSslPort |Indica se a porta não SSL está habilitada. |Falso |
| MaxMemoryPolicy |Este parâmetro foi preterido-use RedisConfiguration em vez disso. | |
| StaticIP |Ao hospedar o cache em uma VNET, especifica um endereço IP exclusivo na sub-rede para o cache. Se não for fornecido, um será escolhido para você na sub-rede. | |
| Subrede |Ao hospedar o cache em uma VNET, especifica o nome da sub-rede na qual o cache será implantado. | |
| VirtualNetwork |Ao hospedar o cache em uma VNET, especifica a ID de recurso da VNET na qual o cache será implantado. | |
| KeyType |Especifica qual chave de acesso será regenerada ao renovar as chaves de acesso. Os valores válidos são: primário, secundário | |

### <a name="redisconfiguration-properties"></a>Propriedades de RedisConfiguration
| Propriedade | Descrição | Escalões de preço |
| --- | --- | --- |
| rdb-backup-enabled |Se a [persistência de dados do Redis](cache-how-to-premium-persistence.md) está habilitada |Somente Premium |
| rdb-storage-connection-string |A cadeia de conexão para a conta de armazenamento para [persistência de dados Redis](cache-how-to-premium-persistence.md) |Somente Premium |
| RDB-backup-Frequency |A frequência de backup para [persistência de dados Redis](cache-how-to-premium-persistence.md) |Somente Premium |
| maxmemory-reserved |Configura a [memória reservada](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) para processos que não são de cache |Standard e Premium |
| MaxMemory – política |Configura a política de [remoção](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) para o cache |Todos os tipos de preço |
| notificar-keyspace-eventos |Configura notificações de [keyspace](cache-configure.md#keyspace-notifications-advanced-settings) |Standard e Premium |
| hash-max-ziplist-entries |Configura a [otimização de memória](https://redis.io/topics/memory-optimization) para tipos de dados agregados pequenos |Standard e Premium |
| hash-max-ziplist-value |Configura a [otimização de memória](https://redis.io/topics/memory-optimization) para tipos de dados agregados pequenos |Standard e Premium |
| set-max-intset-entries |Configura a [otimização de memória](https://redis.io/topics/memory-optimization) para tipos de dados agregados pequenos |Standard e Premium |
| zset-max-ziplist-entries |Configura a [otimização de memória](https://redis.io/topics/memory-optimization) para tipos de dados agregados pequenos |Standard e Premium |
| zset-max-ziplist-value |Configura a [otimização de memória](https://redis.io/topics/memory-optimization) para tipos de dados agregados pequenos |Standard e Premium |
| databases |Configura o número de bancos de dados. Essa propriedade pode ser configurada somente na criação do cache. |Standard e Premium |

## <a name="to-create-an-azure-cache-for-redis"></a>Para criar um cache do Azure para Redis
O novo cache do Azure para instâncias Redis são criados usando o cmdlet [New-AzRedisCache](https://docs.microsoft.com/powershell/module/az.rediscache/new-azrediscache) .

> [!IMPORTANT]
> Na primeira vez que você criar um cache do Azure para Redis em uma assinatura usando o portal do Azure, o portal registrará o namespace `Microsoft.Cache` para essa assinatura. Se você tentar criar o primeiro cache do Azure para Redis em uma assinatura usando o PowerShell, deverá primeiro registrar esse namespace usando o comando a seguir; caso contrário, cmdlets como `New-AzRedisCache` e `Get-AzRedisCache` falharão.
> 
> `Register-AzResourceProvider -ProviderNamespace "Microsoft.Cache"`
> 
> 

Para ver uma lista de parâmetros disponíveis e suas descrições para `New-AzRedisCache`, execute o comando a seguir.

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

Para criar um cache com parâmetros padrão, execute o comando a seguir.

    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US"

`ResourceGroupName`, `Name`e `Location` são parâmetros obrigatórios, mas o restante são opcionais e têm valores padrão. A execução do comando anterior cria um cache de SKU do Azure padrão para a instância Redis com o nome, o local e o grupo de recursos especificados, que tem 1 GB de tamanho com a porta não SSL desabilitada.

Para criar um cache Premium, especifique um tamanho de P1 (6 GB-60 GB), P2 (13 GB-130 GB), P3 (26 GB-260 GB) ou P4 (53 GB-530 GB). Para habilitar o clustering, especifique uma contagem de fragmentos usando o parâmetro `ShardCount`. O exemplo a seguir cria um cache Premium P1 com 3 fragmentos. Um cache Premium P1 tem 6 GB de tamanho e, como especificamos três fragmentos, o tamanho total é de 18 GB (3 x 6 GB).

    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P1 -ShardCount 3

Para especificar valores para o parâmetro `RedisConfiguration`, coloque os valores dentro de `{}` como pares de chave/valor como `@{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}`. O exemplo a seguir cria um cache padrão de 1 GB com `allkeys-random` a política MaxMemory e as notificações de keyspace configuradas com `KEA`. Para obter mais informações, consulte [notificações de keyspace (configurações avançadas)](cache-configure.md#keyspace-notifications-advanced-settings) e [políticas de memória](cache-configure.md#memory-policies).

    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}

<a name="databases"></a>

## <a name="to-configure-the-databases-setting-during-cache-creation"></a>Para configurar a configuração de bancos de dados durante a criação do cache
A configuração `databases` pode ser configurada somente durante a criação do cache. O exemplo a seguir cria um cache Premium P3 (26 GB) com 48 bancos de dados usando o cmdlet [New-AzRedisCache](https://docs.microsoft.com/powershell/module/az.rediscache/New-azRedisCache) .

    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P3 -RedisConfiguration @{"databases" = "48"}

Para obter mais informações sobre a propriedade `databases`, consulte [cache do Azure padrão para configuração do servidor Redis](cache-configure.md#default-redis-server-configuration). Para obter mais informações sobre como criar um cache usando o cmdlet [New-AzRedisCache](https://docs.microsoft.com/powershell/module/az.rediscache/new-azrediscache) , consulte a seção anterior para criar um cache do Azure para Redis.

## <a name="to-update-an-azure-cache-for-redis"></a>Para atualizar um cache do Azure para Redis
O cache do Azure para instâncias Redis são atualizados usando o cmdlet [set-AzRedisCache](https://docs.microsoft.com/powershell/module/az.rediscache/Set-azRedisCache) .

Para ver uma lista de parâmetros disponíveis e suas descrições para `Set-AzRedisCache`, execute o comando a seguir.

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

O cmdlet `Set-AzRedisCache` pode ser usado para atualizar propriedades como `Size`, `Sku`, `EnableNonSslPort`e os valores de `RedisConfiguration`. 

O comando a seguir atualiza o MaxMemory-Policy para o cache do Azure para Redis chamado myCache.

    Set-AzRedisCache -ResourceGroupName "myGroup" -Name "myCache" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random"}

<a name="scale"></a>

## <a name="to-scale-an-azure-cache-for-redis"></a>Para dimensionar um cache do Azure para Redis
`Set-AzRedisCache` pode ser usado para dimensionar um cache do Azure para instância Redis quando as propriedades `Size`, `Sku`ou `ShardCount` são modificadas. 

> [!NOTE]
> O dimensionamento de um cache usando o PowerShell está sujeito aos mesmos limites e diretrizes que o dimensionamento de um cache do portal do Azure. Você pode dimensionar para um tipo de preço diferente com as restrições a seguir.
> 
> * Não é possível dimensionar de um tipo de preço mais alto para um tipo de preço mais baixo.
> * Não é possível dimensionar de um cache **Premium** para um cache **padrão** ou **básico** .
> * Não é possível dimensionar de um cache **padrão** para um cache **básico** .
> * Você pode dimensionar de um cache **básico** para um cache **padrão** , mas não pode alterar o tamanho ao mesmo tempo. Se você precisar de um tamanho diferente, poderá fazer uma operação de dimensionamento subsequente para o tamanho desejado.
> * Não é possível dimensionar de um cache **básico** diretamente para um cache **Premium** . Você deve dimensionar de **básico** para **Standard** em uma operação de dimensionamento e, em seguida, de **Standard** para **Premium** em uma operação de dimensionamento subsequente.
> * Não é possível dimensionar de um tamanho maior para o tamanho de **C0 (250 MB)** .
> 
> Para obter mais informações, consulte [como dimensionar o cache do Azure para Redis](cache-how-to-scale.md).
> 
> 

O exemplo a seguir mostra como dimensionar um cache chamado `myCache` para um cache de 2,5 GB. Observe que esse comando funciona para um cache básico ou Standard.

    Set-AzRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB

Depois que esse comando é emitido, o status do cache é retornado (semelhante à chamada de `Get-AzRedisCache`). Observe que o `ProvisioningState` é `Scaling`.

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

Quando a operação de dimensionamento for concluída, o `ProvisioningState` será alterado para `Succeeded`. Se você precisar fazer uma operação de dimensionamento subsequente, como alterar de básico para padrão e, em seguida, alterar o tamanho, deverá aguardar até que a operação anterior seja concluída ou receber um erro semelhante ao seguinte.

    Set-AzRedisCache : Conflict: The resource '...' is not in a stable state, and is currently unable to accept the update request.

## <a name="to-get-information-about-an-azure-cache-for-redis"></a>Para obter informações sobre um cache do Azure para Redis
Você pode recuperar informações sobre um cache usando o cmdlet [Get-AzRedisCache](https://docs.microsoft.com/powershell/module/az.rediscache/get-azrediscache) .

Para ver uma lista de parâmetros disponíveis e suas descrições para `Get-AzRedisCache`, execute o comando a seguir.

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

Para retornar informações sobre todos os caches na assinatura atual, execute `Get-AzRedisCache` sem nenhum parâmetro.

    Get-AzRedisCache

Para retornar informações sobre todos os caches em um grupo de recursos específico, execute `Get-AzRedisCache` com o parâmetro `ResourceGroupName`.

    Get-AzRedisCache -ResourceGroupName myGroup

Para retornar informações sobre um cache específico, execute `Get-AzRedisCache` com o parâmetro `Name` que contém o nome do cache e o parâmetro `ResourceGroupName` com o grupo de recursos que contém esse cache.

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

## <a name="to-retrieve-the-access-keys-for-an-azure-cache-for-redis"></a>Para recuperar as chaves de acesso para um cache do Azure para Redis
Para recuperar as chaves de acesso para seu cache, você pode usar o cmdlet [Get-AzRedisCacheKey](https://docs.microsoft.com/powershell/module/az.rediscache/Get-azRedisCacheKey) .

Para ver uma lista de parâmetros disponíveis e suas descrições para `Get-AzRedisCacheKey`, execute o comando a seguir.

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

Para recuperar as chaves do seu cache, chame o cmdlet `Get-AzRedisCacheKey` e passe o nome do seu cache o nome do grupo de recursos que contém o cache.

    PS C:\> Get-AzRedisCacheKey -Name myCache -ResourceGroupName myGroup

    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : ABhfB757JgjIgt785JgKH9865eifmekfnn649303JKL=

## <a name="to-regenerate-access-keys-for-your-azure-cache-for-redis"></a>Para regenerar chaves de acesso para o cache do Azure para Redis
Para regenerar as chaves de acesso para seu cache, você pode usar o cmdlet [New-AzRedisCacheKey](https://docs.microsoft.com/powershell/module/az.rediscache/New-azRedisCacheKey) .

Para ver uma lista de parâmetros disponíveis e suas descrições para `New-AzRedisCacheKey`, execute o comando a seguir.

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

Para regenerar a chave primária ou secundária para o cache, chame o cmdlet `New-AzRedisCacheKey` e passe o nome, o grupo de recursos e especifique `Primary` ou `Secondary` para o parâmetro `KeyType`. No exemplo a seguir, a chave de acesso secundária para um cache é regenerada.

    PS C:\> New-AzRedisCacheKey -Name myCache -ResourceGroupName myGroup -KeyType Secondary

    Confirm
    Are you sure you want to regenerate Secondary key for Azure Cache for Redis 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : c53hj3kh4jhHjPJk8l0jji785JgKH9865eifmekfnn6=

## <a name="to-delete-an-azure-cache-for-redis"></a>Para excluir um cache do Azure para Redis
Para excluir um cache do Azure para Redis, use o cmdlet [Remove-AzRedisCache](https://docs.microsoft.com/powershell/module/az.rediscache/remove-azrediscache) .

Para ver uma lista de parâmetros disponíveis e suas descrições para `Remove-AzRedisCache`, execute o comando a seguir.

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

No exemplo a seguir, o cache chamado `myCache` é removido.

    PS C:\> Remove-AzRedisCache -Name myCache -ResourceGroupName myGroup

    Confirm
    Are you sure you want to remove Azure Cache for Redis 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


## <a name="to-import-an-azure-cache-for-redis"></a>Para importar um cache do Azure para Redis
Você pode importar dados para um cache do Azure para a instância Redis usando o cmdlet `Import-AzRedisCache`.

> [!IMPORTANT]
> A importação/exportação só está disponível para caches da [camada Premium](cache-premium-tier-intro.md) . Para obter mais informações sobre importação/exportação, consulte [importar e exportar dados no cache do Azure para Redis](cache-how-to-import-export-data.md).
> 
> 

Para ver uma lista de parâmetros disponíveis e suas descrições para `Import-AzRedisCache`, execute o comando a seguir.

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


O comando a seguir importa dados do blob especificado pelo URI de SAS para o cache do Azure para Redis.

    PS C:\>Import-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Files @("https://mystorageaccount.blob.core.windows.net/mycontainername/blobname?sv=2015-04-05&sr=b&sig=caIwutG2uDa0NZ8mjdNJdgOY8%2F8mhwRuGNdICU%2B0pI4%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwd") -Force

## <a name="to-export-an-azure-cache-for-redis"></a>Para exportar um cache do Azure para Redis
Você pode exportar dados de um cache do Azure para a instância do Redis usando o cmdlet `Export-AzRedisCache`.

> [!IMPORTANT]
> A importação/exportação só está disponível para caches da [camada Premium](cache-premium-tier-intro.md) . Para obter mais informações sobre importação/exportação, consulte [importar e exportar dados no cache do Azure para Redis](cache-how-to-import-export-data.md).
> 
> 

Para ver uma lista de parâmetros disponíveis e suas descrições para `Export-AzRedisCache`, execute o comando a seguir.

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


O comando a seguir exporta dados de um cache do Azure para a instância Redis para o contêiner especificado pelo URI de SAS.

        PS C:\>Export-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Prefix "blobprefix"
        -Container "https://mystorageaccount.blob.core.windows.net/mycontainer?sv=2015-04-05&sr=c&sig=HezZtBZ3DURmEGDduauE7
        pvETY4kqlPI8JCNa8ATmaw%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwdl"

## <a name="to-reboot-an-azure-cache-for-redis"></a>Para reinicializar um cache do Azure para Redis
Você pode reinicializar o cache do Azure para a instância Redis usando o cmdlet `Reset-AzRedisCache`.

> [!IMPORTANT]
> A reinicialização só está disponível para caches da [camada Premium](cache-premium-tier-intro.md) . Para obter mais informações sobre como reinicializar o cache, consulte [Administração de cache – reinicialização](cache-administration.md#reboot).
> 
> 

Para ver uma lista de parâmetros disponíveis e suas descrições para `Reset-AzRedisCache`, execute o comando a seguir.

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


O comando a seguir reinicia ambos os nós do cache especificado.

        PS C:\>Reset-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -RebootType "AllNodes"
        -Force


## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre como usar o Windows PowerShell com o Azure, confira os seguintes recursos:

* [Documentação do cmdlet do cache do Azure para Redis no MSDN](https://docs.microsoft.com/powershell/module/az.rediscache)
* [Azure Resource Manager cmdlets](https://go.microsoft.com/fwlink/?LinkID=394765): Aprenda a usar os cmdlets no módulo Azure Resource Manager.
* [Usando grupos de recursos para gerenciar seus recursos do Azure](../azure-resource-manager/resource-group-template-deploy-portal.md): saiba como criar e gerenciar grupos de recursos no portal do Azure.
* [Blog do Azure](https://azure.microsoft.com/blog/): Saiba mais sobre os novos recursos no Azure.
* [Blog do Windows PowerShell](https://blogs.msdn.com/powershell): Saiba mais sobre os novos recursos do Windows PowerShell.
* ["Ei, equipe de scripts!" Blog](https://blogs.technet.com/b/heyscriptingguy/): obtenha dicas e truques do mundo real da Comunidade do Windows PowerShell.

