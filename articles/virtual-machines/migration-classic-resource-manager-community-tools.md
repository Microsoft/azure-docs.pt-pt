---
title: Ferramentas comunitárias - Mover recursos clássicos para Azure Resource Manager
description: Este artigo cataloga as ferramentas que foram fornecidas pela comunidade para ajudar a migrar os recursos iaaS do clássico para o modelo de implementação do Azure Resource Manager.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.subservice: classic-to-arm-migration
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: f165acb72fdf881a0828c38db577be1f8741384e
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101674737"
---
# <a name="community-tools-to-migrate-iaas-resources-from-classic-to-azure-resource-manager"></a>Ferramentas comunitárias para migrar recursos iaaS do clássico para o Azure Resource Manager

> [!IMPORTANT]
> Hoje, cerca de 90% dos VMs da IaaS estão a usar [o Azure Resource Manager.](https://azure.microsoft.com/features/resource-manager/) A partir de 28 de fevereiro de 2020, os VM clássicos foram depreciados e serão totalmente retirados a 1 de março de 2023. [Saiba mais]( https://aka.ms/classicvmretirement) sobre esta depreciação e [como isso o afeta.](classic-vm-deprecation.md#how-does-this-affect-me)

Este artigo cataloga as ferramentas que foram fornecidas pela comunidade para ajudar na migração de recursos IaaS do clássico para o modelo de implementação do Azure Resource Manager.

> [!NOTE]
> Estas ferramentas não são oficialmente suportadas pelo Microsoft Support. Portanto, eles são de origem aberta no GitHub e estamos felizes em aceitar PRs para correções ou cenários adicionais. Para reportar um problema, use o recurso de problemas do GitHub.
> 
> Migrar com estas ferramentas causará tempo de inatividade para a sua máquina virtual clássica. Se procura a migração apoiada pela plataforma, visite 
> 
>   * [Plataforma apoiou migração de recursos iaaS da pilha Classic para Azure Resource Manager](migration-classic-resource-manager-overview.md)
>   * [Mergulho Profundo Técnico na Plataforma apoiou migração de Classic para Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)
>   * [Migrar recursos IaaS da Classic para Azure Resource Manager usando a Azure PowerShell](migration-classic-resource-manager-ps.md)
> 
> 

## <a name="asmmetadataparser"></a>AsmMetadataParser
Esta é uma coleção de ferramentas auxiliares criadas como parte das migrações empresariais da Azure Service Management para a Azure Resource Manager. Esta ferramenta permite-lhe replicar a sua infraestrutura em outra subscrição que pode ser usada para testar a migração e resolver quaisquer problemas antes de executar a migração na sua subscrição de Produção.

[Ligação à documentação da ferramenta](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

## <a name="migaz"></a>migAz
O migAz é uma opção adicional para migrar um conjunto completo de recursos clássicos da IaaS para os recursos do Azure Resource Manager IaAS. A migração pode ocorrer dentro da mesma subscrição ou entre diferentes subscrições e tipos de subscrição (ex: subscrições CSP).

[Ligação à documentação da ferramenta](https://github.com/Azure/migAz)

## <a name="next-steps"></a>Passos Seguintes

* [Visão geral da migração suportada pela plataforma de recursos iaas do clássico para o Azure Resource Manager](migration-classic-resource-manager-overview.md)
* [Technical deep dive on platform-supported migration from classic to Azure Resource Manager](migration-classic-resource-manager-deep-dive.md) (Análise detalhada técnica sobre a migração suportada por plataforma da clássica para Azure Resource Manager)
* [Planear a migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-plan.md)
* [Use o PowerShell para migrar os recursos iaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-ps.md)
* [Utilize o CLI para migrar os recursos iaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-cli.md)
* [Consultar os erros de migração mais comuns](migration-classic-resource-manager-errors.md)
* [Reveja as perguntas mais frequentes sobre a migração dos recursos da IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-faq.md)
