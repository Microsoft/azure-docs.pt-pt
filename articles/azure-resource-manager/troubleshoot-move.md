---
title: Resolver erros ao mover os recursos do Azure para a nova subscrição ou grupo de recursos
description: Utilize o Azure Resource Manager para mover recursos para um novo grupo de recursos ou subscrição.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: e23d7c571a010e5bfb42e5f15368e0194272ed53
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723470"
---
# <a name="troubleshoot-moving-azure-resources-to-new-resource-group-or-subscription"></a>Resolver problemas de mover os recursos do Azure para um novo grupo de recursos ou subscrição

Este artigo fornece sugestões para ajudar a resolver problemas durante a movimentação de recursos.

## <a name="upgrade-a-subscription"></a>Atualizar uma subscrição

Se realmente quiser atualizar a sua subscrição do Azure (por exemplo, mudar de gratuita para pay as you go), terá de converter a sua subscrição.

* Para atualizar uma versão de avaliação gratuita, consulte [atualizar a sua subscrição de avaliação gratuita ou o Microsoft Imagine Azure para pay as you go](../billing/billing-upgrade-azure-subscription.md).
* Para alterar uma conta pay as you go, consulte [alterar a sua subscrição pay as you go do Azure para outra oferta](../billing/billing-how-to-switch-azure-offer.md).

Se não é possível converter a subscrição [criar um pedido de suporte do Azure](../azure-supportability/how-to-create-azure-support-request.md). Selecione **gestão de subscrições** para o tipo de problema.

## <a name="service-limitations"></a>Limitações de serviço

Alguns serviços requerem considerações adicionais durante a movimentação de recursos. Se estiver a mover os serviços seguintes, certifique-se de que verificar as diretrizes e limitações.

* [Serviços Aplicacionais](./move-limitations/app-service-move-limitations.md)
* [Serviços de DevOps do Azure](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
* [Modelo de implementação clássica](./move-limitations/classic-model-move-limitations.md)
* [Serviços de Recuperação](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
* [Máquinas Virtuais](./move-limitations/virtual-machines-move-limitations.md)
* [Redes virtuais](./move-limitations/virtual-network-move-limitations.md)

## <a name="large-requests"></a>Pedidos de grandes dimensões

Sempre que possível, quebra de grandes passa para operações de movimentação separado. Gestor de recursos devolve imediatamente um erro quando há mais de 800 recurso numa única operação. No entanto, o mover os recursos de menos de 800 também poderá falhar por tempo limite.

## <a name="next-steps"></a>Passos seguintes

Para obter comandos mover os recursos, veja [mover recursos para um novo grupo de recursos ou subscrição](resource-group-move-resources.md).
