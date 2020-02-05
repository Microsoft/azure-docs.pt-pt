---
title: Soluções Azure VMware (AVS) - Visão geral das máquinas virtuais
description: Saiba mais sobre as máquinas virtuais AVS e os seus benefícios.
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0e6cc6d51e37cea98cc553a236295311d21f595a
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024913"
---
# <a name="avs-virtual-machines-overview"></a>Visão geral das máquinas virtuais AVS

O AVS permite-lhe gerir máquinas virtuais VMware (VMs) a partir do portal Azure. Um cluster ou um pool de recursos do seu cluster vSphere é gerenciado por meio do Azure, mapeando-o para sua assinatura.

Para criar um VM AVS a partir de Azure, um modelo VM deve existir no seu VCenter De Nuvem Privada AVS. O modelo é usado para personalizar o sistema operacional e os aplicativos. A VM de modelo pode ser protegida para atender às políticas de segurança corporativa. Você pode usar o modelo para criar VMs e, em seguida, consumi-las do portal do Azure usando um modelo de autoatendimento.

## <a name="benefits"></a>Vantagens

As máquinas virtuais AVS do portal Azure fornecem um mecanismo de self-service para os utilizadores criarem e gerirem máquinas virtuais VMware.

* Crie um VM AVS no seu VCenter De Nuvem Privada AVS
* Gerenciar Propriedades da VM
  * Adicionar/remover discos
  * Adicionar/remover NICs
* Operações de energia do seu VM AVS
  * Ligar e desligar
  * Redefinir VM
* Excluir VM

## <a name="next-steps"></a>Passos seguintes

* Saiba como [consumir VMs VMware no Azure](quickstart-create-vmware-virtual-machine.md)
* Saiba como [mapear sua assinatura do Azure](azure-subscription-mapping.md)
