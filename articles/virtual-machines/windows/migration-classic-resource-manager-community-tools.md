---
title: Ferramentas da Comunidade – mover recursos clássicos para Azure Resource Manager
description: Este artigo cataloga as ferramentas que foram fornecidas pela Comunidade para ajudar a migrar recursos de IaaS do modelo de implantação clássico para o Azure Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 228b697b-3950-49f5-84bb-283bb56621b1
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 03/30/2017
ms.author: kasing
ms.openlocfilehash: a4a7ed6d57e72deb7f49895935e522e65e18f7d9
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033305"
---
# <a name="community-tools-to-migrate-iaas-resources-from-classic-to-azure-resource-manager"></a>Ferramentas da comunidade para migrar recursos de IaaS da implementação clássica para Azure Resource Manager
Este artigo cataloga as ferramentas que foram fornecidas pela Comunidade para auxiliar na migração de recursos de IaaS do modelo de implantação clássico para o Azure Resource Manager.

> [!NOTE]
> Essas ferramentas não são oficialmente suportadas pelo Suporte da Microsoft. Portanto, eles estão em código aberto no GitHub e estamos felizes em aceitar PRs para correções ou cenários adicionais. Para relatar um problema, use o recurso de problemas do GitHub.
> 
> A migração com essas ferramentas causará tempo de inatividade para sua máquina virtual clássica. Se você estiver procurando a migração com suporte da plataforma, visite 
> 
>   * [Plataforma com suporte para migração de recursos de IaaS da pilha clássica para Azure Resource Manager](migration-classic-resource-manager-overview.md)
>   * [Aprofundamento técnico sobre a migração de plataforma com suporte do clássico para o Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)
>   * [Migrar recursos de IaaS do clássico para o Azure Resource Manager usando Azure PowerShell](migration-classic-resource-manager-ps.md)
> 
> 

## <a name="asmmetadataparser"></a>AsmMetadataParser
Esta é uma coleção de ferramentas auxiliares criadas como parte das migrações empresariais do gerenciamento de serviços do Azure para Azure Resource Manager. Essa ferramenta permite que você replique sua infraestrutura em outra assinatura que pode ser usada para testar a migração e passar por problemas antes de executar a migração em sua assinatura de produção.

[Link para a documentação da ferramenta](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

## <a name="migaz"></a>migAz
migAz é uma opção adicional para migrar um conjunto completo de recursos de IaaS clássicos para Azure Resource Manager recursos de IaaS. A migração pode ocorrer na mesma assinatura ou entre diferentes assinaturas e tipos de assinatura (por exemplo, assinaturas do CSP).

[Link para a documentação da ferramenta](https://github.com/Azure/migAz)

## <a name="next-steps"></a>Passos Seguintes

* [Visão geral da migração de recursos de IaaS com suporte da plataforma do clássico para o Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Technical deep dive on platform-supported migration from classic to Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Análise detalhada técnica sobre a migração suportada por plataforma da clássica para Azure Resource Manager)
* [Planear a migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Usar o PowerShell para migrar recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Usar a CLI para migrar recursos de IaaS do clássico para o Azure Resource Manager](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Consultar os erros de migração mais comuns](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Examine as perguntas mais frequentes sobre a migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

