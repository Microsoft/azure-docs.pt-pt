---
title: Solucionar problemas de erros de movimentação
description: Utilize o Azure Resource Manager para mover recursos para um novo grupo de recursos ou subscrição.
ms.topic: conceptual
ms.date: 08/27/2019
ms.openlocfilehash: 5a65f7daa0f5e3b1c8c6ddfdbecc0ff7d53e5afd
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75891265"
---
# <a name="troubleshoot-moving-azure-resources-to-new-resource-group-or-subscription"></a>Solucionar problemas de movimentação de recursos do Azure para novo grupo de recursos ou assinatura

Este artigo fornece sugestões para ajudar a resolver problemas ao mover recursos.

## <a name="upgrade-a-subscription"></a>Atualizar uma assinatura

Se você realmente deseja atualizar sua assinatura do Azure (como alternar de gratuita para pré-pago), precisa converter sua assinatura.

* Para atualizar uma versão de avaliação gratuita, consulte [atualizar a sua subscrição de avaliação gratuita ou o Microsoft Imagine Azure para pay as you go](../../billing/billing-upgrade-azure-subscription.md).
* Para alterar uma conta pay as you go, consulte [alterar a sua subscrição pay as you go do Azure para outra oferta](../../billing/billing-how-to-switch-azure-offer.md).

Se não é possível converter a subscrição [criar um pedido de suporte do Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Selecione **gestão de subscrições** para o tipo de problema.

## <a name="service-limitations"></a>Limitações de serviço

Alguns serviços exigem considerações adicionais ao mover recursos. Se você estiver movendo os seguintes serviços, certifique-se de verificar as diretrizes e as limitações.

* [Serviços Aplicacionais](./move-limitations/app-service-move-limitations.md)
* [Azure DevOps Services](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
* [Modelo de implementação clássica](./move-limitations/classic-model-move-limitations.md)
* [Redes](./move-limitations/networking-move-limitations.md)
* [Serviços de Recuperação](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
* [Máquinas Virtuais](./move-limitations/virtual-machines-move-limitations.md)

## <a name="large-requests"></a>Solicitações grandes

Sempre que possível, quebra de grandes passa para operações de movimentação separado. O Resource Manager retorna imediatamente um erro quando há mais de 800 recursos em uma única operação. No entanto, o mover os recursos de menos de 800 também poderá falhar por tempo limite.

## <a name="resource-not-in-succeeded-state"></a>Recurso que não está no estado com êxito

Quando você recebe uma mensagem de erro que indica que um recurso não pode ser movido porque não está em um estado bem-sucedido, ele pode, na verdade, ser um recurso dependente que está bloqueando a movimentação. Normalmente, o código de erro é **MoveCannotProceedWithResourcesNotInSucceededState**.

Se o grupo de recursos de origem ou de destino contiver uma rede virtual, os Estados de todos os recursos dependentes da rede virtual serão verificados durante a movimentação. A verificação inclui esses recursos diretamente e indiretamente dependente da rede virtual. Se qualquer um desses recursos estiver em um estado de falha, a movimentação será bloqueada. Por exemplo, se uma máquina virtual que usa a rede virtual falhou, a movimentação será bloqueada. A movimentação é bloqueada mesmo quando a máquina virtual não é um dos recursos que estão sendo movidos e não está em um dos grupos de recursos para a movimentação.

Ao receber esse erro, você tem duas opções. Mova seus recursos para um grupo de recursos que não tem uma rede virtual ou [contate o suporte](../../azure-portal/supportability/how-to-create-azure-support-request.md).

## <a name="next-steps"></a>Passos seguintes

Para obter comandos para mover recursos, consulte [mover recursos para um novo grupo de recursos ou assinatura](move-resource-group-and-subscription.md).
