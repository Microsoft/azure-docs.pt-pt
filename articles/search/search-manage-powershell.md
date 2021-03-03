---
title: Scripts PowerShell usando módulo Az.Search
titleSuffix: Azure Cognitive Search
description: Criar e configurar um serviço de Pesquisa Cognitiva Azure com PowerShell. Pode escalar um serviço para cima ou para baixo, gerir a administração e consultar as teclas api e consultar informações do sistema.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: powershell
ms.topic: conceptual
ms.date: 02/09/2021
ms.openlocfilehash: 111424ab3a3bd6020e70a08ddf2c494996f6f0ea
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101676685"
---
# <a name="manage-your-azure-cognitive-search-service-with-powershell"></a>Gerencie o seu serviço de Pesquisa Cognitiva Azure com o PowerShell
> [!div class="op_single_selector"]
> * [Portal](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [CLI do Azure](search-manage-azure-cli.md)
> * [API REST](/rest/api/searchmanagement/)
> * [SDK do .NET](/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)

Pode executar cmdlets e scripts PowerShell no Windows, Linux ou em [Azure Cloud Shell](../cloud-shell/overview.md) para criar e configurar a Pesquisa Cognitiva Azure. O módulo **Az.Search** estende [a Azure PowerShell](/powershell/) com total paridade às APIs de [Gestão de Gestão de Pesquisa](/rest/api/searchmanagement) e a capacidade de executar as seguintes tarefas:

> [!div class="checklist"]
> * [Listar serviços de pesquisa em uma subscrição](#list-search-services)
> * [Informações sobre serviços de devolução](#get-search-service-information)
> * [Criar ou apagar um serviço](#create-or-delete-a-service)
> * [Criar um serviço com um ponto final privado](#create-a-service-with-a-private-endpoint)
> * [Regenerar a admin API-keys](#regenerate-admin-keys)
> * [Criar ou eliminar chaves api de consulta](#create-or-delete-query-keys)
> * [Escalar para cima ou para baixo com réplicas e divisórias](#scale-replicas-and-partitions)
> * [Criar um recurso de ligação privada partilhada](#create-a-shared-private-link-resource)

Ocasionalmente, fazem-se perguntas sobre tarefas *que não* constam da lista acima. Atualmente, não é possível utilizar o módulo **Az.Search** ou a gestão REST API para alterar um nome de servidor, região ou nível. Os recursos dedicados são atribuídos quando um serviço é criado. Como tal, a alteração do hardware subjacente (localização ou tipo de nó) requer um novo serviço. Da mesma forma, não existem ferramentas ou APIs para transferir conteúdo, como um índice, de um serviço para outro.

Dentro de um serviço, a criação e gestão de conteúdos é através [do Serviço de Busca REST API](/rest/api/searchservice/) ou [.NET SDK](/dotnet/api/overview/azure/search.documents-readme). Embora não existam comandos dedicados do PowerShell para conteúdos, pode escrever o script PowerShell que chama REST ou .NET APIs para criar e carregar índices.

<a name="check-versions-and-load"></a>

## <a name="check-versions-and-load-modules"></a>Versões e módulos de carga

Os exemplos deste artigo são interativos e requerem permissões elevadas. Azure PowerShell (o módulo **Az)** deve ser instalado. Para obter mais informações, consulte [instalar a Azure PowerShell](/powershell/azure/).

### <a name="powershell-version-check-51-or-later"></a>Verificação da versão PowerShell (5.1 ou posterior)

O PowerShell local deve ser 5.1 ou mais tarde, em qualquer sistema operativo suportado.

```azurepowershell-interactive
$PSVersionTable.PSVersion
```

### <a name="load-azure-powershell"></a>Carregar Azure PowerShell

Se não tiver a certeza se **o Az** está instalado, executar o seguinte comando como passo de verificação. 

```azurepowershell-interactive
Get-InstalledModule -Name Az
```

Alguns sistemas não carregam automaticamente os módulos. Se tiver um erro no comando anterior, tente carregar o módulo e, se falhar, volte às instruções de instalação para ver se falhou um passo.

```azurepowershell-interactive
Import-Module -Name Az
```

### <a name="connect-to-azure-with-a-browser-sign-in-token"></a>Conecte-se ao Azure com um token de inscrição no navegador

Pode utilizar as suas credenciais de acesso ao portal para se ligar a uma subscrição no PowerShell. Em alternativa, pode [autenticar não interativamente com um diretor de serviço.](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

```azurepowershell-interactive
Connect-AzAccount
```

Se tiver várias subscrições do Azure, detenha a sua subscrição Azure. Para ver uma lista das suas subscrições atuais, execute este comando.

```azurepowershell-interactive
Get-AzSubscription | sort SubscriptionName | Select SubscriptionName
```

Para especificar a subscrição, executar o seguinte comando. No exemplo seguinte, o nome de subscrição é `ContosoSubscription` .

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName ContosoSubscription
```

<a name="list-search-services"></a>

## <a name="list-services-in-a-subscription"></a>Listar serviços numa subscrição

Os seguintes comandos são da [**Az.Resources,**](/powershell/module/az.resources)devolvendo informações sobre os recursos e serviços já prestados na sua subscrição. Se não sabe quantos serviços de pesquisa já foram criados, estes comandos devolvem essa informação, poupando-lhe uma viagem ao portal.

O primeiro comando devolve todos os serviços de pesquisa.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Search/searchServices | ft
```

Da lista de serviços, devolva informações sobre um recurso específico.

```azurepowershell-interactive
Get-AzResource -ResourceName <service-name>
```

Os resultados devem ser semelhantes à seguinte saída.

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : westus
ResourceId        : /subscriptions/<alphanumeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="import-azsearch"></a>Import Az.Search

Os comandos da [**Az.Search**](/powershell/module/az.search) não estão disponíveis até que carregue o módulo.

```azurepowershell-interactive
Install-Module -Name Az.Search
```

### <a name="list-all-azsearch-commands"></a>Listar todos os comandos Az.Search

Como passo de verificação, devolva uma lista de comandos fornecidos no módulo.

```azurepowershell-interactive
Get-Command -Module Az.Search
```

Os resultados devem ser semelhantes à seguinte saída.

```
CommandType     Name                                               Version    Source                                                                
-----------     ----                                               -------    ------                                                                
Cmdlet          Get-AzSearchAdminKeyPair                           0.8.0      Az.Search                                                             
Cmdlet          Get-AzSearchPrivateEndpointConnection              0.8.0      Az.Search                                                             
Cmdlet          Get-AzSearchPrivateLinkResource                    0.8.0      Az.Search                                                             
Cmdlet          Get-AzSearchQueryKey                               0.8.0      Az.Search                                                             
Cmdlet          Get-AzSearchService                                0.8.0      Az.Search                                                             
Cmdlet          Get-AzSearchSharedPrivateLinkResource              0.8.0      Az.Search                                                             
Cmdlet          New-AzSearchAdminKey                               0.8.0      Az.Search                                                             
Cmdlet          New-AzSearchQueryKey                               0.8.0      Az.Search                                                             
Cmdlet          New-AzSearchService                                0.8.0      Az.Search                                                             
Cmdlet          New-AzSearchSharedPrivateLinkResource              0.8.0      Az.Search                                                             
Cmdlet          Remove-AzSearchPrivateEndpointConnection           0.8.0      Az.Search                                                             
Cmdlet          Remove-AzSearchQueryKey                            0.8.0      Az.Search                                                             
Cmdlet          Remove-AzSearchService                             0.8.0      Az.Search                                                             
Cmdlet          Remove-AzSearchSharedPrivateLinkResource           0.8.0      Az.Search                                                             
Cmdlet          Set-AzSearchPrivateEndpointConnection              0.8.0      Az.Search                                                             
Cmdlet          Set-AzSearchService                                0.8.0      Az.Search                                                             
Cmdlet          Set-AzSearchSharedPrivateLinkResource              0.8.0      Az.Search   
```

Se tiver uma versão mais antiga do pacote, atualize o módulo para obter a funcionalidade mais recente.

```azurepowershell-interactive
Update-Module -Name Az.Search
```

## <a name="get-search-service-information"></a>Obtenha informações do serviço de pesquisa

Depois de **a Az.Search** ser importado e conhecer o grupo de recursos que contém o seu serviço de pesquisa, executar [o Get-AzSearchService](/powershell/module/az.search/get-azsearchservice) para devolver a definição de serviço, incluindo nome, região, nível e replicação e contagem de divisórias.

```azurepowershell-interactive
Get-AzSearchService -ResourceGroupName <resource-group-name>
```

Os resultados devem ser semelhantes à seguinte saída.

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : West US
Sku               : Standard
ReplicaCount      : 1
PartitionCount    : 1
HostingMode       : Default
ResourceId        : /subscriptions/<alphanumeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="create-or-delete-a-service"></a>Criar ou apagar um serviço

[**O New-AzSearchService**](/powershell/module/az.search/new-azsearchadminkey) é utilizado para [criar um novo serviço de pesquisa.](search-create-service-portal.md)

```azurepowershell-interactive
New-AzSearchService -ResourceGroupName <resource-group-name> -Name <search-service-name> -Sku "Standard" -Location "West US" -PartitionCount 3 -ReplicaCount 3 -HostingMode Default
``` 
Os resultados devem ser semelhantes à seguinte saída.

```
ResourceGroupName : demo-westus
Name              : my-demo-searchapp
Id                : /subscriptions/<alphanumeric-subscription-ID>/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
Location          : West US
Sku               : Standard
ReplicaCount      : 3
PartitionCount    : 3
HostingMode       : Default
Tags
```     

### <a name="create-a-service-with-ip-rules"></a>Criar um serviço com regras IP

Dependendo dos seus requisitos de segurança, poderá querer criar um serviço de pesquisa com uma [firewall IP configurada](service-configure-firewall.md). Para tal, primeiro defina as Regras de IP e, em seguida, passe-as para o `IPRuleList` parâmetro como mostrado abaixo.

```azurepowershell-interactive
$ipRules = @([pscustomobject]@{Value="55.5.63.73"},
        [pscustomobject]@{Value="52.228.215.197"},
        [pscustomobject]@{Value="101.37.221.205"})

 New-AzSearchService -ResourceGroupName <resource-group-name> `
                      -Name <search-service-name> `
                      -Sku Standard `
                      -Location "West US" `
                      -PartitionCount 3 -ReplicaCount 3 `
                      -HostingMode Default `
                      -IPRuleList $ipRules
```

### <a name="create-a-service-with-a-system-assigned-managed-identity"></a>Criar um serviço com um sistema atribuído identidade gerida

Em alguns casos, como quando [se utiliza identidade gerida para ligar a uma fonte de dados,](search-howto-managed-identities-storage.md)terá de ligar a [identidade gerida do sistema atribuída.](../active-directory/managed-identities-azure-resources/overview.md) Isto é feito adicionando `-IdentityType SystemAssigned` ao comando.

```azurepowershell-interactive
New-AzSearchService -ResourceGroupName <resource-group-name> `
                      -Name <search-service-name> `
                      -Sku Standard `
                      -Location "West US" `
                      -PartitionCount 3 -ReplicaCount 3 `
                      -HostingMode Default `
                      -IdentityType SystemAssigned
```

## <a name="create-a-service-with-a-private-endpoint"></a>Criar um serviço com um ponto final privado

[Os pontos finais privados](../private-link/private-endpoint-overview.md) para a Azure Cognitive Search permitem que um cliente numa rede virtual aceda de forma segura a dados num índice de pesquisa sobre um [Link Privado](../private-link/private-link-overview.md). O ponto final privado utiliza um endereço IP a partir do [espaço de endereço de rede virtual](../virtual-network/private-ip-addresses.md) para o seu serviço de pesquisa. O tráfego de rede entre o cliente e o serviço de pesquisa atravessa a rede virtual e uma ligação privada na rede de espinha dorsal da Microsoft, eliminando a exposição da internet pública. Para mais detalhes, consulte a documentação sobre a [criação de um ponto final privado para a Pesquisa Cognitiva do Azure](service-create-private-endpoint.md)

O exemplo a seguir mostra como criar um serviço de pesquisa com um ponto final privado.

Em primeiro lugar, implementar um serviço de pesquisa com `PublicNetworkAccess` set to `Disabled` .

```azurepowershell-interactive
$searchService = New-AzSearchService `
    -ResourceGroupName <resource-group-name> `
    -Name <search-service-name> `
    -Sku Standard `
    -Location "West US" `
    -PartitionCount 1 -ReplicaCount 1 `
    -HostingMode Default `
    -PublicNetworkAccess Disabled
```

Em seguida, crie uma rede virtual, uma ligação de rede privada e o ponto final privado.

```azurepowershell-interactive
# Create the subnet
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
    -Name <subnet-name> `
    -AddressPrefix 10.1.0.0/24 `
    -PrivateEndpointNetworkPolicies Disabled 

# Create the virtual network
$virtualNetwork = New-AzVirtualNetwork `
    -ResourceGroupName <resource-group-name> `
    -Location "West US" `
    -Name <virtual-network-name> `
    -AddressPrefix 10.1.0.0/16 `
    -Subnet $subnetConfig

# Create the private network connection
$privateLinkConnection = New-AzPrivateLinkServiceConnection `
    -Name <private-link-name> `
    -PrivateLinkServiceId $searchService.Id `
    -GroupId searchService

# Create the private endpoint
$privateEndpoint = New-AzPrivateEndpoint `
    -Name <private-endpoint-name> `
    -ResourceGroupName <resource-group-name> `
    -Location "West US" `
    -Subnet $virtualNetwork.subnets[0] `
    -PrivateLinkServiceConnection $privateLinkConnection
```

Finalmente, crie uma Zona DE DNS privada. 

```azurepowershell-interactive
## Create private dns zone
$zone = New-AzPrivateDnsZone `
    -ResourceGroupName <resource-group-name> `
    -Name "privatelink.search.windows.net"

## Create dns network link
$link = New-AzPrivateDnsVirtualNetworkLink `
    -ResourceGroupName <resource-group-name> `
    -ZoneName "privatelink.search.windows.net" `
    -Name "myLink" `
    -VirtualNetworkId $virtualNetwork.Id

## Create DNS configuration 
$config = New-AzPrivateDnsZoneConfig `
    -Name "privatelink.search.windows.net" `
    -PrivateDnsZoneId $zone.ResourceId

## Create DNS zone group
New-AzPrivateDnsZoneGroup `
    -ResourceGroupName <resource-group-name> `
    -PrivateEndpointName <private-endpoint-name> `
    -Name 'myZoneGroup' `
    -PrivateDnsZoneConfig $config
```

Para obter mais detalhes sobre a criação de pontos finais privados no PowerShell, consulte este [Private Link Quickstart](../private-link/create-private-endpoint-powershell.md)

### <a name="manage-private-endpoint-connections"></a>Gerir ligações privadas de ponto final

Além de criar uma ligação de ponto final privado, também `Get` `Set` pode, e `Remove` a ligação.

[O Get-AzSearchPrivateEndpointConnection](/powershell/module/az.search/Get-AzSearchPrivateEndpointConnection) é utilizado para recuperar uma ligação de ponto final privado e para ver o seu estado.

```azurepowershell-interactive
Get-AzSearchPrivateEndpointConnection -ResourceGroupName <resource-group-name> -ServiceName <search-service-name>
```

[O Set-AzSearchPrivateEndpointConnection](/powershell/module/az.search/Set-AzSearchPrivateEndpointConnection) é utilizado para atualizar a ligação. O exemplo a seguir define uma ligação privada de ponto final para rejeitado:

```azurepowershell-interactive
Set-AzSearchPrivateEndpointConnection -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <pe-connection-name> -Status Rejected  -Description "Rejected"
```

[Remove-AzSearchPrivateEndpointConnection](/powershell/module/az.search/Remove-AzSearchPrivateEndpointConnection) é utilizado para eliminar a ligação de ponto final privado.

```azurepowershell-interactive
 Remove-AzSearchPrivateEndpointConnection -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <pe-connection-name>
```

## <a name="regenerate-admin-keys"></a>Regenerar chaves de administração

[**New-AzSearchAdminKey**](/powershell/module/az.search/new-azsearchadminkey) é usado para rolar sobre [as teclas API](search-security-api-keys.md)de administração . São criadas duas teclas de administração com cada serviço para acesso autenticado. As chaves são necessárias em todos os pedidos. Ambas as teclas de administração são funcionalmente equivalentes, concedendo acesso total a um serviço de pesquisa com a capacidade de recuperar qualquer informação, ou criar e eliminar qualquer objeto. Existem duas chaves para que possa utilizar uma durante a substituição da outra. 

Só se pode regenerar um de cada vez, especificado como a `primary` chave ou `secondary` a chave. Para um serviço ininterrupto, lembre-se de atualizar todo o código do cliente para utilizar uma chave secundária enquanto roda a chave primária. Evite trocar as chaves durante o voo.

Como seria de esperar, se regenerar chaves sem atualizar o código do cliente, os pedidos que utilizam a tecla antiga falharão. A regeneração de todas as chaves novas não o bloqueia permanentemente do seu serviço, podendo ainda aceder ao serviço através do portal. Depois de regenerar as teclas primárias e secundárias, pode atualizar o código do cliente para utilizar as novas teclas e as operações serão retomadas em conformidade.

Os valores das teclas API são gerados pelo serviço. Não é possível fornecer uma chave personalizada para a Azure Cognitive Search usar. Da mesma forma, não existe um nome definido pelo utilizador para as teclas API administradas. As referências à chave são cordas fixas, ou `primary` `secondary` . 

```azurepowershell-interactive
New-AzSearchAdminKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -KeyKind Primary
```

Os resultados devem ser semelhantes à seguinte saída. Ambas as chaves são devolvidas, mesmo que só mude uma de cada vez.

```
Primary                    Secondary
-------                    ---------
<alphanumeric-guid>        <alphanumeric-guid>  
```

## <a name="create-or-delete-query-keys"></a>Criar ou eliminar chaves de consulta

[**O New-AzSearchQueryKey**](/powershell/module/az.search/new-azsearchquerykey) é utilizado para criar [chaves API](search-security-api-keys.md) de consulta para acesso apenas de leitura de aplicações de clientes a um índice de Pesquisa Cognitiva Azure. As teclas de consulta são usadas para autenticar um índice específico com o objetivo de recuperar os resultados da pesquisa. As teclas de consulta não concedem acesso apenas de leitura a outros itens do serviço, tais como um índice, fonte de dados ou indexante.

Não é possível fornecer uma chave para a Pesquisa Cognitiva Azure usar. As chaves API são geradas pelo serviço.

```azurepowershell-interactive
New-AzSearchQueryKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <query-key-name> 
```

## <a name="scale-replicas-and-partitions"></a>Réplicas e divisórias em escala

[**O Set-AzSearchService**](/powershell/module/az.search/set-azsearchservice) é utilizado para [aumentar ou diminuir réplicas e divisórias](search-capacity-planning.md) para reajustar os recursos faturantes dentro do seu serviço. O aumento de réplicas ou divisórias adiciona à sua conta, que tem encargos fixos e variáveis. Se tiver uma necessidade temporária de poder de processamento adicional, pode aumentar réplicas e divisórias para lidar com a carga de trabalho. A área de monitorização na página do portal Overview tem azulejos sobre latência de consulta, consultas por segundo e estrangulamento, indicando se a capacidade atual é adequada.

Pode demorar um pouco para adicionar ou remover o reabastecimento. Os ajustes à capacidade ocorrem em segundo plano, permitindo que as cargas de trabalho existentes continuem. A capacidade adicional é utilizada para pedidos de entrada assim que estiver pronto, sem necessidade de configuração adicional. 

Remover a capacidade pode ser perturbador. Recomenda-se que se impeçam todos os postos de trabalho de indexação e indexante antes de reduzir a capacidade para evitar pedidos de abandono. Se isso não for viável, pode considerar reduzir a capacidade incrementalmente, uma réplica e partição de cada vez, até que os seus novos níveis de alvo sejam atingidos.

Uma vez que envie o comando, não há como terminá-lo a meio do caminho. Terá de esperar até que o comando termine antes de rever as contagens.

```azurepowershell-interactive
Set-AzSearchService -ResourceGroupName <resource-group-name> -Name <search-service-name> -PartitionCount 6 -ReplicaCount 6
```

Os resultados devem ser semelhantes à seguinte saída.

```
ResourceGroupName : demo-westus
Name              : my-demo-searchapp
Location          : West US
Sku               : Standard
ReplicaCount      : 6
PartitionCount    : 6
HostingMode       : Default
Id                : /subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="create-a-shared-private-link-resource"></a>Criar um recurso de ligação privada partilhada

Os pontos finais privados de recursos seguros que são criados através das APIs de Pesquisa Cognitiva Azure são referidos como *recursos de ligação privada partilhados.* Isto porque está a "partilhar" o acesso a um recurso, como uma conta de armazenamento, que foi integrado com o [serviço Azure Private Link.](https://azure.microsoft.com/services/private-link/)

Se estiver a utilizar um indexante para indexar dados na Pesquisa Cognitiva Azure, e a sua fonte de dados estiver numa rede privada, pode criar uma [ligação de ponto final privado](../private-link/private-endpoint-overview.md) de saída para chegar aos dados.

Uma lista completa dos Recursos Azure para os quais pode criar pontos finais privados de saída da Azure Cognitive Search pode ser encontrada [aqui](search-indexer-howto-access-private.md#shared-private-link-resources-management-apis) juntamente com os valores de **ID do Grupo** relacionados.

[New-AzSearchSharedPrivateLinkResource](/powershell/module/az.search/New-AzSearchSharedPrivateLinkResource) é usado para criar o recurso de ligação privada compartilhado. Tenha em mente que poderá ser necessária alguma configuração para a fonte de dados antes de executar este comando.

```azurepowershell-interactive
New-AzSearchSharedPrivateLinkResource -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <spl-name> -PrivateLinkResourceId /subscriptions/<alphanumeric-subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/myBlobStorage -GroupId <group-id> -RequestMessage "Please approve" 
```

[O Get-AzSearchSharedPrivateLinkResource](/powershell/module/az.search/Get-AzSearchSharedPrivateLinkResource) permite-lhe recuperar os recursos de ligação privada partilhados e ver o seu estado.

```azurepowershell-interactive
Get-AzSearchSharedPrivateLinkResource -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <spl-name>
```

Terá de aprovar a ligação com o seguinte comando antes de poder ser utilizado.

```azurepowershell-interactive
Approve-AzPrivateEndpointConnection `
    -Name <spl-name> `
    -ServiceName <search-service-name> `
    -ResourceGroupName <resource-group-name> `
    -Description = "Approved"
```

[Remove-AzSearchSharedPrivateLinkResource](/powershell/module/az.search/Remove-AzSearchSharedPrivateLinkResource) é utilizado para eliminar o recurso de ligação privada partilhado.

```azurepowershell-interactive
$job = Remove-AzSearchSharedPrivateLinkResource -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <spl-name> -Force -AsJob

$job | Get-Job
```

Para obter todos os detalhes sobre a criação de recursos de ligação privada partilhados, consulte a documentação sobre [a realização de ligações indexantes através de um ponto final privado](search-indexer-howto-access-private.md).

## <a name="next-steps"></a>Passos seguintes

Construa um [índice,](search-what-is-an-index.md) [consultar um índice](search-query-overview.md) utilizando o portal, AS APIs de REST ou o .NET SDK.

* [Criar um índice de Pesquisa Cognitiva Azure no portal Azure](search-get-started-portal.md)
* [Configurar um indexador para carregar dados de outros serviços](search-indexer-overview.md)
* [Consulta um índice de Pesquisa Cognitiva Azure usando explorador de pesquisa no portal Azure](search-explorer.md)
* [Como utilizar a Azure Cognitive Search em .NET](search-howto-dotnet-sdk.md)