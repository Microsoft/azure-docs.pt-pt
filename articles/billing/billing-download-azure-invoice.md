---
title: Ver e transferir a fatura do Microsoft Azure
description: Descreve como ver e transferir a fatura do Microsoft Azure.
keywords: faturação, transferência da fatura, fatura do azure, utilização do azure
author: bandersmsft
manager: jureid
tags: billing
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 7d52a589ee7fcb8891f5ea839b2baeb5e4b30733
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224032"
---
# <a name="view-and-download-your-microsoft-azure-invoice"></a>Ver e transferir a fatura do Microsoft Azure

Para a maioria das subscrições, pode transferir a fatura a partir do [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) ou recebê-la por e-mail. Se for um cliente do Azure com um Contrato Enterprise (cliente EA), não poderá transferir as faturas da sua organização. As faturas são enviadas para a pessoa que está configurada para receber faturas para a inscrição.

Apenas determinadas funções não têm permissão para ver faturas. Por exemplo, o Administrador de Conta ou o Administrador do Enterprise. Para obter mais informações sobre como obter acesso às informações de faturação, veja [Gerir acesso às funções de utilização de faturação do Azure](billing-manage-access.md).

Se tiver um Contrato de Cliente da Microsoft (MCA), deverá ter uma das seguintes funções para obter as faturas:

- Proprietário do perfil de faturação
- Contribuinte
- Leitor
- Gestor de faturas

Se tiver um Contrato de Parceiro da Microsoft (MPA), terá de ser Administrador Global ou Agente de Administração na organização parceira para ver e transferir as faturas do Azure. [Verifique o tipo da conta de faturação](#check-your-billing-account-type) para saber as permissões de que precisa.

<!-- For more information about billing roles for Microsoft Customer Agreements, see [Billing profile roles and tasks](billing-understand-mca-roles.md#billing-profile-roles-and-tasks). -->

## <a name="noinvoice"></a> Motivo pelo qual pode não obter uma fatura

Podem existir vários motivos para não ver uma fatura:

- Passaram menos de 30 dias desde o dia em que subscreveu o Azure.

- O Azure cobra-o no final do período da fatura. Portanto, uma fatura pode ainda não ter sido gerada. Aguarde até ao fim do período de faturação.

- Não tem permissão para ver faturas. Se tiver um contrato MCA ou MPA, terá de ser Proprietário, Contribuidor, Leitor ou Gestor de faturas no perfil de faturação. Para as outras subscrições, pode não ver as faturas antigas se não for o Administrador de Conta. Para saber mais sobre como obter acesso a informações de faturação, veja [Gerir acesso às funções de utilização de faturação do Azure](billing-manage-access.md).

- Se tiver uma Avaliação Gratuita ou um montante de crédito mensal com a sua subscrição, receberá apenas uma fatura quando exceder o montante de crédito mensal. Se tiver um Contrato de Cliente da Microsoft ou Contrato de Parceiro da Microsoft, receberá sempre uma fatura.

## <a name="download-invoices-in-the-azure-portal"></a>Transferir as faturas no portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Procure *Cost Management + Faturação*.
1. Dependendo do seu acesso, poderá ser preciso selecionar uma conta de faturação ou um perfil de faturação.
1. No menu esquerdo, selecione **Faturas** em **Faturação**.
1. Na grelha da fatura, localize a linha da fatura que quer transferir.
1. Clique no ícone de transferência ou nas reticências (`...`) no final da linha.
1. No menu da transferência, selecione **Fatura**.

Para obter mais informações sobre a fatura, veja [Compreender a fatura do Microsoft Azure](billing-understand-your-bill.md). Para obter ajuda com a gestão dos custos, veja [Evitar custos inesperados com a faturação e a gestão de custos do Azure](billing-getting-started.md).

## <a name="get-your-subscriptions-invoices-in-email"></a>Obter as faturas da subscrição por e-mail

Pode ativar e configurar destinatários adicionais para receber a fatura do Azure por e-mail. Esta funcionalidade pode não estar disponível para determinadas subscrições, como as ofertas de suporte, os Contratos Enterprise ou o Azure no Open.

1. Selecione a sua subscrição na [página Subscrições](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Escolha Optar por receber para cada subscrição que tiver. Clique em **Faturas** e, em seguida, **Enviar a minha fatura por e-mail**.

    ![Captura de ecrã que mostra o fluxo de optar por receber](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)

2. Clique em **Optar por receber** e aceite os termos.

    ![Captura de ecrã que mostra o passo 2 do fluxo de optar por receber](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)

3. Quando aceitar o contrato, pode configurar destinatários adicionais. Quando um destinatário é removido, o endereço de e-mail deixa de ser armazenado. Se mudar de ideia, terá de os adicionar novamente.

    ![Captura de ecrã que mostra o passo 3 do fluxo de optar por receber](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)

Se não receber um e-mail depois de seguir os passos, confirme que o endereço de e-mail está correto nas [preferências de comunicação do seu perfil](https://account.windowsazure.com/profile).

## <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>Optar por não receber as faturas da subscrição por e-mail

Para optar por não receber as faturas por e-mail, siga os passos anteriores e clique em **Optar por não receber faturas por e-mail**. Esta opção remove todos os endereços de e-mail que foram configurados para receber faturas por e-mail. Pode reconfigurar os destinatários se optar por voltar a receber faturas.

 ![Captura de ecrã que mostra o fluxo de optar por não receber](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep4.PNG)

<!-- Does following section apply to MPA too? -->
## <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>Obter as faturas do Contrato de Cliente da Microsoft por e-mail

Se tiver um Contrato de Cliente da Microsoft, poderá optar por receber a fatura por e-mail. Todos os Proprietários, Contribuidores, Leitores e Gestores de faturas do perfil de faturação receberão a fatura por e-mail. Os leitores não podem atualizar a preferência de fatura por e-mail.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Procure **Cost Management + Faturação**.
1. Selecione um perfil de faturação. Dependendo do seu acesso, pode ser preciso selecionar primeiro uma conta de faturação.
1. Em **Definições**, selecione **Propriedades**.
1. Em **Fatura por E-mail**, selecione **Atualizar preferência de fatura por e-mail**.

    ![Captura de ecrã que mostra as propriedades da fatura por e-mail](./media/billing-download-azure-invoice/billingprofile-email.png)

1. Selecione **Optar por receber**.
1. Clique em **Atualizar**.

## <a name="opt-out-of-getting-your-microsoft-customer-agreement-invoices-in-email"></a>Optar por não receber as faturas por e-mail do Contrato de Cliente Microsoft

Para optar por não receber as faturas por e-mail, siga os passos anteriores e clique em **Optar por não receber**. Todos os Proprietários, Contribuidores, Leitores e Gestores de faturas também deixam de receber a fatura por e-mail. Se for um Leitor, não poderá alterar a preferência de fatura por e-mail.

## <a name="check-your-billing-account-type"></a>Verificar o tipo da conta de faturação
[!INCLUDE [billing-check-account-type](../../includes/billing-check-account-type.md)]

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
