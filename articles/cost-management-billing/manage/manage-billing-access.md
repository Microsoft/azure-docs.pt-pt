---
title: Gerir o acesso à faturação do Azure
description: Saiba como conceder acesso às informações de faturação do Azure aos membros da sua equipa.
author: vikramdesai01
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 01/26/2021
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 23e3adad12a726b03f6efe309dde0ec4a93bcd5b
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897430"
---
# <a name="manage-access-to-billing-information-for-azure"></a>Gerir o acesso às informações de faturação do Azure

Pode fornecer a outros utilizadores acesso às informações de faturação da sua conta no portal do Azure. O tipo de funções de faturação e as instruções para fornecer acesso às informações de faturação variam de acordo com o tipo da conta de faturação. Para determinar o tipo da conta de faturação, veja [Verificar o tipo da conta de faturação](#check-the-type-of-your-billing-account).

O artigo aplica-se a clientes com contas do programa do Serviço Online da Microsoft. Se for um cliente do Azure com um Contrato Enterprise (EA) e for o Administrador da Empresa, poderá conceder permissões aos Administradores do Departamento e aos Proprietários da Conta no Enterprise Portal. Para obter mais informações, veja [Compreender as funções administrativas do Contrato Enterprise no Azure](understand-ea-roles.md). Se for um cliente do Contrato de Cliente da Microsoft, veja [Compreender as funções administrativas do Contrato de Cliente da Microsoft no Azure](understand-mca-roles.md).

## <a name="account-administrators-for-microsoft-online-service-program-accounts"></a>Administradores das conta do programa do Serviço Online da Microsoft

Um Administrador de Conta é o único proprietário da conta de faturação do Programa do Serviço Online da Microsoft. A função é atribuída a uma pessoa inscrita no Azure. Os Administradores de Conta estão autorizados a executar várias tarefas de faturação, como criar subscrições, ver faturas ou alterar a faturação de uma subscrição.

## <a name="give-others-access-to-view-billing-information"></a>Fornecer acesso a outros utilizadores para verem as informações de faturação

O administrador da conta pode conceder acesso às informações de faturação do Azure a outros utilizadores ao atribuir uma das seguintes funções numa subscrição da conta.

- Administrador de Serviços
- Coadministrador
- Proprietário
- Contribuinte
- Leitor
- Leitor de faturação

Estas funções têm acesso a informações de faturação no [portal do Azure](https://portal.azure.com/). As pessoas a quem são atribuídas estas funções também podem utilizar as [APIs de Faturação](consumption-api-overview.md#usage-details-api) para obter programaticamente as faturas e os detalhes de utilização.

Para atribuir funções, veja [Utilizar o portal do Azure para adicionar ou remover atribuições de funções do Azure](../../role-based-access-control/role-assignments-portal.md).

** Se for um cliente EA, um Proprietário da Conta poderá atribuir a função acima a outros utilizadores da sua equipa. Mas para que estes utilizadores vejam as informações de faturação, o Administrador da Empresa deve permitir que o Proprietário da Conta visualize os custos no Enterprise Portal.


### <a name="allow-users-to-download-invoices"></a><a name="opt-in"></a> Permitir que os utilizadores transfiram faturas

Depois de um administrador de conta ter atribuído as funções apropriadas a outros utilizadores, deverá ativar o acesso para transferir faturas no portal do Azure. As faturas anteriores a dezembro de 2016 só estão disponíveis para o Administrador de Conta.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como um Administrador de Conta.

1. Faça uma pesquisa em **Gestão de Custos + Faturação**.

    ![Captura de ecrã que realça Cost Management + Faturação na secção Serviços.](./media/manage-billing-access/billing-search-cost-management-billing.png)

1. Selecione **Subscrições** no painel esquerdo. Consoante o seu acesso, pode ter de selecionar um âmbito da faturação e, em seguida, selecionar **Subscrições**.

    ![Captura de ecrã que mostra a seleção de subscrições](./media/manage-billing-access/billing-select-subscriptions.png)

1. Selecione **Faturas** e, em seguida, **Acesso à fatura**.

    ![Captura de ecrã que mostra como delegar o acesso às faturas](./media/manage-billing-access/aa-optin01.png)

1. Selecione **Ativar** e guarde.

    ![Captura de ecrã que mostra o botão ativar/desativar para delegar acesso às faturas](./media/manage-billing-access/aa-optinallow01.png)

O Administrador de Conta também pode configurar o envio das faturas por e-mail. Para obter mais informações, veja [Get your invoice in email](download-azure-invoice-daily-usage-date.md) (Receber a sua fatura no e-mail).

## <a name="give-read-only-access-to-billing"></a>Conceder acesso só de leitura à faturação

Atribua a função de Leitor de Faturação a alguém que necessite de acesso só de leitura às informações de faturação da subscrição, mas não precise de gerir ou criar serviços do Azure. Esta função é apropriada para os utilizadores numa organização que sejam responsáveis pela gestão financeira e dos custos das subscrições do Azure.

A funcionalidade Leitor de Faturação está em pré-visualização e ainda não suporta as clouds não globais.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como um Administrador de Conta.

1. Faça uma pesquisa em **Gestão de Custos + Faturação**.

    ![Captura de ecrã a mostrar a pesquisa no portal do Azure](./media/manage-billing-access/billing-search-cost-management-billing.png)

1. Selecione **Subscrições** no painel esquerdo. Consoante o seu acesso, pode ter de selecionar um âmbito da faturação e, em seguida, selecionar **Subscrições**.

    ![Captura de ecrã que mostra a seleção de subscrições](./media/manage-billing-access/billing-select-subscriptions.png)

1. Selecione **Controlo de acesso (IAM)** .
1. Selecione **Adicionar** na parte superior da página.

    ![Captura de ecrã que mostra o clique em Adicionar atribuição de função](./media/manage-billing-access/billing-click-add-role-assignment.png)

1. Na lista pendente **Função**, escolha **Leitor de Faturação**.
1. Na caixa de texto **Selecionar**, digite o nome ou o e-mail do utilizador que quer adicionar.
1. Selecione o utilizador.
1. Selecione **Guardar**.
    ![Captura de ecrã que realça o botão Guardar.](./media/manage-billing-access/billing-save-role-assignment.png)

1. Após alguns instantes, a função de Leitor de Faturação é atribuída ao utilizador para essa subscrição.

** Se for um cliente EA, um Proprietário de Conta ou Administrador de Departamento poderá atribuir a função de Leitor de Faturação aos membros da equipa. Mas, para que o Leitor de Faturação visualize as informações de faturação do departamento ou da conta, o Administrador da Empresa deve ativar políticas **Os Proprietários de Contas podem ver os custos** ou **Os Administradores de Departamento podem ver os custos** no Enterprise Portal.

## <a name="check-the-type-of-your-billing-account"></a>Verificar o tipo da conta de faturação
[!INCLUDE [billing-check-account-type](../../../includes/billing-check-account-type.md)]

## <a name="next-steps"></a>Passos seguintes

- Os utilizadores noutras funções, como o Proprietário ou Contribuidor, podem aceder às informações de faturação e aos serviços do Azure. Para gerir estas funções, veja [Utilizar o portal do Azure para adicionar ou remover atribuições de funções do Azure](../../role-based-access-control/role-assignments-portal.md).
- Para obter mais informações sobre as funções, veja [funções incorporadas do Azure](../../role-based-access-control/built-in-roles.md).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).
