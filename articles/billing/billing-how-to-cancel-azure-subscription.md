---
title: Cancelar a sua subscrição do Azure | Documentos da Microsoft
description: Descreve como cancelar a sua subscrição do Azure, como a subscrição de avaliação gratuita
author: bandersmsft
manager: amberb
tags: billing
ms.assetid: 3051d6b0-179f-4e3a-bda4-3fee7135eac5
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: banders
ms.openlocfilehash: 8f4279d9ac085cdd1ded0dfdda4fad9d3fe12fb8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66480230"
---
# <a name="cancel-your-subscription-for-azure"></a>Cancelar a sua subscrição do Azure

Apenas uma subscrição do Azure [conta de administrador](billing-subscription-transfer.md#whoisaa) pode cancelar uma subscrição do Azure. Um administrador de subscrição do Azure pode também [atribuir outro utilizador como um administrador de subscrição](billing-add-change-azure-subscription-administrator.md#assign-a-user-as-an-administrator-of-a-subscription) para que eles podem cancelar uma subscrição. Depois de cancelar a subscrição, o acesso aos recursos e serviços do Azure termina.

Antes de cancelar a sua subscrição:

* Fazer backup dos dados. Por exemplo, se estiver armazenando dados no armazenamento do Azure ou SQL, transfira uma cópia. Se tiver uma máquina virtual, guarde uma imagem dele localmente.
* Encerre os seus serviços. Vá para o [página de recursos no portal de gestão](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources), e **parar** qualquer executar máquinas virtuais, aplicações ou outros serviços.
* Considere a migração dos seus dados. Ver [mover recursos para um novo grupo de recursos ou subscrição](../azure-resource-manager/resource-group-move-resources.md).
* Tem de eliminar todos os recursos e todos os grupos de recursos. É necessário excluí-los antes de pode cancelar uma subscrição. Cada grupo de recursos tem de ser eliminado individualmente. Durante a eliminação do grupo de recursos, tem de confirmar a eliminação ao escrever o nome do grupo de recursos.
* Se tiver quaisquer funções personalizadas que fazem referência a esta subscrição `AssignableScopes`, deve atualizar essas funções personalizadas para remover a subscrição. Se tentar atualizar uma função personalizada, depois de cancelar uma subscrição, poderá receber um erro. Para obter mais informações, consulte [resolver problemas relacionados com funções personalizadas](../role-based-access-control/troubleshooting.md#problems-with-custom-roles) e [funções personalizadas para recursos do Azure](../role-based-access-control/custom-roles.md).

Se cancelar a um plano de suporte do Azure pago, é-lhe cobrada ainda para o restante de subscrição. Para obter mais informações, consulte [planos de suporte do Azure](https://azure.microsoft.com/support/plans/).

## <a name="cancel-subscription-using-the-azure-portal"></a>Cancelar a subscrição com o portal do Azure

1. Selecione a sua subscrição a partir da [página de subscrições no portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
2. Selecione a subscrição que pretende cancelar.
3. Selecione **descrição geral**e, em seguida, selecione **Cancelar subscrição**.

    ![Captura de ecrã que mostra o botão Cancelar](./media/billing-how-to-cancel-azure-subscription/cancel_ibiza.png)
3. Siga instruções e concluir o cancelamento.

## <a name="what-happens-after-i-cancel-my-subscription"></a>O que acontece depois de cancelar a minha subscrição?

Depois de cancelar, a faturação é interrompida imediatamente. No entanto, pode demorar até 10 minutos para que o cancelamento Mostrar no portal. Se cancelar no meio de um período de faturação, enviamos a fatura final correspondente na data da nota fiscal típico após terminar o período.

Depois de cancelar, seus serviços estão desativados. Isso significa que as suas máquinas virtuais são desalocadas, endereços IP temporários são liberados e armazenamento é só de leitura.

Podemos aguardar os 90 dias antes de eliminar permanentemente os seus dados em caso de precisa para aceder ao mesmo ou mudar de ideias. Não cobramos para reter os dados. Para obter mais informações, consulte [Microsoft Trust Center - como podemos gerir os seus dados](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409).

## <a name="reactivate-subscription"></a>Reativar subscrição

Se cancelar a sua subscrição pay as you go acidentalmente, pode [reativá-la no Centro de contas](billing-subscription-become-disable.md).

Se a sua subscrição não está pay as you go, contacte o suporte dentro de 90 dias do cancelamento para reativar a sua subscrição.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).
