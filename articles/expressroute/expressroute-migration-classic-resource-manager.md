---
title: 'Azure ExpressRoute: Migrar VNets clássicos para Gestor de Recursos'
description: Esta página descreve como migrar redes virtuais associadas à ExpressRoute para O Gestor de Recursos depois de mover o seu circuito.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: cherylmc
ms.openlocfilehash: 8033c80b72c19a9473ce7ecfaa8fe5a1da9f12ee
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77061325"
---
# <a name="migrate-expressroute-associated-virtual-networks-from-classic-to-resource-manager"></a>Redes virtuais associadas à Rota Expresso migrar do clássico para o Gestor de Recursos

Este artigo explica como migrar redes virtuais associadas à ExpressRoute do modelo clássico de implementação para o modelo de implementação do Gestor de Recursos Azure após mover o seu circuito ExpressRoute. 

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Verifique se tem as versões mais recentes dos módulos Azure PowerShell. Para obter mais informações, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/overview). Para instalar o módulo powerShell Service Management (que é necessário para o modelo de implementação clássico), consulte a instalação do Módulo de Gestão de [Serviços Azure PowerShell](/powershell/azure/servicemanagement/install-azure-ps).
* Certifique-se de que reviu os [pré-requisitos,](expressroute-prerequisites.md) [os requisitos de encaminhamento](expressroute-routing.md)e [os fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.
* Reveja as informações fornecidas no âmbito da [Mudança de um circuito ExpressRoute do clássico para o Gestor de Recursos](expressroute-move.md). Certifique-se de que compreende completamente os limites e limitações.
* Verifique se o circuito está totalmente operacional no modelo de implantação clássico.
* Certifique-se de que tem um grupo de recursos criado no modelo de implementação do Gestor de Recursos.
* Reveja a seguinte documentação sobre migração de recursos:

    * [Migração apoiada pela plataforma de recursos IaaS do clássico para o Gestor de Recursos Azure](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [Technical deep dive on platform-supported migration from classic to Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md) (Análise detalhada técnica sobre a migração suportada por plataforma da clássica para Azure Resource Manager)
    * [FAQs: Migração apoiada pela plataforma de recursos IaaS do clássico para o Gestor de Recursos Azure](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [Rever os erros e atenuações de migração mais comuns](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="supported-and-unsupported-scenarios"></a>Cenários suportados e não suportados

* Um circuito ExpressRoute pode ser movido do clássico para o ambiente do Gestor de Recursos sem qualquer tempo de inatividade. Pode mover qualquer circuito ExpressRoute do clássico para o ambiente do Gestor de Recursos sem tempo de inatividade. Siga as instruções para mover os [circuitos ExpressRoute do clássico para o modelo](expressroute-howto-move-arm.md)de implementação do Gestor de Recursos utilizando o PowerShell . Este é um pré-requisito para mover recursos ligados à rede virtual.
* Redes virtuais, gateways e implementações associadas dentro da rede virtual que estão ligadas a um circuito ExpressRoute na mesma subscrição podem ser migradas para o ambiente do Gestor de Recursos sem qualquer tempo de inatividade. Pode seguir os passos descritos mais tarde para migrar recursos como redes virtuais, gateways e máquinas virtuais implantadas dentro da rede virtual. Deve certificar-se de que as redes virtuais estão corretamente configuradas antes de serem migradas. 
* Redes virtuais, gateways e implementações associadas dentro da rede virtual que não estão na mesma subscrição que o circuito ExpressRoute requerem algum tempo de inatividade para completar a migração. A última secção do documento descreve os passos a seguir para migrar recursos.
* Uma rede virtual com o ExpressRoute Gateway e o VPN Gateway não pode ser migrada.
* A migração cruzada do circuito ExpressRoute não é suportada. Para mais informações, consulte [o suporte de movimento Microsoft.Network](../azure-resource-manager/management/move-support-resources.md#microsoftnetwork).

## <a name="move-an-expressroute-circuit-from-classic-to-resource-manager"></a>Mova um circuito ExpressRoute do clássico para o Gestor de Recursos
Tem de mover um circuito ExpressRoute do clássico para o ambiente do Gestor de Recursos antes de tentar migrar recursos ligados ao circuito ExpressRoute. Para realizar esta tarefa, consulte os seguintes artigos:

* Reveja as informações fornecidas no âmbito da [Mudança de um circuito ExpressRoute do clássico para o Gestor de Recursos](expressroute-move.md).
* [Mova um circuito do clássico para o Gestor de Recursos utilizando o Azure PowerShell](expressroute-howto-move-arm.md).
* Utilize o portal de Gestão de Serviços Azure. Pode seguir o fluxo de trabalho para [criar um novo circuito ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) e selecionar a opção de importação. 

Esta operação não envolve tempo de inatividade. Pode continuar a transferir dados entre as suas instalações e a Microsoft enquanto a migração está em curso.

## <a name="migrate-virtual-networks-gateways-and-associated-deployments"></a>Migrar redes virtuais, gateways e implantações associadas

Os passos que segue para migrar dependem se os seus recursos estão na mesma subscrição, subscrições diferentes ou ambas.

### <a name="migrate-virtual-networks-gateways-and-associated-deployments-in-the-same-subscription-as-the-expressroute-circuit"></a>Migrar redes virtuais, gateways e implementações associadas na mesma subscrição que o circuito ExpressRoute
Esta secção descreve os passos a seguir para migrar uma rede virtual, gateway e implantações associadas na mesma subscrição que o circuito ExpressRoute. Não há tempo de inatividade associado a esta migração. Pode continuar a utilizar todos os recursos através do processo de migração. O avião de gestão está bloqueado enquanto a migração está em andamento. 

1. Certifique-se de que o circuito ExpressRoute foi transferido do clássico para o ambiente do Gestor de Recursos.
2. Certifique-se de que a rede virtual foi preparada adequadamente para a migração.
3. Registe a sua subscrição para migração de recursos. Para registar a sua subscrição para migração de recursos, utilize o seguinte corte PowerShell:

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
* [Migração apoiada pela plataforma de recursos IaaS do clássico para o Gestor de Recursos Azure](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [Technical deep dive on platform-supported migration from classic to Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md) (Análise detalhada técnica sobre a migração suportada por plataforma da clássica para Azure Resource Manager)
* [FAQs: Migração apoiada pela plataforma de recursos IaaS do clássico para o Gestor de Recursos Azure](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [Rever os erros e atenuações de migração mais comuns](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
