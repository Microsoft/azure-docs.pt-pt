---
title: 'Criar e modificar um circuito do ExpressRoute - PowerShell: Azure | Microsoft Docs'
description: Criar, aprovisionar, certifique-se, atualizar, eliminar e desaprovisionar um circuito do ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 02/20/2019
ms.author: ganesr;cherylmc
ms.custom: seodec18
ms.openlocfilehash: 2b32c97f636cc6b918a883ea3e2a2b540890084f
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57409876"
---
# <a name="create-and-modify-an-expressroute-circuit-using-powershell"></a>Criar e modificar um circuito do ExpressRoute com o PowerShell
> [!div class="op_single_selector"]
> * [Portal do Azure](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [CLI do Azure](howto-circuit-cli.md)
> * [Vídeo - portal do Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-circuit-classic.md)
>

Este artigo ajuda-o a criar um circuito do ExpressRoute com cmdlets do PowerShell e o modelo de implementação Azure Resource Manager. Também pode verificar o estado, atualizar, eliminar ou desaprovisionar um circuito.

## <a name="before-you-begin"></a>Antes de começar

Antes de começar, reveja os [pré-requisitos](expressroute-prerequisites.md) e [fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.

### <a name="working-with-azure-powershell"></a>Trabalhar com o Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="create"></a>Criar e aprovisionar um circuito do ExpressRoute
### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. Inicie sessão na sua conta do Azure e selecione a sua subscrição

[!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. Obter a lista de fornecedores suportados, locais e larguras de banda
Antes de criar um circuito do ExpressRoute, terá da lista de fornecedores de conectividade suportados, locais e opções de largura de banda.

O cmdlet do PowerShell **Get-AzExpressRouteServiceProvider** retorna essas informações, que irá utilizar em passos posteriores:

```azurepowershell-interactive
Get-AzExpressRouteServiceProvider
```

Verifique se o seu fornecedor de conectividade está listado aqui. Tome nota das informações seguintes, que são necessários mais tarde quando criar um circuito:

* Nome
* PeeringLocations
* BandwidthsOffered

Agora, está pronto para criar um circuito do ExpressRoute.

### <a name="3-create-an-expressroute-circuit"></a>3. Criar um circuito do ExpressRoute
Se ainda não tiver um grupo de recursos, tem de criar uma antes de criar o seu circuito do ExpressRoute. Pode fazê-lo ao executar o seguinte comando:

```azurepowershell-interactive
New-AzResourceGroup -Name "ExpressRouteResourceGroup" -Location "West US"
```

O exemplo seguinte mostra como criar um 200 Mbps circuito do ExpressRoute através de Equinix no vale do silício. Se estiver a utilizar um fornecedor diferente e configurações diferentes, substitua essa informação ao fazer o seu pedido. Utilize o exemplo seguinte para solicitar uma nova chave de serviço:

```azurepowershell-interactive
New-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200
```

Certifique-se de que especifica a camada de SKU correta e a família SKU:

* Escalão de SKU determina se a um padrão de ExpressRoute ou de um suplemento ExpressRoute premium é ativado. Pode especificar *padrão* para obter o SKU standard ou *Premium* para o suplemento premium.
* Família SKU determina o tipo de faturação. Pode especificar *Metereddata* de um plano de dados limitados e *Unlimiteddata* para um plano de dados ilimitados. Pode alterar o tipo de faturação de *Metereddata* ao *Unlimiteddata*, mas não é possível alterar o tipo de *Unlimiteddata* para *Metereddata*.

> [!IMPORTANT]
> O circuito de ExpressRoute é cobrado a partir do momento numa que chave de serviço é emitida. Certifique-se de que efetue esta operação quando o fornecedor de conectividade estiver pronto para aprovisionar o circuito.
> 
> 

A resposta contém a chave de serviço. Para obter descrições detalhadas dos parâmetros de executar o seguinte comando:

```azurepowershell-interactive
get-help New-AzExpressRouteCircuit -detailed
```


### <a name="4-list-all-expressroute-circuits"></a>4. Lista de todos os circuitos do ExpressRoute
Para obter uma lista de todos os circuitos do ExpressRoute que criou, execute o **Get-AzExpressRouteCircuit** comando:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

A resposta é semelhante ao seguinte exemplo:

    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                          }
    CircuitProvisioningState          : Enabled
    ServiceProviderProvisioningState  : NotProvisioned
    ServiceProviderNotes              :
    ServiceProviderProperties         : {
                                          "ServiceProviderName": "Equinix",
                                          "PeeringLocation": "Silicon Valley",
                                          "BandwidthInMbps": 200
                                        }
    ServiceKey                        : **************************************
    Peerings                          : []

Pode obter estas informações em qualquer altura utilizando o `Get-AzExpressRouteCircuit` cmdlet. Efetuar a chamada sem parâmetros apresenta uma lista de todos os circuitos. A chave de serviço está listada na *ServiceKey* campo:

```azurepowershell-interactive
Get-AzExpressRouteCircuit
```


A resposta é semelhante ao seguinte exemplo:

    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                          }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : NotProvisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Silicon Valley",
                                         "BandwidthInMbps": 200
                                          }
    ServiceKey                       : **************************************
    Peerings                         : []


### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. Enviar a chave de serviço para o seu fornecedor de conectividade para o aprovisionamento
*ServiceProviderProvisioningState* fornece informações sobre o estado atual do aprovisionamento no lado do fornecedor de serviços. Estado fornece o estado do lado da Microsoft. Para obter mais informações sobre Estados de aprovisionamento do circuito, veja [fluxos de trabalho](expressroute-workflows.md#expressroute-circuit-provisioning-states).

Quando cria um novo circuito do ExpressRoute, o circuito está no estado seguinte:

    ServiceProviderProvisioningState : NotProvisioned
    CircuitProvisioningState         : Enabled



O circuito muda para o seguinte estado quando o fornecedor de conectividade está no processo de ativá-la para:

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled

Para poder ser capaz de utilizar um circuito do ExpressRoute, tem de estar no seguinte estado:

    ServiceProviderProvisioningState : Provisioned
    CircuitProvisioningState         : Enabled

### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. Verificar periodicamente o estado e o estado da chave de circuito
A verificar o estado e o estado da chave de circuito permite-lhe saber quando o fornecedor tiver ativado o seu circuito. Depois do circuito tiver sido configurado, *ServiceProviderProvisioningState* é apresentado como *aprovisionado*, conforme mostrado no exemplo a seguir:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```


A resposta é semelhante ao seguinte exemplo:

    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                       }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Silicon Valley",
                                         "BandwidthInMbps": 200
                                       }
    ServiceKey                       : **************************************
    Peerings                         : []

### <a name="7-create-your-routing-configuration"></a>7. Criar a configuração do encaminhamento
Para obter instruções passo a passo, consulte a [configuração do encaminhamento do circuito de ExpressRoute](expressroute-howto-routing-arm.md) artigo para criar e modificar os peerings de circuito.

> [!IMPORTANT]
> Estas instruções aplicam-se apenas aos circuitos criados com fornecedores de serviços que oferecem serviços de conectividade de 2 de camada. Se estiver a utilizar um fornecedor de serviços que oferece geridos de camada 3 serviços (normalmente uma VPN de IP, como MPLS), o seu fornecedor de conectividade configura e gere o encaminhamento por si.
> 
> 

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. Ligar uma rede virtual a um circuito ExpressRoute
Em seguida, ligar uma rede virtual para o seu circuito do ExpressRoute. Utilize o [ligar redes virtuais para circuitos do ExpressRoute](expressroute-howto-linkvnet-arm.md) artigo quando trabalha com o modelo de implementação do Resource Manager.

## <a name="getting-the-status-of-an-expressroute-circuit"></a>Ao obter o estado de um circuito do ExpressRoute
Pode obter estas informações em qualquer altura utilizando o **Get-AzExpressRouteCircuit** cmdlet. Efetuar a chamada sem parâmetros apresenta uma lista de todos os circuitos.

```azurepowershell-interactive
Get-AzExpressRouteCircuit
```


A resposta é semelhante ao seguinte exemplo:

    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                       }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                            "ServiceProviderName": "Equinix",
                                            "PeeringLocation": "Silicon Valley",
                                            "BandwidthInMbps": 200
                                          }
    ServiceKey                       : **************************************
    Peerings                         : []


Pode obter informações sobre um circuito de ExpressRoute específico passando o nome do grupo de recursos e o nome do circuito como um parâmetro para a chamada:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```


A resposta é semelhante ao seguinte exemplo:

    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                            "Tier": "Standard",
                                            "Family": "MeteredData"
                                          }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Silicon Valley",
                                         "BandwidthInMbps": 200
                                          }
    ServiceKey                       : **************************************
    Peerings                         : []


Para obter descrições detalhadas dos parâmetros de executar o seguinte comando:

```azurepowershell-interactive
get-help get-azurededicatedcircuit -detailed
```

## <a name="modify"></a>Modificar um circuito do ExpressRoute
Pode modificar algumas propriedades de um circuito do ExpressRoute sem afetar a conectividade.

Pode efetuar as seguintes tarefas sem tempo de inatividade:

* Ativar ou desativar um suplemento ExpressRoute premium para o seu circuito do ExpressRoute.
* Aumente a largura de banda do seu circuito do ExpressRoute, desde que haja capacidade na porta. Desatualização de largura de banda de um circuito não é suportada. 
* Altere o plano de medição de dados limitados para dados ilimitados. Não é suportada a alteração do plano de medição de dados ilimitados para dados limitados.
* Pode ativar e desativar *permitir operações clássicas*.

