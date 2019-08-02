---
title: Cancelar sua assinatura do Azure | Microsoft Docs
description: Descreve como cancelar sua assinatura do Azure, como a assinatura de avaliação gratuita
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
ms.openlocfilehash: 7756174f01e3fede17bec3e2ac185e89caddc097
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68666438"
---
# <a name="cancel-your-azure-subscription"></a>Cancelar sua assinatura do Azure

Você pode cancelar sua assinatura do Azure na portal do Azure se não precisar mais da assinatura. 

Antes de cancelar sua assinatura:
* Faça backup dos seus dados. Por exemplo, se você estiver armazenando dados no armazenamento do Azure ou no SQL, Baixe uma cópia. Se você tiver uma máquina virtual, salve uma imagem dela localmente.
* Desligue seus serviços. Vá para a [página de recursos no portal de gerenciamento](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources)e **interrompa** quaisquer máquinas virtuais, aplicativos ou outros serviços em execução.
* Considere migrar seus dados. Consulte [mover recursos para um novo grupo de recursos ou assinatura](../azure-resource-manager/resource-group-move-resources.md).
* Exclua todos os recursos e todos os grupos de recursos. A exclusão delas é necessária para que você possa cancelar uma assinatura. Cada grupo de recursos deve ser excluído individualmente. Durante a exclusão do grupo de recursos, você deve confirmar a exclusão digitando o nome do grupo de recursos.
* Se você tiver funções personalizadas que fazem referência a essa assinatura `AssignableScopes`no, deverá atualizar essas funções personalizadas para remover a assinatura. Se você tentar atualizar uma função personalizada depois de cancelar uma assinatura, você poderá receber um erro. Para obter mais informações, consulte [solucionar problemas com funções personalizadas](../role-based-access-control/troubleshooting.md#problems-with-custom-roles) e [funções personalizadas para recursos do Azure](../role-based-access-control/custom-roles.md).

Se você cancelar um plano de suporte pago do Azure, será cobrado pelo restante do período da assinatura. Para obter mais informações, consulte [planos de suporte do Azure](https://azure.microsoft.com/support/plans/).

## <a name="cancel-subscription-in-the-azure-portal"></a>Cancelar assinatura no portal do Azure

1. Selecione sua assinatura na [página assinaturas no portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
2. Selecione a assinatura que você deseja cancelar.
3. Selecione **visão geral**e, em seguida, selecione **cancelar assinatura**.
    ![Captura de tela que mostra o botão Cancelar](./media/billing-how-to-cancel-azure-subscription/cancel_ibiza.png)
3. Siga as solicitações e conclua o cancelamento.


## <a name="who-can-cancel-a-subscription"></a>Quem pode cancelar uma assinatura?

A tabela a seguir descreve a permissão necessária para cancelar uma assinatura.

|Tipo de subscrição     |Quem pode cancelar  |
|---------|---------|
|Assinaturas criadas quando você se inscreve no Azure por meio do site do Azure. Por exemplo, quando você se inscreve para uma [conta gratuita do Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), [conta com tarifas pagas conforme o uso](https://azure.microsoft.com/offers/ms-azr-0003p/) ou como assinante do [Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/). |  Administrador da conta, proprietários e colaboradores para a assinatura  |
|[Microsoft Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/) e [desenvolvimento/teste Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/)     |  Proprietário da conta, proprietários e colaboradores para a assinatura       |
|[Plano do Azure](https://azure.microsoft.com/offers/ms-azr-0017g/) e [plano do Azure para DevTest](https://azure.microsoft.com/offers/ms-azr-0148g/)     |  Proprietários e colaboradores para a assinatura      |


## <a name="what-happens-after-i-cancel-my-subscription"></a>O que acontece depois de cancelar minha assinatura?

Depois de cancelar, a cobrança é interrompida imediatamente. No entanto, pode levar até 10 minutos para que o cancelamento seja mostrado no Portal. Se você cancelar no meio de um período de cobrança, enviaremos a fatura final em sua data de fatura típica depois que o período terminar.

Depois de cancelar, seus serviços serão desabilitados. Isso significa que as máquinas virtuais são desalocadas, os endereços IP temporários são liberados e o armazenamento é somente leitura.

Aguardamos 90 dias antes de excluir permanentemente seus dados caso você precise acessá-los ou mudar de ideia. Nós não cobramos por manter os dados. Para saber mais, consulte a [central de confiabilidade da Microsoft-como gerenciamos seus dados](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409).

## <a name="reactivate-subscription"></a>Reativar subscrição

Se você cancelar sua assinatura com tarifas pagas conforme o uso acidentalmente, poderá [reativá-la no centro de contas](billing-subscription-become-disable.md).

Se sua assinatura não for uma assinatura com tarifas pagas conforme o uso, entre em contato com o suporte em 90 dias de cancelamento para reativar sua assinatura.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).
