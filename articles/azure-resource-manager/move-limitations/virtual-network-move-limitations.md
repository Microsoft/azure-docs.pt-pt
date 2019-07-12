---
title: Mover os recursos de redes virtuais do Azure para a nova subscrição ou grupo de recursos | Documentos da Microsoft
description: Utilize o Azure Resource Manager para mover redes virtuais para um novo grupo de recursos ou subscrição.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: 809d403a463048910a284e7f8fcdc18cf7c65b69
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723496"
---
# <a name="move-guidance-for-virtual-networks"></a>Mover a documentação de orientação para as redes virtuais

Este artigo descreve como mover as redes virtuais para cenários específicos.

## <a name="dependent-resources"></a>Recursos dependentes

Ao mover uma rede virtual, também tem de mover os recursos dependentes. Para Gateways de VPN, tem de mover os endereços IP, gateways de rede virtual e todos os recursos de ligação associada. Gateways de rede local podem estar num grupo de recursos diferente.

Para mover uma máquina virtual com uma placa de interface de rede, tem de mover todos os recursos dependentes. Mova a rede virtual para a placa de interface de rede, todos os cartões de interface outra rede para a rede virtual e os gateways de VPN.

## <a name="peered-virtual-network"></a>Rede virtual em modo de peering

Para mover uma rede virtual em modo de peering, tem primeiro de desativar o peering de rede virtual. Uma vez desativada, pode mover a rede virtual. Após a mudança, reativar o peering de rede virtual.

## <a name="subnet-links"></a>Ligações de sub-rede

Não é possível mover uma rede virtual para uma subscrição diferente se a rede virtual contém uma sub-rede com ligações de navegação de recursos. Por exemplo, se uma Cache do Azure para o recurso de Redis é implementada numa sub-rede, o que a sub-rede tem uma ligação de navegação de recursos.

## <a name="next-steps"></a>Passos Seguintes

Para obter comandos mover os recursos, veja [mover recursos para um novo grupo de recursos ou subscrição](../resource-group-move-resources.md).
