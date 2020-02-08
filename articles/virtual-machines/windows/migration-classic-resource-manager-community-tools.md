---
title: Ferramentas comunitárias - Mover recursos clássicos para o Gestor de Recursos Azure
description: Este artigo cataloga as ferramentas que foram fornecidas pela comunidade para ajudar a migrar os recursos iaaS do clássico para o modelo de implantação do Gestor de Recursos Azure.
services: virtual-machines-windows
documentationcenter: ''
author: tanmaygore
manager: vashan
editor: ''
tags: azure-resource-manager
ms.assetid: 228b697b-3950-49f5-84bb-283bb56621b1
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 5688854404ca6d3e64ca6d59276628d08bbf3ebe
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2020
ms.locfileid: "77086018"
---
# <a name="community-tools-to-migrate-iaas-resources-from-classic-to-azure-resource-manager"></a>Ferramentas comunitárias para migrar recursos IaaS do clássico para o Gestor de Recursos Azure
Este artigo cataloga as ferramentas que foram fornecidas pela comunidade para ajudar na migração de recursos IaaS do clássico para o modelo de implantação do Gestor de Recursos Azure.

> [!NOTE]
> Estas ferramentas não são suportadas oficialmente pelo Microsoft Support. Portanto, eles são abertos no GitHub e estamos felizes em aceitar PRs para correções ou cenários adicionais. Para relatar um problema, use a funcionalidade de problemas do GitHub.
> 
> Migrar com estas ferramentas causará tempo de inatividade para a sua máquina virtual clássica. Se procura migração apoiada por plataforma, visite 
> 
>   * [Plataforma apoiou a migração de recursos IaaS da pilha Classic para Azure Resource Manager](migration-classic-resource-manager-overview.md)
>   * [Mergulho Profundo Técnico na Plataforma apoiou migração do Classic para o Gestor de Recursos Azure](migration-classic-resource-manager-deep-dive.md)
>   * [Migrar recursos IaaS do Classic para o Azure Resource Manager usando o Azure PowerShell](migration-classic-resource-manager-ps.md)
> 
> 

## <a name="asmmetadataparser"></a>AsmMetadataParser
Esta é uma coleção de ferramentas auxiliares criadas como parte das migrações empresariais da Azure Service Management para o Azure Resource Manager. Esta ferramenta permite-lhe replicar a sua infraestrutura em outra subscrição que pode ser usada para testar a migração e resolver quaisquer problemas antes de executar a migração na sua subscrição de Produção.

[Link para a documentação da ferramenta](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

## <a name="migaz"></a>migAz
MigAz é uma opção adicional para migrar um conjunto completo de recursos clássicos iaaS para os recursos IaaS do Gestor de Recursos Azure. A migração pode ocorrer dentro da mesma subscrição ou entre diferentes subscrições e tipos de subscrição (ex: assinaturas CSP).

[Link para a documentação da ferramenta](https://github.com/Azure/migAz)

## <a name="next-steps"></a>Passos Seguintes

* [Visão geral da migração apoiada pela plataforma de recursos IaaS do clássico para o Gestor de Recursos Azure](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Technical deep dive on platform-supported migration from classic to Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Análise detalhada técnica sobre a migração suportada por plataforma da clássica para Azure Resource Manager)
* [Planear a migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Use a PowerShell para migrar os recursos iaaS do clássico para o Gestor de Recursos Azure](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Use o CLI para migrar recursos IaaS do clássico para o Gestor de Recursos Azure](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Consultar os erros de migração mais comuns](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Reveja as perguntas mais frequentes sobre a migração de recursos IaaS do clássico para o Gestor de Recursos Azure](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