Para obter mais informações sobre limites e limitações, consulte a [FAQ do ExpressRoute](expressroute-faqs.md).

### <a name="to-enable-the-expressroute-premium-add-on"></a>Para ativar o suplemento ExpressRoute premium
Pode ativar o suplemento ExpressRoute premium para o seu circuito existente ao utilizar o seguinte trecho de código do PowerShell:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Premium"
$ckt.sku.Name = "Premium_MeteredData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

O circuito tem agora as funcionalidades de suplemento ExpressRoute premium ativadas. Começamos a faturação é para a capacidade de suplemento premium assim que o comando foi executada com êxito.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Para desativar o suplemento ExpressRoute premium
> [!IMPORTANT]
> Se estiver a utilizar os recursos que são maiores que o que é permitido para o circuito standard, esta operação pode falhar.
> 
> 

Tenha em atenção as seguintes informações:

* Antes de para mudar do premium para standard, certifique-se de que o número de redes virtuais que estejam ligadas ao circuito é inferior a 10. Caso contrário, o seu pedido de atualização falha e é faturado a tarifas premium.
* Deve desassociar a todas as redes virtuais em outras regiões geopolíticas. Se não fizer isso, o seu pedido de atualização falha e é faturado a tarifas premium.
* A tabela de rotas tem de ser inferior a 4000 rotas para peering privado. Se o tamanho da tabela de rota for superior a 4.000, a sessão de BGP ignora e não ser reenabled até que o número de prefixos anunciados fica abaixo de 4.000.

Pode desativar o suplemento ExpressRoute premium para o circuito existente ao utilizar o seguinte cmdlet do PowerShell:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Standard"
$ckt.sku.Name = "Standard_MeteredData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Para atualizar a largura de banda do circuito do ExpressRoute
Para opções de largura de banda suportadas para o seu fornecedor, consulte a [FAQ do ExpressRoute](expressroute-faqs.md). Pode escolher qualquer tamanho superior ao tamanho do seu circuito existente.

> [!IMPORTANT]
> Poderá ter de recriar o circuito do ExpressRoute, se houver a capacidade inadequada na porta existente. Não é possível atualizar o circuito se não existir nenhuma capacidade adicional disponível naquele local.
>
> Não é possível reduzir a largura de banda de um circuito do ExpressRoute sem interrupção. Desatualização de largura de banda exige que desaprovisionar o circuito do ExpressRoute e, em seguida, reaprovisionar um novo circuito do ExpressRoute.
> 

Depois de decidir o tamanho necessário, utilize o seguinte comando para redimensionar o seu circuito:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.ServiceProviderProperties.BandwidthInMbps = 1000

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```


Seu circuito irá ser dimensionado de cópia de segurança no lado do Microsoft. Em seguida, tem de contactar o seu fornecedor de conectividade para atualizar as configurações no seu lado para fazer corresponder esta alteração. Depois de efetuar esta notificação, começaremos a de faturação para a opção de largura de banda atualizado.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>Para mover o SKU de limitado para ilimitado
Pode alterar o SKU de um circuito do ExpressRoute, utilizando o seguinte trecho de código do PowerShell:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Family = "UnlimitedData"
$ckt.sku.Name = "Premium_UnlimitedData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>Para controlar o acesso a clássica e Resource Manager ambientes
Reveja as instruções em [circuitos do ExpressRoute mover do clássico para o modelo de implementação do Resource Manager](expressroute-howto-move-arm.md).  

## <a name="delete"></a>Desaprovisionar e eliminar um circuito do ExpressRoute
Tenha em atenção as seguintes informações:

* Tem de desassociar todas as redes virtuais do circuito do ExpressRoute. Se esta operação falhar, verifique se quaisquer redes virtuais são ligadas ao circuito.
* Se for o fornecedor de serviços de circuito do ExpressRoute estado de aprovisionamento **aprovisionamento** ou **aprovisionado** deve trabalhar com o fornecedor de serviços para desaprovisionar o circuito no seu lado. Continuamos a reservar recursos e a faturação é que o fornecedor de serviços seja concluída o circuito de desaprovisionamento e notifica-nos.
* Se o fornecedor de serviços tiver desaprovisionado o circuito (fornecedor de serviços de estado de aprovisionamento está definido como **não aprovisionado**), pode eliminar o circuito. Isto interrompe a faturação do circuito.

Pode eliminar o seu circuito do ExpressRoute, executando o seguinte comando:

```azurepowershell-interactive
Remove-AzExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"
```

## <a name="next-steps"></a>Passos Seguintes

Depois de criar o seu circuito, certifique-se de que efetua os seguintes passos:

* [Criar e modificar o encaminhamento para o seu circuito do ExpressRoute](expressroute-howto-routing-arm.md)
* [Ligar a rede virtual para o seu circuito do ExpressRoute](expressroute-howto-linkvnet-arm.md)
