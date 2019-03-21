---
title: 'Modificar um circuito do ExpressRoute: PowerShell: Azure clássico | Documentos da Microsoft'
description: Este artigo orienta-o pelos passos para verificar o estado, update ou delete e desaprovisionar o circuito de modelo de implementação clássica do ExpressRoute.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: ganesr;cherylmc
ms.custom: seodec18
ms.openlocfilehash: 5bf6f5662e20b003bd387a6826ab87117be3f113
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58075787"
---
# <a name="modify-an-expressroute-circuit-using-powershell-classic"></a>Modificar um circuito do ExpressRoute com o PowerShell (clássico)

> [!div class="op_single_selector"]
> * [Portal do Azure](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [CLI do Azure](howto-circuit-cli.md)
> * [Vídeo - portal do Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-circuit-classic.md)
>

Este artigo orienta-o pelos passos para verificar o estado, update ou delete e desaprovisionar o circuito de modelo de implementação clássica do ExpressRoute. Este artigo aplica-se ao modelo de implementação clássica.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Acerca dos modelos de implementação do Azure**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-you-begin"></a>Antes de começar

Instale as versões mais recentes de módulos do PowerShell de gestão de serviço do Azure (SM) e o módulo do ExpressRoute.  Ao utilizar o exemplo seguinte, tenha em atenção que o número de versão (neste exemplo, 5.1.1) será alterado à medida que as versões mais recentes dos cmdlets são lançadas.

```powershell
Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
```

Se precisar de mais informações sobre o Azure PowerShell, veja [introdução aos cmdlets do Azure PowerShell](/powershell/azure/overview) para obter orientações passo a passo sobre como configurar o seu computador para utilizar os módulos do Azure PowerShell.

Para iniciar sessão sua conta do Azure, utilize o seguinte exemplo:

1. Abra a consola do PowerShell com direitos elevados e ligue-se à sua conta. Utilize o exemplo seguinte para o ajudar na ligação:

   ```powershell
   Connect-AzureRmAccount
   ```
2. Verifique as subscrições da conta.

   ```powershell
   Get-AzureRmSubscription
   ```
3. Se tiver mais do que uma subscrição, selecione a subscrição que pretende utilizar.

   ```powershell
   Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```

4. Em seguida, utilize o cmdlet seguinte para adicionar a sua subscrição do Azure para o PowerShell para o modelo de implementação clássica.

   ```powershell
   Add-AzureAccount
   ```

## <a name="get-the-status-of-a-circuit"></a>Obter o estado de um circuito

Pode obter estas informações em qualquer altura utilizando o `Get-AzureCircuit` cmdlet. Efetuar a chamada sem quaisquer parâmetros apresenta uma lista de todos os circuitos.

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

Pode obter informações sobre um circuito de ExpressRoute específico passando a chave do serviço como um parâmetro para a chamada.

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

Para obter descrições detalhadas dos parâmetros de executar o exemplo a seguir:

```powershell
get-help get-azurededicatedcircuit -detailed
```

## <a name="modify-a-circuit"></a>Modificar um circuito

Pode modificar algumas propriedades de um circuito do ExpressRoute sem afetar a conectividade.

Pode efetuar as seguintes tarefas sem tempo de inatividade:

* Ativar ou desativar um suplemento ExpressRoute premium para o seu circuito do ExpressRoute.
* Aumente a largura de banda do seu circuito do ExpressRoute, desde que haja capacidade na porta. Desatualização de largura de banda de um circuito não é suportada. 
* Altere o plano de medição de dados limitados para dados ilimitados. Não é suportada a alteração do plano de medição de dados ilimitados para dados limitados.
* Pode ativar e desativar *permitir operações clássicas*.

Consulte a [FAQ do ExpressRoute](expressroute-faqs.md) para obter mais informações sobre limites e limitações.

### <a name="enable-the-expressroute-premium-add-on"></a>Ativar o suplemento ExpressRoute premium

Pode ativar o suplemento ExpressRoute premium para o seu circuito existente ao utilizar o seguinte cmdlet do PowerShell:

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

O circuito agora têm as funcionalidades de suplemento ExpressRoute premium ativadas. Assim que o comando foi executado com êxito, começa a faturação para a capacidade de suplemento premium.

### <a name="disable-the-expressroute-premium-add-on"></a>Desativar o suplemento ExpressRoute premium

> [!IMPORTANT]
> Esta operação pode falhar se estiver a utilizar os recursos que são maiores que o que é permitido para o circuito standard.
> 
> 

#### <a name="considerations"></a>Considerações

* Certifique-se de que o número de redes virtuais ligadas ao circuito é inferior a 10 antes de mudar a versão de premium para standard. Se não o fizer, o seu pedido de atualização falha e é faturado as tarifas premium.
* Deve desassociar a todas as redes virtuais em outras regiões geopolíticas. Se não o fizer, o seu pedido de atualização falha e é faturado as tarifas premium.
* A tabela de rotas tem de ser inferior a 4000 rotas para peering privado. Se o tamanho da tabela de rota for superior a 4.000, a sessão de BGP ignora e não ser reenabled até que o número de prefixos anunciados fica abaixo de 4.000.

#### <a name="to-disable-the-premium-add-on"></a>Para desativar o suplemento premium

Pode desativar o suplemento ExpressRoute premium para o seu circuito existente ao utilizar o seguinte cmdlet do PowerShell:

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

### <a name="update-the-expressroute-circuit-bandwidth"></a>Atualizar a largura de banda do circuito do ExpressRoute

Verifique os [FAQ do ExpressRoute](expressroute-faqs.md) para opções de largura de banda para o seu fornecedor de suporte. Pode escolher qualquer tamanho, que é superior ao tamanho do seu circuito existente, desde que permite a porta física (em que o circuito é criado).

> [!IMPORTANT]
> Poderá ter de recriar o circuito do ExpressRoute, se houver a capacidade inadequada na porta existente. Não é possível atualizar o circuito se não existir nenhuma capacidade adicional disponível naquele local.
>
> Não é possível reduzir a largura de banda de um circuito do ExpressRoute sem interrupção. Desatualização de largura de banda exige que desaprovisionar o circuito do ExpressRoute e, em seguida, reaprovisionar um novo circuito do ExpressRoute.
> 
> 

#### <a name="resize-a-circuit"></a>Redimensionar um circuito

Depois de decidir o tamanho necessário, pode utilizar o seguinte comando para redimensionar o seu circuito:

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

Assim que o seu circuito tem sido dimensionado a cópia de segurança no lado do Microsoft, tem de contactar o seu fornecedor de conectividade para atualizar as configurações no seu lado para fazer corresponder esta alteração. Início da faturação para a opção de largura de banda atualizado a partir deste ponto num.

Se vir o erro seguinte ao aumentar a largura de banda do circuito, isso significa que há não resta nenhuma largura de banda suficiente na porta física onde o seu circuito existente é criado. Tem de eliminar este circuito e criar um novo circuito do tamanho que necessário.

```powershell
Set-AzureDedicatedCircuitProperties : InvalidOperation : Insufficient bandwidth available to perform this circuit
update operation
At line:1 char:1
+ Set-AzureDedicatedCircuitProperties -ServiceKey ********************* ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
  + CategoryInfo          : CloseError: (:) [Set-AzureDedicatedCircuitProperties], CloudException
  + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.SetAzureDedicatedCircuitPropertiesCommand
```

## <a name="deprovision-and-delete-a-circuit"></a>Desaprovisionar e eliminar um circuito

### <a name="considerations"></a>Considerações

* Deve desassociar a todas as redes virtuais do circuito ExpressRoute para esta operação seja bem-sucedida. Verifique se tem quaisquer redes virtuais que estão ligadas ao circuito se esta operação falhar.
* Se for o fornecedor de serviços de circuito do ExpressRoute estado de aprovisionamento **aprovisionamento** ou **aprovisionado** deve trabalhar com o fornecedor de serviços para desaprovisionar o circuito no seu lado. Continuamos a reservar recursos e a faturação é que o fornecedor de serviços seja concluída o circuito de desaprovisionamento e notifica-nos.
* Se o fornecedor de serviços tiver desaprovisionado o circuito (fornecedor de serviços de estado de aprovisionamento está definido como **não aprovisionado**), em seguida, pode eliminar o circuito. Isto interrompe a faturação do circuito.

#### <a name="delete-a-circuit"></a>Eliminar um circuito

Pode eliminar o seu circuito do ExpressRoute, executando o seguinte comando:

```powershell
Remove-AzureDedicatedCircuit -ServiceKey "*********************************"
```
