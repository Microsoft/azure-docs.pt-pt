---
title: Gerir a Cache do Azure para Redis com o Azure PowerShell | Documentos da Microsoft
description: Saiba como executar tarefas administrativas para o Azure Cache de Redis com o Azure PowerShell.
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 1136efe5-1e33-4d91-bb49-c8e2a6dca475
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: yegu
ms.openlocfilehash: f7f4f9ae6a80052e06b2cafa68cb5c11dfa1333a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62097934"
---
# <a name="manage-azure-cache-for-redis-with-azure-powershell"></a>Gerir a Cache do Azure para Redis com o Azure PowerShell
> [!div class="op_single_selector"]
> * [PowerShell](cache-howto-manage-redis-cache-powershell.md)
> * [CLI do Azure](cache-manage-cli.md)
> 
> 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Este tópico mostra-lhe como realizar tarefas comuns, como de criar, atualizar e dimensionar a sua Cache do Azure para Redis instâncias, como voltar a gerar chaves de acesso e como visualizar informações sobre as caches. Para obter uma lista completa de Cache do Azure para os cmdlets do PowerShell de Redis, veja [Cache do Azure para cmdlets de Redis](https://docs.microsoft.com/powershell/module/az.rediscache).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

Para obter mais informações sobre o modelo de implementação clássica, consulte [vs. de implementação clássica do Azure Resource Manager: Compreender os modelos de implementação e o estado dos seus recursos](../azure-resource-manager/resource-manager-deployment-model.md).

## <a name="prerequisites"></a>Pré-requisitos
Se já tiver instalado o Azure PowerShell, tem de ter o Azure PowerShell versão 1.0.0 ou posterior. Pode verificar a versão do PowerShell do Azure que tenha instalado com o comando no prompt de comando do PowerShell do Azure.

    Get-Module Az | format-table version


Em primeiro lugar, tem de iniciar sessão Azure com este comando.

    Connect-AzAccount

Especifica o endereço de e-mail da sua conta do Azure e a respetiva palavra-passe no diálogo de início de sessão do Microsoft Azure.

Em seguida, se tiver várias subscrições do Azure, terá de definir a sua subscrição do Azure. Para ver uma lista das suas subscrições atuais, execute este comando.

    Get-AzSubscription | sort SubscriptionName | Select SubscriptionName

Para especificar a subscrição, execute o seguinte comando. No exemplo a seguir, é o nome da subscrição `ContosoSubscription`.

    Select-AzSubscription -SubscriptionName ContosoSubscription

Antes de poder utilizar o Windows PowerShell com o Azure Resource Manager, precisa do seguinte:

* Windows PowerShell, versão 3.0 ou 4.0. Para localizar a versão do Windows PowerShell, escreva:`$PSVersionTable` e verifique se o valor de `PSVersion` é a 3.0 ou 4.0. Para instalar uma versão compatível, consulte [Windows Management Framework 3.0](https://www.microsoft.com/download/details.aspx?id=34595) ou [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855).

Para obter ajuda detalhada para qualquer cmdlet que visualiza neste tutorial, utilize o cmdlet Get-Help.

    Get-Help <cmdlet-name> -Detailed

Por exemplo, para obter ajuda para o `New-AzRedisCache` cmdlet, tipo:

    Get-Help New-AzRedisCache -Detailed

### <a name="how-to-connect-to-other-clouds"></a>Como ligar a outras clouds
Por predefinição, o Azure é ambiente `AzureCloud`, que representa a instância de cloud do Azure global. Para ligar a uma instância diferente, utilize o `Connect-AzAccount` comando com o `-Environment` ou -`EnvironmentName` comutador de linha de comandos com o ambiente pretendido ou o nome de ambiente.

Para ver a lista de ambientes disponíveis, execute o `Get-AzEnvironment` cmdlet.

### <a name="to-connect-to-the-azure-government-cloud"></a>Para ligar à Cloud do Azure Government
Para ligar à Cloud do Azure Government, utilize um dos seguintes comandos.

    Connect-AzAccount -EnvironmentName AzureUSGovernment

ou

    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureUSGovernment)

Para criar uma cache na Cloud do Azure Government, utilize uma das seguintes localizações.

* Virgínia USGov
* USGov Iowa

Para obter mais informações sobre a Cloud do Azure Government, consulte [o Microsoft Azure Government](https://azure.microsoft.com/features/gov/) e [manual de programação do Microsoft Azure Government](../azure-government-developer-guide.md).

### <a name="to-connect-to-the-azure-china-cloud"></a>Para ligar à Cloud do Azure China
Para ligar para a Cloud do Azure na China, utilize um dos seguintes comandos.

    Connect-AzAccount -EnvironmentName AzureChinaCloud

ou

    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureChinaCloud)

Para criar uma cache na Cloud do Azure na China, utilize uma das seguintes localizações.

* Leste da China
* China Norte

Para obter mais informações sobre a Cloud do Azure na China, consulte [AzureChinaCloud para o Azure operado pela 21Vianet na China](http://www.windowsazure.cn/).

### <a name="to-connect-to-microsoft-azure-germany"></a>Para ligar ao Microsoft Azure Alemanha
Para ligar ao Microsoft Azure Alemanha, utilize um dos seguintes comandos.

    Connect-AzAccount -EnvironmentName AzureGermanCloud


ou

    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureGermanCloud)

Para criar uma cache no Microsoft Azure Alemanha, utilize uma das seguintes localizações.

* Alemanha Central
* Alemanha Nordeste

Para obter mais informações sobre o Microsoft Azure Alemanha, consulte [Microsoft Azure Alemanha](https://azure.microsoft.com/overview/clouds/germany/).

### <a name="properties-used-for-azure-cache-for-redis-powershell"></a>Propriedades utilizadas para a Cache do Azure para Redis PowerShell
A tabela seguinte contém as propriedades e as descrições para os parâmetros usadas quando criar e gerir a sua Cache do Azure para instâncias de Redis com o Azure PowerShell.

| Parâmetro | Descrição | Predefinição |
| --- | --- | --- |
| Name |Nome da cache | |
| Location |Localização da cache | |
| ResourceGroupName |Nome do grupo de recursos no qual pretende criar a cache | |
| Size |O tamanho da cache. Valores válidos são: P1, P2, P3, P4, C0, C1, C2, C3, C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB |1GB |
| ShardCount |O número de partições horizontais para criar quando criar uma cache premium com clustering ativado. Valores válidos são: 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 | |
| SKU |Especifica o SKU da cache. Valores válidos são: Básico, Standard e Premium |Standard |
| RedisConfiguration |Especifica as definições de configuração do Redis. Para obter detalhes sobre cada definição, consulte o seguinte procedimento [RedisConfiguration propriedades](#redisconfiguration-properties) tabela. | |
| EnableNonSslPort |Indica se a porta não SSL está ativada. |Falso |
| MaxMemoryPolicy |Este parâmetro foi preterido - utilizar RedisConfiguration em vez disso. | |
| StaticIP |Ao alojar a cache numa VNET, especifica um endereço IP exclusivo na sub-rede para a cache. Se não for indicado, um é escolhido por da sub-rede. | |
| Subnet |Ao alojar a cache numa VNET, especifica o nome da sub-rede na qual implementar a cache. | |
| VirtualNetwork |Ao alojar a cache numa VNET, especifica o ID de recurso da VNET na qual implementar a cache. | |
| KeyType |Especifica que a chave de acesso para voltar a gerar quando renovar teclas de acesso. Valores válidos são: Servidor primário, secundário | |

### <a name="redisconfiguration-properties"></a>Propriedades de RedisConfiguration
| Propriedade | Descrição | Escalões de preço |
| --- | --- | --- |
| rdb-backup-enabled |Se [persistência de dados de Redis](cache-how-to-premium-persistence.md) está ativada |Apenas Premium |
| rdb-storage-connection-string |A cadeia de ligação para a conta de armazenamento para [persistência de dados de Redis](cache-how-to-premium-persistence.md) |Apenas Premium |
| rdb-backup-frequency |A frequência de cópia de segurança de [persistência de dados de Redis](cache-how-to-premium-persistence.md) |Apenas Premium |
| maxmemory-reserved |Configura a [memória reservada](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) para processos de fora de cache |Standard e Premium |
| maxmemory-policy |Configura a [política de expulsão](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) para a cache |Todos os escalões de preço |
| notify-keyspace-events |Configura [notificações keyspace](cache-configure.md#keyspace-notifications-advanced-settings) |Standard e Premium |
| hash-max-ziplist-entries |Configura [otimização de memória](https://redis.io/topics/memory-optimization) para tipos de dados de agregação pequeno |Standard e Premium |
| hash-max-ziplist-value |Configura [otimização de memória](https://redis.io/topics/memory-optimization) para tipos de dados de agregação pequeno |Standard e Premium |
| set-max-intset-entries |Configura [otimização de memória](https://redis.io/topics/memory-optimization) para tipos de dados de agregação pequeno |Standard e Premium |
| zset-max-ziplist-entries |Configura [otimização de memória](https://redis.io/topics/memory-optimization) para tipos de dados de agregação pequeno |Standard e Premium |
| zset-max-ziplist-value |Configura [otimização de memória](https://redis.io/topics/memory-optimization) para tipos de dados de agregação pequeno |Standard e Premium |
| databases |Configura o número de bases de dados. Esta propriedade pode ser configurada apenas durante a criação de cache. |Standard e Premium |

## <a name="to-create-an-azure-cache-for-redis"></a>Para criar uma Cache do Azure para Redis
Nova Cache do Azure para instâncias de Redis são criadas com o [New-AzRedisCache](https://docs.microsoft.com/powershell/module/az.rediscache/new-azrediscache) cmdlet.

> [!IMPORTANT]
> Na primeira vez criar uma Cache do Azure para o Redis numa subscrição utilizando o portal do Azure, o portal regista o `Microsoft.Cache` espaço de nomes para essa subscrição. Se está tentando criar a Cache do Azure primeiro para o Redis numa subscrição utilizando o PowerShell, primeiro tem de registar nesse espaço de nomes com o seguinte comando; caso contrário, cmdlets, como `New-AzRedisCache` e `Get-AzRedisCache` falhar.
> 
> `Register-AzResourceProvider -ProviderNamespace "Microsoft.Cache"`
> 
> 

Para ver uma lista dos parâmetros disponíveis e suas descrições para `New-AzRedisCache`, execute o seguinte comando.

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

Para criar uma cache com parâmetros padrão, execute o seguinte comando.

    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US"

`ResourceGroupName`, `Name`, e `Location` são parâmetros necessários, mas os restantes são opcionais e têm valores predefinidos. Executar o comando anterior cria uma Cache do Azure de SKU padrão para a instância de Redis com o nome, a localização e o recurso grupo especificado, que é de 1 GB de tamanho com a porta não SSL desativado.

Para criar uma cache premium, especifique um tamanho de P1 (6 GB a 60 GB), P2 (13 GB 130 GB), P3 (26 GB 260 GB), ou P4 (53 GB a 530 GB). Para ativar o clustering, especifique uma contagem de partições horizontais utilizando o `ShardCount` parâmetro. O exemplo seguinte cria uma cache de premium P1 com partições 3 horizontais. Uma cache de premium P1 é 6 GB de tamanho, e como especificamos as partições três horizontais o tamanho total é 18 GB (3 x 6 GB).

    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P1 -ShardCount 3

Para especificar valores para o `RedisConfiguration` parâmetro, colocar os valores dentro `{}` como a chave/valor, como pares `@{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}`. O exemplo seguinte cria uma cache standard de 1 GB com `allkeys-random` notificações de keyspace e a política de maxmemory configuradas com `KEA`. Para obter mais informações, consulte [notificações Keyspace (definições avançadas)](cache-configure.md#keyspace-notifications-advanced-settings) e [políticas de memória](cache-configure.md#memory-policies).

    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}

<a name="databases"></a>

## <a name="to-configure-the-databases-setting-during-cache-creation"></a>Para configurar as bases de dados a definir durante a criação da cache
O `databases` definição pode ser configurada apenas durante a criação de cache. O exemplo seguinte cria uma premium P3 (26 GB) de cache com 48 bases de dados com o [New-AzRedisCache](https://docs.microsoft.com/powershell/module/az.rediscache/New-azRedisCache) cmdlet.

    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P3 -RedisConfiguration @{"databases" = "48"}

Para obter mais informações sobre o `databases` propriedade, veja [predefinido a Cache do Azure para a configuração do servidor Redis](cache-configure.md#default-redis-server-configuration). Para obter mais informações sobre como criar uma cache com o [New-AzRedisCache](https://docs.microsoft.com/powershell/module/az.rediscache/new-azrediscache) cmdlet, consulte o anterior para criar uma Cache do Azure para a secção de Redis.

## <a name="to-update-an-azure-cache-for-redis"></a>Para atualizar uma Cache do Azure para Redis
A Cache para instâncias de Redis do Azure são atualizados com o [Set-AzRedisCache](https://docs.microsoft.com/powershell/module/az.rediscache/Set-azRedisCache) cmdlet.

Para ver uma lista dos parâmetros disponíveis e suas descrições para `Set-AzRedisCache`, execute o seguinte comando.

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

O `Set-AzRedisCache` cmdlet pode ser utilizado para atualizar propriedades como `Size`, `Sku`, `EnableNonSslPort`e o `RedisConfiguration` valores. 

O seguinte comando atualiza a política de maxmemory para a Cache do Azure para Redis com o nome myCache.

    Set-AzRedisCache -ResourceGroupName "myGroup" -Name "myCache" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random"}

<a name="scale"></a>

## <a name="to-scale-an-azure-cache-for-redis"></a>Dimensionar uma Cache do Azure para Redis
`Set-AzRedisCache` pode ser usado para dimensionar um Cache do Azure para Redis instância quando a `Size`, `Sku`, ou `ShardCount` propriedades são modificadas. 

> [!NOTE]
> Dimensionar uma cache com o PowerShell está sujeita aos mesmos limites e diretrizes como no aumento de uma cache a partir do portal do Azure. Pode dimensionar para um escalão de preço diferente com as seguintes restrições.
> 
> * Não é possível dimensionar a de um escalão de preço mais elevado para um escalão de preço mais baixo.
> * Não é possível dimensionar de um **Premium** colocar em cache para um **padrão** ou uma **básica** cache.
> * Não é possível dimensionar de um **padrão** colocar em cache para um **básica** cache.
> * Pode dimensionar a partir de um **básica** colocar em cache para um **padrão** cache, mas não é possível alterar o tamanho, ao mesmo tempo. Se precisar de um tamanho diferente, pode fazer uma operação de dimensionamento subsequente para o tamanho pretendido.
> * Não é possível dimensionar de um **básica** colocar em cache diretamente para um **Premium** cache. Deve aumentar de **básica** para **padrão** numa única operação de dimensionamento e, em seguida, do **padrão** para **Premium** num dimensionamento subsequentes operação.
> * Não é possível dimensionar a partir de um tamanho maior para o **C0 (250 MB)** tamanho.
> 
> Para obter mais informações, consulte [como dimensionar a cache do Azure para Redis](cache-how-to-scale.md).
> 
> 

O exemplo seguinte mostra como dimensionar uma cache com o nome `myCache` a uma cache de 2,5 GB. Tenha em atenção que este comando funciona para um básico ou de uma cache Standard.

    Set-AzRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB

Depois deste comando é emitido, o estado da cache é devolvido (semelhante ao chamar `Get-AzRedisCache`). Tenha em atenção que o `ProvisioningState` é `Scaling`.

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

Quando a operação de dimensionamento estiver concluída, o `ProvisioningState` é alterado para `Succeeded`. Se precisar de fazer uma operação de dimensionamento subsequente, como alteração de básico para Standard e, em seguida, alterar o tamanho, têm de aguardar até que a operação anterior seja concluída ou receber um erro semelhante ao seguinte.

    Set-AzRedisCache : Conflict: The resource '...' is not in a stable state, and is currently unable to accept the update request.

## <a name="to-get-information-about-an-azure-cache-for-redis"></a>Para obter informações sobre uma Cache do Azure para Redis
Pode obter informações sobre uma cache com o [Get-AzRedisCache](https://docs.microsoft.com/powershell/module/az.rediscache/get-azrediscache) cmdlet.

Para ver uma lista dos parâmetros disponíveis e suas descrições para `Get-AzRedisCache`, execute o seguinte comando.

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

Para devolver informações sobre todas as caches na subscrição atual, execute `Get-AzRedisCache` sem quaisquer parâmetros.

    Get-AzRedisCache

Para devolver informações sobre todas as caches no grupo de recursos específico, execute `Get-AzRedisCache` com o `ResourceGroupName` parâmetro.

    Get-AzRedisCache -ResourceGroupName myGroup

Para devolver informações sobre a uma cache específico, execute `Get-AzRedisCache` com o `Name` parâmetro que contém o nome da cache e o `ResourceGroupName` parâmetro com o grupo de recursos que contém esse cache.

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

## <a name="to-retrieve-the-access-keys-for-an-azure-cache-for-redis"></a>Para obter as chaves de acesso para uma Cache do Azure para Redis
Para obter as chaves de acesso para a sua cache, pode utilizar o [Get-AzRedisCacheKey](https://docs.microsoft.com/powershell/module/az.rediscache/Get-azRedisCacheKey) cmdlet.

Para ver uma lista dos parâmetros disponíveis e suas descrições para `Get-AzRedisCacheKey`, execute o seguinte comando.

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

Para obter as chaves para a sua cache, chame o `Get-AzRedisCacheKey` cmdlet e transmite, nome de seu cache, o nome do grupo de recursos que contém a cache.

    PS C:\> Get-AzRedisCacheKey -Name myCache -ResourceGroupName myGroup

    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : ABhfB757JgjIgt785JgKH9865eifmekfnn649303JKL=

## <a name="to-regenerate-access-keys-for-your-azure-cache-for-redis"></a>Para voltar a gerar chaves de acesso para a sua Cache do Azure para Redis
Para voltar a gerar as chaves de acesso para a sua cache, pode utilizar o [New-AzRedisCacheKey](https://docs.microsoft.com/powershell/module/az.rediscache/New-azRedisCacheKey) cmdlet.

Para ver uma lista dos parâmetros disponíveis e suas descrições para `New-AzRedisCacheKey`, execute o seguinte comando.

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

Para voltar a gerar a chave primária ou secundária para a sua cache, chame o `New-AzRedisCacheKey` cmdlet e transmita o nome, grupo de recursos e especifique `Primary` ou `Secondary` para o `KeyType` parâmetro. No exemplo a seguir, é gerada novamente a chave de acesso secundária para uma cache.

    PS C:\> New-AzRedisCacheKey -Name myCache -ResourceGroupName myGroup -KeyType Secondary

    Confirm
    Are you sure you want to regenerate Secondary key for Azure Cache for Redis 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : c53hj3kh4jhHjPJk8l0jji785JgKH9865eifmekfnn6=

## <a name="to-delete-an-azure-cache-for-redis"></a>Para eliminar uma Cache do Azure para Redis
Para eliminar uma Cache do Azure para Redis, utilize o [Remove-AzRedisCache](https://docs.microsoft.com/powershell/module/az.rediscache/remove-azrediscache) cmdlet.

Para ver uma lista dos parâmetros disponíveis e suas descrições para `Remove-AzRedisCache`, execute o seguinte comando.

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

No exemplo seguinte, a cache com o nome `myCache` é removido.

    PS C:\> Remove-AzRedisCache -Name myCache -ResourceGroupName myGroup

    Confirm
    Are you sure you want to remove Azure Cache for Redis 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


## <a name="to-import-an-azure-cache-for-redis"></a>Para importar uma Cache do Azure para Redis
Pode importar dados para uma Cache do Azure para a instância de Redis com o `Import-AzRedisCache` cmdlet.

> [!IMPORTANT]
> Importar/exportar só está disponível para [escalão premium](cache-premium-tier-intro.md) coloca em cache. Para obter mais informações sobre a importação/exportação, consulte [importar e exportar dados na Cache do Azure para Redis](cache-how-to-import-export-data.md).
> 
> 

Para ver uma lista dos parâmetros disponíveis e suas descrições para `Import-AzRedisCache`, execute o seguinte comando.

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


O seguinte comando importa os dados do blob especificado pelo SAS uri para a Cache do Azure para Redis.

    PS C:\>Import-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Files @("https://mystorageaccount.blob.core.windows.net/mycontainername/blobname?sv=2015-04-05&sr=b&sig=caIwutG2uDa0NZ8mjdNJdgOY8%2F8mhwRuGNdICU%2B0pI4%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwd") -Force

## <a name="to-export-an-azure-cache-for-redis"></a>Para exportar uma Cache do Azure para Redis
Pode exportar dados de uma Cache do Azure para a instância de Redis com o `Export-AzRedisCache` cmdlet.

> [!IMPORTANT]
> Importar/exportar só está disponível para [escalão premium](cache-premium-tier-intro.md) coloca em cache. Para obter mais informações sobre a importação/exportação, consulte [importar e exportar dados na Cache do Azure para Redis](cache-how-to-import-export-data.md).
> 
> 

Para ver uma lista dos parâmetros disponíveis e suas descrições para `Export-AzRedisCache`, execute o seguinte comando.

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


O seguinte comando exporta dados a partir de uma Cache do Azure para a instância de Redis para o contentor especificado pelo uri SAS.

        PS C:\>Export-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Prefix "blobprefix"
        -Container "https://mystorageaccount.blob.core.windows.net/mycontainer?sv=2015-04-05&sr=c&sig=HezZtBZ3DURmEGDduauE7
        pvETY4kqlPI8JCNa8ATmaw%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwdl"

## <a name="to-reboot-an-azure-cache-for-redis"></a>Para reiniciar uma Cache do Azure para Redis
Pode reiniciar o Cache do Azure para a instância de Redis com o `Reset-AzRedisCache` cmdlet.

> [!IMPORTANT]
> Reinício só está disponível para [escalão premium](cache-premium-tier-intro.md) coloca em cache. Para obter mais informações sobre a reinicialização de seu cache, consulte [colocar em Cache administração - reiniciar](cache-administration.md#reboot).
> 
> 

Para ver uma lista dos parâmetros disponíveis e suas descrições para `Reset-AzRedisCache`, execute o seguinte comando.

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


O seguinte comando reinicia ambos os nós da cache especificada.

        PS C:\>Reset-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -RebootType "AllNodes"
        -Force


## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre como utilizar o Windows PowerShell com o Azure, consulte os seguintes recursos:

* [Cache de Redis cmdlet documentação no MSDN do Azure](https://docs.microsoft.com/powershell/module/az.rediscache)
* [Cmdlets do Gestor de recursos do Azure](https://go.microsoft.com/fwlink/?LinkID=394765): Saiba como utilizar os cmdlets no módulo do Azure Resource Manager.
* [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/resource-group-template-deploy-portal.md): Saiba como criar e gerir grupos de recursos no portal do Azure.
* [Blogue do Azure](https://azure.microsoft.com/blog/): Saiba mais sobre os novos recursos do Azure.
* [Blog do Windows PowerShell](https://blogs.msdn.com/powershell): Saiba mais sobre os novos recursos do Windows PowerShell.
* ["EI, equipe de scripts!" Blog](https://blogs.technet.com/b/heyscriptingguy/): Obtenha dicas de mundo real e truques da Comunidade do Windows PowerShell.

