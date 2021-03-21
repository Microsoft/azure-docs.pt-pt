---
title: 'Azure ExpressRoute: Migrar VNets clássicos para gestor de recursos'
description: Esta página descreve como migrar redes virtuais associadas ao ExpressRoute para o Gestor de Recursos depois de mover o circuito.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 02/06/2020
ms.author: duau
ms.openlocfilehash: 57c439cf8ac52d93d231d6ff33f72a5a942dec6a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96351610"
---
# <a name="migrate-expressroute-associated-virtual-networks-from-classic-to-resource-manager"></a>Migrar redes virtuais associadas à ExpressRoute do clássico ao Gestor de Recursos

Este artigo explica como migrar redes virtuais associadas ao ExpressRoute do modelo clássico de implementação para o modelo de implementação do Azure Resource Manager depois de mover o circuito ExpressRoute. 

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Verifique se tem as versões mais recentes dos módulos Azure PowerShell. Para obter mais informações, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/). Para instalar o módulo de Gestão de Serviços PowerShell (que é necessário para o modelo de implantação clássico), consulte [instalar o Módulo de Gestão de Serviços Azure PowerShell](/powershell/azure/servicemanagement/install-azure-ps).
* Certifique-se de que reviu os [pré-requisitos,](expressroute-prerequisites.md) [os requisitos de encaminhamento](expressroute-routing.md)e [os fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.
* Reveja as informações fornecidas no [movimento de um circuito ExpressRoute de clássico para gestor de recursos.](expressroute-move.md) Certifique-se de que compreende completamente os limites e limitações.
* Verifique se o circuito está totalmente operacional no modelo clássico de implantação.
* Certifique-se de que tem um grupo de recursos criado no modelo de implementação do Gestor de Recursos.
* Reveja a seguinte documentação de migração de recursos:

    * [Migração suportada por plataforma dos recursos IaaS clássicos para o Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-overview.md)
    * [Technical deep dive on platform-supported migration from classic to Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-deep-dive.md) (Análise detalhada técnica sobre a migração suportada por plataforma da clássica para Azure Resource Manager)
    * [FAQs: Migração suportada por plataformas de recursos iaas do clássico para o Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-faq.md)
    * [Reveja os erros e mitigações de migração mais comuns](../virtual-machines/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="supported-and-unsupported-scenarios"></a>Cenários suportados e não suportados

* Um circuito ExpressRoute pode ser movido do clássico para o ambiente de Gestor de Recursos sem qualquer tempo de inatividade. Pode mover qualquer circuito ExpressRoute do clássico para o ambiente de Gestor de Recursos sem tempo de inatividade. Siga as instruções para mover os [circuitos ExpressRoute do clássico para o modelo de implementação do Gestor de Recursos utilizando o PowerShell](expressroute-howto-move-arm.md). Este é um pré-requisito para mover recursos ligados à rede virtual.
* Redes virtuais, gateways e implementações associadas dentro da rede virtual que estão ligadas a um circuito ExpressRoute na mesma subscrição podem ser migradas para o ambiente de Gestor de Recursos sem qualquer tempo de inatividade. Pode seguir os passos descritos mais tarde para migrar recursos como redes virtuais, gateways e máquinas virtuais implantadas dentro da rede virtual. Deve certificar-se de que as redes virtuais estão configuradas corretamente antes de serem migradas. 
* Redes virtuais, gateways e implementações associadas dentro da rede virtual que não estão na mesma subscrição que o circuito ExpressRoute requerem algum tempo de inatividade para completar a migração. A última secção do documento descreve os passos a seguir para migrar recursos.
* Uma rede virtual com o ExpressRoute Gateway e o VPN Gateway não pode ser migrada.
* A migração de assinaturas cruzadas do circuito ExpressRoute não é suportada. Para obter mais informações, consulte [o suporte para movimentos Microsoft.Network](../azure-resource-manager/management/move-support-resources.md#microsoftnetwork).

## <a name="move-an-expressroute-circuit-from-classic-to-resource-manager"></a>Mova um circuito ExpressRoute do clássico para o Gestor de Recursos
Tem de mover um circuito ExpressRoute do clássico para o ambiente de Gestor de Recursos antes de tentar migrar recursos que estão ligados ao circuito ExpressRoute. Para realizar esta tarefa, consulte os seguintes artigos:

* Reveja as informações fornecidas no [movimento de um circuito ExpressRoute de clássico para gestor de recursos.](expressroute-move.md)
* [Mover um circuito do clássico para o Gestor de Recursos utilizando a Azure PowerShell](expressroute-howto-move-arm.md).
* Utilize o portal de Gestão de Serviços Azure. Pode seguir o fluxo de trabalho para [criar um novo circuito ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) e selecionar a opção de importação. 

Esta operação não envolve tempo de inatividade. Pode continuar a transferir dados entre as suas instalações e a Microsoft enquanto a migração estiver em curso.

## <a name="migrate-virtual-networks-gateways-and-associated-deployments"></a>Migrar redes virtuais, gateways e implementações associadas

Os passos que segue para migrar dependem se os seus recursos estão na mesma subscrição, subscrições diferentes ou ambas.

### <a name="migrate-virtual-networks-gateways-and-associated-deployments-in-the-same-subscription-as-the-expressroute-circuit"></a>Migrar redes virtuais, gateways e implementações associadas na mesma subscrição que o circuito ExpressRoute
Esta secção descreve os passos a seguir para migrar uma rede virtual, gateway e implementações associadas na mesma subscrição que o circuito ExpressRoute. Nenhum tempo de inatividade está associado a esta migração. Pode continuar a utilizar todos os recursos através do processo de migração. O avião de gestão está bloqueado enquanto a migração está em curso. 

1. Certifique-se de que o circuito ExpressRoute foi deslocado do clássico para o ambiente de Gestor de Recursos.
2. Certifique-se de que a rede virtual foi preparada adequadamente para a migração.
3. Registe a sua subscrição para a migração de recursos. Para registar a sua subscrição para a migração de recursos, utilize o seguinte corte PowerShell:

   ```powershell 
   Select-AzSubscription -SubscriptionName <Your Subscription Name>
   Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
   Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
   ```
4. Validar, preparar e migrar. Para mover a rede virtual, utilize o seguinte corte PowerShell:

   ```powershell
   Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
   Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
   Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
   ```

   Também pode abortar a migração executando o seguinte cmdlet PowerShell:

   ```powershell
   Move-AzureVirtualNetwork -Abort $vnetName
   ```

## <a name="next-steps"></a>Passos seguintes
* [Migração suportada por plataforma dos recursos IaaS clássicos para o Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-overview.md)
* [Technical deep dive on platform-supported migration from classic to Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-deep-dive.md) (Análise detalhada técnica sobre a migração suportada por plataforma da clássica para Azure Resource Manager)
* [FAQs: Migração suportada por plataformas de recursos iaas do clássico para o Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-faq.md)
* [Reveja os erros e mitigações de migração mais comuns](../virtual-machines/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)