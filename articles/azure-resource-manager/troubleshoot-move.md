---
title: Solucionar erros de movimentação-Azure Resource Manager
description: Use Azure Resource Manager para mover recursos para um novo grupo de recursos ou assinatura.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/27/2019
ms.author: tomfitz
ms.openlocfilehash: 383098ab227013119bca668b42035c93334c8464
ms.sourcegitcommit: 6eecb9a71f8d69851bc962e2751971fccf29557f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72533349"
---
# <a name="troubleshoot-moving-azure-resources-to-new-resource-group-or-subscription"></a>Solucionar problemas de movimentação de recursos do Azure para novo grupo de recursos ou assinatura

Este artigo fornece sugestões para ajudar a resolver problemas ao mover recursos.

## <a name="upgrade-a-subscription"></a>Atualizar uma assinatura

Se você realmente deseja atualizar sua assinatura do Azure (como alternar de gratuita para pré-pago), precisa converter sua assinatura.

* Para atualizar uma avaliação gratuita, consulte [atualizar sua avaliação gratuita ou Microsoft imagine assinatura do Azure para](../billing/billing-upgrade-azure-subscription.md)pré-pago.
* Para alterar uma conta paga conforme o uso, consulte [alterar sua assinatura paga conforme o uso do Azure para uma oferta diferente](../billing/billing-how-to-switch-azure-offer.md).

Se você não puder converter a assinatura, [crie uma solicitação de suporte do Azure](../azure-supportability/how-to-create-azure-support-request.md). Selecione **Gerenciamento de assinatura** para o tipo de problema.

## <a name="service-limitations"></a>Limitações de serviço

Alguns serviços exigem considerações adicionais ao mover recursos. Se você estiver movendo os seguintes serviços, certifique-se de verificar as diretrizes e as limitações.

* [Serviços Aplicacionais](./move-limitations/app-service-move-limitations.md)
* [Azure DevOps Services](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
* [Modelo de implementação clássica](./move-limitations/classic-model-move-limitations.md)
* [Redes](./move-limitations/networking-move-limitations.md)
* [Serviços de Recuperação](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
* [Máquinas Virtuais](./move-limitations/virtual-machines-move-limitations.md)

## <a name="large-requests"></a>Solicitações grandes

Quando possível, divida grandes movimentações em operações de movimentação separadas. O Resource Manager retorna imediatamente um erro quando há mais de 800 recursos em uma única operação. No entanto, a movimentação de menos de 800 recursos também pode falhar por tempo limite.

## <a name="resource-not-in-succeeded-state"></a>Recurso que não está no estado com êxito

Quando você recebe uma mensagem de erro que indica que um recurso não pode ser movido porque não está em um estado bem-sucedido, ele pode, na verdade, ser um recurso dependente que está bloqueando a movimentação. Normalmente, o código de erro é **MoveCannotProceedWithResourcesNotInSucceededState**.

Se o grupo de recursos de origem ou de destino contiver uma rede virtual, os Estados de todos os recursos dependentes da rede virtual serão verificados durante a movimentação. A verificação inclui esses recursos diretamente e indiretamente dependente da rede virtual. Se qualquer um desses recursos estiver em um estado de falha, a movimentação será bloqueada. Por exemplo, se uma máquina virtual que usa a rede virtual falhou, a movimentação será bloqueada. A movimentação é bloqueada mesmo quando a máquina virtual não é um dos recursos que estão sendo movidos e não está em um dos grupos de recursos para a movimentação.

Ao receber esse erro, você tem duas opções. Mova seus recursos para um grupo de recursos que não tem uma rede virtual ou [contate o suporte](../azure-supportability/how-to-create-azure-support-request.md).

## <a name="next-steps"></a>Passos seguintes

Para obter comandos para mover recursos, consulte [mover recursos para um novo grupo de recursos ou assinatura](resource-group-move-resources.md).
