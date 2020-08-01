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
ms.openlocfilehash: d86805975b082136879c0a98ce2817f4f491a9a0
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87461209"
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

- Para questões técnicas, problemas e adição de subscrições à lista de admissões, [suporte de contacto](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"8a82f77d-c3ab-7b08-d915-776b4ff64ff4"}).

- Para assistência durante a migração, [contacte o apoio à migração](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"1135e3d0-20e2-aec5-4ef0-55fd3dae2d58"})

- Para outras questões que não fazem parte das FAQ e feedback, comente abaixo.
