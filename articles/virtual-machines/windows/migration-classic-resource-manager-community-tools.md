---
title: Ferramentas comunitárias - Mover recursos clássicos para Azure Resource Manager
description: Este artigo cataloga as ferramentas que foram fornecidas pela comunidade para ajudar a migrar os recursos iaaS do clássico para o modelo de implementação do Azure Resource Manager.
author: tanmaygore
manager: vashan
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: afc20a39a5b426f37d6a6752056e3dd35dd8438a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88504964"
---
# <a name="community-tools-to-migrate-iaas-resources-from-classic-to-azure-resource-manager"></a>Ferramentas comunitárias para migrar recursos iaaS do clássico para o Azure Resource Manager

> [!IMPORTANT]
> Hoje, cerca de 90% dos VMs da IaaS estão a usar [o Azure Resource Manager.](https://azure.microsoft.com/features/resource-manager/) A partir de 28 de fevereiro de 2020, os VM clássicos foram depreciados e serão totalmente retirados a 1 de março de 2023. [Saiba mais]( https://aka.ms/classicvmretirement) sobre esta depreciação e [como isso o afeta.](../classic-vm-deprecation.md#how-does-this-affect-me)

Este artigo cataloga as ferramentas que foram fornecidas pela comunidade para ajudar na migração de recursos IaaS do clássico para o modelo de implementação do Azure Resource Manager.

> [!NOTE]
> Estas ferramentas não são oficialmente suportadas pelo Microsoft Support. Portanto, eles são de origem aberta no GitHub e estamos felizes em aceitar PRs para correções ou cenários adicionais. Para reportar um problema, use o recurso de problemas do GitHub.
> 
> Migrar com estas ferramentas causará tempo de inatividade para a sua máquina virtual clássica. Se procura a migração apoiada pela plataforma, visite 
> 
>   * [Plataforma apoiou migração de recursos iaaS da pilha Classic para Azure Resource Manager](migration-classic-resource-manager-overview.md)
>   * [Mergulho Profundo Técnico na Plataforma apoiou migração de Classic para Azure Resource Manager](../migration-classic-resource-manager-deep-dive.md)
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

* [Visão geral da migração suportada pela plataforma de recursos iaas do clássico para o Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Technical deep dive on platform-supported migration from classic to Azure Resource Manager](../migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Análise detalhada técnica sobre a migração suportada por plataforma da clássica para Azure Resource Manager)
* [Planear a migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Use o PowerShell para migrar os recursos iaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Utilize o CLI para migrar os recursos iaaS do clássico para o Azure Resource Manager](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Consultar os erros de migração mais comuns](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Reveja as perguntas mais frequentes sobre a migração dos recursos da IaaS do clássico para o Azure Resource Manager](../migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
