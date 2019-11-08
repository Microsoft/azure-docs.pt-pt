---
title: 'Mover circuitos do clássico para o Resource Manager-ExpressRoute: PowerShell: Azure | Microsoft Docs'
description: Esta página descreve como mover um circuito clássico para o modelo de implantação do Gerenciador de recursos usando o PowerShell.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 34dd7ed4c6352ed90793288f918d78b7fb07af80
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748238"
---
# <a name="move-expressroute-circuits-from-classic-to-resource-manager-deployment-model-using-powershell"></a>Mover circuitos do ExpressRoute do modelo de implantação clássico para o Gerenciador de recursos usando o PowerShell

Para usar um circuito do ExpressRoute para os modelos de implantação clássico e do Resource Manager, você deve mover o circuito para o modelo de implantação do Gerenciador de recursos. As seções a seguir ajudam a mover seu circuito usando o PowerShell.

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

* Verifique se você instalou os módulos clássico e AZ Azure PowerShell localmente no seu computador. Para obter mais informações, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/overview).
* Verifique se você examinou os [pré-requisitos](expressroute-prerequisites.md), [os requisitos de roteamento](expressroute-routing.md)e os [fluxos de trabalho](expressroute-workflows.md) antes de começar a configuração.
* Examine as informações fornecidas em [movendo um circuito do ExpressRoute do clássico para o Gerenciador de recursos](expressroute-move.md). Certifique-se de compreender totalmente os limites e as limitações.
* Verifique se o circuito está totalmente operacional no modelo de implantação clássico.
* Verifique se você tem um grupo de recursos que foi criado no modelo de implantação do Gerenciador de recursos.

## <a name="move-an-expressroute-circuit"></a>Mover um circuito do ExpressRoute

### <a name="step-1-gather-circuit-details-from-the-classic-deployment-model"></a>Etapa 1: coletar detalhes do circuito do modelo de implantação clássico

Entre no ambiente clássico do Azure e colete a chave de serviço.

1. Inicie sessão na sua conta do Azure.

   ```powershell
   Add-AzureAccount
   ```

2. Selecione a subscrição do Azure adequada.

   ```powershell
   Select-AzureSubscription "<Enter Subscription Name here>"
   ```

3. Importe os módulos do PowerShell para o Azure e o ExpressRoute.

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
   ```

4. Use o cmdlet abaixo para obter as chaves de serviço para todos os seus circuitos do ExpressRoute. Depois de recuperar as chaves, copie a **chave de serviço** do circuito que você deseja mover para o modelo de implantação do Resource Manager.

   ```powershell
   Get-AzureDedicatedCircuit
   ```

### <a name="step-2-sign-in-and-create-a-resource-group"></a>Etapa 2: entrar e criar um grupo de recursos

Entre no ambiente do Resource Manager e crie um novo grupo de recursos.

1. Entre em seu ambiente de Azure Resource Manager.

   ```powershell
   Connect-AzAccount
   ```

2. Selecione a subscrição do Azure adequada.

   ```powershell
   Get-AzSubscription -SubscriptionName "<Enter Subscription Name here>" | Select-AzSubscription
   ```

3. Modifique o trecho abaixo para criar um novo grupo de recursos se você ainda não tiver um grupo de recursos.

   ```powershell
   New-AzResourceGroup -Name "DemoRG" -Location "West US"
   ```

### <a name="step-3-move-the-expressroute-circuit-to-the-resource-manager-deployment-model"></a>Etapa 3: mover o circuito do ExpressRoute para o modelo de implantação do Gerenciador de recursos

Agora você está pronto para mover o circuito do ExpressRoute do modelo de implantação clássico para o modelo de implantação do Gerenciador de recursos. Antes de continuar, examine as informações fornecidas em [movendo um circuito do ExpressRoute do clássico para o modelo de implantação do Resource Manager](expressroute-move.md).

Para mover o circuito, modifique e execute o seguinte trecho de código:

```powershell
Move-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "DemoRG" -Location "West US" -ServiceKey "<Service-key>"
```

No modo clássico, um circuito do ExpressRoute não tem o conceito de estar vinculado a uma região. No entanto, no Gerenciador de recursos, todos os recursos precisam ser mapeados para uma região do Azure. A região especificada no cmdlet Move-AzExpressRouteCircuit pode ser tecnicamente qualquer região. Para fins organizacionais, convém escolher uma região que represente seu local de emparelhamento.

> [!NOTE]
> Após a conclusão da movimentação, o novo nome listado no cmdlet anterior será usado para endereçar o recurso. O circuito será basicamente renomeado.
> 

## <a name="modify-circuit-access"></a>Modificar o acesso ao circuito

### <a name="to-enable-expressroute-circuit-access-for-both-deployment-models"></a>Para habilitar o acesso ao circuito do ExpressRoute para ambos os modelos de implantação

Depois de mover o circuito do ExpressRoute clássico para o modelo de implantação do Gerenciador de recursos, você pode habilitar o acesso a ambos os modelos de implantação. Execute os seguintes cmdlets para habilitar o acesso a ambos os modelos de implantação:

1. Obtenha os detalhes do circuito.

   ```powershell
   $ckt = Get-AzExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
   ```

2. Defina "permitir operações clássicas" como TRUE.

   ```powershell
   $ckt.AllowClassicOperations = $true
   ```

3. Atualize o circuito. Depois que essa operação for concluída com êxito, você poderá exibir o circuito no modelo de implantação clássico.

   ```powershell
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

4. Execute o cmdlet a seguir para obter os detalhes do circuito do ExpressRoute. Você deve ser capaz de ver a chave de serviço listada.

   ```powershell
   get-azurededicatedcircuit
   ```

5. Agora você pode gerenciar links para o circuito do ExpressRoute usando os comandos do modelo de implantação clássico para VNets clássicas e os comandos do Gerenciador de recursos para VNets do Resource Manager. Os artigos a seguir ajudam a gerenciar links para o circuito do ExpressRoute:

    * [Vincular sua rede virtual ao circuito do ExpressRoute no modelo de implantação do Gerenciador de recursos](expressroute-howto-linkvnet-arm.md)
    * [Vincular sua rede virtual ao circuito do ExpressRoute no modelo de implantação clássico](expressroute-howto-linkvnet-classic.md)

### <a name="to-disable-expressroute-circuit-access-to-the-classic-deployment-model"></a>Para desabilitar o acesso do circuito do ExpressRoute ao modelo de implantação clássico

Execute os cmdlets a seguir para desabilitar o acesso ao modelo de implantação clássico.

1. Obtenha detalhes do circuito do ExpressRoute.

   ```powershell
   $ckt = Get-AzExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
   ```

2. Defina "permitir operações clássicas" como falso.

   ```powershell
   $ckt.AllowClassicOperations = $false
   ```

3. Atualize o circuito. Depois que essa operação for concluída com êxito, você não poderá exibir o circuito no modelo de implantação clássico.

   ```powershell
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

## <a name="next-steps"></a>Passos seguintes

* [Criar e modificar o roteamento para o circuito do ExpressRoute](expressroute-howto-routing-arm.md)
* [Vincular sua rede virtual ao circuito do ExpressRoute](expressroute-howto-linkvnet-arm.md)
