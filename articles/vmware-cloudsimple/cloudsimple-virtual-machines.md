---
title: Solução VMware de CloudSimple – visão geral das máquinas virtuais do Azure
description: Saiba mais sobre as máquinas virtuais CloudSimple e seus benefícios.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0f4967bbd12107bf6a04cb80537d4425c75c5f46
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812488"
---
# <a name="cloudsimple-virtual-machines-overview"></a>Visão geral das máquinas virtuais CloudSimple

O CloudSimple permite que você gerencie VMs VMware do portal do Azure.  Um cluster ou um pool de recursos do seu cluster vSphere é gerenciado por meio do Azure, mapeando-o para sua assinatura.  A máquina virtual CloudSimple traz o gerenciamento de autoatendimento das VMs VMware do portal do Azure.  

Para criar uma VM CloudSimple do Azure, um modelo de VM deve existir em seu vCenter de nuvem privada.  O modelo é usado para personalizar o sistema operacional e os aplicativos.  A VM de modelo pode ser protegida para atender às políticas de segurança corporativa.  Você pode usar o modelo para criar VMs e consumi-las de portal do Azure usando um modelo de autoatendimento.

## <a name="benefits"></a>Benefícios

As máquinas virtuais CloudSimple da portal do Azure fornecem um mecanismo de autoatendimento para que os usuários criem e gerenciem máquinas virtuais VMware.

* Criar uma VM CloudSimple em sua nuvem privada vCenter
* Gerenciar Propriedades da VM
  * Adicionar/remover discos
  * Adicionar/remover NICs
* Operações de energia de sua VM CloudSimple
  * Ligar e desligar
  * Redefinir VM
* Excluir VM

## <a name="next-steps"></a>Passos Seguintes

* Saiba como [consumir VMs VMware no Azure](quickstart-create-vmware-virtual-machine.md)
* Saiba como [mapear sua assinatura do Azure](https://docs.azure.cloudsimple.com/azure-subscription-mapping/)