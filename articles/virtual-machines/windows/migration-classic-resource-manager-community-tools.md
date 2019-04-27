---
title: Ferramentas de Comunidade - mover recursos clássicos para o Azure Resource Manager | Documentos da Microsoft
description: Este artigo cataloga as ferramentas que foram fornecidas pela Comunidade para ajudar a migrar recursos de IaaS do clássico para o modelo de implementação Azure Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 228b697b-3950-49f5-84bb-283bb56621b1
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: kasing
ms.openlocfilehash: 085a0ff0eee260069d693b339521a9489df78e40
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60848237"
---
# <a name="community-tools-to-migrate-iaas-resources-from-classic-to-azure-resource-manager"></a>Ferramentas da comunidade para migrar recursos de IaaS da implementação clássica para Azure Resource Manager
Este artigo cataloga as ferramentas que foram fornecidas pela Comunidade para ajudá-lo com a migração de recursos de IaaS do clássico para o modelo de implementação Azure Resource Manager.

> [!NOTE]
> Essas ferramentas não são suportadas oficialmente pelo Support da Microsoft. Portanto, eles são de código aberto no GitHub e estamos felizes por aceitar pedidos pull para correções ou cenários adicionais. Para comunicar um problema, utilize a funcionalidade de problemas do GitHub.
> 
> Migrar com essas ferramentas fará com que os períodos de indisponibilidade para a Máquina Virtual clássica. Se estiver à procura de migração de plataforma suportada, visite 
> 
>   * [Plataforma suportada a migração de recursos de IaaS do clássico para a pilha do Azure Resource Manager](migration-classic-resource-manager-overview.md)
>   * [Descrição técnica aprofundada sobre a plataforma de suporte à migração da implementação clássica para Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)
>   * [Migrar recursos de IaaS do clássico para o Azure Resource Manager com o Azure PowerShell](migration-classic-resource-manager-ps.md)
> 
> 

## <a name="asmmetadataparser"></a>AsmMetadataParser
Esta é uma coleção de ferramentas de programa auxiliar criada como parte das migrações de empresa da gestão de serviço do Azure para o Azure Resource Manager. Esta ferramenta permite-lhe replicar sua infraestrutura para outra subscrição que pode ser utilizada para testar a migração e iron eventuais problemas antes de executar a migração na sua subscrição de produção.

[Ligação para a documentação da ferramenta](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

## <a name="migaz"></a>migAz
migAz é uma opção adicional para migrar um conjunto completo de recursos de IaaS clássicos para recursos do Azure Resource Manager IaaS. A migração pode ocorrer dentro da mesma subscrição ou entre subscrições e tipos de subscrição (ex: Subscrições de CSP).

[Ligação para a documentação da ferramenta](https://github.com/Azure/migAz)

## <a name="next-steps"></a>Próximos Passos

* [Descrição geral da migração suportada por plataforma de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Technical deep dive on platform-supported migration from classic to Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Análise detalhada técnica sobre a migração suportada por plataforma da clássica para Azure Resource Manager)
* [Planear a migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Utilizar o PowerShell para migrar recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Utilizar a CLI para migrar recursos de IaaS do clássico para o Azure Resource Manager](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Consultar os erros de migração mais comuns](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Reveja as perguntas mais frequentes sobre migrar recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

