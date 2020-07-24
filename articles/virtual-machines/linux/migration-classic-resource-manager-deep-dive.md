---
title: Mergulho profundo técnico clássico para Azure Resource Manager
description: Mergulho técnico profundo na migração de recursos suportados pela plataforma do modelo de implantação clássico para OZure Resource Manager
author: tanmaygore
manager: vashan
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 71e8ced873f358942c8ecd942eb2a00d5aec0701
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87088802"
---
# <a name="technical-deep-dive-on-platform-supported-migration-from-classic-to-azure-resource-manager"></a>Technical deep dive on platform-supported migration from classic to Azure Resource Manager (Análise detalhada técnica sobre a migração suportada por plataforma da clássica para Azure Resource Manager)

> [!IMPORTANT]
> Hoje, cerca de 90% dos VMs da IaaS estão a usar [o Azure Resource Manager.](https://azure.microsoft.com/features/resource-manager/) A partir de 28 de fevereiro de 2020, os VM clássicos foram depreciados e serão totalmente retirados a 1 de março de 2023. [Saiba mais]( https://aka.ms/classicvmretirement) sobre esta depreciação e [como isso o afeta.](../classic-vm-deprecation.md#how-does-this-affect-me)

Vamos mergulhar profundamente na migração do modelo clássico de implementação do Azure para o modelo de implementação do Azure Resource Manager. Olhamos para os recursos a um nível de recursos e recursos para ajudá-lo a entender como a plataforma Azure migra recursos entre os dois modelos de implementação. Para mais informações, leia o artigo de anúncio de serviço: [Migração suportada pela plataforma de recursos IaaS do clássico para o Azure Resource Manager.](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[!INCLUDE [virtual-machines-common-migration-deep-dive](../../../includes/virtual-machines-common-classic-resource-manager-migration-deep-dive.md)]

## <a name="next-steps"></a>Passos seguintes

* [Visão geral da migração suportada pela plataforma de recursos iaas do clássico para o Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Planear a migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Use o PowerShell para migrar os recursos iaaS do clássico para o Azure Resource Manager](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Utilize o CLI para migrar os recursos iaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ferramentas comunitárias para ajudar na migração de recursos iaas do clássico para o Azure Resource Manager](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Consultar os erros de migração mais comuns](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Reveja as perguntas mais frequentes sobre a migração dos recursos da IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
