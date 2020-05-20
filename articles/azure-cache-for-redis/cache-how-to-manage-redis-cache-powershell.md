---
title: Gerir o Azure Cache para Redis com a Azure PowerShell
description: Aprenda a executar tarefas administrativas para azure cache para Redis usando Azure PowerShell.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/13/2017
ms.author: yegu
ms.openlocfilehash: 838835cf44b5ca5048ea6cb7bc1bba582b2a0926
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83647976"
---
# <a name="manage-azure-cache-for-redis-with-azure-powershell"></a>Gerir o Azure Cache para Redis com a Azure PowerShell
> [!div class="op_single_selector"]
> * [PowerShell](cache-how-to-manage-redis-cache-powershell.md)
> * [CLI do Azure](cache-manage-cli.md)
> 
> 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Este tópico mostra-lhe como executar tarefas comuns como criar, atualizar e escalar o seu Cache Azure para casos Redis, como regenerar chaves de acesso e como visualizar informações sobre os seus caches. Para obter uma lista completa de císeros Rede Cache para Redis PowerShell, consulte [Azure Cache para os cmdlets Redis](https://docs.microsoft.com/powershell/module/az.rediscache).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

Para obter mais informações sobre o modelo clássico de implementação, consulte [O Gestor de Recursos azure vs. implantação clássica: Compreenda os modelos de implantação e o estado dos seus recursos](../azure-resource-manager/management/deployment-models.md).

## <a name="prerequisites"></a>Pré-requisitos
Se já instalou o Azure PowerShell, tem de ter a versão 1.0.0 ou mais tarde do Azure PowerShell. Pode verificar a versão do Azure PowerShell que instalou com este comando no comando Azure PowerShell.

    Get-Module Az | format-table version


Primeiro, tem de entrar no Azure com este comando.

    Connect-AzAccount

Especifique o endereço de e-mail da sua conta Azure e a sua palavra-passe no diálogo de entrada do Microsoft Azure.

Em seguida, se tiver várias subscrições Azure, precisa de definir a sua subscrição Azure. Para ver uma lista das suas subscrições atuais, execute este comando.

    Get-AzSubscription | sort SubscriptionName | Select SubscriptionName

Para especificar a subscrição, execute o seguinte comando. No exemplo seguinte, o nome da subscrição é `ContosoSubscription` .

    Select-AzSubscription -SubscriptionName ContosoSubscription

Antes de poder utilizar o Windows PowerShell com o Gestor de Recursos Azure, precisa do seguinte:

* Windows PowerShell, Versão 3.0 ou 4.0. Para encontrar a versão do Windows PowerShell, escreva: `$PSVersionTable` e verifique o valor de `PSVersion` 3.0 ou 4.0. Para instalar uma versão compatível, consulte o [Windows Management Framework 3.0](https://www.microsoft.com/download/details.aspx?id=34595) ou o [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855).

Para obter ajuda detalhada para qualquer cmdlet que você vê neste tutorial, use o cmdlet Get-Help.

    Get-Help <cmdlet-name> -Detailed

Por exemplo, para obter ajuda para o `New-AzRedisCache` cmdlet, escreva:

    Get-Help New-AzRedisCache -Detailed

### <a name="how-to-connect-to-other-clouds"></a>Como ligar a outras nuvens
Por defeito, o ambiente Azure é, o que representa a instância global de `AzureCloud` nuvem Azure. Para se ligar a uma instância diferente, utilize o comando com o interruptor de linha de comando ou - `Connect-AzAccount` com o ambiente ou o nome ambiente `-Environment` `EnvironmentName` desejado.

Para ver a lista de ambientes disponíveis, corra o `Get-AzEnvironment` cmdlet.

### <a name="to-connect-to-the-azure-government-cloud"></a>Para ligar à Nuvem do Governo De Azure
Para ligar à Nuvem do Governo Azure, utilize um dos seguintes comandos.

    Connect-AzAccount -EnvironmentName AzureUSGovernment

ou

    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureUSGovernment)

Para criar uma cache na Nuvem do Governo Azure, utilize um dos seguintes locais.

* USGov Virginia
* USGov Iowa

Para obter mais informações sobre a Nuvem do Governo Azure, consulte o [Microsoft Azure Government](https://azure.microsoft.com/features/gov/) e o [Microsoft Azure Government Developer Guide](../azure-government-developer-guide.md).

### <a name="to-connect-to-the-azure-china-cloud"></a>Para ligar à Nuvem Azure China
Para ligar à Nuvem Azure China, use um dos seguintes comandos.

    Connect-AzAccount -EnvironmentName AzureChinaCloud

ou

    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureChinaCloud)

Para criar uma cache na Nuvem Azure China, use um dos seguintes locais.

* Leste da China
* Norte da China

Para mais informações sobre a Nuvem Azure China, consulte [o AzureChinaCloud para azure operado pela 21Vianet na China.](https://www.windowsazure.cn/)

### <a name="to-connect-to-microsoft-azure-germany"></a>Para ligar ao Microsoft Azure Alemanha
Para ligar ao Microsoft Azure Alemanha, utilize um dos seguintes comandos.

    Connect-AzAccount -EnvironmentName AzureGermanCloud


ou

    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureGermanCloud)

Para criar uma cache na Microsoft Azure Germany, utilize um dos seguintes locais.

* Alemanha Central
* Nordeste da Alemanha

Para mais informações sobre o Microsoft Azure Germany, consulte o [Microsoft Azure Germany](https://azure.microsoft.com/overview/clouds/germany/).

### <a name="properties-used-for-azure-cache-for-redis-powershell"></a>Propriedades utilizadas para Azure Cache para Redis PowerShell
A tabela seguinte contém propriedades e descrições para parâmetros comumente utilizados ao criar e gerir o seu Azure Cache para casos Redis usando o Azure PowerShell.

| Parâmetro | Descrição | Predefinição |
| --- | --- | --- |
| Name |Nome da cache | |
| Localização |Localização da cache | |
| ResourceGroupName |Nome de grupo de recursos para criar a cache | |
| Tamanho |Do tamanho da cache. Os valores válidos são: P1, P2, P3, P4, C0, C1, C2, C3, C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB |1GB |
| ShardCount |O número de fragmentos a criar ao criar uma cache premium com clustering habilitado. Valores válidos são: 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 | |
| SKU |Especifica o SKU da cache. Os valores válidos são: Básico, Standard, Premium |Standard |
| RedisConfiguration |Especifica as definições de configuração do Redis. Para mais detalhes sobre cada definição, consulte a tabela de [propriedades redisConfiguration](#redisconfiguration-properties) seguinte. | |
| EnableNonSslPort |Indica se a porta não-SSL está ativada. |Falso |
| Política MaxMemory |Este parâmetro foi depreciado - use redisConfiguration em vez disso. | |
| StaticIP |Ao hospedar a sua cache num VNET, especifica um endereço IP único na sub-rede para a cache. Se não for fornecido, um é escolhido para si a partir da sub-rede. | |
| Subrede |Ao hospedar a sua cache num VNET, especifica o nome da sub-rede para implantar a cache. | |
| VirtualNetwork |Ao hospedar a sua cache num VNET, especifica o ID de recurso do VNET no qual se implanta a cache. | |
| Tipo de chave |Especifica qual a chave de acesso para regenerar ao renovar as teclas de acesso. Valores válidos são: Primário, Secundário | |

### <a name="redisconfiguration-properties"></a>Propriedades redisConfiguration
| Propriedade | Descrição | Escalões de preço |
| --- | --- | --- |
| rdb-backup-habilitado |Se a [persistência de dados redis](cache-how-to-premium-persistence.md) está ativada |Apenas premium |
| rdb-armazenamento-conexão-string |A cadeia de ligação à conta de armazenamento para [a persistência de dados redis](cache-how-to-premium-persistence.md) |Apenas premium |
| rdb-backup-frequência |A frequência de backup para a [persistência de dados redis](cache-how-to-premium-persistence.md) |Apenas premium |
| maxmemória reservado |Configura a [memória reservada](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) para processos não cache |Standard e Premium |
| maxmemory-política |Configura a política de [despejo](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) para a cache |Todos os níveis de preços |
| notificar-keyspace-eventos |Configures [notificações do espaço-chave](cache-configure.md#keyspace-notifications-advanced-settings) |Standard e Premium |
| hash-max-ziplist-entradas |Configura [a otimização da memória](https://redis.io/topics/memory-optimization) para pequenos tipos de dados agregados |Standard e Premium |
| hash-max-ziplist-valor |Configura [a otimização da memória](https://redis.io/topics/memory-optimization) para pequenos tipos de dados agregados |Standard e Premium |
| set-max-intset-entradas |Configura [a otimização da memória](https://redis.io/topics/memory-optimization) para pequenos tipos de dados agregados |Standard e Premium |
| zset-max-ziplist-entradas |Configura [a otimização da memória](https://redis.io/topics/memory-optimization) para pequenos tipos de dados agregados |Standard e Premium |
| zset-max-ziplist-valor |Configura [a otimização da memória](https://redis.io/topics/memory-optimization) para pequenos tipos de dados agregados |Standard e Premium |
| bases de dados |Configura o número de bases de dados. Esta propriedade só pode ser configurada na criação de cache. |Standard e Premium |

## <a name="to-create-an-azure-cache-for-redis"></a>Para criar um Cache Azure para Redis
Os novos casos azure cache para redis são criados usando o cmdlet [New-AzRedisCache.](https://docs.microsoft.com/powershell/module/az.rediscache/new-azrediscache)

> [!IMPORTANT]
> A primeira vez que cria um Azure Cache para Redis numa subscrição utilizando o portal Azure, o portal regista o `Microsoft.Cache` espaço de nome para essa subscrição. Se tentar criar o primeiro Cache Azure para Redis numa subscrição utilizando o PowerShell, deve primeiro registar esse espaço de nome utilizando o seguinte comando; de outra forma cmdlets tais `New-AzRedisCache` como e `Get-AzRedisCache` falhar.
> 
> `Register-AzResourceProvider -ProviderNamespace "Microsoft.Cache"`
> 
> 

Para ver uma lista de parâmetros disponíveis e suas descrições `New-AzRedisCache` para, executar o seguinte comando.

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

Para criar uma cache com parâmetros predefinidos, execute o seguinte comando.

    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US"

`ResourceGroupName`, `Name` e `Location` são necessários parâmetros, mas o resto é opcional e tem valores predefinidos. Executar o comando anterior cria um Cache SKU Azure Standard para redis com o nome, localização e grupo de recursos especificados, que é de 1 GB de tamanho com a porta não SSL desativada.

Para criar uma cache premium, especifique um tamanho de P1 (6 GB - 60 GB), P2 (13 GB - 130 GB), P3 (26 GB - 260 GB) ou P4 (53 GB - 530 GB). Para ativar o agrupamento, especifique uma contagem de fragmentos utilizando o `ShardCount` parâmetro. O exemplo seguinte cria uma cache premium P1 com 3 fragmentos. Uma cache premium P1 tem 6 GB de tamanho, e uma vez que especificámos três fragmentos, o tamanho total é de 18 GB (3 x 6 GB).

    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P1 -ShardCount 3

Para especificar valores para o `RedisConfiguration` parâmetro, encerre os valores no interior `{}` como pares chave/valor como `@{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}` . O exemplo seguinte cria uma cache padrão de 1 GB com política de `allkeys-random` maxmemory e notificações de espaço-chave configuradas com `KEA` . Para mais informações, consulte [as notificações do Keyspace (definições avançadas)](cache-configure.md#keyspace-notifications-advanced-settings) e [as políticas de Memória](cache-configure.md#memory-policies).

    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}

<a name="databases"></a>

## <a name="to-configure-the-databases-setting-during-cache-creation"></a>Para configurar a definição de bases de dados durante a criação de cache
A `databases` definição só pode ser configurada durante a criação de cache. O exemplo seguinte cria uma cache Premium P3 (26 GB) com 48 bases de dados utilizando o cmdlet [New-AzRedisCache.](https://docs.microsoft.com/powershell/module/az.rediscache/New-azRedisCache)

    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P3 -RedisConfiguration @{"databases" = "48"}

Para obter mais informações sobre a `databases` propriedade, consulte [Predefinido Azure Cache para configuração](cache-configure.md#default-redis-server-configuration)do servidor Redis . Para obter mais informações sobre a criação de uma cache utilizando o cmdlet [New-AzRedisCache,](https://docs.microsoft.com/powershell/module/az.rediscache/new-azrediscache) consulte o anterior Para criar uma secção Azure Cache para Redis.

## <a name="to-update-an-azure-cache-for-redis"></a>Para atualizar um Azure Cache para Redis
Os casos Azure Cache para Redis são atualizados utilizando o [cmdlet Set-AzRedisCache.](https://docs.microsoft.com/powershell/module/az.rediscache/Set-azRedisCache)

Para ver uma lista de parâmetros disponíveis e suas descrições `Set-AzRedisCache` para, executar o seguinte comando.

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

O `Set-AzRedisCache` cmdlet pode ser usado para atualizar propriedades `Size` como, `Sku` , e os `EnableNonSslPort` `RedisConfiguration` valores. 

O comando seguinte atualiza a política de memória máxima para o Azure Cache para Redis chamado myCache.

    Set-AzRedisCache -ResourceGroupName "myGroup" -Name "myCache" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random"}

<a name="scale"></a>

## <a name="to-scale-an-azure-cache-for-redis"></a>Para escalar um Cache Azure para Redis
`Set-AzRedisCache`pode ser usado para escalar um Cache Azure para o caso Redis quando o `Size` , ou as propriedades são `Sku` `ShardCount` modificadas. 

> [!NOTE]
> A escala de uma cache utilizando o PowerShell está sujeita aos mesmos limites e diretrizes que a escala de uma cache do portal Azure. Pode escalar para um nível de preços diferente com as seguintes restrições.
> 
> * Não se pode escalar de um nível de preços mais alto para um nível de preços mais baixo.
> * Não pode escalar de uma cache **Premium** até um **Standard** ou um cache **Básico.**
> * Não se pode escalar de uma cache **padrão** até uma cache **básica.**
> * Pode escalar de uma cache **Básica** para uma cache **Standard,** mas não pode alterar o tamanho ao mesmo tempo. Se precisar de um tamanho diferente, pode fazer uma operação de escala subsequente ao tamanho desejado.
> * Não se pode escalar de uma cache **básica** diretamente para uma cache **Premium.** Deve escalar de **Basic** a **Standard** numa operação de escala, e depois de **Standard** a **Premium** numa operação de escala subsequente.
> * Não se pode escalar de um tamanho maior até ao tamanho **C0 (250 MB).**
> 
> Para mais informações, consulte [Como Escalar O Cache Azure para Redis](cache-how-to-scale.md).
> 
> 

O exemplo seguinte mostra como escalar uma cache com o nome `myCache` de uma cache de 2,5 GB. Note que este comando funciona tanto para uma cache Basic como standard.

    Set-AzRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB

Após a emissão deste comando, o estado da cache é devolvido (semelhante à `Get-AzRedisCache` chamada). Note que o `ProvisioningState` é `Scaling` .

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

Quando a operação de escala estiver completa, as `ProvisioningState` alterações para `Succeeded` . Se precisar de efetuar uma operação de escala subsequente, como mudar de Basic para Standard e, em seguida, alterar o tamanho, deve esperar até que a operação anterior esteja completa ou receba um erro semelhante ao seguinte.

    Set-AzRedisCache : Conflict: The resource '...' is not in a stable state, and is currently unable to accept the update request.

## <a name="to-get-information-about-an-azure-cache-for-redis"></a>Para obter informações sobre um Azure Cache para Redis
Pode obter informações sobre uma cache utilizando o cmdlet [Get-AzRedisCache.](https://docs.microsoft.com/powershell/module/az.rediscache/get-azrediscache)

Para ver uma lista de parâmetros disponíveis e suas descrições `Get-AzRedisCache` para, executar o seguinte comando.

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

Para devolver informações sobre todos os caches da subscrição atual, corra `Get-AzRedisCache` sem parâmetros.

    Get-AzRedisCache

Para devolver informações sobre todos os caches de um grupo de recursos específicos, corra `Get-AzRedisCache` com o `ResourceGroupName` parâmetro.

    Get-AzRedisCache -ResourceGroupName myGroup

Para devolver informações sobre uma cache específica, corra com o parâmetro que contém o nome da cache e o parâmetro com o grupo de `Get-AzRedisCache` recursos que contém essa `Name` `ResourceGroupName` cache.

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

## <a name="to-retrieve-the-access-keys-for-an-azure-cache-for-redis"></a>Para recuperar as chaves de acesso para um Azure Cache para Redis
Para recuperar as teclas de acesso para a sua cache, pode utilizar o cmdlet [Get-AzRedisCacheKey.](https://docs.microsoft.com/powershell/module/az.rediscache/Get-azRedisCacheKey)

Para ver uma lista de parâmetros disponíveis e suas descrições `Get-AzRedisCacheKey` para, executar o seguinte comando.

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

Para recuperar as chaves da sua cache, ligue para o `Get-AzRedisCacheKey` cmdlet e passe em nome da sua cache o nome do grupo de recursos que contém a cache.

    PS C:\> Get-AzRedisCacheKey -Name myCache -ResourceGroupName myGroup

    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : ABhfB757JgjIgt785JgKH9865eifmekfnn649303JKL=

## <a name="to-regenerate-access-keys-for-your-azure-cache-for-redis"></a>Para regenerar as chaves de acesso para o seu Azure Cache para Redis
Para regenerar as teclas de acesso da sua cache, pode utilizar o cmdlet [New-AzRedisCacheKey.](https://docs.microsoft.com/powershell/module/az.rediscache/New-azRedisCacheKey)

Para ver uma lista de parâmetros disponíveis e suas descrições `New-AzRedisCacheKey` para, executar o seguinte comando.

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

Para regenerar a chave primária ou secundária para a sua cache, ligue para o `New-AzRedisCacheKey` cmdlet e passe no nome, grupo de recursos e especifique `Primary` ou para o `Secondary` `KeyType` parâmetro. No exemplo seguinte, a chave de acesso secundária para uma cache é regenerada.

    PS C:\> New-AzRedisCacheKey -Name myCache -ResourceGroupName myGroup -KeyType Secondary

    Confirm
    Are you sure you want to regenerate Secondary key for Azure Cache for Redis 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : c53hj3kh4jhHjPJk8l0jji785JgKH9865eifmekfnn6=

## <a name="to-delete-an-azure-cache-for-redis"></a>Para apagar um Cache Azure para Redis
Para eliminar um Cache Azure para Redis, utilize o cmdlet [Remove-AzRedisCache.](https://docs.microsoft.com/powershell/module/az.rediscache/remove-azrediscache)

Para ver uma lista de parâmetros disponíveis e suas descrições `Remove-AzRedisCache` para, executar o seguinte comando.

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

No exemplo seguinte, a cache nomeada `myCache` é removida.

    PS C:\> Remove-AzRedisCache -Name myCache -ResourceGroupName myGroup

    Confirm
    Are you sure you want to remove Azure Cache for Redis 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


## <a name="to-import-an-azure-cache-for-redis"></a>Para importar um Cache Azure para Redis
Pode importar dados para um Azure Cache para a instância Redis utilizando o `Import-AzRedisCache` cmdlet.

> [!IMPORTANT]
> A importação/exportação só está disponível para caches [de nível premium.](cache-premium-tier-intro.md) Para obter mais informações sobre importância/exportação, consulte [os dados de importação e exportação em Azure Cache for Redis](cache-how-to-import-export-data.md).
> 
> 

Para ver uma lista de parâmetros disponíveis e suas descrições `Import-AzRedisCache` para, executar o seguinte comando.

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


O comando seguinte importa dados da bolha especificada pelo SAS uri em Azure Cache for Redis.

    PS C:\>Import-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Files @("https://mystorageaccount.blob.core.windows.net/mycontainername/blobname?sv=2015-04-05&sr=b&sig=caIwutG2uDa0NZ8mjdNJdgOY8%2F8mhwRuGNdICU%2B0pI4%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwd") -Force

## <a name="to-export-an-azure-cache-for-redis"></a>Para exportar um Azure Cache para redis
Pode exportar dados de um Azure Cache para a instância Redis utilizando o `Export-AzRedisCache` cmdlet.

> [!IMPORTANT]
> A importação/exportação só está disponível para caches [de nível premium.](cache-premium-tier-intro.md) Para obter mais informações sobre importância/exportação, consulte [os dados de importação e exportação em Azure Cache for Redis](cache-how-to-import-export-data.md).
> 
> 

Para ver uma lista de parâmetros disponíveis e suas descrições `Export-AzRedisCache` para, executar o seguinte comando.

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


O comando seguinte exporta dados de um Azure Cache para redis, no recipiente especificado pelo SAS uri.

        PS C:\>Export-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Prefix "blobprefix"
        -Container "https://mystorageaccount.blob.core.windows.net/mycontainer?sv=2015-04-05&sr=c&sig=HezZtBZ3DURmEGDduauE7
        pvETY4kqlPI8JCNa8ATmaw%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwdl"

## <a name="to-reboot-an-azure-cache-for-redis"></a>Para reiniciar um Cache Azure para Redis
Pode reiniciar o seu Azure Cache para a instância Redis utilizando o `Reset-AzRedisCache` cmdlet.

> [!IMPORTANT]
> O reboot só está disponível para caches [de nível premium.](cache-premium-tier-intro.md) Para mais informações sobre o reinício da sua cache, consulte a [administração Cache - reiniciar](cache-administration.md#reboot).
> 
> 

Para ver uma lista de parâmetros disponíveis e suas descrições `Reset-AzRedisCache` para, executar o seguinte comando.

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


O comando seguinte reinicia ambos os nós da cache especificada.

        PS C:\>Reset-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -RebootType "AllNodes"
        -Force


## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre a utilização do Windows PowerShell com o Azure, consulte os seguintes recursos:

* [Azure Cache para a documentação de cmdlet Redis na MSDN](https://docs.microsoft.com/powershell/module/az.rediscache)
* [Cmdlets:](https://go.microsoft.com/fwlink/?LinkID=394765)Aprenda a utilizar os cmdlets no módulo Azure Resource Manager.
* [Utilizar grupos de Recursos para gerir os seus recursos Azure](../azure-resource-manager/templates/deploy-portal.md): Aprenda a criar e gerir grupos de recursos no portal Azure.
* [Blog Azure](https://azure.microsoft.com/blog/): Conheça novas funcionalidades no Azure.
* [Blog Windows PowerShell](https://devblogs.microsoft.com/powershell/): Saiba sobre novas funcionalidades no Windows PowerShell.
* ["Ei, Cara de Scripting!" Blog](https://blogs.technet.com/b/heyscriptingguy/): Obtenha dicas e truques do mundo real da comunidade Windows PowerShell.

