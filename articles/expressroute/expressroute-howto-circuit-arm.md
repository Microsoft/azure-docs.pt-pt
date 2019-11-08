---
title: 'Criar e modificar um circuito do ExpressRoute – PowerShell: Azure | Microsoft Docs'
description: Criar, provisionar, verificar, atualizar, excluir e desprovisionar um circuito do ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 02/20/2019
ms.author: cherylmc
ms.reviewer: ganesr
ms.custom: seodec18
ms.openlocfilehash: 9d0df2d9eda861a06e2952ff1623fb4ad5160e81
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748327"
---
# <a name="create-and-modify-an-expressroute-circuit-using-powershell"></a>Criar e modificar um circuito do ExpressRoute usando o PowerShell
> [!div class="op_single_selector"]
> * [Portal do Azure](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [CLI do Azure](howto-circuit-cli.md)
> * [Modelo do Azure Resource Manager](expressroute-howto-circuit-resource-manager-template.md)
> * [portal do Azure de vídeo](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-circuit-classic.md)
>

Este artigo ajuda você a criar um circuito do ExpressRoute usando cmdlets do PowerShell e o modelo de implantação Azure Resource Manager. Você também pode verificar o status, atualizar, excluir ou desprovisionar um circuito.

## <a name="before-you-begin"></a>Antes de começar

Antes de começar, examine os [pré-requisitos](expressroute-prerequisites.md) e os [fluxos de trabalho](expressroute-workflows.md) antes de começar a configuração.

### <a name="working-with-azure-powershell"></a>Trabalhando com Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="create"></a>Criar e provisionar um circuito do ExpressRoute
### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. entre em sua conta do Azure e selecione sua assinatura

[!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. obter a lista de provedores, locais e larguras de banda com suporte
Antes de criar um circuito do ExpressRoute, você precisa da lista de provedores de conectividade, locais e opções de largura de banda com suporte.

O cmdlet **Get-AzExpressRouteServiceProvider** do PowerShell retorna essas informações, que você usará em etapas posteriores:

```azurepowershell-interactive
Get-AzExpressRouteServiceProvider
```

Verifique se seu provedor de conectividade está listado lá. Anote as informações a seguir, que você precisará mais tarde quando criar um circuito:

* Nome
* PeeringLocations
* BandwidthsOffered

Agora você está pronto para criar um circuito de ExpressRoute.

### <a name="3-create-an-expressroute-circuit"></a>3. criar um circuito do ExpressRoute
Se você ainda não tiver um grupo de recursos, deverá criar um antes de criar o circuito do ExpressRoute. Você pode fazer isso executando o seguinte comando:

```azurepowershell-interactive
New-AzResourceGroup -Name "ExpressRouteResourceGroup" -Location "West US"
```

O exemplo a seguir mostra como criar um circuito de ExpressRoute de 200 Mbps por meio de Equinix no vale do silício. Se você estiver usando um provedor diferente e configurações diferentes, substitua essas informações quando fizer a solicitação. Use o exemplo a seguir para solicitar uma nova chave de serviço:

```azurepowershell-interactive
New-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200
```

Certifique-se de especificar a camada de SKU e a família de SKU corretas:

* A camada de SKU determina se um complemento de ExpressRoute Standard ou ExpressRoute Premium está habilitado. Você pode especificar *Standard* para obter o SKU Standard ou *Premium* para o complemento Premium.
* A família de SKUs determina o tipo de cobrança. Você pode especificar *Metereddata* para um plano de dados limitado e *Unlimiteddata* para um plano de dados ilimitado. Você pode alterar o tipo de cobrança de *Metereddata* para *Unlimiteddata*, mas não pode alterar o tipo de *Unlimiteddata* para *Metereddata*.

> [!IMPORTANT]
> O circuito do ExpressRoute é cobrado a partir do momento em que uma chave de serviço é emitida. Certifique-se de executar essa operação quando o provedor de conectividade estiver pronto para provisionar o circuito.
>
>

A resposta contém a chave de serviço. Você pode obter descrições detalhadas de todos os parâmetros executando o seguinte comando:

```azurepowershell-interactive
get-help New-AzExpressRouteCircuit -detailed
```


### <a name="4-list-all-expressroute-circuits"></a>4. listar todos os circuitos de ExpressRoute
Para obter uma lista de todos os circuitos do ExpressRoute que você criou, execute o comando **Get-AzExpressRouteCircuit** :

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

A resposta é semelhante ao exemplo a seguir:

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

Você pode recuperar essas informações a qualquer momento usando o cmdlet `Get-AzExpressRouteCircuit`. Fazer a chamada sem parâmetros lista todos os circuitos. Sua chave de serviço está listada no campo *ServiceKey* :

```azurepowershell-interactive
Get-AzExpressRouteCircuit
```


A resposta é semelhante ao exemplo a seguir:

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


### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. enviar a chave de serviço para o provedor de conectividade para provisionamento
O *ServiceProviderProvisioningState* fornece informações sobre o estado atual do provisionamento no lado do provedor de serviço. Status fornece o estado no lado da Microsoft. Para obter mais informações sobre os Estados de provisionamento de circuito, consulte [fluxos de trabalho](expressroute-workflows.md#expressroute-circuit-provisioning-states).

Quando você cria um novo circuito do ExpressRoute, o circuito está no seguinte Estado:

    ServiceProviderProvisioningState : NotProvisioned
    CircuitProvisioningState         : Enabled



O circuito muda para o seguinte estado quando o provedor de conectividade está no processo de habilitá-lo para você:

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled

Para que você possa usar um circuito do ExpressRoute, ele deve estar no seguinte Estado:

    ServiceProviderProvisioningState : Provisioned
    CircuitProvisioningState         : Enabled

### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. Verifique periodicamente o status e o estado da chave de circuito
Verificar o status e o estado da chave de circuito permite que você saiba quando o seu provedor habilitou o circuito. Depois que o circuito tiver sido configurado, o *ServiceProviderProvisioningState* aparecerá como *provisionado*, conforme mostrado no exemplo a seguir:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```


A resposta é semelhante ao exemplo a seguir:

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

### <a name="7-create-your-routing-configuration"></a>7. criar sua configuração de roteamento
Para obter as instruções passo a passo, consulte o artigo [configuração de roteamento de circuito do ExpressRoute](expressroute-howto-routing-arm.md) para criar e modificar emparelhamentos de circuito.

> [!IMPORTANT]
> Essas instruções se aplicam somente a circuitos criados com provedores de serviço que oferecem serviços de conectividade de camada 2. Se você estiver usando um provedor de serviços que oferece serviços gerenciados de camada 3 (normalmente uma VPN IP, como MPLS), seu provedor de conectividade configurará e gerenciará o roteamento para você.
>
>

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. vincular uma rede virtual a um circuito do ExpressRoute
Em seguida, vincule uma rede virtual ao circuito do ExpressRoute. Use o artigo [vinculando redes virtuais a circuitos do ExpressRoute](expressroute-howto-linkvnet-arm.md) ao trabalhar com o modelo de implantação do Resource Manager.

## <a name="getting-the-status-of-an-expressroute-circuit"></a>Obtendo o status de um circuito do ExpressRoute
Você pode recuperar essas informações a qualquer momento usando o cmdlet **Get-AzExpressRouteCircuit** . Fazer a chamada sem parâmetros lista todos os circuitos.

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


Você pode obter informações sobre um circuito do ExpressRoute específico passando o nome do grupo de recursos e o nome do circuito como um parâmetro para a chamada:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```


A resposta é semelhante ao exemplo a seguir:

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


Você pode obter descrições detalhadas de todos os parâmetros executando o seguinte comando:

```azurepowershell-interactive
get-help get-azurededicatedcircuit -detailed
```

## <a name="modify"></a>Modificando um circuito do ExpressRoute
Você pode modificar determinadas propriedades de um circuito do ExpressRoute sem afetar a conectividade.

Você pode executar as seguintes tarefas sem tempo de inatividade:

* Habilite ou desabilite um complemento Premium do ExpressRoute para o circuito do ExpressRoute.
* Aumente a largura de banda do circuito do ExpressRoute, desde que haja capacidade disponível na porta. Desatualização de largura de banda de um circuito não é suportada.
* Altere o plano de medição de dados limitados para dados ilimitados. Não há suporte para a alteração do plano de medição de dados ilimitados para dados limitados.
* Você pode habilitar e desabilitar *permitir operações clássicas*.

Para obter mais informações sobre limites e limitações, consulte as [perguntas frequentes sobre o ExpressRoute](expressroute-faqs.md).

### <a name="to-enable-the-expressroute-premium-add-on"></a>Para habilitar o complemento Premium do ExpressRoute
Você pode habilitar o complemento Premium do ExpressRoute para seu circuito existente usando o seguinte trecho do PowerShell:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Premium"
$ckt.sku.Name = "Premium_MeteredData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

O circuito agora tem os recursos complementares do ExpressRoute Premium habilitados. Começamos a cobrar pelo recurso de complemento Premium assim que o comando for executado com êxito.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Para desabilitar o complemento Premium do ExpressRoute
> [!IMPORTANT]
> Se você estiver usando recursos que são maiores do que o permitido para o circuito padrão, essa operação poderá falhar.
>
>

Tenha em atenção as seguintes informações:

* Antes de fazer o downgrade do Premium para o Standard, você deve garantir que o número de redes virtuais vinculadas ao circuito seja inferior a 10. Se você não fizer isso, a solicitação de atualização falhará e cobraremos por tarifas Premium.
* Você deve desvincular todas as redes virtuais em outras regiões do geopolítica. Se você não fizer isso, a solicitação de atualização falhará e cobraremos por tarifas Premium.
* Sua tabela de rotas deve ter menos de 4.000 rotas para o emparelhamento privado. Se o tamanho da tabela de rotas for maior que 4.000 rotas, a sessão BGP cai e não será reabilitada até que o número de prefixos anunciados fique abaixo de 4.000.

Você pode desabilitar o complemento Premium do ExpressRoute para o circuito existente usando o seguinte cmdlet do PowerShell:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Standard"
$ckt.sku.Name = "Standard_MeteredData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Para atualizar a largura de banda do circuito do ExpressRoute
Para opções de largura de banda com suporte para seu provedor, verifique as [perguntas frequentes do ExpressRoute](expressroute-faqs.md). Você pode escolher qualquer tamanho maior que o tamanho do circuito existente.

> [!IMPORTANT]
> Talvez seja necessário recriar o circuito do ExpressRoute se houver capacidade inadequada na porta existente. Você não poderá atualizar o circuito se não houver capacidade adicional disponível nesse local.
>
> Não é possível reduzir a largura de banda de um circuito do ExpressRoute sem interrupções. O downgrade da largura de banda exige o desprovisionamento do circuito do ExpressRoute e o reprovisionamento de um novo circuito do ExpressRoute.
>

Depois de decidir qual tamanho você precisa, use o seguinte comando para redimensionar o circuito:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.ServiceProviderProperties.BandwidthInMbps = 1000

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```


Seu circuito será dimensionado no lado da Microsoft. Em seguida, você deve entrar em contato com seu provedor de conectividade para atualizar as configurações em seu lado para corresponder a essa alteração. Depois de fazer essa notificação, começaremos a cobrar pela opção de largura de banda atualizada.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>Para mover a SKU de limitada para ilimitada
Você pode alterar a SKU de um circuito do ExpressRoute usando o seguinte trecho do PowerShell:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Family = "UnlimitedData"
$ckt.sku.Name = "Premium_UnlimitedData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>Para controlar o acesso aos ambientes clássico e do Resource Manager
Examine as instruções em [mover circuitos do ExpressRoute do modelo de implantação clássico para o Gerenciador de recursos](expressroute-howto-move-arm.md).

## <a name="delete"></a>Desprovisionamento e exclusão de um circuito do ExpressRoute
Tenha em atenção as seguintes informações:

* Tem de desassociar todas as redes virtuais do circuito do ExpressRoute. Se essa operação falhar, verifique se as redes virtuais estão vinculadas ao circuito.
* Se o estado de provisionamento do provedor de serviço de circuito do **ExpressRoute for Provisionando** ou **provisionado** , você deverá trabalhar com seu provedor de serviços para desprovisionar o circuito no lado deles. Continuamos reservando recursos e cobramos até que o provedor de serviços conclua o desprovisionamento do circuito e nos notifique.
* Se o provedor de serviços tiver desprovisionado o circuito (o estado de provisionamento do provedor de serviços estiver definido como **não provisionado**), você poderá excluir o circuito. Isto interrompe a faturação do circuito.

Você pode excluir o circuito do ExpressRoute executando o seguinte comando:

```azurepowershell-interactive
Remove-AzExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"
```

## <a name="next-steps"></a>Passos seguintes

Depois de criar o circuito, certifique-se de executar as próximas etapas a seguir:

* [Criar e modificar o roteamento para o circuito do ExpressRoute](expressroute-howto-routing-arm.md)
* [Vincular sua rede virtual ao circuito do ExpressRoute](expressroute-howto-linkvnet-arm.md)
