---
title: Alterar o cartão de crédito do Azure
description: Descreve como alterar o cartão de crédito utilizado para pagar a subscrição do Azure.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 05/22/2020
ms.author: banders
ms.openlocfilehash: 387e10e1e9e22e425e5849f4341052ab0f8ae1fb
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88509829"
---
# <a name="add-update-or-remove-a-credit-card-for-azure"></a>Adicionar, atualizar ou remover um cartão de crédito do Azure

Este documento aplica-se aos clientes que se inscreveram no Azure online com um cartão de crédito.

No portal do Azure, pode alterar o método de pagamento predefinido para um novo cartão de crédito, atualizar os dados do seu cartão de crédito e eliminar os cartões de crédito que não utiliza. Tem de ser um [Administrador de Conta](billing-subscription-transfer.md#whoisaa) para fazer estas alterações.

Os métodos de pagamento suportados para o Microsoft Azure são cartões de crédito e cheque/transferência bancária. Para ter aprovação para pagar por cheque/transferência bancária, veja [Pagamento das suas subscrições do Azure por fatura](pay-by-invoice.md).

Se tiver um Contrato de Cliente Microsoft, os métodos de pagamento estão associados aos perfis de faturação. Saiba como [verificar o acesso a um Contrato de Cliente Microsoft](#check-the-type-of-your-account). Se tiver um MCA, avance para [Gerir cartões de crédito para um Contrato de Cliente Microsoft](#manage-credit-cards-for-a-microsoft-customer-agreement).

<a id="addcard"></a>

## <a name="manage-credit-cards-for-an-azure-subscription"></a>Gerir cartões de crédito para uma subscrição do Azure

As secções seguintes aplicam-se aos clientes que tenham uma conta de faturação do Programa Microsoft Online Services. Saiba como [verificar o tipo de conta de faturação](#check-the-type-of-your-account). Se o seu tipo de conta de faturação for o Programa Microsoft Online Services, os métodos de pagamento estão associados a subscrições individuais do Azure. Se obtiver um erro depois de adicionar o cartão de crédito, veja [Cartão de crédito recusado na inscrição do Azure](../../billing/billing-credit-card-fails-during-azure-sign-up.md).

### <a name="change-credit-card-for-a-subscription-by-adding-a-new-credit-card"></a>Alterar o cartão de crédito de uma subscrição adicionando um novo cartão de crédito

Pode alterar o crédito predefinido da sua subscrição do Azure para um novo cartão de crédito ou um cartão de crédito previamente guardado no portal do Azure. Tem de ser o Administrador de Conta para alterar o cartão de crédito. Se mais de uma das subscrições tiver o mesmo método de pagamento ativo, a alteração do método de pagamento ativo em qualquer uma dessas subscrições também atualizará o método de pagamento ativo nas outras subscrições.


Para alterar o cartão de crédito predefinido da sua subscrição para um novo, siga estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como Administrador de Conta.
1. Procure **Cost Management + Faturação**.

    ![Captura de ecrã a mostrar a pesquisa](./media/change-credit-card/search.png)

1. Selecione a subscrição à qual quer adicionar o cartão de crédito.
1. Selecione **Métodos de pagamento**.

    ![Captura de ecrã que mostra a opção Gerir métodos de pagamento selecionada.](./media/change-credit-card/payment-methods-blade-x.png)

1. No canto superior esquerdo, selecione "+" para adicionar um cartão. Será apresentado à direita um formulário de cartão de crédito.
1. Introduza os detalhes do cartão de crédito.

    ![Captura de ecrã que mostra a adição de um novo cartão.](./media/change-credit-card/sub-add-new-x.png)

1. Para definir este cartão como o método de pagamento ativo, selecione a caixa junto a **Definir o meu método de pagamento ativo** acima do formulário. Este cartão tornar-se-á o instrumento de pagamento ativo para todas as subscrições com o mesmo cartão da subscrição selecionada.

1. Selecione **Seguinte**.

### <a name="change-credit-card-for-a-subscription-to-a-previously-saved-credit-card"></a>Alterar o cartão de crédito de uma subscrição para um cartão de crédito guardado anteriormente

Também pode alterar o cartão de crédito predefinido da sua subscrição para um que já esteja guardado na sua conta, seguindo estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como Administrador de Conta.
1. Procure **Cost Management + Faturação**.

    ![Captura de ecrã a mostrar a pesquisa](./media/change-credit-card/search.png)

1. Selecione a subscrição à qual quer adicionar o cartão de crédito.
1. Selecione **Métodos de pagamento**.

    ![Captura de ecrã que mostra a opção Gerir métodos de pagamento selecionada.](./media/change-credit-card/payment-methods-blade-x.png)

1. Selecione a caixa junto ao cartão que quer definir como o método de pagamento ativo.
1. Clique em **Definir como ativo**.
    ![Captura de ecrã que mostra o cartão de crédito selecionado e definido como ativo](./media/change-credit-card/sub-change-active-x.png).

### <a name="edit-credit-card-details"></a>Editar detalhes do cartão de crédito

Se o cartão de crédito for renovado e o número continuar o mesmo, atualize os detalhes do cartão de crédito existente, como a data de validade. Se o número do cartão de crédito for alterado porque o cartão foi perdido, roubado ou expirou, siga os passos em [Adicionar um cartão de crédito como um método de pagamento](#addcard). Não precisa de atualizar o CVV.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como Administrador de Conta.
1. Procure **Cost Management + Faturação**.

    ![Captura de ecrã que mostra a pesquisa](./media/change-credit-card/search.png)

1. Selecione **Métodos de pagamento**.

    ![Captura de ecrã que mostra a opção Gerir métodos de pagamento selecionada.](./media/change-credit-card/payment-methods-blade-x.png)

1. Clique no cartão de crédito que quer editar. Será apresentado à direita um formulário de cartão de crédito.

    ![Captura de ecrã que mostra o cartão de crédito selecionado.](./media/change-credit-card/edit-card-x.png)

1. Atualize os detalhes do cartão de crédito.
1. Selecione **Guardar**.

### <a name="delete-a-credit-card-from-the-account"></a>Eliminar um cartão de crédito da conta

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como Administrador de Conta.
1. Selecione **Cost Management + Faturação** no lado esquerdo da página.

    ![Captura de ecrã a mostrar a pesquisa](./media/change-credit-card/search.png)

1. Em **Faturação**, selecione **Métodos de pagamento**.

    ![Captura de ecrã que mostra a opção Gerir métodos de pagamento selecionada.](./media/change-credit-card/payment-methods-blade-x.png)

1. Selecione a caixa junto ao cartão que quer remover.
1. Clique em **Eliminar**.

Se o cartão de crédito for o método de pagamento ativo para qualquer uma das suas subscrições da Microsoft, não o poderá remover da conta do Azure. Altere o método de pagamento ativo para todas as subscrições associadas a este cartão de crédito e tente novamente

## <a name="manage-credit-cards-for-a-microsoft-customer-agreement"></a>Gerir cartões de crédito para um Contrato de Cliente Microsoft

As secções seguintes aplicam-se aos clientes que tenham um Contrato de Cliente Microsoft e que se inscreveram no Azure online com um cartão de crédito. [Saiba como verificar se tem um Contrato de Cliente Microsoft](#check-the-type-of-your-account).

### <a name="change-default-credit-card"></a>Alterar o cartão de crédito predefinido

Se tiver um Contrato de Cliente Microsoft, o seu cartão de crédito está associado a um perfil de faturação. Para alterar o método de pagamento de um perfil de faturação, tem de ser a pessoa que se inscreveu no Azure e criou a conta de faturação.

Se quiser alterar o método de pagamento predefinido do seu perfil de faturação para cheque/transferência bancária, veja [Pagamento das suas subscrições do Azure por fatura](pay-by-invoice.md).

Para alterar o seu cartão de crédito, siga estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Faça uma pesquisa em **Gestão de Custos + Faturação**.
1. No menu esquerdo, clique em **Perfis de faturação**.
1. Selecione um perfil de faturação.
1. No menu esquerdo, selecione **Métodos de pagamento**.

   ![Captura de ecrã que mostra os métodos de pagamento no menu](./media/change-credit-card/payment-methods-tab-mca.png)

1. Na secção **Método de pagamento predefinido**, clique em **Alterar**.

    ![Captura de ecrã que mostra o botão Alterar](./media/change-credit-card/change-payment-method-mca.png)

1. No novo painel à direita, selecione um cartão existente na lista pendente ou adicione um novo, clicando na ligação "Adicionar novo método de pagamento" azul.

### <a name="edit-or-delete-a-credit-card"></a>Editar ou eliminar um cartão de crédito

Pode editar os dados do cartão de crédito (como atualizar a data de validade) e eliminar cartões de crédito da sua conta no portal do Azure. Só é possível eliminar um cartão de crédito se não estiver associado a uma subscrição do Azure ou perfil de faturação. Se estiver associado a uma subscrição do Azure desativada, tem de aguardar até que a subscrição seja eliminada (30-90 dias após o cancelamento) para eliminar o cartão de crédito.

Para editar ou eliminar um cartão de crédito, siga estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Faça uma pesquisa em **Gestão de Custos + Faturação**.
1. No menu esquerdo, clique em **Perfis de faturação**.
1. Selecione um perfil de faturação.
1. No menu esquerdo, selecione **Métodos de pagamento**.

   ![Captura de ecrã que mostra os métodos de pagamento no menu](./media/change-credit-card/payment-methods-tab-mca.png)

1. Na secção **Os seus cartões de crédito**, localize o cartão de crédito que pretende editar ou eliminar.
1. Selecione as reticências (`...`) no fim da linha.

    ![Captura de ecrã que mostra as reticências](./media/change-credit-card/edit-delete-credit-card-mca.png)

1. Para editar os dados do seu cartão de crédito, selecione **Editar** no menu de contexto.
1. Para eliminar o seu cartão de crédito, selecione **Eliminar** no menu de contexto.

## <a name="troubleshooting"></a>Resolução de problemas

Não suportamos cartões virtuais ou pré-pagos. Se ocorrerem erros ao adicionar ou atualizar um cartão de crédito válido, tente abrir o browser em modo privado.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

As secções a seguir respondem a perguntas frequentes sobre como alterar as informações do cartão de crédito.

### <a name="my-subscription-is-disabled-why-cant-i-remove-my-credit-card-now"></a>A minha subscrição está desativada. Por que não consigo remover o meu cartão de crédito agora?

Depois de a subscrição ter sido desativada ou cancelada, aguardaremos 90 dias antes de eliminar permanentemente a subscrição. O método de pagamento é mantido em registo durante o período de retenção no caso de pretender reativar a subscrição. Após esse período, a subscrição é eliminada permanentemente.

Se quiser remover o cartão de crédito antes do período de retenção de 90 dias, [reative a subscrição](subscription-disabled.md). Se não conseguir reativar, [contacte o suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>Por que continuo a obter “O início de sessão expirou. Clique aqui para iniciar sessão novamente”?

Se continuar a obter esta mensagem de erro mesmo que tenha terminado e iniciado sessão novamente, tente novamente com uma sessão de navegação privada.

### <a name="how-do-i-use-a-different-card-for-each-subscription-i-have"></a>Como posso utilizar um cartão diferente para cada das minhas subscrições?

Infelizmente, se as suas subscrições já estiverem a utilizar o mesmo cartão, não será possível separá-las para utilizarem cartões diferentes. No entanto, quando se inscrever numa nova subscrição, poderá optar por utilizar um novo método de pagamento para essa subscrição.

### <a name="how-do-i-make-payments"></a>Como fazer os pagamentos?

Se configurar um cartão de crédito como método de pagamento, o cartão será debitado automaticamente após cada período de faturação. Não é necessário fazer nada.

Se [pagar por fatura](pay-by-invoice.md), envie o pagamento para a localização indicada na parte inferior da fatura.

### <a name="how-do-i-change-the-tax-id"></a>Como posso alterar o NIF?

Para adicionar ou atualizar o NIF, atualize o seu perfil no [Centro de Contas do Azure](https://account.azure.com/Profile) e, em seguida, selecione **Registo fiscal**. O NIF é utilizado para cálculos de isenção fiscal e é apresentado na sua fatura.

## <a name="check-the-type-of-your-account"></a>Verificar o tipo de conta

[!INCLUDE [billing-check-mca](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre as [Reservas do Azure](../reservations/save-compute-costs-reservations.md) para determinar se são mais económicas.
