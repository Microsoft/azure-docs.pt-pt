---
title: Mover os recursos de rede do Azure para a nova assinatura ou grupo de recursos
description: Use Azure Resource Manager para mover redes virtuais e outros recursos de rede para um novo grupo de recursos ou assinatura.
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: 0cd6887d3489f2ffede0f5e3d63533a33a6ccc04
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75485237"
---
# <a name="move-guidance-for-networking-resources"></a>Mover diretrizes para recursos de rede

Este artigo descreve como mover redes virtuais e outros recursos de rede para cenários específicos.

## <a name="dependent-resources"></a>Recursos dependentes

Ao mover uma rede virtual, também tem de mover os recursos dependentes. Para Gateways de VPN, tem de mover os endereços IP, gateways de rede virtual e todos os recursos de ligação associada. Gateways de rede local podem estar num grupo de recursos diferente.

Para mover uma máquina virtual com uma placa de interface de rede para uma nova assinatura, você deve mover todos os recursos dependentes. Mova a rede virtual para a placa de interface de rede, todas as outras placas de interface de rede para a rede virtual e os gateways de VPN.

Para obter mais informações, consulte [cenário para mover entre assinaturas](../move-resource-group-and-subscription.md#scenario-for-move-across-subscriptions).

## <a name="peered-virtual-network"></a>Rede virtual emparelhada

Para mover uma rede virtual em modo de peering, tem primeiro de desativar o peering de rede virtual. Uma vez desativada, pode mover a rede virtual. Após a mudança, reativar o peering de rede virtual.

## <a name="subnet-links"></a>Links de sub-rede

Não é possível mover uma rede virtual para uma subscrição diferente se a rede virtual contém uma sub-rede com ligações de navegação de recursos. Por exemplo, se uma Cache do Azure para o recurso de Redis é implementada numa sub-rede, o que a sub-rede tem uma ligação de navegação de recursos.

## <a name="next-steps"></a>Passos seguintes

Para obter comandos para mover recursos, consulte [mover recursos para um novo grupo de recursos ou assinatura](../move-resource-group-and-subscription.md).
