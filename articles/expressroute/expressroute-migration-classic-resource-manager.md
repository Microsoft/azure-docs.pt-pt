---
title: 'Migre redes virtuais do clássico para Resource Manager - ExpressRoute: Azure: PowerShell | Microsoft Docs'
description: Esta página descreve como migrar redes virtuais associadas do ExpressRoute para o Resource Manager depois de mover o seu circuito.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: conceptual
ms.date: 01/17/2019
ms.author: ganesr;cherylmc
ms.custom: seodec18
ms.openlocfilehash: 5fab6c6d6af7b0f7c61fcff269a214aff96c7a1e
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57407328"
---
# <a name="migrate-expressroute-associated-virtual-networks-from-classic-to-resource-manager"></a>Migrar redes virtuais associadas do ExpressRoute do clássico para Resource Manager

Este artigo explica como migrar redes virtuais associadas do ExpressRoute do modelo de implementação clássica para o modelo de implementação Azure Resource Manager depois de mover o seu circuito do ExpressRoute. 

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Certifique-se de que tem a versão mais recente dos módulos do Azure PowerShell. Para obter mais informações, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/overview).
* Certifique-se de que consultou os [pré-requisitos](expressroute-prerequisites.md), [requisitos de encaminhamento](expressroute-routing.md), e [fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.
* Reveja as informações que são fornecidas através de [mover um circuito do ExpressRoute do clássico para Resource Manager](expressroute-move.md). Certifique-se de que compreende totalmente os limites e limitações.
* Certifique-se de que o circuito é totalmente operacional no modelo de implementação clássica.
* Certifique-se de que tem um grupo de recursos que foi criado no modelo de implementação do Resource Manager.
* Reveja a seguinte documentação de migração de recursos:

    * [Migração suportada por plataforma de recursos de IaaS do clássico para o Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [Technical deep dive on platform-supported migration from classic to Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md) (Análise detalhada técnica sobre a migração suportada por plataforma da clássica para Azure Resource Manager)
    * [Perguntas mais frequentes: Migração suportada por plataforma de recursos de IaaS do clássico para o Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [Reveja os erros mais comuns de migração e atenuações](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="supported-and-unsupported-scenarios"></a>Cenários suportados e não suportados

* Um circuito do ExpressRoute pode ser movido do clássico para o ambiente de Gestor de recursos sem qualquer período de inatividade. Pode mover qualquer circuito ExpressRoute do clássico para o ambiente de Gestor de recursos sem períodos de indisponibilidade. Siga as instruções em [mover circuitos ExpressRoute do clássico para o modelo de implementação do Resource Manager com o PowerShell](expressroute-howto-move-arm.md). Este é um pré-requisito para mover recursos ligados à rede virtual.
* Implementações associadas na rede virtual que estão ligadas a um circuito do ExpressRoute na mesma subscrição, gateways e redes virtuais podem ser migradas para o ambiente de Gestor de recursos sem qualquer período de inatividade. Pode seguir os passos descritos mais tarde para migrar os recursos, tais como redes virtuais, os gateways e máquinas virtuais implementadas na rede virtual. Tem de se certificar de que as redes virtuais estão configuradas corretamente antes de eles são migrados. 
* Redes virtuais, os gateways e as implementações associadas dentro da rede virtual que não estão na mesma subscrição que o circuito de ExpressRoute requerem algum período de indisponibilidade para concluir a migração. A última seção do documento descreve os passos a ser seguido para migrar os recursos.
* Não é possível migrar uma rede virtual com o Gateway do ExpressRoute e Gateway de VPN.
* Não é suportada a migração de subscrição cruzada de circuito do ExpressRoute. Para obter mais informações, consulte [serviços que não não possível mover](../azure-resource-manager/resource-group-move-resources.md#services-that-cannot-be-moved).

## <a name="move-an-expressroute-circuit-from-classic-to-resource-manager"></a>Mover um circuito ExpressRoute do clássico para Resource Manager
Tem de mover um circuito do ExpressRoute do clássico para o ambiente do Gestor de recursos antes de tentar migrar recursos que estão anexados ao circuito do ExpressRoute. Para realizar esta tarefa, consulte os artigos seguintes:

* Reveja as informações que são fornecidas através de [mover um circuito do ExpressRoute do clássico para Resource Manager](expressroute-move.md).
* [Mover um circuito de clássico para Resource Manager com o Azure PowerShell](expressroute-howto-move-arm.md).
* Utilize o portal de gestão de serviço do Azure. Pode seguir o fluxo de trabalho [criar um novo circuito de ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) e selecione a opção de importação. 

Esta operação não envolve o tempo de inatividade. Pode continuar a transferir dados entre o local e a Microsoft, enquanto a migração está em curso.

## <a name="migrate-virtual-networks-gateways-and-associated-deployments"></a>Migrar redes virtuais, gateways e as implementações associadas

Os passos que se segue para migrar dependem se os seus recursos estão na mesma subscrição, subscrições diferentes ou ambos.

### <a name="migrate-virtual-networks-gateways-and-associated-deployments-in-the-same-subscription-as-the-expressroute-circuit"></a>Migrar redes virtuais, gateways e as implementações associadas na mesma subscrição que o circuito do ExpressRoute
Esta secção descreve os passos a ser seguido para migrar uma rede virtual, gateway e implementações associadas na mesma subscrição que o circuito do ExpressRoute. Sem períodos de indisponibilidade está associado esta migração. Pode continuar a utilizar todos os recursos através do processo de migração. O plano de gestão está bloqueado durante a migração está em curso. 

1. Certifique-se de que o circuito do ExpressRoute foi movido do clássico para o ambiente do Resource Manager.
2. Certifique-se de que a rede virtual foi preparada adequadamente para a migração.
3. Registe a sua subscrição para a migração de recursos. Para registar a sua subscrição para a migração de recursos, utilize o seguinte fragmento do PowerShell:

  ```powershell 
  Select-AzSubscription -SubscriptionName <Your Subscription Name>
  Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
  Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
  ```
4. Validar e preparar para migrar. Para mover a rede virtual, utilize o seguinte fragmento do PowerShell:

  ```powershell
  Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
  Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
  Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
  ```

  Também pode abortar a migração, executando o seguinte cmdlet do PowerShell:

  ```powershell
  Move-AzureVirtualNetwork -Abort $vnetName
  ```

## <a name="next-steps"></a>Passos Seguintes
* [Migração suportada por plataforma de recursos de IaaS do clássico para o Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [Technical deep dive on platform-supported migration from classic to Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md) (Análise detalhada técnica sobre a migração suportada por plataforma da clássica para Azure Resource Manager)
* [Perguntas mais frequentes: Migração suportada por plataforma de recursos de IaaS do clássico para o Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [Reveja os erros mais comuns de migração e atenuações](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
