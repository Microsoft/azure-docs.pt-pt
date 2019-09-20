---
title: Ver e transferir a fatura do Microsoft Azure
description: Descreve como ver e transferir a fatura do Microsoft Azure.
keywords: faturação, transferência da fatura, fatura do azure, utilização do azure
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: banders
ms.openlocfilehash: 4dc5f88f7a5994e5bcd50d71c86bf1ba35b10734
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "68321768"
---
# <a name="view-and-download-your-microsoft-azure-invoice"></a>Ver e transferir a fatura do Microsoft Azure

Para a maioria das subscrições, pode transferir a fatura a partir do [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) ou recebê-la por e-mail. Se for um cliente do Azure com um Contrato Enterprise (cliente EA), não poderá transferir as faturas da sua organização. As faturas são enviadas para a pessoa que está configurada para receber faturas para a inscrição.

Apenas determinadas funções não têm permissão para ver faturas. Por exemplo, o Administrador de Conta ou o Administrador do Enterprise. Para obter mais informações sobre como obter acesso às informações de faturação, veja [Gerir acesso às funções de utilização de faturação do Azure](billing-manage-access.md).

Se tiver um [Contrato de Cliente da Microsoft](#check-your-access-to-a-microsoft-customer-agreement), deve ter uma das seguintes funções para obter as faturas:

- Proprietário do perfil de faturação
- Contribuinte
- Leitor
- Gestor de faturas

Para obter mais informações sobre as funções de faturação dos Contratos de Cliente da Microsoft, veja [Funções e tarefas do perfil de faturação](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="noinvoice"></a> Motivo pelo qual pode não obter uma fatura

Podem existir vários motivos para não ver uma fatura:

- Passaram menos de 30 dias desde o dia em que subscreveu o Azure.

- O Azure cobra-o no final do período da fatura. Portanto, uma fatura pode ainda não ter sido gerada. Aguarde até ao fim do período de faturação.

- Não tem permissão para ver faturas. Se tiver um Contrato de Cliente da Microsoft, tem de ser Proprietário, Contribuidor, Leitor ou Gestor de faturas de um perfil de faturação. Para as outras subscrições, pode não ver as faturas antigas se não for o Administrador de Conta. Para saber mais sobre como obter acesso a informações de faturação, veja [Gerir acesso às funções de utilização de faturação do Azure](billing-manage-access.md).

- Se tiver uma Avaliação Gratuita ou um montante de crédito mensal com a sua subscrição, receberá apenas uma fatura quando exceder o montante de crédito mensal. Se tiver um Contrato de Cliente da Microsoft, receberá sempre uma fatura.

## <a name="download-your-azure-invoices-pdf"></a>Transferir as faturas do Azure (.pdf)

Para a maioria das subscrições, pode transferir a fatura a partir do portal do Azure. Se tiver um Contrato de Cliente da Microsoft, veja [Transferir as faturas de um Contrato de Cliente da Microsoft](#download-invoices-for-a-microsoft-customer-agreement).

### <a name="download-invoices-for-an-individual-subscription"></a>Transferir faturas de uma subscrição individual

1. Selecione a sua subscrição na [página Subscrições](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) no portal do Azure como [utilizador com acesso às faturas](billing-manage-access.md).

2. Selecione **Faturas**.

    ![Captura de ecrã que mostra a opção Faturação e utilização](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png)

3. Clique em **Transferir Fatura** para ver uma cópia da fatura em PDF. Se indicar **Não disponível**, veja [Por que motivo não vejo uma fatura para o último período de faturação?](#noinvoice)

    ![Captura de ecrã que mostra os períodos de faturação, a opção de transferência e os custos totais de cada período de faturação](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

4. Também pode visualizar a sua utilização diária ao clicar no período de faturação.

Para obter mais informações sobre a fatura, veja [Compreender a fatura do Microsoft Azure](billing-understand-your-bill.md). Para obter ajuda com a gestão dos custos, veja [Evitar custos inesperados com a faturação e a gestão de custos do Azure](billing-getting-started.md).

### <a name="download-invoices-for-a-microsoft-customer-agreement"></a>Transferir as faturas de um Contrato de Cliente da Microsoft

São geradas faturas para cada [perfil de faturação](billing-mca-overview.md#billing-profiles) no Contrato de Cliente da Microsoft. Tem de ser Proprietário, Contribuidor, Leitor ou Gestor de faturas do perfil de faturação para transferir faturas do portal do Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Procure *Cost Management + Faturação*.
1. Selecione um perfil de faturação. Dependendo do seu acesso, pode ser preciso selecionar primeiro uma conta de faturação.
1. Selecione **Faturas**.
1. Na grelha da fatura, localize a linha da fatura que quer transferir.
1. Clique nas reticências (`...`) no final da linha.
    ![Captura de ecrã que mostra as reticências no final da linha](./media/billing-download-azure-invoice/billingprofile-invoicegrid.png)
1. No menu de contexto da transferência, selecione **Fatura**.

    ![Captura de ecrã que mostra o menu de contexto](./media/billing-download-azure-invoice/contextmenu.png)

Se não vir uma fatura do último período de faturação, veja [Por que motivo não vejo uma fatura para o último período de faturação?](#noinvoice)

## <a name="get-your-invoice-in-email-pdf"></a>Obter a fatura por e-mail (.pdf)

Pode ativar e configurar destinatários adicionais para receber a fatura do Azure por e-mail. Esta funcionalidade pode não estar disponível para determinadas subscrições, como as ofertas de suporte, os Contratos Enterprise ou o Azure no Open. Se tiver um Contrato de Cliente da Microsoft, veja a próxima secção [Obter as faturas do perfil de faturação por e-mail](#get-your-subscriptions-invoices-in-email).

### <a name="get-your-subscriptions-invoices-in-email"></a>Obter as faturas da subscrição por e-mail

1. Selecione a sua subscrição na [página Subscrições](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Escolha Optar por receber para cada subscrição que tiver. Clique em **Faturas** e, em seguida, **Enviar a minha fatura por e-mail**.

    ![Captura de ecrã que mostra o fluxo de optar por receber](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)

2. Clique em **Optar por receber** e aceite os termos.

    ![Captura de ecrã que mostra o passo 2 do fluxo de optar por receber](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)

3. Quando aceitar o contrato, pode configurar destinatários adicionais. Quando um destinatário é removido, o endereço de e-mail deixa de ser armazenado. Se mudar de ideia, terá de os adicionar novamente.

    ![Captura de ecrã que mostra o passo 3 do fluxo de optar por receber](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)

Se não receber um e-mail depois de seguir os passos, confirme que o endereço de e-mail está correto nas [preferências de comunicação do seu perfil](https://account.windowsazure.com/profile).

### <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>Optar por não receber as faturas da subscrição por e-mail

Para optar por não receber as faturas por e-mail, siga os passos anteriores e clique em **Optar por não receber faturas por e-mail**. Esta opção remove todos os endereços de e-mail que foram configurados para receber faturas por e-mail. Pode reconfigurar os destinatários se optar por voltar a receber faturas.

 ![Captura de ecrã que mostra o fluxo de optar por não receber](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep4.PNG)

### <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>Obter as faturas do Contrato de Cliente da Microsoft por e-mail

Se tiver um Contrato de Cliente da Microsoft, poderá optar por receber a fatura por e-mail. Todos os Proprietários, Contribuidores, Leitores e Gestores de faturas do perfil de faturação receberão a fatura por e-mail. Os leitores não podem atualizar a preferência de fatura por e-mail.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Procure **Cost Management + Faturação**.
1. Selecione um perfil de faturação. Dependendo do seu acesso, pode ser preciso selecionar primeiro uma conta de faturação.
1. Em **Definições**, selecione **Propriedades**.
1. Em **Fatura por E-mail**, selecione **Atualizar preferência de fatura por e-mail**.

    ![Captura de ecrã que mostra as propriedades da fatura por e-mail](./media/billing-download-azure-invoice/billingprofile-email.png)

1. Selecione **Optar por receber**.
1. Clique em **Atualizar**.

### <a name="opt-out-of-getting-your-microsoft-customer-agreement-invoices-in-email"></a>Optar por não receber as faturas por e-mail do Contrato de Cliente Microsoft

Para optar por não receber as faturas por e-mail, siga os passos anteriores e clique em **Optar por não receber**. Todos os Proprietários, Contribuidores, Leitores e Gestores de faturas também deixam de receber a fatura por e-mail. Se for um Leitor, não poderá alterar a preferência de fatura por e-mail.



## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso ao Contrato de Cliente da Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a fatura e os custos, veja:

- [Ver e transferir a utilização e os custos do Microsoft Azure](billing-download-azure-daily-usage.md)
- [Understand your bill for Microsoft Azure](billing-understand-your-bill.md) (Compreender a sua fatura do Microsoft Azure)
- [Compreender os termos na fatura do Azure](billing-understand-your-invoice.md)
- [Compreender os termos na utilização diária do Microsoft Azure](billing-understand-your-usage.md)
- [Ver os preços do Azure da sua organização](billing-ea-pricing.md)

Se tiver um Contrato de Cliente da Microsoft, veja:

- [Compreender os custos na fatura para o seu perfil de faturação](billing-mca-understand-your-bill.md)
- [Compreender os termos na fatura para o seu perfil de faturação](billing-mca-understand-your-invoice.md)
- [Compreender o ficheiro de utilização e de custos do Azure para o seu perfil de faturação](billing-mca-understand-your-usage.md)
- [Ver e transferir os documentos fiscais para o seu perfil de faturação](billing-mca-download-tax-document.md)
- [Ver os preços do Azure da sua organização](billing-ea-pricing.md)
