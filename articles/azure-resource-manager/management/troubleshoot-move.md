---
title: Erros de movimento de resolução de problemas
description: Resolução de problemas de transferência de recursos para um novo grupo de recursos ou subscrição.
ms.topic: conceptual
ms.date: 08/27/2019
ms.openlocfilehash: 41b1e2435caf9874f3582a3394664c7b7f5a8d29
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "90054167"
---
# <a name="troubleshoot-moving-azure-resources-to-new-resource-group-or-subscription"></a>Resolver problemas de movimentação de recursos do Azure para um novo grupo de recursos ou uma nova subscrição

Este artigo fornece sugestões para ajudar a resolver problemas na movimentação de recursos.

## <a name="upgrade-a-subscription"></a>Atualizar uma subscrição

Se realmente pretender atualizar a sua subscrição Azure (como mudar de livre para pagar), tem de converter a sua subscrição.

* Para atualizar um teste gratuito, consulte [a atualização do seu Teste Gratuito ou subscrição do Microsoft Imagine Azure para Pay-As-You-Go](../../cost-management-billing/manage/upgrade-azure-subscription.md).
* Para alterar uma conta pay-as-you-go, consulte [alterar a sua subscrição Azure Pay-As-You-Go para uma oferta diferente](../../cost-management-billing/manage/switch-azure-offer.md).

Se não conseguir converter a subscrição, crie um pedido de [suporte Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Selecione **Gestão de Subscrição** para o tipo de emissão.

## <a name="service-limitations"></a>Limitações de serviço

Alguns serviços requerem considerações adicionais na movimentação de recursos. Se estiver a mover os seguintes serviços, verifique as orientações e limitações.

* [Serviços Aplicacionais](./move-limitations/app-service-move-limitations.md)
* [Azure DevOps Services](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
* [Modelo de implementação clássica](./move-limitations/classic-model-move-limitations.md)
* [Rede](./move-limitations/networking-move-limitations.md)
* [Serviços de Recuperação](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
* [Máquinas Virtuais](./move-limitations/virtual-machines-move-limitations.md)

## <a name="large-requests"></a>Grandes pedidos

Quando possível, parta grandes movimentos em operações de movimento separados. O Gestor de Recursos retorna imediatamente um erro quando existem mais de 800 recursos numa única operação. No entanto, a movimentação de menos de 800 recursos também pode falhar com o timing.

## <a name="resource-not-in-succeeded-state"></a>Recurso não em estado bem sucedido

Quando recebe uma mensagem de erro que indica que um recurso não pode ser movido porque não está num estado bem sucedido, pode ser um recurso dependente que está a bloquear o movimento. Normalmente, o código de erro é **MoveCannotProceedWithResourcesNotInSucceedEdState**.

Se o grupo de recursos de origem ou alvo contiver uma rede virtual, os estados de todos os recursos dependentes da rede virtual são verificados durante a mudança. O cheque inclui esses recursos direta e indiretamente dependentes da rede virtual. Se algum desses recursos estiver num estado falhado, a mudança está bloqueada. Por exemplo, se uma máquina virtual que utiliza a rede virtual tiver falhado, o movimento está bloqueado. O movimento é bloqueado mesmo quando a máquina virtual não é um dos recursos que estão a ser movidos e não está num dos grupos de recursos para a mudança.

Quando recebes este erro, tens duas opções. Ou move os seus recursos para um grupo de recursos que não tem uma rede virtual, ou [suporte de contato](../../azure-portal/supportability/how-to-create-azure-support-request.md).

## <a name="next-steps"></a>Passos seguintes

Para que os comandos movimentem recursos, consulte [mover recursos para novo grupo de recursos ou subscrição](move-resource-group-and-subscription.md).
