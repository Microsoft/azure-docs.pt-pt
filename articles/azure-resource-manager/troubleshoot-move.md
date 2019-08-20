---
title: Solucionar erros ao mover recursos do Azure para uma nova assinatura ou grupo de recursos
description: Utilize o Azure Resource Manager para mover recursos para um novo grupo de recursos ou subscrição.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/19/2019
ms.author: tomfitz
ms.openlocfilehash: 445ee2784a74a366089a49a0e2f2f17d51ef93bf
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624307"
---
# <a name="troubleshoot-moving-azure-resources-to-new-resource-group-or-subscription"></a>Solucionar problemas de movimentação de recursos do Azure para novo grupo de recursos ou assinatura

Este artigo fornece sugestões para ajudar a resolver problemas ao mover recursos.

## <a name="upgrade-a-subscription"></a>Atualizar uma assinatura

Se você realmente deseja atualizar sua assinatura do Azure (como alternar de gratuita para pré-pago), precisa converter sua assinatura.

* Para atualizar uma versão de avaliação gratuita, consulte [atualizar a sua subscrição de avaliação gratuita ou o Microsoft Imagine Azure para pay as you go](../billing/billing-upgrade-azure-subscription.md).
* Para alterar uma conta pay as you go, consulte [alterar a sua subscrição pay as you go do Azure para outra oferta](../billing/billing-how-to-switch-azure-offer.md).

Se não é possível converter a subscrição [criar um pedido de suporte do Azure](../azure-supportability/how-to-create-azure-support-request.md). Selecione **gestão de subscrições** para o tipo de problema.

## <a name="service-limitations"></a>Limitações de serviço

Alguns serviços exigem considerações adicionais ao mover recursos. Se você estiver movendo os seguintes serviços, certifique-se de verificar as diretrizes e as limitações.

* [Serviços Aplicacionais](./move-limitations/app-service-move-limitations.md)
* [Azure DevOps Services](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
* [Modelo de implementação clássica](./move-limitations/classic-model-move-limitations.md)
* [Redes](./move-limitations/networking-move-limitations.md)
* [Serviços de Recuperação](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
* [Máquinas Virtuais](./move-limitations/virtual-machines-move-limitations.md)

## <a name="large-requests"></a>Solicitações grandes

Sempre que possível, quebra de grandes passa para operações de movimentação separado. O Resource Manager retorna imediatamente um erro quando há mais de 800 recursos em uma única operação. No entanto, o mover os recursos de menos de 800 também poderá falhar por tempo limite.

## <a name="resource-not-in-succeeded-state"></a>Recurso que não está no estado com êxito

Se você receber uma mensagem de erro que indica que um recurso não pode ser movido porque não está em um estado de êxito, ele poderá, na verdade, ser um recurso dependente que está bloqueando a movimentação. Consulte [estado dos recursos dependentes](./move-limitations/networking-move-limitations.md#state-of-dependent-resources).

## <a name="next-steps"></a>Passos Seguintes

Para obter comandos para mover recursos, consulte [mover recursos para um novo grupo de recursos ou assinatura](resource-group-move-resources.md).
