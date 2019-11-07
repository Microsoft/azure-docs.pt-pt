---
title: 'Modificar um circuito do ExpressRoute: PowerShell: Azure clássico | Microsoft Docs'
description: Este artigo orienta você pelas etapas para verificar o status, atualizar ou excluir e desprovisionar seu circuito de modelo de implantação clássico do ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: cherylmc
ms.openlocfilehash: 9f1c05b85fac6dd0168d9c2b2944326800e90493
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73643676"
---
# <a name="modify-an-expressroute-circuit-using-powershell-classic"></a>Modificar um circuito do ExpressRoute com o PowerShell (clássico)

> [!div class="op_single_selector"]
> * [Portal do Azure](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [CLI do Azure](howto-circuit-cli.md)
> * [Modelo do Azure Resource Manager](expressroute-howto-circuit-resource-manager-template.md)
> * [portal do Azure de vídeo](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-circuit-classic.md)
>

Este artigo orienta você pelas etapas para verificar o status, atualizar ou excluir e desprovisionar seu circuito de modelo de implantação clássico do ExpressRoute. Este artigo aplica-se ao modelo de implementação clássica.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Acerca dos modelos de implementação do Azure**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-you-begin"></a>Antes de começar

Instale as versões mais recentes dos módulos do PowerShell do gerenciamento de serviços do Azure (SM) e do módulo ExpressRoute. Você não pode usar o ambiente CloudShell do Azure para executar módulos SM.

1. Use as instruções no artigo [instalando o módulo de gerenciamento de serviços](/powershell/azure/servicemanagement/install-azure-ps) para instalar o módulo de gerenciamento de serviços do Azure. Se você tiver o módulo AZ ou RM já instalado, certifique-se de usar '-AllowClobber '.
2. Importe os módulos instalados. Ao usar o exemplo a seguir, ajuste o caminho para refletir o local dos seus módulos do PowerShell instalados.

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\Azure.psd1'
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\ExpressRoute\ExpressRoute.psd1'
   ```
3. Para entrar em sua conta do Azure, abra o console do PowerShell com direitos elevados e conecte-se à sua conta. Use o exemplo a seguir para ajudá-lo a se conectar usando o módulo de gerenciamento de serviços:

   ```powershell
   Add-AzureAccount
   ```

## <a name="get-the-status-of-a-circuit"></a>Obter o status de um circuito

Você pode recuperar essas informações a qualquer momento usando o cmdlet `Get-AzureCircuit`. Fazer a chamada sem parâmetros lista todos os circuitos.

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

Você pode obter informações sobre um circuito do ExpressRoute específico passando a chave de serviço como um parâmetro para a chamada.

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

Você pode obter descrições detalhadas de todos os parâmetros executando o seguinte exemplo:

```powershell
get-help get-azurededicatedcircuit -detailed
```

## <a name="modify-a-circuit"></a>Modificar um circuito

Você pode modificar determinadas propriedades de um circuito do ExpressRoute sem afetar a conectividade.

Você pode executar as seguintes tarefas sem tempo de inatividade:

* Habilite ou desabilite um complemento Premium do ExpressRoute para o circuito do ExpressRoute.
* Aumente a largura de banda do circuito do ExpressRoute, desde que haja capacidade disponível na porta. Desatualização de largura de banda de um circuito não é suportada.
* Altere o plano de medição de dados limitados para dados ilimitados. Não há suporte para a alteração do plano de medição de dados ilimitados para dados limitados.
* Você pode habilitar e desabilitar *permitir operações clássicas*.

Consulte as [perguntas frequentes sobre o ExpressRoute](expressroute-faqs.md) para obter mais informações sobre limites e limitações.

### <a name="enable-the-expressroute-premium-add-on"></a>Habilitar o complemento Premium do ExpressRoute

Você pode habilitar o complemento Premium do ExpressRoute para seu circuito existente usando o seguinte cmdlet do PowerShell:

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

Seu circuito agora terá os recursos complementares do ExpressRoute Premium habilitados. Assim que o comando for executado com êxito, a cobrança do recurso de complemento Premium começará.

### <a name="disable-the-expressroute-premium-add-on"></a>Desabilitar o complemento Premium do ExpressRoute

> [!IMPORTANT]
> Essa operação poderá falhar se você estiver usando recursos que são maiores do que o permitido para o circuito padrão.
>
>

#### <a name="considerations"></a>Considerações

* Verifique se o número de redes virtuais vinculadas ao circuito é menor que 10 antes de fazer o downgrade de Premium para Standard. Se você não fizer isso, a solicitação de atualização falhará e você será cobrado pelas tarifas Premium.
* Você deve desvincular todas as redes virtuais em outras regiões do geopolítica. Se você não fizer isso, a solicitação de atualização falhará e você será cobrado pelas tarifas Premium.
* Sua tabela de rotas deve ter menos de 4.000 rotas para o emparelhamento privado. Se o tamanho da tabela de rotas for maior que 4.000 rotas, a sessão BGP cai e não será reabilitada até que o número de prefixos anunciados fique abaixo de 4.000.

#### <a name="to-disable-the-premium-add-on"></a>Para desabilitar o complemento Premium

Você pode desabilitar o complemento Premium do ExpressRoute para o circuito existente usando o seguinte cmdlet do PowerShell:

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

Verifique as [perguntas frequentes do ExpressRoute](expressroute-faqs.md) para obter as opções de largura de banda com suporte para seu provedor Você pode escolher qualquer tamanho maior que o tamanho do seu circuito existente, desde que a porta física (na qual o circuito é criado) permita.

> [!IMPORTANT]
> Talvez seja necessário recriar o circuito do ExpressRoute se houver capacidade inadequada na porta existente. Você não poderá atualizar o circuito se não houver capacidade adicional disponível nesse local.
>
> Não é possível reduzir a largura de banda de um circuito do ExpressRoute sem interrupções. O downgrade da largura de banda exige o desprovisionamento do circuito do ExpressRoute e o reprovisionamento de um novo circuito do ExpressRoute.
>
>

#### <a name="resize-a-circuit"></a>Redimensionar um circuito

Depois de decidir de qual tamanho você precisa, você pode usar o seguinte comando para redimensionar o circuito:

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

Depois que o circuito for dimensionado no lado da Microsoft, você deverá entrar em contato com seu provedor de conectividade para atualizar as configurações em seu lado para corresponder a essa alteração. A cobrança começa pela opção de largura de banda atualizada deste ponto em diante.

Se você vir o erro a seguir ao aumentar a largura de banda do circuito, isso significa que não há largura de banda suficiente restante na porta física em que o circuito existente é criado. Você deve excluir esse circuito e criar um novo circuito do tamanho necessário.

```powershell
Set-AzureDedicatedCircuitProperties : InvalidOperation : Insufficient bandwidth available to perform this circuit
update operation
At line:1 char:1
+ Set-AzureDedicatedCircuitProperties -ServiceKey ********************* ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
  + CategoryInfo          : CloseError: (:) [Set-AzureDedicatedCircuitProperties], CloudException
  + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.SetAzureDedicatedCircuitPropertiesCommand
```

## <a name="deprovision-and-delete-a-circuit"></a>Desprovisionar e excluir um circuito

### <a name="considerations"></a>Considerações

* Você deve desvincular todas as redes virtuais do circuito do ExpressRoute para que essa operação tenha sucesso. Verifique se você tem redes virtuais vinculadas ao circuito se essa operação falhar.
* Se o estado de provisionamento do provedor de serviço de circuito do **ExpressRoute for Provisionando** ou **provisionado** , você deverá trabalhar com seu provedor de serviços para desprovisionar o circuito no lado deles. Continuamos reservando recursos e cobramos até que o provedor de serviços conclua o desprovisionamento do circuito e nos notifique.
* Se o provedor de serviços tiver desprovisionado o circuito (o estado de provisionamento do provedor de serviços estiver definido como **não provisionado**), você poderá excluir o circuito. Isto interrompe a faturação do circuito.

#### <a name="delete-a-circuit"></a>Excluir um circuito

Você pode excluir o circuito do ExpressRoute executando o seguinte comando:

```powershell
Remove-AzureDedicatedCircuit -ServiceKey "*********************************"
```
