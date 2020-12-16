---
title: Tarefas de Administrador de Conta no portal do Azure
description: Descreve como executar operações de pagamento no portal do Azure
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 10/26/2020
ms.author: banders
ms.custom: contperf-fy21q2
ms.openlocfilehash: bd46e7b2f0713da67842def47dfeadc837027d8f
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97027973"
---
# <a name="account-administrator-tasks-in-the-azure-portal"></a>Tarefas de Administrador de Conta no portal do Azure

Este artigo explica como executar as seguintes tarefas no portal do Azure:
- Gerir os métodos de pagamento da sua subscrição
- Remover o limite de gastos da sua subscrição
- Adicionar créditos à subscrição do Azure no Licenciamento Open

Tem de ser o Administrador de Conta para realizar qualquer uma destas tarefas.

## <a name="navigate-to-your-subscriptions-payment-methods"></a>Navegar até aos métodos de pagamento da sua subscrição

1. Inicie sessão no portal do Azure como Administrador de Conta.

1. Procure **Cost Management + Faturação**.

    ![Captura de ecrã que mostra a pesquisa para Cost Management + Faturação ](./media/account-admin-tasks/search-bar.png)

1. Na lista **As minhas subscrições**, selecione a subscrição à qual quer adicionar o cartão de crédito.

   ![Captura de ecrã que mostra a página Cost Management + Faturação em que pode selecionar uma subscrição.](./media/account-admin-tasks/cost-management-billing-overview-x.png)

   > [!NOTE]
   > Se não vir algumas das suas subscrições aqui, tal poderá dever-se ao facto de ter alterado o diretório das subscrições em determinado momento. No caso destas subscrições, tem de alterar o diretório para o diretório original (o diretório no qual inicialmente se inscreveu). Em seguida, repita o passo 2.

1. Selecione **Métodos de pagamento**.

    ![Captura de ecrã que mostra a página Métodos de pagamento em que pode adicionar um método de pagamento.](./media/account-admin-tasks/subscription-payment-methods-blade.png)

Aqui, pode adicionar um novo cartão de crédito, alterar o método de pagamento ativo, editar detalhes do cartão de crédito e eliminar cartões de crédito.

### <a name="change-active-payment-method"></a>Alterar o método de pagamento ativo

Pode alterar o método de pagamento ativo adicionando um novo cartão de crédito ou escolhendo um que já esteja guardado. Para alterar o método de pagamento ativo para um novo cartão de crédito:

1. No canto superior esquerdo, selecione “+” para adicionar um cartão de crédito.

    ![Captura de ecrã que mostra o sinal de adição](./media/account-admin-tasks/subscription-payment-methods-plus.png)

1. Introduza os detalhes do cartão de crédito no formulário à direita.

    ![Captura de ecrã que mostra o formulário de adição de cartão de crédito.](./media/account-admin-tasks/subscription-add-payment-method-x.png)

1. Para definir este cartão como o método de pagamento ativo, selecione a caixa junto a **Definir o meu método de pagamento ativo** acima do formulário. Este cartão tornar-se-á o instrumento de pagamento ativo para todas as subscrições com o mesmo cartão da subscrição selecionada.

    ![Captura de ecrã que mostra a caixa de verificação para tornar o cartão no método de pagamento ativo.](./media/account-admin-tasks/subscription-make-active-payment-method-x.png)

1. Selecione **Seguinte**.

Para alterar o método de pagamento ativo para um cartão de crédito que já esteja guardado:

1. Selecione a caixa junto ao cartão que quer definir como o método de pagamento ativo.

    ![Captura de ecrã que mostra a caixa assinalada ao lado do cartão de crédito](./media/account-admin-tasks/subscription-checked-payment-method-x.png)

1. Clique em **Definir como ativo** na barra de comandos.

    ![Captura de ecrã que mostra o botão Definir como ativo](./media/account-admin-tasks/subscription-checked-payment-method-set-active.png)

### <a name="edit-credit-card-details"></a>Editar detalhes do cartão de crédito

Para editar os detalhes do cartão de crédito, como a data de validade ou o endereço, clique no cartão de crédito que pretende editar. Será apresentado à direita um formulário de cartão de crédito.

![Captura de ecrã que mostra o cartão de crédito selecionado](./media/account-admin-tasks/subscription-edit-payment-method-x.png)

Atualize os detalhes do cartão de crédito e clique em **Guardar**.

### <a name="remove-a-credit-card-from-the-account"></a>Remover o cartão de crédito da conta

1. Selecione a caixa junto ao cartão que pretende eliminar.

    ![Captura de ecrã que mostra a caixa assinalada ao lado do cartão de crédito](./media/account-admin-tasks/subscription-checked-payment-method-x.png)

1. Clique em **Eliminar** na barra de comandos.

    ![Captura de ecrã que mostra o botão Eliminar](./media/account-admin-tasks/subscription-checked-payment-method-delete.png)

Se o cartão de crédito for o método de pagamento ativo para qualquer uma das suas subscrições da Microsoft, não o poderá remover da conta do Azure. Altere o método de pagamento ativo para todas as subscrições associadas a este cartão de crédito e tente novamente.

### <a name="switch-to-invoice-payment"></a>Mudar para pagamento por fatura

Se for elegível para pagar por fatura (cheque/transferência bancária), pode mudar a sua subscrição para pagamento por fatura (cheque/transferência bancária) no portal do Azure.

