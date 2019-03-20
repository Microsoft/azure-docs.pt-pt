---
title: Gerir o acesso a faturação do Azure | Documentos da Microsoft
description: Saiba como conceder acesso às suas informações de faturas do Azure para os membros de sua equipe.
services: ''
documentationcenter: ''
author: vikramdesai01
manager: amberb
editor: ''
tags: billing
ms.assetid: e4c4d136-2826-4938-868f-a7e67ff6b025
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 8c2843840790d1e0dbfd4a789775c6c7ceb51a54
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57838260"
---
# <a name="manage-access-to-billing-information-for-azure"></a>Gerir o acesso a informações de faturação para o Azure

Para a maioria das subscrições, pode dar acesso à informação de faturação para membros de sua equipe a partir **subscrições** no portal do Azure. Se for um cliente do Azure com um Enterprise Agreement (cliente EA) e o administrador de empresa, pode conceder permissões para os administradores de departamento e os proprietários da conta no portal da empresa.

## <a name="give-access-to-billing"></a>Conceder acesso à faturação

Todos, exceto os clientes com EA podem conceder o acesso a informações de faturação do Azure através da atribuição de uma das seguintes funções de utilizador para membros de sua equipe:

- Administrador de Conta
- Administrador de Serviços
- Coadministrador
- Proprietário
- Contribuinte
- Leitor
- Leitor de Faturação

Para atribuir funções, consulte [gerir o acesso com RBAC e o portal do Azure](../role-based-access-control/role-assignments-portal.md).

Essas funções têm acesso às informações de faturas no [portal do Azure](https://portal.azure.com/). As pessoas que estão atribuídas essas funções também podem utilizar o [APIs de faturação](billing-usage-rate-card-overview.md) para obter programaticamente notas fiscais e detalhes de utilização. Para obter mais informações, consulte [funções no Azure RBAC](../role-based-access-control/built-in-roles.md).

### <a name="opt-in"></a> Permitir que os utilizadores a transferência de faturas

Depois de atribuir funções adequadas para membros de sua equipe, o administrador de conta tem de ativar o acesso a transferência de faturas no portal do Azure. Notas fiscais com mais de Dezembro de 2016 estão disponíveis apenas para o administrador de conta.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Como administrador de conta, selecione a sua subscrição do [painel de subscrições](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) no portal do Azure.

1. Selecione **notas fiscais** e, em seguida **acesso às notas fiscais**.

    ![Captura de ecrã mostra como delegar o acesso às notas fiscais](./media/billing-manage-access/AA-optin.png)

1. Selecione **no** e guardar.

    ![Captura de ecrã mostra no-off para delegar o acesso à fatura](./media/billing-manage-access/AA-optinAllow.png)

O Administrador de Conta também pode configurar para que as faturas sejam enviadas por e-mail. Para obter mais informações, veja [Receber a sua fatura no e-mail](billing-download-azure-invoice-daily-usage-date.md).

## <a name="give-read-only-access-to-billing"></a>Conceder acesso só de leitura para a faturação

Atribua a função de leitor de faturação para alguém que precisa de acesso só de leitura para as informações de faturação de subscrição, mas não a capacidade para gerir ou criar serviços do Azure. Esta função é adequada para os utilizadores numa organização que são responsáveis pela gestão financeira e de custo das subscrições do Azure.

Se for um cliente EA, um proprietário da conta ou administrador do departamento pode atribuir a função de leitor de faturação para os membros da equipe. Mas para esse leitor de faturação de mensagens em fila ver informações de faturação para o departamento ou uma conta, tem de ativar o administrador Corporativo **cobranças de exibição de pedidos** ou **cobranças de vista DA** políticas no portal da empresa.

A funcionalidade de leitor de faturação está em pré-visualização e ainda não suporta nuvens não global.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Selecione a sua subscrição no [painel de Subscrições](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) no portal do Azure.

1. Selecione **controlo de acesso (IAM)**.
1. Selecione **atribuições de funções** para ver todas as atribuições de função para esta subscrição.
1. Selecione **adicionar atribuição de função**.
1. Na **função** pendente lista, escolha **leitor de faturação**.
1. Na **selecione** caixa de texto, escreva o nome ou e-mail para o utilizador que pretende adicionar.
1. Selecione o utilizador.
1. Selecione **Guardar**.
1. Após alguns instantes, o utilizador tem atribuída a função de leitor de faturação no âmbito da subscrição.
1. O leitor de faturação recebe um e-mail com uma ligação para iniciar sessão.

    ![Captura de ecrã que mostra o que pode ver o leitor de faturação no portal do Azure](./media/billing-manage-access/billing-reader-view.png)

## <a name="allow-department-administrator-or-account-owner-billing-access"></a>Permitir o acesso de faturação de administrador do departamento ou proprietário da conta

O administrador de empresa pode permitir que os administradores de departamento e os proprietários da conta ver os detalhes de utilização e os custos associados aos departamentos e contas que eles gerenciam.

1. Como o administrador de empresa, inicie sessão para o [portal EA](https://ea.azure.com/).
1. Selecione **gerir**.
1. Sob **inscrição**, altere a **cobranças de vista DA** para **ativado** para o administrador do departamento para ver a utilização e os custos.
1. Alteração **cobranças de exibição de pedidos** ao **ativado** para o proprietário da conta para ver a utilização e os custos.


Para obter mais informações, consulte [funções administrativas do Azure Enterprise Agreement compreender no Azure](billing-understand-ea-roles.md).

## <a name="only-account-admins-can-access-account-center"></a>Apenas os administradores de conta pode acessar o Centro de contas

O administrador de conta é o proprietário legal da subscrição. Por predefinição, a pessoa que inscreveu ou comprou a subscrição do Azure é o administrador de conta, a menos que o [a propriedade de subscrição foi transferida](billing-subscription-transfer.md) para outra pessoa. O administrador de conta pode criar subscrições, cancelar subscrições, alterar o endereço de cobrança de uma subscrição e gerir políticas de acesso para a subscrição a partir da [Centro de contas](https://account.azure.com/Subscriptions).

## <a name="next-steps"></a>Passos Seguintes

- Utilizadores nas outras funções, como proprietário ou contribuinte, podem aceder a informações de faturação não apenas, mas também para serviços do Azure. Para gerir estas funções, consulte [gerir o acesso com RBAC e o portal do Azure](../role-based-access-control/role-assignments-portal.md).
- Para obter mais informações sobre as funções, consulte [funções incorporadas para recursos do Azure](../role-based-access-control/built-in-roles.md).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).
