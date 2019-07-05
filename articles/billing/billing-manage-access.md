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
ms.openlocfilehash: 6726c876d0895f9488aa2ae5c225a6b2ac19e69f
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491117"
---
# <a name="manage-access-to-billing-information-for-azure"></a>Gerir o acesso a informações de faturação para o Azure

Pode fornecer outras pessoas acesso às informações de faturas para a sua conta no portal do Azure. O tipo de funções de faturas e as instruções para fornecer acesso às informações de faturas variam consoante o tipo da sua conta de cobrança. Para determinar o tipo de sua conta de cobrança, veja [verificar o tipo de sua conta de cobrança](#check-the-type-of-your-billing-account).

O artigo aplica-se aos clientes com contas do programa de serviço Online da Microsoft. Se for um cliente do Azure com um contrato Enterprise (EA) e o administrador de empresa, pode conceder permissões para os administradores de departamento e os proprietários da conta no portal da empresa. Para obter mais informações, consulte [funções administrativas do Azure Enterprise Agreement compreender no Azure](billing-understand-ea-roles.md). Se for um cliente de contrato de cliente da Microsoft, consulte, [funções administrativas de compreender contrato de cliente da Microsoft no Azure](billing-understand-mca-roles.md). 

## <a name="account-administrators-for-microsoft-online-service-program-accounts"></a>Administradores de conta para contas de programa de serviço Online da Microsoft

Um administrador de conta é o único proprietário para uma conta de cobrança do programa de serviço Online da Microsoft. A função é atribuída a uma pessoa que se inscreveu para o Azure. Os administradores de conta estão autorizados a efetuar várias tarefas de faturas como criar subscrições, ver notas fiscais ou alterar a faturação de uma subscrição.

## <a name="give-others-access-to-view-billing-information"></a>Atribuir a outras pessoas acesso para ver informações de faturação

Administrador de conta pode conceder outras pessoas acesso a informações de faturação do Azure através da atribuição de uma das seguintes funções numa assinatura na respetiva conta.

- Administrador de Serviços
- Coadministrador
- Proprietário
- Contribuinte
- Leitor
- Leitor de faturação

Estas funções têm acesso a informações de faturação no [portal do Azure](https://portal.azure.com/). As pessoas que estão atribuídas estas funções também podem utilizar o [APIs de faturação](billing-usage-rate-card-overview.md) para obter programaticamente notas fiscais e detalhes de utilização.

Para atribuir funções, consulte [gerir o acesso com RBAC e o portal do Azure](../role-based-access-control/role-assignments-portal.md).

\* * Se estiver num cliente EA, o proprietário da conta pode atribuir a função acima a outros utilizadores da sua equipe. Mas esses usuários ver informações de faturação, o administrador de empresa tem de ativar AO modo de exibição de custos no Enterprise portal.


### <a name="opt-in"></a> Permitir que os utilizadores a transferência de faturas

Depois de um administrador de conta tiver atribuído as funções adequadas a outros utilizadores, eles tem de ativar o acesso a transferência de faturas no portal do Azure. Notas fiscais com mais de Dezembro de 2016 estão disponíveis apenas para o administrador de conta.

1. Inicie sessão para o [portal do Azure](https://portal.azure.com/), como um administrador de conta,

1. Pesquisar nos **custo Management + faturação**.

    ![Captura de ecrã que mostra a pesquisa de portal do Azure](./media/billing-manage-access/billing-search-cost-management-billing.png)
 
1. Selecione **subscrições** no painel esquerdo. Consoante o acesso, poderá ter de selecionar um âmbito de faturação e, em seguida, selecione **subscrições**.
 
    ![Captura de ecrã que mostra selecionar subscrições](./media/billing-manage-access/billing-select-subscriptions.png)

1. Selecione **notas fiscais** e, em seguida **acesso à fatura**.

    ![Captura de ecrã mostra como delegar o acesso às notas fiscais](./media/billing-manage-access/AA-optin.png)

1. Selecione **no** e guardar.

    ![Captura de ecrã mostra no-off para delegar o acesso à fatura](./media/billing-manage-access/AA-optinAllow.png)

O Administrador de Conta também pode configurar para que as faturas sejam enviadas por e-mail. Para obter mais informações, veja [Receber a sua fatura no e-mail](billing-download-azure-invoice-daily-usage-date.md).

## <a name="give-read-only-access-to-billing"></a>Conceder acesso só de leitura para a faturação

Atribua a função de leitor de faturação para alguém que precisa de acesso só de leitura para as informações de faturação de subscrição, mas não a capacidade para gerir ou criar serviços do Azure. Esta função é adequada para os utilizadores numa organização que são responsáveis pela gestão financeira e de custo das subscrições do Azure.

A funcionalidade de leitor de faturação está em pré-visualização e ainda não suporta nuvens não global.

1. Inicie sessão para o [portal do Azure](https://portal.azure.com/), como um administrador de conta,

1. Pesquisar nos **custo Management + faturação**.

    ![Captura de ecrã que mostra a pesquisa de portal do Azure](./media/billing-manage-access/billing-search-cost-management-billing.png)

1. Selecione **subscrições** no painel esquerdo. Consoante o acesso, poderá ter de selecionar um âmbito de faturação e, em seguida, selecione **subscrições**.
 
    ![Captura de ecrã que mostra selecionar subscrições](./media/billing-manage-access/billing-select-subscriptions.png)

1. Selecione **controlo de acesso (IAM)** .
1. Selecione **adicionar** da parte superior da página.

    ![Captura de ecrã que mostra a clicar em Adicionar atribuição de função](./media/billing-manage-access/billing-click-add-role-assignment.png)

1. Na **função** pendente lista, escolha **leitor de faturação**.
1. Na **selecione** caixa de texto, escreva o nome ou e-mail para o utilizador que pretende adicionar.
1. Selecione o utilizador.
1. Selecione **Guardar**.
    ![Captura de ecrã que mostra a clicar em Adicionar atribuição de função](./media/billing-manage-access/billing-save-role-assignment.png)

1. Após alguns instantes, o utilizador tem atribuída a função de leitor de faturação da subscrição.

\* * Se estiver num cliente EA, um proprietário da conta ou administrador do departamento pode atribuir a função de leitor de faturação para os membros da equipe. Mas para esse leitor de faturação de mensagens em fila ver informações de faturação para o departamento ou uma conta, tem de ativar o administrador Corporativo **cobranças de exibição de pedidos** ou **cobranças de vista DA** políticas no portal da empresa.

## <a name="check-the-type-of-your-billing-account"></a>Verifique o tipo de sua conta de cobrança
[!INCLUDE [billing-check-account-type](../../includes/billing-check-account-type.md)]

## <a name="next-steps"></a>Passos Seguintes

- Utilizadores nas outras funções, como proprietário ou contribuinte, podem aceder a informações de faturação não apenas, mas também para serviços do Azure. Para gerir estas funções, consulte [gerir o acesso com RBAC e o portal do Azure](../role-based-access-control/role-assignments-portal.md).
- Para obter mais informações sobre as funções, consulte [funções incorporadas para recursos do Azure](../role-based-access-control/built-in-roles.md).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).
