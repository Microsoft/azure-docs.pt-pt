---
title: Mover recursos de rede Azure para novo grupo de subscrição ou recursos
description: Utilize o Azure Resource Manager para mover redes virtuais e outros recursos de rede para um novo grupo de recursos ou subscrição.
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: b7aaf01b696b13136a0f4077f315b137c8917906
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106120134"
---
# <a name="move-guidance-for-networking-resources"></a>Orientar para os recursos em rede

Este artigo descreve como mover redes virtuais e outros recursos de networking para cenários específicos.

## <a name="dependent-resources"></a>Recursos dependentes

> [!NOTE]
> Por favor, note que os Gateways VPN associados a endereços IP públicos não são atualmente capazes de se mover entre grupos de recursos ou subscrições.

Ao mover um recurso, deve também mover os seus recursos dependentes (por exemplo, endereços IP públicos, gateways de rede virtuais, todos os recursos de conexão associados). As portas de rede locais podem estar num grupo de recursos diferente.

Para mover uma máquina virtual com um cartão de interface de rede para uma nova subscrição, tem de mover todos os recursos dependentes. Mova a rede virtual para o cartão de interface de rede, todos os outros cartões de interface de rede para a rede virtual e os gateways VPN.

Para obter mais informações, consulte [Cenário para mover-se através de subscrições.](../move-resource-group-and-subscription.md#scenario-for-move-across-subscriptions)

## <a name="peered-virtual-network"></a>Rede virtual esprevada

Para mover uma rede virtual espreitada, tem primeiro de desativar a rede virtual que espreita. Uma vez desativado, pode mover a rede virtual. Após o movimento, reenutilize o olhar da rede virtual.

## <a name="subnet-links"></a>Links de sub-rede

Não é possível mover uma rede virtual para uma subscrição diferente se a rede virtual contiver uma sub-rede com ligações de navegação de recursos. Por exemplo, se um recurso Azure Cache for Redis for implantado numa sub-rede, essa sub-rede tem uma ligação de navegação de recursos.

## <a name="next-steps"></a>Passos seguintes

Para que os comandos movimentem recursos, consulte [mover recursos para novo grupo de recursos ou subscrição](../move-resource-group-and-subscription.md).
