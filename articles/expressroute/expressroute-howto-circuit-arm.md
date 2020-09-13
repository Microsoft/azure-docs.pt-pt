---
title: 'Azure ExpressRoute: Modificar um circuito: PowerShell'
description: Criar, providenciar, verificar, atualizar, eliminar e desprovisionar um circuito ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 01/08/2020
ms.author: duau
ms.openlocfilehash: e9bf9dbe0f4146101513ab9786b298ac6b43b6a3
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89566302"
---
# <a name="create-and-modify-an-expressroute-circuit-using-powershell"></a>Criar e modificar um circuito do ExpressRoute através do PowerShell
> [!div class="op_single_selector"]
> * [Portal do Azure](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [CLI do Azure](howto-circuit-cli.md)
> * [Modelo Azure Resource Manager](expressroute-howto-circuit-resource-manager-template.md)
> * [Vídeo - Portal Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-circuit-classic.md)
>

Este artigo ajuda-o a criar um circuito ExpressRoute utilizando cmdlets PowerShell e o modelo de implementação do Gestor de Recursos Azure. Também pode verificar o estado, atualizar, excluir ou desprovisionar um circuito.

## <a name="before-you-begin"></a>Before you begin

Antes de começar, reveja os [pré-requisitos](expressroute-prerequisites.md) e [fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.

### <a name="working-with-azure-powershell"></a>Trabalhar com a Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Criar e providenciar um circuito ExpressRoute
### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. Inscreva-se na sua conta Azure e selecione a sua subscrição

[!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. Obter a lista de fornecedores, locais e larguras de banda apoiadas
Antes de criar um circuito ExpressRoute, precisa da lista de fornecedores de conectividade suportados, locais e opções de largura de banda.

O cmdlet PowerShell **Get-AzExpressRouteServiceProvider** devolve estas informações, que utilizará em etapas posteriores:

```azurepowershell-interactive
Get-AzExpressRouteServiceProvider
```

Verifique se o seu fornecedor de conectividade está listado lá. Tome nota das seguintes informações, que necessita mais tarde quando criar um circuito:

* Name
* Localizaçãos de pares
* Largura de bandaOffered

Está agora pronto para criar um circuito ExpressRoute.

### <a name="3-create-an-expressroute-circuit"></a>3. Criar um circuito ExpressRoute
Se ainda não tem um grupo de recursos, tem de criar um antes de criar o seu circuito ExpressRoute. Pode fazê-lo executando o seguinte comando:

```azurepowershell-interactive
New-AzResourceGroup -Name "ExpressRouteResourceGroup" -Location "West US"
```

O exemplo a seguir mostra como criar um circuito ExpressRoute de 200-Mbps através do Equinix em Silicon Valley. Se estiver a utilizar um fornecedor diferente e diferentes definições, substitua essa informação quando fizer o seu pedido. Utilize o seguinte exemplo para solicitar uma nova chave de serviço:

```azurepowershell-interactive
New-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200
```

Certifique-se de que especifica o nível SKU e a família SKU corretas:

* O nível SKU determina se um circuito ExpressRoute é [Local,](expressroute-faqs.md#expressroute-local)Standard ou [Premium](expressroute-faqs.md#expressroute-premium). Pode especificar *Local,* *Standard* ou *Premium.* Não é possível alterar o SKU de *Standard/Premium* para *Local.*
* A família SKU determina o tipo de faturação. Pode especificar *os dados medidos* para um plano de dados medido e *dados ilimitados* para um plano de dados ilimitado. Pode alterar o tipo de faturação de *Metereddata* para *Unlimiteddata,* mas não pode alterar o tipo de *dados ilimitados* para *Metereddata*. Um circuito *local* é sempre *dados ilimitados.*

> [!IMPORTANT]
> O seu circuito ExpressRoute é faturado a partir do momento em que uma chave de serviço é emitida. Certifique-se de que efetua esta operação quando o fornecedor de conectividade estiver pronto para o fornecimento do circuito.
>
>

A resposta contém a chave de serviço. Pode obter descrições detalhadas de todos os parâmetros executando o seguinte comando:

```azurepowershell-interactive
get-help New-AzExpressRouteCircuit -detailed
```


### <a name="4-list-all-expressroute-circuits"></a>4. Lista todos os circuitos ExpressRoute
Para obter uma lista de todos os circuitos ExpressRoute que criou, execute o comando **Get-AzExpressRouteCircuit:**

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

A resposta é semelhante ao seguinte exemplo:

```azurepowershell
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
```

Pode recuperar esta informação a qualquer momento utilizando o `Get-AzExpressRouteCircuit` cmdlet. Fazer a chamada sem parâmetros lista todos os circuitos. A sua chave de serviço está listada no campo *ServiceKey:*

```azurepowershell-interactive
Get-AzExpressRouteCircuit
```


A resposta é semelhante ao seguinte exemplo:

```azurepowershell
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
```


### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. Envie a chave de serviço para o seu fornecedor de conectividade para o provisionamento
*ServiceProviderProvisioningState* fornece informações sobre o estado atual do fornecimento do lado do prestador de serviços. O estado fornece o estado do lado da Microsoft. Para obter mais informações sobre estados de provisão de circuitos, consulte [Workflows](expressroute-workflows.md#expressroute-circuit-provisioning-states).

Quando se cria um novo circuito ExpressRoute, o circuito encontra-se no seguinte estado:

```azurepowershell
ServiceProviderProvisioningState : NotProvisioned
CircuitProvisioningState         : Enabled
```



O circuito muda para o seguinte estado quando o fornecedor de conectividade está em processo de permitir-lhe:

```azurepowershell
ServiceProviderProvisioningState : Provisioning
Status                           : Enabled
```

Para poder utilizar um circuito ExpressRoute, deve estar no seguinte estado:

```azurepowershell
ServiceProviderProvisioningState : Provisioned
CircuitProvisioningState         : Enabled
```

### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. Verifique periodicamente o estado e o estado da chave do circuito
A verificação do estado e do estado da chave de circuitos permite-lhe saber quando o seu fornecedor ativou o seu circuito. Após a configuração do circuito, o *ServiceProviderProvisioningState* aparece como *Provisionado,* como mostra o seguinte exemplo:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```


A resposta é semelhante ao seguinte exemplo:

```azurepowershell
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
```

### <a name="7-create-your-routing-configuration"></a>7. Crie a sua configuração de encaminhamento
Para obter instruções passo a passo, consulte o artigo de configuração do [roteamento do circuito ExpressRoute](expressroute-howto-routing-arm.md) para criar e modificar os pares de circuitos.

> [!IMPORTANT]
> Estas instruções aplicam-se apenas aos circuitos criados com prestadores de serviços que oferecem serviços de conectividade de camada 2. Se estiver a utilizar um prestador de serviços que oferece serviços geridos de camada 3 (normalmente um IP VPN, como MPLS), o seu fornecedor de conectividade configura e gere o encaminhamento para si.
>
>

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. Ligue uma rede virtual a um circuito ExpressRoute
Em seguida, ligue uma rede virtual ao seu circuito ExpressRoute. Utilize as redes virtuais de ligação ao artigo [dos circuitos ExpressRoute](expressroute-howto-linkvnet-arm.md) quando trabalhar com o modelo de implementação do Gestor de Recursos.

## <a name="getting-the-status-of-an-expressroute-circuit"></a>Obtenção do estatuto de circuito ExpressRoute
Pode obter esta informação a qualquer momento utilizando o **cmdlet Get-AzExpressRouteCircuit.** Fazer a chamada sem parâmetros lista todos os circuitos.

```azurepowershell-interactive
Get-AzExpressRouteCircuit
```


A resposta é semelhante ao seguinte exemplo:

```azurepowershell
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
```


Pode obter informações sobre um circuito ExpressRoute específico, passando o nome do grupo de recursos e o nome do circuito como parâmetro para a chamada:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```


A resposta é semelhante ao seguinte exemplo:

```azurepowershell
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
```


Pode obter descrições detalhadas de todos os parâmetros executando o seguinte comando:

```azurepowershell-interactive
get-help Get-AzExpressRouteCircuit -detailed
```

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>Modificação de um circuito ExpressRoute
Pode modificar certas propriedades de um circuito ExpressRoute sem afetar a conectividade.

Pode fazer as seguintes tarefas sem tempo de inatividade:

* Ative ou desative um addon premium ExpressRoute para o seu circuito ExpressRoute. A alteração do SKU de *Standard/Premium* para *Local* não é suportada.
* Aumente a largura de banda do seu circuito ExpressRoute desde que haja capacidade disponível na porta. A redução da largura de banda de um circuito não é suportada.
* Altere o plano de medição de Dados Medidos para Dados Ilimitados. A alteração do plano de medição de Dados Ilimitados para Dados Medidos não é suportada.
* Pode ativar e desativar *permitir operações clássicas.*

Para obter mais informações sobre limites e limitações, consulte as [FAQ expressRoute.](expressroute-faqs.md)

### <a name="to-enable-the-expressroute-premium-add-on"></a>Para ativar o complemento premium ExpressRoute
Pode ativar o addon premium ExpressRoute para o circuito existente utilizando o seguinte corte PowerShell:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Premium"
$ckt.sku.Name = "Premium_MeteredData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

O circuito tem agora as funcionalidades adicionais premium ExpressRoute ativadas. Começamos a cobrar-lhe a capacidade de adicionar premium assim que o comando tiver sido executado com sucesso.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Para desativar o complemento premium ExpressRoute
> [!IMPORTANT]
> Se estiver a usar recursos superiores ao permitido para o circuito padrão, esta operação pode falhar.
>
>

Tenha em atenção as seguintes informações:

* Antes de baixar de prémio para padrão, deve garantir que o número de redes virtuais ligadas ao circuito seja inferior a 10. Se não o fizer, o seu pedido de atualização falha e cobramos-lhe taxas premium.
* Deve desvincular todas as redes virtuais de outras regiões geopolíticas. Se não o fizer, o seu pedido de atualização falha e cobramos-lhe taxas premium.
* Sua mesa de rota deve ser menos de 4.000 rotas para espreitar privados. Se o tamanho da tabela de rotas for superior a 4.000 rotas, a sessão de BGP cai e não será reenutilizada até que o número de prefixos anunciados desça abaixo de 4.000.

Pode desativar o addon premium ExpressRoute para o circuito existente utilizando o seguinte cmdlet PowerShell:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Standard"
$ckt.sku.Name = "Standard_MeteredData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Para atualizar a largura de banda do circuito ExpressRoute
Para obter opções de largura de banda suportadas para o seu fornecedor, consulte as [FAQ ExpressRoute](expressroute-faqs.md). Pode escolher qualquer tamanho maior do que o tamanho do seu circuito existente.

> [!IMPORTANT]
> Poderá ter de recriar o circuito ExpressRoute se não houver capacidade inadequada na porta existente. Não é possível atualizar o circuito se não houver capacidade adicional disponível nesse local.
>
> Não é possível reduzir a largura de banda de um circuito ExpressRoute sem interrupções. A redução da largura de banda requer que desprovisione o circuito ExpressRoute e, em seguida, reprovisione um novo circuito ExpressRoute.
>

Depois de decidir qual o tamanho necessário, use o seguinte comando para redimensionar o seu circuito:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.ServiceProviderProperties.BandwidthInMbps = 1000

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```


O seu circuito será dimensionado pelo lado da Microsoft. Em seguida, deve contactar o seu fornecedor de conectividade para atualizar as configurações do seu lado para corresponder a esta alteração. Depois de fazer esta notificação, começaremos a cobrar-lhe a opção de largura de banda atualizada.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>Para mover o SKU de medido para ilimitado
Pode alterar o SKU de um circuito ExpressRoute utilizando o seguinte corte PowerShell:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Family = "UnlimitedData"
$ckt.sku.Name = "Premium_UnlimitedData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>Para controlar o acesso aos ambientes clássicos e gestores de recursos
Reveja as instruções nos [circuitos Move ExpressRoute do clássico para o modelo de implementação do Gestor de Recursos](expressroute-howto-move-arm.md).

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a><a name="delete"></a>Deprovisioning and deleting an ExpressRoute circuit (Desaprovisionar e eliminar um circuito do ExpressRoute)
Tenha em atenção as seguintes informações:

* Tem de desassociar todas as redes virtuais do circuito do ExpressRoute. Se esta operação falhar, verifique se alguma rede virtual está ligada ao circuito.
* Se o estado de prestação do serviço de circuito ExpressRoute for **Provisioning** ou **Provisioned,** deve trabalhar com o seu prestador de serviços para desprovisionar o circuito do seu lado. Continuamos a reservar recursos e a cobrar até que o prestador de serviços complete a desprovisionamento do circuito e nos notifique.
* Se o prestador de serviços tiver desprovisionado o circuito (o estado de prestação do prestador de serviços está definido para **Não aprovisionado),** pode apagar o circuito. Isto interrompe a faturação do circuito.

Pode eliminar o seu circuito ExpressRoute executando o seguinte comando:

```azurepowershell-interactive
Remove-AzExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"
```

## <a name="next-steps"></a>Próximos passos

Depois de criar o seu circuito, certifique-se de que faz os seguintes passos:

* [Crie e modifique o encaminhamento para o seu circuito ExpressRoute](expressroute-howto-routing-arm.md)
* [Ligue a sua rede virtual ao circuito ExpressRoute](expressroute-howto-linkvnet-arm.md)
