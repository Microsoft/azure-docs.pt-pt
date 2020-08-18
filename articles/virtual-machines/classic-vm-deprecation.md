---
title: Vamos retirar os VMs clássicos do Azure a 1 de março de 2023
description: Artigo fornece uma visão geral de alto nível da reforma clássica de VM
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: tagore
ms.openlocfilehash: c1807da76fe25d31581a07706489967d6e83f748
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88505313"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>Migrar os seus recursos IaaS para o Azure Resource Manager até 1 de março de 2023 

Em 2014, lançámos o IaaS na Azure Resource Manager, e temos vindo a aumentar as capacidades desde então. Como [o Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) tem agora todas as capacidades iaaS e outros avanços, depreciemos a gestão da IaaS VMs através do [Azure Service Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-faq#what-is-azure-service-manager-and-what-does-it-mean-by-classic) em 28 de fevereiro de 2020 e esta funcionalidade será totalmente aposentada a 1 de março de 2023. 

Hoje, cerca de 90% dos VMs da IaaS estão a usar o Azure Resource Manager. Se utilizar recursos iaaS através do Azure Service Manager (ASM), comece a planear a sua migração agora e complete-a até 1 de março de 2023 para aproveitar [o Azure Resource Manager.](../azure-resource-manager/management/index.yml)

Os VM clássicos seguirão a [Política moderna de ciclo de vida](https://support.microsoft.com/help/30881/modern-lifecycle-policy) para a reforma.

## <a name="how-does-this-affect-me"></a>Como é que isto me afeta? 

- A partir de 28 de fevereiro de 2020, os clientes que não utilizaram os IaaS VMs através do Azure Service Manager (ASM) no mês de fevereiro de 2020 deixarão de poder criar VMs clássicos. 
- No dia 1 de março de 2023, os clientes deixarão de poder iniciar os IaaS VMs utilizando o Azure Service Manager e todos os que ainda estiverem em execução ou atribuídos serão interrompidos e contratados. 
- No dia 1 de março de 2023, as subscrições que não forem migradas para a Azure Resource Manager serão informadas sobre os prazos para a eliminação de quaisquer VMs clássicos restantes.  

Os seguintes serviços e funcionalidades da Azure **NÃO** serão impactados por esta reforma: 
- Serviços Cloud 
- Contas de armazenamento **não** utilizadas por VMs clássicos 
- Redes virtuais (VNets) **não** utilizadas por VMs clássicos. 
- Outros recursos clássicos

## <a name="what-actions-should-i-take"></a>Que ações devo tomar? 

- Comece a planear a sua migração para o Azure Resource Manager, hoje. 

- [Saiba mais](./windows/migration-classic-resource-manager-overview.md) sobre a migração dos seus [VMs clássicos do Linux](./linux/migration-classic-resource-manager-plan.md) e [windows](./windows/migration-classic-resource-manager-plan.md) para o Azure Resource Manager.

- Para mais informações, consulte as [perguntas frequentes sobre a migração clássica para a migração do Azure Resource Manager](./migration-classic-resource-manager-faq.md)

- Para questões técnicas, problemas e adição de subscrições à lista de admissões, [suporte de contacto](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"8a82f77d-c3ab-7b08-d915-776b4ff64ff4"}).

- Para outras questões que não fazem parte das FAQ e feedback, comente abaixo.

- Complete a migração o mais rapidamente possível para evitar o impacto do negócio e alavancar um melhor desempenho, segurança & novas funcionalidades fornecidas pelo Azure Resource Manager. 

## <a name="what-resources-are-provided-to-me-for-this-migration"></a>Que recursos me são fornecidos para esta migração?

- [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-virtual-machines-migration.html): Microsoft & suporte comunitário para a migração

- [Apoio à Migração Azure](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"1135e3d0-20e2-aec5-4ef0-55fd3dae2d58"}): Equipa de apoio dedicada à assistência técnica durante a migração

- [Microsoft Fast Track](https://www.microsoft.com/fasttrack): A equipa do Microsoft Fast Track pode prestar assistência técnica durante a migração a clientes elegíveis. 

- Se a sua empresa/organização tiver uma parceria com a Microsoft e/ou trabalhar com o representante da Microsoft como (Cloud Solution Architect (CSA), Technical Account Managers (TAMs)), trabalhe com eles para obter recursos adicionais para a migração. 

