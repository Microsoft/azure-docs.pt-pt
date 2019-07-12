---
title: Criar um FQDN para uma VM Windows no portal do Azure | Documentos da Microsoft
description: Saiba como criar um nome de domínio completamente qualificado ou FQDN, para um Gerenciador de recursos com base em máquina virtual no portal do Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: a2ae5887-76df-485e-ae19-0efd96df8600
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/15/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 48ad63cb39218f099d9128dae379c7df105764b9
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723037"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-windows-vm"></a>Criar um nome de domínio completamente qualificado no portal do Azure para uma VM do Windows

Quando cria uma máquina virtual (VM) no [portal do Azure](https://portal.azure.com), um recurso IP público para a máquina virtual é criado automaticamente. Utilize este endereço IP para aceder remotamente à VM. Embora o portal não cria um [nome de domínio completamente qualificado](https://en.wikipedia.org/wiki/Fully_qualified_domain_name), ou FQDN, pode criar um quando a VM é criada. Este artigo demonstra os passos para criar um nome DNS ou o FQDN.

## <a name="create-a-fqdn"></a>Criar um FQDN
Este artigo pressupõe que já criou uma VM. Se for necessário, pode [criar uma VM no portal do](quick-create-portal.md) ou [com o Azure PowerShell](quick-create-powershell.md). Assim que a sua VM está a funcionar, siga estes passos:

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

Agora pode ligar remotamente à VM com este nome DNS, tais como para o protocolo RDP (Remote Desktop).

## <a name="next-steps"></a>Passos Seguintes
Agora que a VM tem um nome IP e DNS público, pode implementar arquiteturas de aplicações comuns ou serviços, como o IIS, o SQL ou o SharePoint.

Também pode ler mais sobre [com o Resource Manager](../../azure-resource-manager/resource-group-overview.md) para obter dicas sobre a criação de suas implementações do Azure.

