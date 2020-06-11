---
title: Vamos retirar os VMs clássicos do Azure a 1 de março de 2023
description: Artigo fornece uma visão geral de alto nível da reforma clássica de VM
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/10/2020
ms.author: tagore
ms.openlocfilehash: 7488ef45d665d95a28f69b7af887b98dd5a76376
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/10/2020
ms.locfileid: "84678380"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>Migrar os seus recursos IaaS para o Azure Resource Manager até 1 de março de 2023 

Em 2014, lançámos o IaaS na Azure Resource Manager, e temos vindo a aumentar as capacidades desde então. Como [o Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) tem agora todas as capacidades iaaS e outros avanços, depreciemos a gestão da IaaS VMs através do Azure Service Manager em 28 de fevereiro de 2020 e esta funcionalidade será totalmente aposentada a 1 de março de 2023. 

Hoje, cerca de 90% dos VMs da IaaS estão a usar o Azure Resource Manager. Se utilizar recursos iaaS através do Azure Service Manager (ASM), comece a planear a sua migração agora e complete-a até 1 de março de 2023 para aproveitar [o Azure Resource Manager.](https://docs.microsoft.com/azure/azure-resource-manager/management/)

Os VM clássicos seguirão a [Política moderna de ciclo de vida](https://support.microsoft.com/help/30881/modern-lifecycle-policy) para a reforma.

## <a name="how-does-this-affect-me"></a>Como é que isto me afeta? 

1) A partir de 28 de fevereiro de 2020, os clientes que não utilizaram os IaaS VMs através do Azure Service Manager (ASM) no mês de fevereiro de 2020 deixarão de poder criar VMs clássicos. 
2) No dia 1 de março de 2023, os clientes deixarão de poder iniciar os IaaS VMs utilizando o Azure Service Manager e todos os que ainda estiverem em execução ou atribuídos serão interrompidos e contratados. 
2) No dia 1 de março de 2023, as subscrições que não tenham migrado para a Azure Resource Manager serão informadas sobre os prazos para a eliminação de quaisquer VMs clássicos restantes.  

Os seguintes serviços e funcionalidades da Azure **NÃO** serão impactados por esta reforma: 
- Serviços Cloud 
- Contas de armazenamento **não** utilizadas por VMs clássicos 
- Redes virtuais (VNets) **não** utilizadas por VMs clássicos. 
- Outros recursos clássicos

## <a name="what-actions-should-i-take"></a>Que ações devo tomar? 

- Comece a planear a sua migração para o Azure Resource Manager, hoje. 

- [Saiba mais](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview) sobre a migração dos seus [VMs clássicos do Linux](./linux/migration-classic-resource-manager-plan.md) e [windows](./windows/migration-classic-resource-manager-plan.md) para o Azure Resource Manager.

- Para mais informações, consulte as [perguntas frequentes sobre a migração clássica para a migração do Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-faq)

- Para questões técnicas, questões e suporte de [contato](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)whitelisting por subscrição .

- Para outras questões que não fazem parte das FAQ e feedback, comente abaixo.
