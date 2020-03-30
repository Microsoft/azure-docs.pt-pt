---
title: 'Azure ExpressRoute: Mover circuitos clássicos para Gestor de Recursos'
description: Esta página descreve como mover um circuito clássico para o modelo de implementação do Gestor de Recursos usando o PowerShell.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: cherylmc
ms.openlocfilehash: 4e49a3bc803733f5e78207fa3573c93395924d6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74080159"
---
# <a name="move-expressroute-circuits-from-classic-to-resource-manager-deployment-model-using-powershell"></a>Mova os circuitos ExpressRoute do modelo clássico para o gestor de recursos usando powerShell

Para utilizar um circuito ExpressRoute para os modelos clássicos e de implementação do Gestor de Recursos, tem de mover o circuito para o modelo de implementação do Gestor de Recursos. As seguintes secções ajudam-no a mover o seu circuito utilizando o PowerShell.

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

* Verifique se instalou os módulos clássicos e az Azure PowerShell localmente no seu computador. Para obter mais informações, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/overview).
* Certifique-se de que reviu os [pré-requisitos,](expressroute-prerequisites.md) [os requisitos de encaminhamento](expressroute-routing.md)e [os fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.
* Reveja as informações fornecidas no âmbito da [Mudança de um circuito ExpressRoute do clássico para o Gestor de Recursos](expressroute-move.md). Certifique-se de que compreende completamente os limites e limitações.
* Verifique se o circuito está totalmente operacional no modelo de implantação clássico.
* Certifique-se de que tem um grupo de recursos criado no modelo de implementação do Gestor de Recursos.

## <a name="move-an-expressroute-circuit"></a>Mova um circuito ExpressRoute

### <a name="step-1-gather-circuit-details-from-the-classic-deployment-model"></a>Passo 1: Recolher detalhes do circuito do modelo clássico de implantação

Inscreva-se no ambiente clássico do Azure e recolha a chave de serviço.

1. Inicie sessão na sua conta do Azure.

   ```powershell
   Add-AzureAccount
   ```

2. Selecione a subscrição do Azure adequada.

   ```powershell
   Select-AzureSubscription "<Enter Subscription Name here>"
   ```

3. Importar os módulos PowerShell para Azure e ExpressRoute.

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
   ```

4. Utilize o cmdlet abaixo para obter as chaves de serviço para todos os seus circuitos ExpressRoute. Depois de recuperar as teclas, copie a chave de **serviço** do circuito que pretende mover-se para o modelo de implementação do Gestor de Recursos.

   ```powershell
   Get-AzureDedicatedCircuit
   ```

### <a name="step-2-sign-in-and-create-a-resource-group"></a>Passo 2: Iniciar sessão e criar um grupo de recursos

Inscreva-se no ambiente do Gestor de Recursos e crie um novo grupo de recursos.

1. Inscreva-se no seu ambiente de Gestor de Recursos Azure.

   ```powershell
   Connect-AzAccount
   ```

2. Selecione a subscrição do Azure adequada.

   ```powershell
   Get-AzSubscription -SubscriptionName "<Enter Subscription Name here>" | Select-AzSubscription
   ```

3. Modifique o corte abaixo para criar um novo grupo de recursos se ainda não tiver um grupo de recursos.

   ```powershell
   New-AzResourceGroup -Name "DemoRG" -Location "West US"
   ```

### <a name="step-3-move-the-expressroute-circuit-to-the-resource-manager-deployment-model"></a>Passo 3: Mover o circuito ExpressRoute para o modelo de implementação do Gestor de Recursos

Está agora pronto para mover o seu circuito ExpressRoute do modelo de implementação clássico para o modelo de implementação do Gestor de Recursos. Antes de prosseguir, reveja as informações fornecidas na Mudança de [um circuito ExpressRoute do clássico para o modelo](expressroute-move.md)de implementação do Gestor de Recursos .

Para mover o seu circuito, modifique e corra o seguinte corte:

```powershell
Move-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "DemoRG" -Location "West US" -ServiceKey "<Service-key>"
```

Em modo clássico, um circuito ExpressRoute não tem o conceito de estar ligado a uma região. No entanto, no Gestor de Recursos, todos os recursos precisam de ser mapeados para uma região do Azure. A região especificada no Move-AzExpressRouteCircuit cmdlet pode ser tecnicamente qualquer região. Para fins organizacionais, você pode querer escolher uma região que represente de perto a sua localização de pares.

> [!NOTE]
> Após a mudança ter terminado, o novo nome listado no cmdlet anterior será utilizado para abordar o recurso. O circuito será essencialmente renomeado.
> 

## <a name="modify-circuit-access"></a>Modificar o acesso ao circuito

### <a name="to-enable-expressroute-circuit-access-for-both-deployment-models"></a>Para permitir o acesso ao circuito ExpressRoute para ambos os modelos de implementação

Depois de mover o seu circuito clássico ExpressRoute para o modelo de implementação do Gestor de Recursos, pode permitir o acesso a ambos os modelos de implementação. Executar os seguintes cmdlets para permitir o acesso a ambos os modelos de implementação:

1. Pegue os detalhes do circuito.

   ```powershell
   $ckt = Get-AzExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
   ```

2. Definir "Permitir operações clássicas" para TRUE.

   ```powershell
   $ckt.AllowClassicOperations = $true
   ```

3. Atualize o circuito. Depois desta operação ter terminado com sucesso, poderá ver o circuito no modelo de implantação clássico.

   ```powershell
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

4. Corra o seguinte cmdlet para obter os detalhes do circuito ExpressRoute. Deve poder ver a chave de serviço listada.

   ```powershell
   get-azurededicatedcircuit
   ```

5. Agora pode gerir ligações para o circuito ExpressRoute utilizando os comandos clássicos do modelo de implementação para VNets clássicos, e os comandos do Gestor de Recursos para VNets gestor de recursos. Os seguintes artigos ajudam-no a gerir ligações ao circuito ExpressRoute:

    * [Ligue a sua rede virtual ao circuito ExpressRoute no modelo de implementação do Gestor de Recursos](expressroute-howto-linkvnet-arm.md)
    * [Ligue a sua rede virtual ao circuito ExpressRoute no modelo clássico de implementação](expressroute-howto-linkvnet-classic.md)

### <a name="to-disable-expressroute-circuit-access-to-the-classic-deployment-model"></a>Para desativar o acesso ao circuito ExpressRoute ao modelo clássico de implementação

Executar os seguintes cmdlets para desativar o acesso ao modelo de implantação clássico.

1. Obtenha detalhes do circuito ExpressRoute.

   ```powershell
   $ckt = Get-AzExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
   ```

2. Descoloque "Permitir operações clássicas" para FALSE.

   ```powershell
   $ckt.AllowClassicOperations = $false
   ```

3. Atualize o circuito. Depois desta operação ter terminado com sucesso, não poderá ver o circuito no modelo de implantação clássico.

   ```powershell
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

## <a name="next-steps"></a>Passos seguintes

* [Crie e modifique o encaminhamento para o seu circuito ExpressRoute](expressroute-howto-routing-arm.md)
* [Ligue a sua rede virtual ao circuito ExpressRoute](expressroute-howto-linkvnet-arm.md)
