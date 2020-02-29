---
title: Clássico para Azure Resource Manager migração fundo técnico
description: Mergulho profundo técnico na migração de recursos suportados pela plataforma do modelo clássico de implantação para O Gestor de Recursos Azure
services: virtual-machines-linux
documentationcenter: ''
author: tanmaygore
manager: vashan
editor: ''
tags: azure-resource-manager
ms.assetid: 29267453-f894-4180-bb67-dce2a0e062bb
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 75732cf58a68948bf225e15e9227a3fa7592e738
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77914930"
---
# <a name="technical-deep-dive-on-platform-supported-migration-from-classic-to-azure-resource-manager"></a>Mergulho profundo técnico na migração apoiada pela plataforma do clássico para o Gestor de Recursos Azure

> [!IMPORTANT]
> Hoje, cerca de 90% dos VMs iaas estão usando O Gestor de [Recursos Azure.](https://azure.microsoft.com/features/resource-manager/) A partir de 28 de fevereiro de 2020, os VMs clássicos foram depreciados e serão totalmente aposentados a 1 de março de 2023. [Saiba mais]( https://aka.ms/classicvmretirement) sobre esta depreciação e [como isso o afeta.](https://docs.microsoft.com/azure/virtual-machines/classic-vm-deprecation#how-does-this-affect-me)

Vamos mergulhar profundamente na migração do modelo de implantação clássico do Azure para o modelo de implantação do Gestor de Recursos Azure. Olhamos para os recursos a um nível de recursos e recursos para ajudá-lo a entender como a plataforma Azure migra recursos entre os dois modelos de implementação. Para mais informações, leia o artigo de anúncio de serviço: [Migração apoiada pela plataforma de recursos IaaS do clássico para o Gestor de Recursos Azure.](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[!INCLUDE [virtual-machines-common-migration-deep-dive](../../../includes/virtual-machines-common-classic-resource-manager-migration-deep-dive.md)]

## <a name="next-steps"></a>Passos seguintes

* [Visão geral da migração apoiada pela plataforma de recursos IaaS do clássico para o Gestor de Recursos Azure](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Planear a migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Use a PowerShell para migrar os recursos iaaS do clássico para o Gestor de Recursos Azure](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Use o CLI para migrar recursos IaaS do clássico para o Gestor de Recursos Azure](migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ferramentas comunitárias para ajudar na migração de recursos iaaS do clássico para o Gestor de Recursos Azure](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Consultar os erros de migração mais comuns](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Reveja as perguntas mais frequentes sobre a migração de recursos IaaS do clássico para o Gestor de Recursos Azure](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
