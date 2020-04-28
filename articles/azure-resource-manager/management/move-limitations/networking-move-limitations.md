---
title: Mover os recursos da Rede Azure para um novo grupo de subscrição ou recursos
description: Utilize o Gestor de Recursos Azure para mover redes virtuais e outros recursos de networking para um novo grupo de recursos ou subscrição.
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: 0cd6887d3489f2ffede0f5e3d63533a33a6ccc04
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75485237"
---
# <a name="move-guidance-for-networking-resources"></a>Mover orientação para recursos de networking

Este artigo descreve como mover redes virtuais e outros recursos de networking para cenários específicos.

## <a name="dependent-resources"></a>Recursos dependentes

Ao mover uma rede virtual, também deve mover os seus recursos dependentes. Para os Gateways VPN, deve mover endereços IP, gateways de rede virtual e todos os recursos de ligação associados. Os gateways da rede local podem estar num grupo de recursos diferente.

Para mover uma máquina virtual com um cartão de interface de rede para uma nova subscrição, você deve mover todos os recursos dependentes. Mova a rede virtual para o cartão de interface de rede, todos os outros cartões de interface de rede para a rede virtual e os gateways VPN.

Para mais informações, consulte Cenário para se deslocar através de [subscrições](../move-resource-group-and-subscription.md#scenario-for-move-across-subscriptions).

## <a name="peered-virtual-network"></a>Rede virtual peered

Para mover uma rede virtual esparsa, tem primeiro de desativar o olhar de rede virtual. Uma vez desativado, pode mover a rede virtual. Após a mudança, reabilite o olhar virtual da rede.

## <a name="subnet-links"></a>Ligações de sub-rede

Não é possível mover uma rede virtual para uma subscrição diferente se a rede virtual contiver uma subrede com links de navegação de recursos. Por exemplo, se um recurso Azure Cache for implantado numa subrede, essa sub-rede tem uma ligação de navegação de recursos.

## <a name="next-steps"></a>Passos seguintes

Para que os comandos movam recursos, consulte [mover recursos para um novo grupo de recursos ou subscrição](../move-resource-group-and-subscription.md).
