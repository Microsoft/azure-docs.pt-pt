---
title: Vamos reformar os VMs Clássicos do Azure a 1 de março de 2023
description: Artigo fornece uma visão geral de alto nível da reforma clássica da VM
services: virtual-machines
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 02/10/2020
ms.author: tagore
ms.openlocfilehash: e56aa5ec073aadc2a16d53c266d33255a34077cb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80668812"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>Migrar os seus recursos IaaS para o Gestor de Recursos Azure até 1 de março de 2023 

Em 2014, lançámos o IaaS no Azure Resource Manager, e temos vindo a melhorar as capacidades desde então. Uma vez que o Gestor de [Recursos Azure](https://azure.microsoft.com/features/resource-manager/) tem agora capacidades iaaS completas e outros avanços, deprecemos a gestão de VMs IaaS através do Gestor de Serviços Azure em 28 de fevereiro de 2020 e esta funcionalidade será totalmente aposentada a 1 de março de 2023. 

Hoje, cerca de 90% dos VMs iaas estão usando O Gestor de Recursos Azure. Se utilizar os recursos do IaaS através do Gestor de Serviços Azure (ASM), comece a planear a sua migração agora e complete-a até 1 de março de 2023 para tirar partido do Gestor de [Recursos Azure.](https://docs.microsoft.com/azure/azure-resource-manager/management/)

Os VMs clássicos seguirão a [Política moderna](https://support.microsoft.com/help/30881/modern-lifecycle-policy) de ciclo de vida para a reforma.

## <a name="how-does-this-affect-me"></a>Como é que isto me afeta? 

1) A partir de 28 de fevereiro de 2020, os clientes que não utilizaram Os VMs IaaS através do Azure Service Manager (ASM) no mês de fevereiro de 2020 deixarão de poder criar VMs clássicos. 
2) No dia 1 de março de 2023, os clientes deixarão de poder iniciar Os VMs IaaS utilizando o Gestor de Serviços Azure e quaisquer que ainda estejam em funcionamento ou alocados serão parados e transferidos. 
2) Em 1 de março de 2023, as subscrições que não migraram para o Azure Resource Manager serão informadas sobre os prazos para a apagar quaisquer VMs clássicos restantes.  

Os seguintes serviços e funcionalidades do Azure **NÃO** serão impactados por esta reforma: 
- Serviços Cloud 
- Contas de armazenamento **não** utilizadas por VMs clássicos 
- Redes virtuais (VNets) **não** utilizadas por VMs clássicos. 
- Outros recursos clássicos

## <a name="what-actions-should-i-take"></a>Que ações devo tomar? 

- Comece a planear a sua migração para o Gestor de Recursos Azure, hoje. 

- [Saiba mais](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview) sobre a migração dos seus [VMs clássicos do Linux](./linux/migration-classic-resource-manager-plan.md) e [windows](./windows/migration-classic-resource-manager-plan.md) para o Azure Resource Manager.

- Para mais informações, consulte as perguntas frequentes sobre o clássico para a migração do [Gestor de Recursos Azure](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-faq)

- Para questões técnicas, questões e suporte de [contacto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)de listagem de assinaturas.

- Para outras questões que não fazem parte das FAQ e feedback, comente abaixo.
