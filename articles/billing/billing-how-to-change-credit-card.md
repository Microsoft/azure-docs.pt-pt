---
title: Alterar o seu cartão de crédito para o Azure
description: Descreve como alterar o cartão de crédito utilizado para pagar uma subscrição do Azure.
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 7c04e33d6199d3930be28ce84458e9c3a743eb8a
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491285"
---
# <a name="add-update-or-remove-a-credit-card-for-azure"></a>Adicionar, atualizar ou remover um cartão de crédito para o Azure

No portal do Azure, pode adicionar um novo cartão de crédito, atualizar um cartão de crédito existente ou eliminar um cartão de crédito que não usa. Tem de ser um [conta de administrador](billing-subscription-transfer.md#whoisaa) para efetuar estas alterações.

Se tiver um [contrato de cliente da Microsoft](#check-access-to-a-microsoft-customer-agreement), métodos de pagamento associadas a perfis de faturação. Saiba como [alterar o método de pagamento predefinido para um perfil de faturação](#change-payment-method-for-a-billing-profile). Apenas o utilizador que inscreveu no Azure pode atualizar o método de pagamento.

**Deseja mudar para pagar por fatura (transferência de verificação/durante a transmissão)?** Ver [comprar subscrições do Azure por fatura](billing-how-to-pay-by-invoice.md).

<a id="addcard"></a>

## <a name="add-a-new-credit-card-to-an-azure-subscription"></a>Adicionar um novo cartão de crédito para uma subscrição do Azure

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) como o administrador de conta.
1. Procure **custo Management + faturação**.

    ![Captura de ecrã que mostra a pesquisa](./media/billing-how-to-change-credit-card/search.png)

1. Selecione a subscrição que pretende adicionar o cartão de crédito.
1. Selecione **Métodos de pagamento**.

    ![Captura de ecrã que mostra a opção de métodos de pagamento de gerir selecionada.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. No canto superior esquerdo, selecione "+" para adicionar um cartão. Será apresentado à direita um formulário de cartão de crédito.
1. Introduza os detalhes de cartão de crédito.

    ![Captura de ecrã que mostra a adição de um novo cartão.](./media/billing-how-to-change-credit-card/sub-add-new-x.png)

1. Para facilitar o seu método de pagamento ativo de cartão, marque a caixa junto a **tornam isso meu método de pagamento ativo** acima do formulário. Este cartão tornar-se-á o instrumento de pagamento ativo para todas as subscrições com o mesmo cartão da subscrição selecionada.

1. Selecione **Seguinte**.

Se obtiver um erro depois de adicionar o cartão de crédito, consulte [recusado de cartão de crédito no Azure, inscreva-se](billing-credit-card-fails-during-azure-sign-up.md).

## <a name="update-existing-credit-card"></a>Atualizar o cartão de crédito existente

Se for renovado seu cartão de crédito e o número permanece o mesmo, atualize os detalhes de cartão de crédito existente como a data de expiração. Se as alterações de número de cartão de crédito porque o cartão é perdido, roubado ou expirado, siga os passos a [adicionar um cartão de crédito como um método de pagamento](#addcard) secção. Não precisa de atualizar o CVV.

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) como o administrador de conta.
1. Procure **custo Management + faturação**.

    ![Captura de ecrã que mostra a pesquisa](./media/billing-how-to-change-credit-card/search.png)

1. Selecione **Métodos de pagamento**.

    ![Captura de ecrã que mostra a opção de métodos de pagamento de gerir selecionada.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Clique no cartão de crédito que quiser para editar. Será apresentado à direita um formulário de cartão de crédito.

    ![Captura de ecrã que mostra o cartão de crédito selecionado.](./media/billing-how-to-change-credit-card/edit-card-x.png)

1. Atualize os detalhes do cartão de crédito.
1. Selecione **Guardar**.

## <a name="use-a-different-credit-card"></a>Utilizar outro cartão de crédito

Se tiver mais do que uma das suas subscrições do mesmo método de pagamento ativo, em seguida, alterando o método de pagamento ativo em qualquer um destas subscrições também atualiza o método de pagamento ativo nos outros.

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) como o administrador de conta.
1. Procure **custo Management + faturação**.

    ![Captura de ecrã que mostra a pesquisa](./media/billing-how-to-change-credit-card/search.png)

1. Selecione a subscrição que pretende adicionar o cartão de crédito.
1. Selecione **Métodos de pagamento**.

    ![Captura de ecrã que mostra a opção de métodos de pagamento de gerir selecionada.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Selecione a caixa junto do cartão que gostaria de usar o método de pagamento ativo.
1. Clique em **definida como ativa**.
    ![Captura de ecrã que mostra o cartão de crédito selecionado e definida como ativa.](./media/billing-how-to-change-credit-card/sub-change-active-x.png)

## <a name="remove-a-credit-card-from-the-account"></a>Remover um cartão de crédito da conta

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) como o administrador de conta.
1. Selecione **gestão de custos + faturação** no lado esquerdo da página.

    ![Captura de ecrã que mostra a pesquisa](./media/billing-how-to-change-credit-card/search.png)

1. Sob **faturação**, selecione **métodos de pagamento**.

    ![Captura de ecrã que mostra a opção de métodos de pagamento de gerir selecionada.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Selecione a caixa junto do cartão que pretende remover.
1. Clique em **Eliminar**.

Se o seu cartão de crédito é o método de pagamento ativo para qualquer uma das suas subscrições do Microsoft, não é possível removê-lo a partir da sua conta do Azure. Alterar o método de pagamento ativo para todas as subscrições ligado a este cartão de crédito e tente novamente
<!-- # Add, update, or remove a credit card for Azure

In the Account Center, you can add a new credit card, update an existing credit card, or delete a credit card that you don't use. You must be an [Account Administrator](billing-subscription-transfer.md#whoisaa) to make these changes.

**Want to switch to pay by invoice?** See [Pay for Azure subscriptions by invoice](billing-how-to-pay-by-invoice.md).

<a id="addcard"></a>

## Add a new credit or debit card

1. Sign in to the [Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select a subscription.
1. On the right side of the page, select **Manage payment methods**.

    ![Screenshot that shows Manage payment methods option selected.](./media/billing-how-to-change-credit-card/changesub_new.png)
1. Select “+” to add a card.

    ![Screenshot that shows the edit option next to the payment method.](./media/billing-how-to-change-credit-card/editcard_new.png)
1. Enter credit or debit card details.
1. Select **Save**.

If you get an error after you add the credit card, see [Credit card declined at Azure sign-up](billing-credit-card-fails-during-azure-sign-up.md).

## Update existing credit or debit card

If your credit card gets renewed and the number remains the same, update the existing credit card details like the expiration date. If your credit card number changes because the card is lost, stolen, or expired, follow the steps in the [Add a credit card as a payment method](#addcard) section. You don't need to update the CVV.

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
1. Select **Manage payment methods**.
1. Select **Edit** next to the card you want to update.
1. Update the credit or debit card details.
1. Select **Save**.

## Use a different credit card for the Azure subscription

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
1. On the right side of the page, select **Manage payment methods**.
1. Click **Use Instead** next to the card that you want to use. This also updates any other subscriptions currently associated with this card.

## Remove a credit or debit card from the account

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
3. On the right side of the page, select **Manage payment methods**.
4. Click **Delete** for the credit card that you want to delete.

If your credit card is associated with other active Microsoft subscriptions, you can't remove it from your Azure account. Remove the credit card from all active subscriptions that you have with Microsoft and try again. -->

## <a name="change-payment-method-for-a-billing-profile"></a>Alterar método de pagamento para um perfil de faturação

Para alterar o método de pagamento para um perfil de faturação, tem de ser a pessoa que se inscreveu no Azure.

Se gostaria de mudar o método de pagamento predefinido para verificação/durante a transmissão transferência, saiba como [mudar de um perfil de faturação para verificação/durante a transmissão transferência](billing-how-to-pay-by-invoice.md).

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Pesquisar nos **custo Management + faturação**.
1. No menu à esquerda, clique em **perfis de faturação**.

    ![captura de ecrã que mostra o perfil de faturação no menu](./media/billing-how-to-change-credit-card/billing-profile.png)

1. Selecione um perfil de faturação.
1. No menu à esquerda, selecione **métodos de pagamento**.

   ![Captura de ecrã que mostra os métodos de pagamento no menu](./media/billing-how-to-change-credit-card/billing-profile-payment-methods.png)

1. Acima do método de pagamento predefinido, clique em **alteração**.

    ![Captura de ecrã que mostra alterar botão](./media/billing-how-to-change-credit-card/customer-led-switch-credit-card.png)

1. Selecione um cartão existente ou adicionar um novo.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes
As secções seguintes responder a perguntas mais frequentes sobre como alterar as suas informações de cartão de crédito ou débito.

### <a name="my-subscription-is-disabled-why-cant-i-remove-my-credit-card-now"></a>A minha subscrição está desativada. Por que motivo não posso remover meu cartão de crédito agora?

Após a sua subscrição está desativada ou foi cancelada, podemos esperar 90 dias antes de serem eliminados permanentemente a sua subscrição. Mantemos o método de pagamento registado durante o período de retenção, no caso de que pretende reativar a subscrição. Depois disso, a subscrição é eliminada permanentemente.

Se tiver de remover o seu cartão de crédito antes de terminar o período de retenção de 90 dias, [reativar a sua subscrição](billing-subscription-become-disable.md). Se não é possível reativar, [contacte o suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>Por que posso continuar a receber "a sua sessão de início de sessão expirou. Clique aqui para voltar a iniciar sessão"?

Se continuar a receber esta mensagem de erro, mesmo que já tiver iniciado sessão e iniciá-la no, tente novamente com uma sessão de navegação privada.

### <a name="how-do-i-use-a-different-card-for-each-subscription-i-have"></a>Como posso utilizar um cartão diferente para cada subscrição que tenho?

Infelizmente, se as suas subscrições já estiver a utilizar o cartão, não é possível para separá-las para utilizar cartões diferentes. No entanto, quando se inscreve para uma nova subscrição, pode optar por utilizar um novo método de pagamento para essa subscrição.

### <a name="how-do-i-make-payments"></a>Como me certifico de pagamentos?

Se configurar um cartão de crédito como método de pagamento, é cobrada automaticamente seu cartão após cada período de faturação. Não precisa de fazer nada.

Se estiver [pagar por fatura](billing-how-to-pay-by-invoice.md), envie o pagamento para a localização listado na parte inferior da sua fatura.

### <a name="how-do-i-change-the-tax-id"></a>Como posso alterar o ID de imposto?

Para adicionar ou atualizar o ID de imposto, atualize o seu perfil no [Centro de contas do Azure](https://account.azure.com/Profile), em seguida, selecione **imposto sobre vendas registo**. O NIF é utilizado para cálculos de isenção fiscal e é apresentado na sua fatura.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso a um contrato de cliente da Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre [reservas Azure](billing-save-compute-costs-reservations.md) para ver se podem poupar dinheiro.
