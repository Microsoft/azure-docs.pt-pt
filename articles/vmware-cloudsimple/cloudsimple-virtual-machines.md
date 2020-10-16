---
title: Visão geral das máquinas virtuais
titleSuffix: Azure VMware Solution by CloudSimple
description: Saiba mais sobre as máquinas virtuais CloudSimple e os seus benefícios. Pode gerir máquinas virtuais VMware a partir do portal Azure.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6f9be035978667287b8b88ec1bb64b3882b0f929
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88141981"
---
# <a name="cloudsimple-virtual-machines-overview"></a>Visão geral das máquinas virtuais CloudSimple

O CloudSimple permite-lhe gerir máquinas virtuais VMware (VMs) a partir do portal Azure.  Um cluster ou um conjunto de recursos do seu cluster vSphere é gerido através do Azure mapeando-o para a sua subscrição.

Para criar um CloudSimple VM de Azure, um modelo VM deve existir no seu VCenter Private Cloud.  O modelo é utilizado para personalizar o sistema operativo e as aplicações.  O modelo VM pode ser endurecido para atender às políticas de segurança da empresa.  Pode utilizar o modelo para criar VMs e depois consumi-los a partir do portal Azure utilizando um modelo de self-service.

## <a name="benefits"></a>Benefícios

As máquinas virtuais CloudSimple do portal Azure fornecem um mecanismo de self-service para os utilizadores criarem e gerirem máquinas virtuais VMware.

* Crie um CloudSimple VM no seu vCenter Cloud Privado
* Gerir propriedades VM
  * Adicionar/remover discos
  * Adicionar/remover NICs
* Operações de energia do seu CloudSimple VM
  * Ligar e desligar
  * Repor VM
* Eliminar VM

## <a name="next-steps"></a>Passos seguintes

* Saiba como [consumir VMware VMs em Azure](quickstart-create-vmware-virtual-machine.md)
* Saiba como mapear a [sua subscrição do Azure](azure-subscription-mapping.md)