1. Selecione **Pagar por fatura** na barra de comandos.

    ![Captura de ecrã que mostra a página Métodos de pagamento com Pagar por fatura selecionado.](./media/account-admin-tasks/subscription-payment-methods-pay-by-invoice.png)

1. Introduza o endereço para o método de pagamento por fatura.
1. Clique em **Seguinte**.

Se quiser ser aprovado para pagar por fatura, veja [como pagar por fatura](pay-by-invoice.md).

### <a name="edit-invoice-payment-address"></a>Editar endereço de pagamento por fatura

Para editar o endereço do seu método de pagamento por fatura, clique em **Fatura** na lista de métodos de pagamento para a sua subscrição. O formulário de endereço será aberto à direita.

## <a name="remove-spending-limit"></a>Remover limite de gastos

O limite de gastos no Azure impede que gaste mais do que o montante do seu crédito. Pode remover o limite de gastos a qualquer momento, desde que exista um método de pagamento válido associado à subscrição do Azure. Para os tipos de subscrição que utilizam crédito ao longo de vários meses, como o Visual Studio Enterprise e o Visual Studio Professional, pode optar por ativar o limite de gastos no início do próximo período de faturação.

O limite de gastos não está disponível para as subscrições com planos de alocação ou planos com preços pay as you go.

1. Inicie sessão no portal do Azure como Administrador de Conta.
1. Procure **Cost Management + Faturação**.

    ![Captura de ecrã que mostra a pesquisa para Cost Management + Faturação ](./media/account-admin-tasks/search-bar.png)

1. Na lista **As minhas subscrições**, selecione a sua subscrição do Visual Studio Enterprise.

   ![Captura de ecrã que mostra a área As minhas subscrições em que pode selecionar a sua subscrição do Visual Studio Enterprise.](./media/account-admin-tasks/cost-management-overview-msdn-x.png)

    > [!NOTE]
    > Se não vir algumas das suas subscrições do Visual Studio aqui, tal poderá dever-se ao facto de ter alterado o diretório de uma subscrição em determinado momento. No caso destas subscrições, tem de alterar o diretório para o diretório original (o diretório no qual inicialmente se inscreveu). Em seguida, repita o passo 2.

1. Na descrição geral de Subscrição, clique na faixa laranja para remover o limite de gastos.

    ![Captura de ecrã que mostra a faixa para remover o limite de gastos](./media/account-admin-tasks/msdn-remove-spending-limit-banner-x.png)

1. Escolha se pretende remover o limite de gastos indefinidamente ou apenas para o período de faturação atual.

   ![Captura de ecrã que mostra o painel para remover o limite de gastos](./media/account-admin-tasks/remove-spending-limit-blade-x.png)

1. Clique em **Selecionar método de pagamento** para escolher um método de pagamento para a sua subscrição. Este passará a ser o método de pagamento ativo para a sua subscrição.

1. Clique em **Concluir**.

## <a name="add-credits-to-azure-in-open-subscription"></a>Adicionar créditos à subscrição Azure no Licenciamento Open

Se tiver uma subscrição Azure no Licenciamento Open, pode adicionar créditos à sua subscrição no portal do Azure, resgatando uma chave de produto ou comprando créditos com um cartão de crédito.

1. Inicie sessão no portal do Azure como Administrador de Conta.
1. Procure **Cost Management + Faturação**.

    ![Captura de ecrã que mostra a pesquisa para Cost Management + Faturação ](./media/account-admin-tasks/search-bar.png)

1. Na lista **As minhas subscrições**, selecione a sua subscrição Azure no Licenciamento Open.

    ![Captura de ecrã que mostra a área As minhas subscrições em que pode selecionar a sua subscrição do Azure no Open.](./media/account-admin-tasks/cost-management-overview-aio-x.png)

   > [!NOTE]
   > Se não vir a sua subscrição aqui, tal poderá dever-se ao facto de ter alterado o diretório em determinado momento. Tem de alterar o diretório da subscrição para o diretório original (o diretório no qual inicialmente se inscreveu). Em seguida, repita o passo 2.

1. Selecione **Histórico de crédito**.

    ![Captura de ecrã que mostra o histórico de crédito](./media/account-admin-tasks/aio-credit-history-blade.png)

1. No canto superior esquerdo, selecione "+" para adicionar mais créditos.

    ![Captura de ecrã que mostra o botão Adicionar créditos](./media/account-admin-tasks/aio-credit-history-plus.png)

1. Selecione um tipo de método de pagamento na lista pendente. Pode adicionar uma chave de produto ou comprar créditos com um cartão de crédito.

    ![Captura de ecrã que mostra a lista pendente de método de pagamento no painel Adicionar créditos](./media/account-admin-tasks/add-credits-select-payment-method.png)

1. Se escolheu chave de produto:
    - Introduza a chave de produto
    - Clique em **Validar**

1. Se escolheu cartão de crédito:
    - Clique em **Selecionar método de pagamento** para adicionar um cartão de crédito ou selecionar um existente.
    - Especifique a quantidade de créditos que pretende adicionar.

1. Clique em **Aplicar**

## <a name="troubleshooting"></a>Resolução de problemas
Não suportamos cartões virtuais ou pré-pagos. Se ocorrerem erros ao adicionar ou atualizar um cartão de crédito válido, tente abrir o browser em modo privado.

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre como [analisar custos inesperados](../understand/analyze-unexpected-charges.md)
