---
title: Criar FQDN para uma VM do Linux no portal do Azure | Microsoft Docs
description: Saiba como criar um nome de domínio totalmente qualificado, ou FQDN, para uma máquina virtual baseada no Resource Manager no portal do Azure.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: 2cd6c249-a737-4a0a-b5ba-e1c09e551b30
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/15/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d7309f4be43c6b653f261e5de5fbe3e638e83294
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70082432"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>Criar um nome de domínio totalmente qualificado no portal do Azure para uma VM do Linux

Quando você cria uma VM (máquina virtual) no [portal do Azure](https://portal.azure.com), um recurso de IP público para a máquina virtual é criado automaticamente. Use esse endereço IP para acessar remotamente a VM. Embora o portal não crie um [nome de domínio totalmente qualificado](https://en.wikipedia.org/wiki/Fully_qualified_domain_name), ou FQDN, você pode adicionar um depois que a VM é criada. Este artigo demonstra as etapas para criar um nome DNS ou FQDN.

## <a name="create-a-fqdn"></a>Criar um FQDN
Este artigo pressupõe que você já criou uma VM. Se necessário, você pode [criar uma VM no portal](quick-create-portal.md) ou [com o CLI do Azure](quick-create-cli.md). Siga estas etapas quando sua VM estiver em execução:

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

Agora você pode se conectar remotamente à VM usando esse nome DNS, como com `ssh azureuser@mydns.westus.cloudapp.azure.com`.

## <a name="next-steps"></a>Passos Seguintes
Agora que sua VM tem um IP público e um nome DNS, você pode implantar estruturas de aplicativo ou serviços comuns, como Nginx, MongoDB, Docker, etc.

Você também pode ler mais sobre como [usar o Gerenciador de recursos](../../azure-resource-manager/resource-group-overview.md) para obter dicas sobre como criar suas implantações do Azure.

