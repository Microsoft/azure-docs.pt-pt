---
title: Solução de VMware ao CloudSimple - descrição geral de máquinas virtuais do Azure
description: Saiba mais sobre as máquinas virtuais de CloudSimple e seus benefícios.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 77a2901ae2d81f42780110b1576c1f32c7ff397b
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65209491"
---
# <a name="cloudsimple-virtual-machines-overview"></a>Descrição geral de máquinas virtuais de CloudSimple

CloudSimple permite-lhe gerir VMs do VMware a partir do portal do Azure.  Um cluster ou um agrupamento de recursos do seu cluster do vSphere é gerido através do Azure, mapeando-o à sua subscrição.  Máquina virtual de CloudSimple traz gestão self-service de VMware VMs no portal do Azure.  

Para criar uma VM de CloudSimple do Azure, um modelo de VM tem de existir no seu vCenter de nuvem privada.  O modelo é utilizado para personalizar o sistema operacional e aplicativos.  O modelo de VM pode ser protegido para atender às políticas de segurança da empresa.  Pode utilizar o modelo para criar VMs e usá-los a partir do portal do Azure através de um modelo de self-service.

## <a name="benefits"></a>Benefícios

Máquinas de virtuais de CloudSimple partir do portal do Azure fornecem um mecanismo de autoatendimento para usuários criar e gerir máquinas virtuais VMware.

* Criar uma VM de CloudSimple no seu vCenter de nuvem privada
* Gerir as propriedades da VM
  * Adicionar ou remover discos
  * Adicionar/remover NICs
* Operações de energia da CloudSimple VM
  * Ligar e desligar
  * Repor VM
* Eliminar VM

## <a name="next-steps"></a>Passos Seguintes

* Saiba como [consumir as VMs de VMware no Azure](quickstart-create-vmware-virtual-machine.md)
* Saiba como [mapear a sua subscrição do Azure](https://docs.azure.cloudsimple.com/azure-subscription-mapping/)