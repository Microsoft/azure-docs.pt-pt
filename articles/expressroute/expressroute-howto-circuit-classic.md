---
title: 'Azure ExpressRoute: Modificar um circuito: PowerShell:classic'
description: Este artigo percorre-o através dos passos para verificar o estado, atualizar ou eliminar e desprovisionar o seu circuito de modelo seleto clássico expressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: cherylmc
ms.openlocfilehash: e421a534b04f74d2a2eb0bc06aeffa52684ae17a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74931967"
---
# <a name="modify-an-expressroute-circuit-using-powershell-classic"></a>Modificar um circuito ExpressRoute utilizando powerShell (clássico)

> [!div class="op_single_selector"]
> * [Portal Azure](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Modelo Azure Resource Manager](expressroute-howto-circuit-resource-manager-template.md)
> * [Vídeo - Portal Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-circuit-classic.md)
>

Este artigo percorre-o através dos passos para verificar o estado, atualizar ou eliminar e desprovisionar o seu circuito de modelo seleto clássico expressRoute. Este artigo aplica-se ao modelo de implementação clássica.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Acerca dos modelos de implementação do Azure**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [classic powershell install instructions](../../includes/expressroute-poweshell-classic-install-include.md)]

## <a name="get-the-status-of-a-circuit"></a>Obtenha o estado de um circuito

Pode recuperar esta informação a `Get-AzureCircuit` qualquer momento utilizando o cmdlet. Fazer a chamada sem parâmetros lista todos os circuitos.

```powershell
Get-AzureDedicatedCircuit

Bandwidth                        : 200
CircuitName                      : MyTestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled

Bandwidth                        : 1000
CircuitName                      : MyAsiaCircuit
Location                         : Singapore
ServiceKey                       : #################################
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

Pode obter informações sobre um circuito expressRoute específico, passando a chave de serviço como parâmetro para a chamada.

```powershell
Get-AzureDedicatedCircuit -ServiceKey "*********************************"

Bandwidth                        : 200
CircuitName                      : MyTestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

Pode obter descrições detalhadas de todos os parâmetros executando o seguinte exemplo:

```powershell
get-help get-azurededicatedcircuit -detailed
```

## <a name="modify-a-circuit"></a>Modificar um circuito

Pode modificar certas propriedades de um circuito ExpressRoute sem afetar a conectividade.

Pode fazer as seguintes tarefas sem tempo de inatividade:

* Ative ou desative um complemento premium ExpressRoute para o seu circuito ExpressRoute.
* Aumente a largura de banda do seu circuito ExpressRoute desde que haja capacidade disponível na porta. Não é suportada a degradação da largura de banda de um circuito.
* Mude o plano de medição de Dados Medidos para Dados Ilimitados. Não é suportada a alteração do plano de medição de Dados Ilimitados para Dados Medidos.
* Pode ativar e desativar *permitir operações clássicas.*

Consulte o [ExpressRoute FAQ](expressroute-faqs.md) para obter mais informações sobre limites e limitações.

### <a name="enable-the-expressroute-premium-add-on"></a>Ativar o add-on premium ExpressRoute

Pode ativar o complemento premium ExpressRoute para o seu circuito existente utilizando o seguinte cmdlet PowerShell:

```powershell
Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Premium

Bandwidth                        : 1000
CircuitName                      : TestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Premium
Status                           : Enabled
```

O seu circuito terá agora as funcionalidades de complemento premium ExpressRoute ativadas. Assim que o comando tiver sido executado com sucesso, começa a faturação para a capacidade de complemento premium.

### <a name="disable-the-expressroute-premium-add-on"></a>Desativar o add-on premium ExpressRoute

> [!IMPORTANT]
> Esta operação pode falhar se estiver a utilizar recursos superiores aos permitidos para o circuito padrão.
>
>

#### <a name="considerations"></a>Considerações

