---
title: Alterar o cartão de crédito do Azure
description: Descreve como alterar o cartão de crédito utilizado para pagar a subscrição do Azure.
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: banders
ms.openlocfilehash: e652237e6faa705aa3ea09e7cf80c4eb692acc98
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72375592"
---
# <a name="add-update-or-remove-a-credit-card-for-azure"></a>Adicionar, atualizar ou remover um cartão de crédito do Azure

Este documento aplica-se aos clientes que se inscreveram no Azure online com um cartão de crédito.

No portal do Azure, pode adicionar um novo cartão de crédito, atualizar um cartão de crédito existente ou eliminar um cartão de crédito que não utiliza. Tem de ser um [Administrador de Conta](billing-subscription-transfer.md#whoisaa) para fazer estas alterações.

Se tiver um [Contrato de Cliente da Microsoft](#check-access-to-a-microsoft-customer-agreement), os métodos de pagamento estão associados aos perfis de faturação. Saiba como [alterar o método de pagamento predefinido de um perfil de faturação](#change-payment-method-for-a-billing-profile). Apenas o utilizador que se inscreveu no Azure pode atualizar o método de pagamento.

**Quer mudar para o pagamento por fatura (cheque/transferência bancária)?** Veja [Pagamento das subscrições através de fatura](billing-how-to-pay-by-invoice.md).

<a id="addcard"></a>

## <a name="add-a-new-credit-card-to-an-azure-subscription"></a>Adicionar um novo cartão de crédito a uma subscrição do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como Administrador de Conta.
1. Procure **Cost Management + Faturação**.

    ![Captura de ecrã que mostra a pesquisa](./media/billing-how-to-change-credit-card/search.png)

1. Selecione a subscrição à qual quer adicionar o cartão de crédito.
1. Selecione **Métodos de pagamento**.

    ![Captura de ecrã que mostra a opção Gerir métodos de pagamento selecionada.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. No canto superior esquerdo, selecione "+" para adicionar um cartão. Será apresentado à direita um formulário de cartão de crédito.
1. Introduza os detalhes do cartão de crédito.

    ![Captura de ecrã que mostra a adição de um novo cartão.](./media/billing-how-to-change-credit-card/sub-add-new-x.png)

1. Para definir este cartão como o método de pagamento ativo, selecione a caixa junto a **Definir o meu método de pagamento ativo** acima do formulário. Este cartão tornar-se-á o instrumento de pagamento ativo para todas as subscrições com o mesmo cartão da subscrição selecionada.

1. Selecione **Seguinte**.

Se obtiver um erro depois de adicionar o cartão de crédito, veja [Cartão de crédito recusado na inscrição do Azure](billing-credit-card-fails-during-azure-sign-up.md).

## <a name="update-existing-credit-card"></a>Atualizar cartão de crédito existente

Se o cartão de crédito for renovado e o número continuar o mesmo, atualize os detalhes do cartão de crédito existente, como a data de validade. Se o número do cartão de crédito for alterado porque o cartão foi perdido, roubado ou expirou, siga os passos em [Adicionar um cartão de crédito como um método de pagamento](#addcard). Não precisa de atualizar o CVV.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como Administrador de Conta.
1. Procure **Cost Management + Faturação**.

    ![Captura de ecrã que mostra a pesquisa](./media/billing-how-to-change-credit-card/search.png)

1. Selecione **Métodos de pagamento**.

    ![Captura de ecrã que mostra a opção Gerir métodos de pagamento selecionada.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Clique no cartão de crédito que quer editar. Será apresentado à direita um formulário de cartão de crédito.

    ![Captura de ecrã que mostra o cartão de crédito selecionado.](./media/billing-how-to-change-credit-card/edit-card-x.png)

1. Atualize os detalhes do cartão de crédito.
1. Selecione **Guardar**.

## <a name="use-a-different-credit-card"></a>Utilizar um cartão de crédito diferente

Se mais de uma das subscrições tiver o mesmo método de pagamento ativo, a alteração do método de pagamento ativo em qualquer uma dessas subscrições também atualizará o método de pagamento ativo nas outras subscrições.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como Administrador de Conta.
1. Procure **Cost Management + Faturação**.

    ![Captura de ecrã que mostra a pesquisa](./media/billing-how-to-change-credit-card/search.png)

1. Selecione a subscrição à qual quer adicionar o cartão de crédito.
1. Selecione **Métodos de pagamento**.

    ![Captura de ecrã que mostra a opção Gerir métodos de pagamento selecionada.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Selecione a caixa junto ao cartão que quer definir como o método de pagamento ativo.
1. Clique em **Definir como ativo**.
    ![Captura de ecrã que mostra o cartão de crédito selecionado e definido como ativo](./media/billing-how-to-change-credit-card/sub-change-active-x.png).

## <a name="remove-a-credit-card-from-the-account"></a>Remover o cartão de crédito da conta

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como Administrador de Conta.
1. Selecione **Cost Management + Faturação** no lado esquerdo da página.

    ![Captura de ecrã que mostra a pesquisa](./media/billing-how-to-change-credit-card/search.png)

1. Em **Faturação**, selecione **Métodos de pagamento**.

    ![Captura de ecrã que mostra a opção Gerir métodos de pagamento selecionada.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Selecione a caixa junto ao cartão que quer remover.
1. Clique em **Eliminar**.

Se o cartão de crédito for o método de pagamento ativo para qualquer uma das suas subscrições da Microsoft, não o poderá remover da conta do Azure. Altere o método de pagamento ativo para todas as subscrições associadas a este cartão de crédito e tente novamente

## <a name="change-payment-method-for-a-billing-profile"></a>Alterar o método de pagamento de um perfil de faturação

Para alterar o método de pagamento de um perfil de faturação, tem de ser a pessoa que se inscreveu no Azure.

Se quiser mudar o método de pagamento predefinido para cheque/transferência bancária, saiba como [mudar um perfil de faturação para cheque/transferência bancária](billing-how-to-pay-by-invoice.md).

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Faça uma pesquisa em **Gestão de Custos + Faturação**.
1. No menu esquerdo, clique em **Perfis de faturação**.

    ![captura de ecrã que mostra o perfil de faturação no menu](./media/billing-how-to-change-credit-card/billing-profile.png)

1. Selecione um perfil de faturação.
1. No menu esquerdo, selecione **Métodos de pagamento**.

   ![Captura de ecrã que mostra os métodos de pagamento no menu](./media/billing-how-to-change-credit-card/billing-profile-payment-methods.png)

1. Acima do método de pagamento predefinido, clique em **Alterar**.

    ![Captura de ecrã que mostra o botão Alterar](./media/billing-how-to-change-credit-card/customer-led-switch-credit-card.png)

1. Selecione um cartão existente ou adicione um novo.

## <a name="troubleshooting"></a>Resolução de problemas
Não suportamos cartões virtuais ou pré-pagos. Se ocorrerem erros ao adicionar ou atualizar um cartão de crédito válido, tente abrir o browser em modo privado.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes
As secções a seguir respondem a perguntas frequentes sobre como alterar as informações do cartão de crédito.

### <a name="my-subscription-is-disabled-why-cant-i-remove-my-credit-card-now"></a>A minha subscrição está desativada. Por que não consigo remover o meu cartão de crédito agora?

Depois de a subscrição ter sido desativada ou cancelada, aguardaremos 90 dias antes de eliminar permanentemente a subscrição. O método de pagamento é mantido em registo durante o período de retenção no caso de pretender reativar a subscrição. Após esse período, a subscrição é eliminada permanentemente.

Se quiser remover o cartão de crédito antes do período de retenção de 90 dias, [reative a subscrição](billing-subscription-become-disable.md). Se não conseguir reativar, [contacte o suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>Por que continuo a obter “O início de sessão expirou. Clique aqui para iniciar sessão novamente”?

Se continuar a obter esta mensagem de erro mesmo que tenha terminado e iniciado sessão novamente, tente novamente com uma sessão de navegação privada.

### <a name="how-do-i-use-a-different-card-for-each-subscription-i-have"></a>Como posso utilizar um cartão diferente para cada das minhas subscrições?

Infelizmente, se as suas subscrições já estiverem a utilizar o mesmo cartão, não será possível separá-las para utilizarem cartões diferentes. No entanto, quando se inscrever numa nova subscrição, poderá optar por utilizar um novo método de pagamento para essa subscrição.

### <a name="how-do-i-make-payments"></a>Como fazer os pagamentos?

Se configurar um cartão de crédito como método de pagamento, o cartão será debitado automaticamente após cada período de faturação. Não é necessário fazer nada.

Se [pagar por fatura](billing-how-to-pay-by-invoice.md), envie o pagamento para a localização indicada na parte inferior da fatura.

### <a name="how-do-i-change-the-tax-id"></a>Como posso alterar o NIF?

Para adicionar ou atualizar o NIF, atualize o seu perfil no [Centro de Contas do Azure](https://account.azure.com/Profile) e, em seguida, selecione **Registo fiscal**. O NIF é utilizado para cálculos de isenção fiscal e é apresentado na sua fatura.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso ao Contrato de Cliente da Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre as [Reservas do Azure](billing-save-compute-costs-reservations.md) para determinar se são mais económicas.
