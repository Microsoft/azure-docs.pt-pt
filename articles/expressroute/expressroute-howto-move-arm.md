---
title: 'Azure ExpressRoute: Mova os circuitos clássicos para o Gestor de Recursos'
description: Esta página descreve como mover um circuito clássico para o modelo de implementação do Gestor de Recursos usando o PowerShell.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 02/25/2019
ms.author: duau
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 460ea446fc6dfc43e81a1a57bbba032a61f3a72d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "90532550"
---
# <a name="move-expressroute-circuits-from-classic-to-resource-manager-deployment-model-using-powershell"></a>Mova os circuitos ExpressRoute do modelo de implementação clássico para o gestor de recursos usando o PowerShell

Para utilizar um circuito ExpressRoute para os modelos clássicos e gestores de recursos, tem de mover o circuito para o modelo de implementação do Gestor de Recursos. As seguintes secções ajudam-no a mover o circuito utilizando o PowerShell.

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

* Verifique se instalou os módulos clássicos e Az Azure PowerShell localmente no seu computador. Para obter mais informações, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/).
* Certifique-se de que reviu os [pré-requisitos,](expressroute-prerequisites.md) [os requisitos de encaminhamento](expressroute-routing.md)e [os fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.
* Reveja as informações fornecidas no [movimento de um circuito ExpressRoute de clássico para gestor de recursos.](expressroute-move.md) Certifique-se de que compreende completamente os limites e limitações.
* Verifique se o circuito está totalmente operacional no modelo clássico de implantação.
* Certifique-se de que tem um grupo de recursos criado no modelo de implementação do Gestor de Recursos.

## <a name="move-an-expressroute-circuit"></a>Mover um circuito ExpressRoute

### <a name="step-1-gather-circuit-details-from-the-classic-deployment-model"></a>Passo 1: Recolha detalhes do circuito do modelo clássico de implementação

Inscreva-se no ambiente clássico do Azure e recolha a chave de serviço.

1. Inicie sessão na sua conta do Azure.

   ```powershell
   Add-AzureAccount
   ```

2. Selecione a subscrição do Azure adequada.

   ```powershell
   Select-AzureSubscription "<Enter Subscription Name here>"
   ```

3. Importe os módulos PowerShell para Azure e ExpressRoute.

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
   ```

4. Utilize o cmdlet abaixo para obter as chaves de serviço de todos os seus circuitos ExpressRoute. Depois de recuperar as teclas, copie a chave de **serviço** do circuito que pretende mover para o modelo de implementação do Gestor de Recursos.

   ```powershell
   Get-AzureDedicatedCircuit
   ```

### <a name="step-2-sign-in-and-create-a-resource-group"></a>Passo 2: Iniciar sessão e criar um grupo de recursos

Inscreva-se no ambiente de Gestor de Recursos e crie um novo grupo de recursos.

1. Inscreva-se no ambiente do Gestor de Recursos Azure.

   ```powershell
   Connect-AzAccount
   ```

2. Selecione a subscrição do Azure adequada.

   ```powershell
   Get-AzSubscription -SubscriptionName "<Enter Subscription Name here>" | Select-AzSubscription
   ```

3. Modifique o snippet abaixo para criar um novo grupo de recursos se ainda não tiver um grupo de recursos.

   ```powershell
   New-AzResourceGroup -Name "DemoRG" -Location "West US"
   ```

### <a name="step-3-move-the-expressroute-circuit-to-the-resource-manager-deployment-model"></a>Passo 3: Mover o circuito ExpressRoute para o modelo de implementação do Gestor de Recursos

Está agora pronto para mover o seu circuito ExpressRoute do modelo clássico de implementação para o modelo de implementação do Gestor de Recursos. Antes de prosseguir, reveja as informações fornecidas na [Moveção de um circuito ExpressRoute do clássico para o modelo de implementação do Gestor de Recursos](expressroute-move.md).

Para mover o seu circuito, modifique e corra o seguinte corte:

```powershell
Move-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "DemoRG" -Location "West US" -ServiceKey "<Service-key>"
```

Em modo clássico, um circuito ExpressRoute não tem o conceito de estar ligado a uma região. No entanto, no Gestor de Recursos, todos os recursos precisam de ser mapeados para uma região de Azure. A região especificada no Move-AzExpressRouteCircuit cmdlet pode ser tecnicamente qualquer região. Para fins organizacionais, pode querer escolher uma região que represente de perto a sua localização de observação.

> [!NOTE]
> * Depois de mover o seu circuito expressRoute clássico para o modelo de implementação do Gestor de Recursos, terá acesso aos modelos clássicos e gestores de recursos por padrão.
> * O novo nome que estiver listado no cmdlet anterior será utilizado para endereçar o recurso. O circuito será essencialmente renomeado.

## <a name="modify-circuit-access"></a>Modificar o acesso ao circuito

### <a name="to-enable-expressroute-circuit-access-for-both-deployment-models"></a>Para permitir o acesso ao circuito ExpressRoute para ambos os modelos de implantação

Pode permitir o acesso ao modelo clássico de implementação dos circuitos ExpressRoute que foram criados no modelo de implementação do Gestor de Recursos. Executar os seguintes cmdlets para permitir o acesso a ambos os modelos de implantação:

1. Pegue os detalhes do circuito.

   ```powershell
   $ckt = Get-AzExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
   ```

2. Desa um conjunto de "Permitir operações clássicas" para TRUE.

   ```powershell
   $ckt.AllowClassicOperations = $true
   ```

3. Atualize o circuito. Depois de concluída esta operação com sucesso, poderá ver o circuito no modelo clássico de implantação.

   ```powershell
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

4. Faça o seguinte cmdlet para obter os detalhes do circuito ExpressRoute. Deve poder ver a chave de serviço listada.

   ```powershell
   get-azurededicatedcircuit
   ```

5. Agora pode gerir ligações ao circuito ExpressRoute utilizando os comandos clássicos do modelo de implementação para VNets clássicos, e os comandos do Resource Manager para VNets do Gestor de Recursos. Os seguintes artigos ajudam-no a gerir ligações ao circuito ExpressRoute:

    * [Ligue a sua rede virtual ao seu circuito ExpressRoute no modelo de implementação do Gestor de Recursos](expressroute-howto-linkvnet-arm.md)
    * [Ligue a sua rede virtual ao seu circuito ExpressRoute no modelo clássico de implementação](expressroute-howto-linkvnet-classic.md)

### <a name="to-disable-expressroute-circuit-access-to-the-classic-deployment-model"></a>Para desativar o acesso do circuito ExpressRoute ao modelo clássico de implementação

Executar os seguintes cmdlets para desativar o acesso ao modelo de implementação clássico.

1. Obtenha detalhes do circuito ExpressRoute.

   ```powershell
   $ckt = Get-AzExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
   ```

2. Desa um conjunto de "Permitir operações clássicas" a FALSOs.

   ```powershell
   $ckt.AllowClassicOperations = $false
   ```

3. Atualize o circuito. Depois de concluída esta operação com sucesso, não poderá ver o circuito no modelo clássico de implantação.

   ```powershell
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

## <a name="next-steps"></a>Passos seguintes

* [Crie e modifique o encaminhamento para o seu circuito ExpressRoute](expressroute-howto-routing-arm.md)
* [Ligue a sua rede virtual ao circuito ExpressRoute](expressroute-howto-linkvnet-arm.md)