* Certifique-se de que o número de redes virtuais ligadas ao circuito é inferior a 10 antes de descer de prémio para padrão. Se não o fizer, o seu pedido de atualização falha e é cobrado as tarifas premium.
* É preciso desligar todas as redes virtuais de outras regiões geopolíticas. Se não o fizer, o seu pedido de atualização falha e é cobrado as tarifas premium.
* A sua mesa de rotas deve ser inferior a 4.000 rotas para o peering privado. Se o tamanho da tabela de rotas for superior a 4.000 rotas, a sessão do BGP cai e não será reativada até que o número de prefixos anunciados seja inferior a 4.000.

#### <a name="to-disable-the-premium-add-on"></a>Para desativar o complemento premium

Pode desativar o complemento premium ExpressRoute para o seu circuito existente utilizando o seguinte cmdlet PowerShell:

```powershell

Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Standard

Bandwidth                        : 1000
CircuitName                      : TestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

### <a name="update-the-expressroute-circuit-bandwidth"></a>Atualize a largura de banda do circuito ExpressRoute

Verifique as opções de largura de banda suportadas do [ExpressRoute](expressroute-faqs.md) para o seu fornecedor. Pode escolher qualquer tamanho maior do que o tamanho do seu circuito existente, desde que a porta física (na qual o seu circuito é criado) o permita.

> [!IMPORTANT]
> Poderá ter de recriar o circuito ExpressRoute se houver uma capacidade inadequada na porta existente. Não é possível atualizar o circuito se não houver capacidade adicional disponível nesse local.
>
> Não é possível reduzir a largura de banda de um circuito ExpressRoute sem interrupções. A degradação da largura de banda requer que desprovisionie o circuito ExpressRoute e, em seguida, reforte um novo circuito ExpressRoute.
>
>

#### <a name="resize-a-circuit"></a>Redimensionar um circuito

Depois de decidir qual o tamanho de que precisa, pode utilizar o seguinte comando para redimensionar o seu circuito:

```powershell
Set-AzureDedicatedCircuitProperties -ServiceKey ********************************* -Bandwidth 1000

Bandwidth                        : 1000
CircuitName                      : TestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

Uma vez que o seu circuito tenha sido dimensionado do lado da Microsoft, deve contactar o seu fornecedor de conectividade para atualizar as configurações do seu lado para corresponder a esta alteração. A faturação começa para a opção de largura de banda atualizada a partir deste ponto.

Se vir o seguinte erro ao aumentar a largura de banda do circuito, significa que não resta largura de banda suficiente na porta física onde o circuito existente é criado. Tem de apagar este circuito e criar um novo circuito do tamanho de que necessita.

```powershell
Set-AzureDedicatedCircuitProperties : InvalidOperation : Insufficient bandwidth available to perform this circuit
update operation
At line:1 char:1
+ Set-AzureDedicatedCircuitProperties -ServiceKey ********************* ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
  + CategoryInfo          : CloseError: (:) [Set-AzureDedicatedCircuitProperties], CloudException
  + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.SetAzureDedicatedCircuitPropertiesCommand
```

## <a name="deprovision-and-delete-a-circuit"></a>Desprovisionamento e exclusão de um circuito

### <a name="considerations"></a>Considerações

* Deve desligar todas as redes virtuais do circuito ExpressRoute para que esta operação tenha sucesso. Verifique se tem alguma rede virtual ligada ao circuito se esta operação falhar.
* Se o estado de provisionamento do prestador de serviços do circuito ExpressRoute estiver **provisionamento** ou **provisionado,** deve trabalhar com o seu prestador de serviços para desprovisionar o circuito do seu lado. Continuamos a reservar recursos e a cobrar até que o prestador de serviços complete a desprovisionamento do circuito e nos notifique.
* Se o prestador de serviços tiver desprovisionado o circuito (o estado de provisionamento do prestador de serviços está definido para **não provisionado),** pode então apagar o circuito. Isto interrompe a faturação do circuito.

#### <a name="delete-a-circuit"></a>Apagar um circuito

Pode eliminar o circuito ExpressRoute executando o seguinte comando:

```powershell
Remove-AzureDedicatedCircuit -ServiceKey "*********************************"
```
