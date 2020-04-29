---
title: Visão geral das máquinas virtuais
titleSuffix: Azure VMware Solution by CloudSimple
description: Saiba mais sobre as máquinas virtuais CloudSimple e os seus benefícios.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 436a5ab6fb286ce206f981487b6a29d774ecd1a1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77024913"
---
# <a name="cloudsimple-virtual-machines-overview"></a>Visão geral das máquinas virtuais CloudSimple

O CloudSimple permite-lhe gerir máquinas virtuais VMware (VMs) a partir do portal Azure.  Um cluster ou um conjunto de recursos do seu cluster vSphere é gerido através do Azure mapeando-o para a sua subscrição.

Para criar um VM CloudSimple a partir de Azure, um modelo VM deve existir no seu vCenter De Nuvem Privada.  O modelo é usado para personalizar o sistema operativo e aplicações.  O modelo VM pode ser endurecido para atender às políticas de segurança da empresa.  Pode usar o modelo para criar VMs e depois consumi-los a partir do portal Azure usando um modelo de self-service.

## <a name="benefits"></a>Benefícios

As máquinas virtuais CloudSimple do portal Azure fornecem um mecanismo de self-service para os utilizadores criarem e gerirem máquinas virtuais VMware.

* Crie um VM CloudSimple no seu vCenter de Nuvem Privada
* Gerir propriedades VM
  * Adicionar/remover discos
  * Adicionar/remover NICs
* Operações de energia do seu CloudSimple VM
  * Energia e desligar
  * Repor VM
* Eliminar VM

## <a name="next-steps"></a>Passos seguintes

* Saiba como [consumir VMs VMware no Azure](quickstart-create-vmware-virtual-machine.md)
* Saiba como mapear a [sua subscrição do Azure](azure-subscription-mapping.md)
