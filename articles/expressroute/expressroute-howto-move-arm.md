---
title: 'Azure ExpressRoute: mover circuitos clássicos para o Gerenciador de recursos'
description: Esta página descreve como mover um circuito de clássico para o modelo de implementação do Resource Manager com o PowerShell.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: cherylmc
ms.openlocfilehash: 4e49a3bc803733f5e78207fa3573c93395924d6a
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74080159"
---
# <a name="move-expressroute-circuits-from-classic-to-resource-manager-deployment-model-using-powershell"></a>Mover circuitos do ExpressRoute do clássico para o modelo de implementação do Resource Manager com o PowerShell

Para utilizar um circuito do ExpressRoute para o clássico e modelos de implementação do Resource Manager, tem de mover o circuito para o modelo de implementação do Resource Manager. As secções seguintes ajudam a mover o seu circuito com o PowerShell.

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

* Verifique se você instalou os módulos clássico e AZ Azure PowerShell localmente no seu computador. Para obter mais informações, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/overview).
* Certifique-se de que consultou os [pré-requisitos](expressroute-prerequisites.md), [requisitos de encaminhamento](expressroute-routing.md), e [fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.
* Reveja as informações que são fornecidas através de [mover um circuito do ExpressRoute do clássico para Resource Manager](expressroute-move.md). Certifique-se de que compreende totalmente os limites e limitações.
* Certifique-se de que o circuito é totalmente operacional no modelo de implementação clássica.
* Certifique-se de que tem um grupo de recursos que foi criado no modelo de implementação do Resource Manager.

## <a name="move-an-expressroute-circuit"></a>Mover um circuito do ExpressRoute

### <a name="step-1-gather-circuit-details-from-the-classic-deployment-model"></a>Passo 1: Recolher detalhes de circuito do modelo de implementação clássica

Inicie sessão no ambiente do Azure clássico e reunir a chave de serviço.

1. Inicie sessão na sua conta do Azure.

   ```powershell
   Add-AzureAccount
   ```

2. Selecione a subscrição do Azure adequada.

   ```powershell
   Select-AzureSubscription "<Enter Subscription Name here>"
   ```

3. Importe os módulos do PowerShell para o Azure e ExpressRoute.

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
   ```

4. Utilize o cmdlet abaixo para obter as chaves de serviço para todos os circuitos do ExpressRoute. Depois de obter as chaves, copiar os **chave de serviço** do circuito que pretende mover para o modelo de implementação do Resource Manager.

   ```powershell
   Get-AzureDedicatedCircuit
   ```

### <a name="step-2-sign-in-and-create-a-resource-group"></a>Passo 2: Iniciar sessão e criar um grupo de recursos

Inicie sessão no ambiente do Resource Manager e crie um novo grupo de recursos.

1. Inicie sessão no seu ambiente do Azure Resource Manager.

   ```powershell
   Connect-AzAccount
   ```

2. Selecione a subscrição do Azure adequada.

   ```powershell
   Get-AzSubscription -SubscriptionName "<Enter Subscription Name here>" | Select-AzSubscription
   ```

3. Modificar o fragmento abaixo para criar um novo grupo de recursos, se ainda não tiver um grupo de recursos.

   ```powershell
   New-AzResourceGroup -Name "DemoRG" -Location "West US"
   ```

### <a name="step-3-move-the-expressroute-circuit-to-the-resource-manager-deployment-model"></a>Passo 3: Mover o circuito do ExpressRoute para o modelo de implementação do Resource Manager

Está agora pronto para mudar o seu circuito do ExpressRoute do modelo de implementação clássica para o modelo de implementação do Resource Manager. Antes de continuar, reveja as informações fornecidas na [mover um circuito do ExpressRoute do clássico para o modelo de implementação do Resource Manager](expressroute-move.md).

Para mover o seu circuito, modificar e execute o seguinte fragmento:

```powershell
Move-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "DemoRG" -Location "West US" -ServiceKey "<Service-key>"
```

No modo clássico, um circuito do ExpressRoute não tem o conceito de que está a ser ligados a uma região. No entanto, no Gestor de recursos, cada recurso tem de ser mapeada para uma região do Azure. A região especificada no cmdlet Move-AzExpressRouteCircuit pode ser tecnicamente qualquer região. Para fins de organização, poderá escolher uma região que se representa a localização de peering.

> [!NOTE]
> Após concluir a movimentação, o novo nome listado no cmdlet anterior será utilizado para resolver o recurso. O circuito, essencialmente, será possível mudar o nome.
> 

## <a name="modify-circuit-access"></a>Modificar o acesso de circuito

### <a name="to-enable-expressroute-circuit-access-for-both-deployment-models"></a>Para ativar o acesso de circuito do ExpressRoute para ambos os modelos de implementação

Depois de mover o seu circuito de ExpressRoute clássico ao modelo de implementação do Resource Manager, pode ativar o acesso a ambos os modelos de implementação. Execute os seguintes cmdlets para permitir o acesso a ambos os modelos de implementação:

1. Obtenha os detalhes do circuito.

   ```powershell
   $ckt = Get-AzExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
   ```

2. Definir "Permitir operações clássicas" como TRUE.

   ```powershell
   $ckt.AllowClassicOperations = $true
   ```

3. Atualize o circuito. Depois desta operação foi concluída com êxito, poderá ver o circuito no modelo de implementação clássica.

   ```powershell
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

4. Execute o seguinte cmdlet para obter os detalhes do circuito ExpressRoute. Tem de ser capaz de ver a chave de serviço listada.

   ```powershell
   get-azurededicatedcircuit
   ```

5. Agora pode gerir ligações para o circuito do ExpressRoute com os comandos de modelo de implementação clássica para VNets clássicas e os comandos do Gestor de recursos para VNets do Resource Manager. Os seguintes artigos ajudam a gerir ligações para o circuito do ExpressRoute:

    * [Ligar a rede virtual para o seu circuito do ExpressRoute no modelo de implementação do Resource Manager](expressroute-howto-linkvnet-arm.md)
    * [Ligar a rede virtual para o seu circuito do ExpressRoute no modelo de implementação clássica](expressroute-howto-linkvnet-classic.md)

### <a name="to-disable-expressroute-circuit-access-to-the-classic-deployment-model"></a>Para desativar o acesso de circuito do ExpressRoute para o modelo de implementação clássica

Execute os seguintes cmdlets para desativar o acesso ao modelo de implementação clássica.

1. Obter os detalhes do circuito ExpressRoute.

   ```powershell
   $ckt = Get-AzExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
   ```

2. Definir "Permitir operações clássicas" para FALSO.

   ```powershell
   $ckt.AllowClassicOperations = $false
   ```

3. Atualize o circuito. Depois desta operação foi concluída com êxito, não será capaz de ver o circuito no modelo de implementação clássica.

   ```powershell
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

## <a name="next-steps"></a>Passos seguintes

* [Criar e modificar o encaminhamento para o seu circuito do ExpressRoute](expressroute-howto-routing-arm.md)
* [Ligar a rede virtual para o seu circuito do ExpressRoute](expressroute-howto-linkvnet-arm.md)
