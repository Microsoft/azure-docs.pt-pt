---
title: Vamos retirar Azure VMs (clássico) em 1 de março de 2023
description: Este artigo fornece uma visão geral de alto nível da reforma dos VMs criados usando o modelo de implementação clássico.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: tagore
ms.openlocfilehash: 004a84cd98381af027c554a7ef40e27e69ec6dbc
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100587924"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>Migrar os seus recursos IaaS para o Azure Resource Manager até 1 de março de 2023 

Em 2014, lançámos a infraestrutura como um serviço (IaaS) na [Azure Resource Manager.](https://azure.microsoft.com/features/resource-manager/) Temos vindo a aumentar as capacidades desde então. Como o Azure Resource Manager tem agora todas as capacidades iaaS e outros avanços, depreciemos a gestão de máquinas virtuais IaaS (VMs) através [do Azure Service Manager](./migration-classic-resource-manager-faq.md#what-is-azure-service-manager-and-what-does-it-mean-by-classic) (ASM) em 28 de fevereiro de 2020. Esta funcionalidade será totalmente reformada a 1 de março de 2023. 

Hoje, cerca de 90% dos VMs da IaaS estão a usar o Azure Resource Manager. Se utilizar recursos iaaS através da ASM, comece já a planear a sua migração. Complete-o até 1 de março de 2023, para aproveitar [o Azure Resource Manager](../azure-resource-manager/management/index.yml).

Os VMs criados utilizando o modelo de implementação clássico seguirão a [Política moderna de ciclo de vida](https://support.microsoft.com/help/30881/modern-lifecycle-policy) para a reforma.

## <a name="how-does-this-affect-me"></a>Como é que isto me afeta? 

- A partir de 28 de fevereiro de 2020, os clientes que não utilizaram iaaS VMs através da ASM no mês de fevereiro de 2020 já não podem criar VMs (clássicos). 
- No dia 1 de março de 2023, os clientes deixarão de poder iniciar os IaaS VMs utilizando a ASM. Qualquer um que ainda esteja em execução ou atribuído será interrompido e será contratado. 
- No dia 1 de março de 2023, as subscrições que não forem migradas para o Azure Resource Manager serão informadas sobre os prazos para a eliminação de quaisquer VMs restantes (clássicos).  

Esta aposentadoria *não* afeta os seguintes serviços e funcionalidades da Azure: 
- [Azure Cloud Services (clássico)](https://docs.microsoft.com/azure/cloud-services/cloud-services-choose-me)
- Contas de armazenamento *não* utilizadas por VMs (clássico) 
- Redes virtuais *não* utilizadas por VMs (clássico) 
- Outros recursos clássicos

## <a name="what-actions-should-i-take"></a>Que ações devo tomar? 

Comece a planear a sua migração para o Azure Resource Manager, hoje. 

1. Faça uma lista de todos os VMs afetados: 

   - Os VMs de máquinas virtuais tipo **(clássicas)** no [painel VM do portal Azure](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ClassicCompute%2FVirtualMachines) são todos os VMs afetados dentro da subscrição. 
   - Também pode consultar o Azure Resource Graph utilizando o [portal](https://portal.azure.com/#blade/HubsExtension/ArgQueryBlade/query/resources%0A%7C%20where%20type%20%3D%3D%20%22microsoft.classiccompute%2Fvirtualmachines%22) ou [o PowerShell](../governance/resource-graph/concepts/work-with-data.md) para ver a lista de todos os VMs sinalizados (clássicos) e informações relacionadas para as subscrições selecionadas. 
   - Nos dias 8 de fevereiro e 2 de setembro de 2020, enviamos e-mails aos proprietários de subscrições com uma lista de todas as subscrições que contêm estes VMs (clássico). Por favor, use-os para construir esta lista. 

1. [Saiba mais](./migration-classic-resource-manager-overview.md) sobre a migração dos seus [VMs Linux](./migration-classic-resource-manager-plan.md) e [Windows](./migration-classic-resource-manager-plan.md) (clássico) para o Azure Resource Manager. Para obter mais informações, consulte [Perguntas frequentes sobre a migração clássica do Azure Resource Manager.](./migration-classic-resource-manager-faq.md)

1. Recomendamos iniciar o planeamento utilizando a [ferramenta de migração de suporte](./migration-classic-resource-manager-overview.md) da plataforma para migrar os seus VMs existentes com três passos fáceis: validar, preparar e comprometer-se. A ferramenta foi concebida para migrar os seus VMs dentro do mínimo para nenhum tempo de inatividade. 

   - O primeiro passo, validado, não tem impacto na sua implantação existente e fornece uma lista de todos os cenários não apoiados para a migração. 
   - Procure a [lista de soluções alternativas](./migration-classic-resource-manager-overview.md#unsupported-features-and-configurations) para corrigir o seu destacamento e prepare-a para a migração. 
   - Idealmente, depois de todos os erros de validação serem corrigidos, não deverá encontrar quaisquer problemas durante os passos de preparação e de empenhamento. Após o compromisso ser bem sucedido, a sua implementação é migrada para o Azure Resource Manager e pode ser gerida através de novas APIs expostas pelo Azure Resource Manager. 

   Se a ferramenta de migração não for adequada para a sua migração, pode explorar [outras ofertas de computação](/azure/architecture/guide/technology-choices/compute-decision-tree) para a migração. Porque há muitas ofertas de cálculo Azure, e são diferentes umas das outras, não podemos fornecer um caminho de migração apoiado pela plataforma para eles.  

1. Para questões técnicas, problemas e ajuda na adição de subscrições à lista de admissões, [suporte de contacto](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"8a82f77d-c3ab-7b08-d915-776b4ff64ff4"}).

1. Complete a migração o mais rapidamente possível para prevenir o impacto do negócio e para tirar partido do melhor desempenho, segurança e novas funcionalidades do Azure Resource Manager. 

## <a name="what-resources-are-available-for-this-migration"></a>Que recursos estão disponíveis para esta migração?

- [Microsoft Q&A](/answers/topics/azure-virtual-machines-migration.html): Microsoft e suporte comunitário para a migração.

- [Apoio à Migração Azure](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"1135e3d0-20e2-aec5-4ef0-55fd3dae2d58"}): Equipa de apoio dedicada à assistência técnica durante a migração.

- [Microsoft Fast Track](https://www.microsoft.com/fasttrack): Fast track pode ajudar clientes elegíveis a planear & execução desta migração. [Nomeie-se](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fprograms%2Fazure-fasttrack%2F%23nomination&data=02%7C01%7CTanmay.Gore%40microsoft.com%7C3e75bbf3617944ec663a08d85c058340%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637360526032558561&sdata=CxWTVQQPVWNwEqDZKktXzNV74pX91uyJ8dY8YecIgGc%3D&reserved=0) para o Programa de Migração de DC.  

- Se a sua empresa/organização tiver uma parceria com a Microsoft ou trabalhar com representantes da Microsoft (como arquitetos de solução em nuvem (CSAs) ou gestores técnicos de contas (TAMs)), trabalhe com eles para obter recursos adicionais para a migração.
