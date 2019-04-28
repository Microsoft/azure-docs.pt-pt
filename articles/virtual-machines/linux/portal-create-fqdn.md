---
title: Criar o FQDN para uma VM do Linux no portal do Azure | Documentos da Microsoft
description: Saiba como criar um nome de domínio completamente qualificado ou FQDN, para um Gerenciador de recursos com base em máquina virtual no portal do Azure.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 2cd6c249-a737-4a0a-b5ba-e1c09e551b30
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/15/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 54d1f77823b982ee8f49122c46b0a01cb27390f2
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62129422"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>Criar um nome de domínio completamente qualificado no portal do Azure para uma VM do Linux

Quando cria uma máquina virtual (VM) no [portal do Azure](https://portal.azure.com), um recurso IP público para a máquina virtual é criado automaticamente. Utilize este endereço IP para aceder remotamente à VM. Embora o portal não cria um [nome de domínio completamente qualificado](https://en.wikipedia.org/wiki/Fully_qualified_domain_name), ou FQDN, pode adicionar um quando a VM é criada. Este artigo demonstra os passos para criar um nome DNS ou o FQDN.

## <a name="create-a-fqdn"></a>Criar um FQDN
Este artigo pressupõe que já criou uma VM. Se for necessário, pode [criar uma VM no portal do](quick-create-portal.md) ou [com a CLI do Azure](quick-create-cli.md). Assim que a sua VM está a funcionar, siga estes passos:

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

Pode agora ligar remotamente à VM com este nome DNS, tal como com `ssh azureuser@mydns.westus.cloudapp.azure.com`.

## <a name="next-steps"></a>Passos Seguintes
Agora que a VM tem um nome IP e DNS público, pode implementar comuns estruturas de aplicações ou serviços, como o nginx, MongoDB, Docker, etc.

Também pode ler mais sobre [com o Resource Manager](../../azure-resource-manager/resource-group-overview.md) para obter dicas sobre a criação de suas implementações do Azure.

