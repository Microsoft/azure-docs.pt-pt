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
ms.openlocfilehash: 8c3e55d13c0b5fbf5b813f1669587836bf8b5e2d
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86503018"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>Migrar os seus recursos IaaS para o Azure Resource Manager até 1 de março de 2023 

Em 2014, lançámos o IaaS na Azure Resource Manager, e temos vindo a aumentar as capacidades desde então. Como [o Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) tem agora todas as capacidades iaaS e outros avanços, depreciemos a gestão da IaaS VMs através do Azure Service Manager em 28 de fevereiro de 2020 e esta funcionalidade será totalmente aposentada a 1 de março de 2023. 

Hoje, cerca de 90% dos VMs da IaaS estão a usar o Azure Resource Manager. Se utilizar recursos iaaS através do Azure Service Manager (ASM), comece a planear a sua migração agora e complete-a até 1 de março de 2023 para aproveitar [o Azure Resource Manager.](../azure-resource-manager/management/index.yml)

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

- [Saiba mais](./windows/migration-classic-resource-manager-overview.md) sobre a migração dos seus [VMs clássicos do Linux](./linux/migration-classic-resource-manager-plan.md) e [windows](./windows/migration-classic-resource-manager-plan.md) para o Azure Resource Manager.

- Para mais informações, consulte as [perguntas frequentes sobre a migração clássica para a migração do Azure Resource Manager](./windows/migration-classic-resource-manager-faq.md)

- Para questões técnicas, problemas e adição de subscrições à lista de admissões, [suporte de contacto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

- Para outras questões que não fazem parte das FAQ e feedback, comente abaixo.
