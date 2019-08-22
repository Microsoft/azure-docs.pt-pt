---
title: Solução do Azure VMware da CloudSimple – visão geral das máquinas virtuais
description: Saiba mais sobre as máquinas virtuais CloudSimple e seus benefícios.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 213ab51dae20d281a1a0e0f8ea18f4bde888e64d
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877905"
---
# <a name="cloudsimple-virtual-machines-overview"></a>Visão geral das máquinas virtuais CloudSimple

O CloudSimple permite que você gerencie VMs (máquinas virtuais) VMware do portal do Azure.  Um cluster ou um pool de recursos do seu cluster vSphere é gerenciado por meio do Azure, mapeando-o para sua assinatura.

Para criar uma VM CloudSimple do Azure, um modelo de VM deve existir em seu vCenter de nuvem privada.  O modelo é usado para personalizar o sistema operacional e os aplicativos.  A VM de modelo pode ser protegida para atender às políticas de segurança corporativa.  Você pode usar o modelo para criar VMs e, em seguida, consumi-las do portal do Azure usando um modelo de autoatendimento.

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

## <a name="next-steps"></a>Passos seguintes

* Saiba como [consumir VMs VMware no Azure](quickstart-create-vmware-virtual-machine.md)
* Saiba como [mapear sua assinatura do Azure](azure-subscription-mapping.md)
