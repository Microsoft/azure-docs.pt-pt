---
title: Erros de movimento de resolução de problemas
description: Utilize o Gestor de Recursos Azure para transferir recursos para um novo grupo de recursos ou subscrição.
ms.topic: conceptual
ms.date: 08/27/2019
ms.openlocfilehash: 5a65f7daa0f5e3b1c8c6ddfdbecc0ff7d53e5afd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75891265"
---
# <a name="troubleshoot-moving-azure-resources-to-new-resource-group-or-subscription"></a>Resolver problemas de movimentação de recursos do Azure para um novo grupo de recursos ou uma nova subscrição

Este artigo fornece sugestões para ajudar a resolver problemas ao mover recursos.

## <a name="upgrade-a-subscription"></a>Atualizar uma subscrição

Se realmente quer atualizar a sua subscrição Azure (como mudar de grátis para pay-as-you-go), precisa de converter a sua subscrição.

* Para atualizar um teste gratuito, consulte o upgrade do seu Teste Gratuito ou a [subscrição do Microsoft Imagine Azure para Pay-As-You-Go](../../billing/billing-upgrade-azure-subscription.md).
* Para alterar uma conta pay-as-you-go, consulte Alterar a [subscrição do Seu Pay-As-You-Go azure para uma oferta diferente](../../billing/billing-how-to-switch-azure-offer.md).

Se não conseguir converter a subscrição, crie um pedido de [apoio Azure.](../../azure-portal/supportability/how-to-create-azure-support-request.md) Selecione **Gestão de Assinatura** para o tipo de problema.

## <a name="service-limitations"></a>Limitações de serviço

Alguns serviços requerem considerações adicionais na mudança de recursos. Se estiver a mover os seguintes serviços, certifique-se de verificar as orientações e limitações.

* [Serviços Aplicacionais](./move-limitations/app-service-move-limitations.md)
* [Serviços de DevOps do Azure](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
* [Modelo de implantação clássico](./move-limitations/classic-model-move-limitations.md)
* [Redes](./move-limitations/networking-move-limitations.md)
* [Serviços de Recuperação](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
* [Máquinas Virtuais](./move-limitations/virtual-machines-move-limitations.md)

## <a name="large-requests"></a>Grandes pedidos

Quando possível, parta grandes movimentos em operações de movimento separadas. O Gestor de Recursos devolve imediatamente um erro quando há mais de 800 recursos numa única operação. No entanto, a movimentação de menos de 800 recursos também pode falhar com o tempo de saída.

## <a name="resource-not-in-succeeded-state"></a>Recurso não em estado bem sucedido

Quando se recebe uma mensagem de erro que indica que um recurso não pode ser movido porque não está num estado bem sucedido, pode ser um recurso dependente que está a bloquear o movimento. Tipicamente, o código de erro é **MoveCanProceedWithResourcesNotInSucceededState**.

Se o grupo de recursos de origem ou alvo contiver uma rede virtual, os estados de todos os recursos dependentes para a rede virtual são verificados durante a mudança. O cheque inclui esses recursos direta e indiretamente dependentes da rede virtual. Se algum desses recursos estiver em estado falhado, a mudança está bloqueada. Por exemplo, se uma máquina virtual que utiliza a rede virtual falhou, o movimento está bloqueado. O movimento está bloqueado mesmo quando a máquina virtual não é um dos recursos a ser movido e não está num dos grupos de recursos para a mudança.

Quando recebes este erro, tens duas opções. Ou transfere os seus recursos para um grupo de recursos que não tenha uma rede virtual, ou suporte de [contato.](../../azure-portal/supportability/how-to-create-azure-support-request.md)

## <a name="next-steps"></a>Passos seguintes

Para que os comandos movam recursos, consulte [mover recursos para um novo grupo de recursos ou subscrição](move-resource-group-and-subscription.md).
