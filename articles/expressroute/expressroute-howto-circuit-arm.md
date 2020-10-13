---
title: 'Quickstart: Criar e modificar um circuito com ExpressRoute - Azure PowerShell'
description: Criar, providenciar, verificar, atualizar, eliminar e desprovisionar um circuito ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: quickstart
ms.date: 10/05/2020
ms.author: duau
ms.openlocfilehash: b5ac53c44429e23e2d22a934a9dc71bd485ec4cd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91761911"
---
# <a name="quickstart-create-and-modify-an-expressroute-circuit-using-azure-powershell"></a>Quickstart: Criar e modificar um circuito ExpressRoute utilizando a Azure PowerShell

Este quickstart mostra-lhe como criar um circuito ExpressRoute utilizando cmdlets PowerShell e o modelo de implementação do Gestor de Recursos Azure. Também pode verificar o estado, atualizar, excluir ou desprovisionar um circuito.

## <a name="prerequisites"></a>Pré-requisitos

* Reveja os [pré-requisitos](expressroute-prerequisites.md) e [fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.
* Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Azure PowerShell instalado localmente ou Azure Cloud Shell

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Criar e providenciar um circuito ExpressRoute
### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Inscreva-se na sua conta Azure e selecione a sua subscrição

[!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

### <a name="get-the-list-of-supported-providers-locations-and-bandwidths"></a>Obtenha a lista de fornecedores, locais e larguras de banda apoiadas
Antes de criar um circuito ExpressRoute, precisa da lista de fornecedores de conectividade suportados, locais e opções de largura de banda.

O cmdlet PowerShell **Get-AzExpressRouteServiceProvider** devolve estas informações, que utilizará em etapas posteriores:

```azurepowershell-interactive
Get-AzExpressRouteServiceProvider
```

Verifique se o seu fornecedor de conectividade está listado lá. Tome nota das seguintes informações, que necessita mais tarde quando criar um circuito:

* Nome
* Localizaçãos de pares
* Largura de bandaOffered

Está agora pronto para criar um circuito ExpressRoute.

### <a name="create-an-expressroute-circuit"></a>Criar um circuito do ExpressRoute
Se ainda não tem um grupo de recursos, tem de criar um antes de criar o seu circuito ExpressRoute. Pode fazê-lo executando o seguinte comando:

```azurepowershell-interactive
New-AzResourceGroup -Name "ExpressRouteResourceGroup" -Location "West US"
```

O exemplo a seguir mostra como criar um circuito ExpressRoute de 200-Mbps através do Equinix em Silicon Valley. Se estiver a utilizar um fornecedor diferente e diferentes definições, substitua essa informação quando fizer o seu pedido. Utilize o seguinte exemplo para solicitar uma nova chave de serviço:

```azurepowershell-interactive
New-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200
```

Certifique-se de que especifica o nível SKU e a família SKU corretas:

* O nível SKU determina se um circuito ExpressRoute é [Local,](expressroute-faqs.md#expressroute-local)Standard ou [Premium](expressroute-faqs.md#expressroute-premium). Pode especificar *Local,**Standard ou *Premium.* Não pode mudar o SKU de *Standard/Premium* para *Local.*
* A família SKU determina o tipo de faturação. Pode especificar *o MeteredData* para um plano de dados medido e *oData Ilimitado* para um plano de dados ilimitado. Pode alterar o tipo de faturação de *MeteredData* para *UnlimitedData,* mas não pode alterar o tipo de *Data Ilimitado* para *MeteredData*. Um circuito *local* é sempre *OData Ilimitado*.

> [!IMPORTANT]
> O seu circuito ExpressRoute é faturado a partir do momento em que uma chave de serviço é emitida. Certifique-se de que efetua esta operação quando o fornecedor de conectividade estiver pronto para o fornecimento do circuito.
>

A resposta contém a chave de serviço. Pode obter descrições detalhadas de todos os parâmetros executando o seguinte comando:

```azurepowershell-interactive
get-help New-AzExpressRouteCircuit -detailed
```


### <a name="list-all-expressroute-circuits"></a>Listar todos os circuitos ExpressRoute
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

### <a name="send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>Envie a chave de serviço para o seu fornecedor de conectividade para provisionamento
*O ServiceProviderProvisioningState* fornece-lhe informações sobre o estado atual do fornecimento do lado do prestador de serviços. O estado fornece-lhe o estado do lado da Microsoft. Para obter mais informações sobre estados de provisão de circuitos, consulte [Workflows](expressroute-workflows.md#expressroute-circuit-provisioning-states).

Quando se cria um novo circuito ExpressRoute, o circuito encontra-se no seguinte estado:

```azurepowershell
ServiceProviderProvisioningState : NotProvisioned
CircuitProvisioningState         : Enabled
```

O circuito muda para o seguinte estado quando o fornecedor de conectividade está atualmente a permitir-lhe:

```azurepowershell
ServiceProviderProvisioningState : Provisioning
Status                           : Enabled
```

Para utilizar o circuito ExpressRoute, deve estar no seguinte estado:

```azurepowershell
ServiceProviderProvisioningState : Provisioned
CircuitProvisioningState         : Enabled
```

### <a name="periodically-check-the-status-and-the-state-of-the-circuit-key"></a>Verifique periodicamente o estado e o estado da chave do circuito
A verificação do estado e do estado da chave de serviço informá-lo-á quando o seu fornecedor tiver provisionado o seu circuito. Após a configuração do circuito, o *ServiceProviderProvisioningState* aparece como *Provisionado,* como mostra o seguinte exemplo:

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

### <a name="create-your-routing-configuration"></a>Crie a sua configuração de encaminhamento
Para obter instruções passo a passo, consulte o artigo de configuração do [roteamento do circuito ExpressRoute](expressroute-howto-routing-arm.md) para criar e modificar os pares de circuitos.

> [!IMPORTANT]
> Estas instruções aplicam-se apenas aos circuitos criados com prestadores de serviços que oferecem serviços de conectividade de camada 2. Se estiver a utilizar um prestador de serviços que oferece serviços geridos de camada 3 (normalmente um IP VPN, como MPLS), o seu fornecedor de conectividade configura e gere o encaminhamento para si.
>

### <a name="link-a-virtual-network-to-an-expressroute-circuit"></a>Ligar uma rede virtual a um circuito ExpressRoute
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

* Ative ou desative um addon premium ExpressRoute para o seu circuito ExpressRoute. Mudar o SKU de *Standard/Premium* para *Local* não é suportado.
* Aumente a largura de banda do seu circuito ExpressRoute desde que haja capacidade disponível na porta. Reduzir a largura de banda de um circuito não é suportado.
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

Tenha em atenção as seguintes informações:

* Antes de baixar de prémio para padrão, deve garantir que o número de redes virtuais ligadas ao circuito seja inferior a 10. Se não o fizer, o seu pedido de atualização falha e cobramos-lhe taxas premium.
* Todas as redes virtuais de outras regiões geopolíticas devem ser inicialmente desvinculadas. Se não remover o link, o seu pedido de atualização falhará e continuaremos a cobrar-lhe tarifas premium.
* Sua mesa de rota deve ser menos de 4.000 rotas para espreitar privados. Se o tamanho da tabela de rotas for superior a 4.000 rotas, a sessão de BGP diminuirá. A sessão de BGP não será re habilitada até que o número de prefixos anunciados seja inferior a 4.000.

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

O seu circuito será atualizado do lado da Microsoft. Em seguida, deve contactar o seu fornecedor de conectividade para atualizar as configurações do seu lado para corresponder a esta alteração. Depois de fazer esta notificação, começaremos a cobrar-lhe a opção de largura de banda atualizada.

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

## <a name="deprovisioning-an-expressroute-circuit"></a><a name="delete"></a>Desprovisionamento de um circuito ExpressRoute
Tenha em atenção as seguintes informações:

* Todas as redes virtuais devem ser desligadas do circuito ExpressRoute. Se esta operação falhar, verifique se alguma rede virtual está ligada ao circuito.
* Se o estado de prestação do serviço de circuito ExpressRoute for **Provisioning** ou **Provisioned,** deve trabalhar com o seu prestador de serviços para desprovisionar o circuito do seu lado. Continuamos a reservar recursos e a cobrar até que o prestador de serviços complete a desprovisionamento do circuito e nos notifique.
* Se o prestador de serviços tiver desprovisionado o circuito, o que significa que o estado de fornecimento do prestador de serviços é definido como **Não provisionado,** pode eliminar o circuito. A faturação do circuito vai parar.

## <a name="clean-up-resources"></a><a name="cleanup"></a>Limpar os recursos

Pode eliminar o seu circuito ExpressRoute executando o seguinte comando:

```azurepowershell-interactive
Remove-AzExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"
```

## <a name="next-steps"></a>Passos seguintes

Depois de criar o seu circuito e foreá-lo com o seu fornecedor, continue até ao passo seguinte para configurar o espreitamento:

> [!div class="nextstepaction"]
> [Crie e modifique o encaminhamento para o seu circuito ExpressRoute](expressroute-howto-routing-arm.md)
