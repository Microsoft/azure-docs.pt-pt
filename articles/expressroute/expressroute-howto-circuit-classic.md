---
title: 'Azure ExpressRoute: Modificar um circuito: PowerShell:classic'
description: Este artigo percorre os passos para verificar o estado, atualizar ou eliminar e desprovisionar o seu circuito de modelo clássico de implementação ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 11/05/2019
ms.author: duau
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: be45d49d3f445810c7ac6a38e3e12abe178a4bed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89396289"
---
# <a name="modify-an-expressroute-circuit-using-powershell-classic"></a>Modificar um circuito ExpressRoute utilizando o PowerShell (clássico)

> [!div class="op_single_selector"]
> * [Portal do Azure](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [CLI do Azure](howto-circuit-cli.md)
> * [Modelo Azure Resource Manager](expressroute-howto-circuit-resource-manager-template.md)
> * [Vídeo - Portal Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-circuit-classic.md)
>

Este artigo percorre os passos para verificar o estado, atualizar ou eliminar e desprovisionar o seu circuito de modelo clássico de implementação ExpressRoute. Este artigo aplica-se ao modelo de implementação clássica.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Acerca dos modelos de implementação do Azure**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [classic powershell install instructions](../../includes/expressroute-poweshell-classic-install-include.md)]

## <a name="get-the-status-of-a-circuit"></a>Obtenha o estado de um circuito

Pode recuperar esta informação a qualquer momento utilizando o `Get-AzureCircuit` cmdlet. Fazer a chamada sem parâmetros lista todos os circuitos.

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

Pode obter informações sobre um circuito ExpressRoute específico, passando a chave de serviço como parâmetro para a chamada.

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

* Ative ou desative um addon premium ExpressRoute para o seu circuito ExpressRoute.
* Aumente a largura de banda do seu circuito ExpressRoute desde que haja capacidade disponível na porta. A redução da largura de banda de um circuito não é suportada.
* Altere o plano de medição de Dados Medidos para Dados Ilimitados. A alteração do plano de medição de Dados Ilimitados para Dados Medidos não é suportada.
* Pode ativar e desativar *permitir operações clássicas.*

Consulte as [FAQ ExpressRoute](expressroute-faqs.md) para obter mais informações sobre limites e limitações.

### <a name="enable-the-expressroute-premium-add-on"></a>Ativar o complemento premium ExpressRoute

Pode ativar o addon premium ExpressRoute para o circuito existente utilizando o seguinte cmdlet PowerShell:

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

O seu circuito terá agora as funcionalidades adicionais premium ExpressRoute ativadas. Assim que o comando tiver sido executado com sucesso, começa a faturação para a capacidade de complemento premium.

### <a name="disable-the-expressroute-premium-add-on"></a>Desativar o complemento premium ExpressRoute

> [!IMPORTANT]
> Esta operação pode falhar se estiver a utilizar recursos superiores ao permitido para o circuito padrão.
>
>

#### <a name="considerations"></a>Considerações

* Certifique-se de que o número de redes virtuais ligadas ao circuito é inferior a 10 antes de reduzir de premium para padrão. Se não o fizer, o seu pedido de atualização falha e é cobrado as tarifas premium.
* Deve desvincular todas as redes virtuais de outras regiões geopolíticas. Se não o fizer, o seu pedido de atualização falha e é cobrado as tarifas premium.
* Sua mesa de rota deve ser menos de 4.000 rotas para espreitar privados. Se o tamanho da tabela de rotas for superior a 4.000 rotas, a sessão de BGP cai e não será reenutilizada até que o número de prefixos anunciados desça abaixo de 4.000.

#### <a name="to-disable-the-premium-add-on"></a>Para desativar o complemento premium

Pode desativar o addon premium ExpressRoute para o circuito existente utilizando o seguinte cmdlet PowerShell:

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

### <a name="update-the-expressroute-circuit-bandwidth"></a>Atualizar a largura de banda do circuito ExpressRoute

Consulte as [FAQ expressRoute](expressroute-faqs.md) para obter opções de largura de banda suportadas para o seu fornecedor. Pode escolher qualquer tamanho que seja maior do que o tamanho do circuito existente, desde que a porta física (na qual o circuito é criado) o permita.

> [!IMPORTANT]
> Poderá ter de recriar o circuito ExpressRoute se não houver capacidade inadequada na porta existente. Não é possível atualizar o circuito se não houver capacidade adicional disponível nesse local.
>
> Não é possível reduzir a largura de banda de um circuito ExpressRoute sem interrupções. A redução da largura de banda requer que desprovisione o circuito ExpressRoute e, em seguida, reprovisione um novo circuito ExpressRoute.
>
>

#### <a name="resize-a-circuit"></a>Redimensionar um circuito

Depois de decidir o tamanho de que precisa, pode usar o seguinte comando para redimensionar o seu circuito:

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

Uma vez que o seu circuito tenha sido dimensionado no lado da Microsoft, deve contactar o seu fornecedor de conectividade para atualizar as configurações do seu lado para corresponder a esta alteração. A faturação começa para a opção de largura de banda atualizada a partir deste ponto.

Se vir o seguinte erro ao aumentar a largura de banda do circuito, significa que não há largura de banda suficiente na porta física onde o circuito existente é criado. Tem de eliminar este circuito e criar um novo circuito do tamanho de que necessita.

```powershell
Set-AzureDedicatedCircuitProperties : InvalidOperation : Insufficient bandwidth available to perform this circuit
update operation
At line:1 char:1
+ Set-AzureDedicatedCircuitProperties -ServiceKey ********************* ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
  + CategoryInfo          : CloseError: (:) [Set-AzureDedicatedCircuitProperties], CloudException
  + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.SetAzureDedicatedCircuitPropertiesCommand
```

## <a name="deprovision-and-delete-a-circuit"></a>Desprovisionar e apagar um circuito

### <a name="considerations"></a>Considerações

* Tem de desvincular todas as redes virtuais do circuito ExpressRoute para que esta operação tenha êxito. Verifique se tem redes virtuais ligadas ao circuito se esta operação falhar.
* Se o estado de prestação do serviço de circuito ExpressRoute for **Provisioning** ou **Provisioned,** deve trabalhar com o seu prestador de serviços para desprovisionar o circuito do seu lado. Continuamos a reservar recursos e a cobrar até que o prestador de serviços complete a desprovisionamento do circuito e nos notifique.
* Se o prestador de serviços tiver desprovisionado o circuito (o estado de prestação do prestador de serviços está definido para **Não aprovisionado),** pode então apagar o circuito. Isto interrompe a faturação do circuito.

#### <a name="delete-a-circuit"></a>Apagar um circuito

Pode eliminar o seu circuito ExpressRoute executando o seguinte comando:

```powershell
Remove-AzureDedicatedCircuit -ServiceKey "*********************************"
```
