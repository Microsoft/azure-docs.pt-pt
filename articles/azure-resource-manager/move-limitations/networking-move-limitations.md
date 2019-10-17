---
title: Mover os recursos de rede do Azure para uma nova assinatura ou grupo de recursos | Microsoft Docs
description: Use Azure Resource Manager para mover redes virtuais e outros recursos de rede para um novo grupo de recursos ou assinatura.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: tomfitz
ms.openlocfilehash: 14a7cb326a3017b1bdbcad21c8483eaaacd54674
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72437816"
---
# <a name="move-guidance-for-networking-resources"></a>Mover diretrizes para recursos de rede

Este artigo descreve como mover redes virtuais e outros recursos de rede para cenários específicos.

## <a name="dependent-resources"></a>Recursos dependentes

Ao mover uma rede virtual, você também deve mover seus recursos dependentes. Para gateways de VPN, você deve mover endereços IP, gateways de rede virtual e todos os recursos de conexão associados. Os gateways de rede local podem estar em um grupo de recursos diferente.

Para mover uma máquina virtual com uma placa de interface de rede para uma nova assinatura, você deve mover todos os recursos dependentes. Mova a rede virtual para a placa de interface de rede, todas as outras placas de interface de rede para a rede virtual e os gateways de VPN.

Para obter mais informações, consulte [cenário para mover entre assinaturas](../resource-group-move-resources.md#scenario-for-move-across-subscriptions).

## <a name="peered-virtual-network"></a>Rede virtual emparelhada

Para mover uma rede virtual emparelhada, primeiro você deve desabilitar o emparelhamento de rede virtual. Depois de desabilitado, você pode mover a rede virtual. Após a movimentação, reabilite o emparelhamento de rede virtual.

## <a name="subnet-links"></a>Links de sub-rede

Você não poderá mover uma rede virtual para uma assinatura diferente se a rede virtual contiver uma sub-rede com links de navegação de recursos. Por exemplo, se um cache do Azure para o recurso Redis for implantado em uma sub-rede, essa sub-rede terá um link de navegação de recurso.

## <a name="next-steps"></a>Passos seguintes

Para obter comandos para mover recursos, consulte [mover recursos para um novo grupo de recursos ou assinatura](../resource-group-move-resources.md).
