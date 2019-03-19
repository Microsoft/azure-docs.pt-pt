---
title: Ver e transferir a fatura do Microsoft Azure | Documentos da Microsoft
description: Descreve como ver e transferir a fatura do Microsoft Azure
keywords: cobrança de nota fiscal, a transferência de faturas, a fatura do azure, utilização do azure
services: billing
documentationcenter: ''
author: genlin
manager: jureid
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: 6b82c0d2d4a5e22094bf0a408d6a42f17bb3bea4
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57839819"
---
# <a name="view-and-download-your-microsoft-azure-invoice"></a>Ver e transferir a fatura do Microsoft Azure

Para a maioria das subscrições, pode transferir a fatura do [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) ou enviá-lo no e-mail. Se for um cliente do Azure com um Enterprise Agreement (cliente EA), não é possível transferir as notas fiscais de sua organização. Notas fiscais são enviadas para quem está configurado para receber as notas fiscais para a inscrição.

Apenas determinadas funções do utilizador tem permissão para ver as notas fiscais, como o administrador de conta ou administrador de empresa. Para saber mais sobre como obter acesso a informações de faturação, veja [Gerir acesso às funções de utilização de faturação do Azure](billing-manage-access.md).

Se tiver um [contrato de cliente da Microsoft](#Check-your-access-to-a-Microsoft-Customer-Agreement), tem de ser um perfil de faturação proprietário, Contribuidor, leitor, ou criar uma fatura do gestor para as faturas. Para saber mais sobre as funções de faturas para contratos de cliente da Microsoft, consulte [funções de perfis e tarefas de faturação](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="download-your-azure-invoices-pdf"></a>Transferir as faturas do Azure (. pdf)

Para a maioria das subscrições, pode transferir a fatura do portal do Azure. Se tiver um contrato de cliente da Microsoft, consulte o Download notas fiscais para um perfil de faturação.

### <a name="download-invoices-for-an-individual-subscription"></a>Transferência de faturas para uma subscrição individual

1. Selecione a sua subscrição do [página subscrições](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) no portal do Azure, como [um utilizador com acesso às notas fiscais](billing-manage-access.md).

2. Selecione **Faturas**.

    ![Captura de ecrã que mostra a opção de faturação e utilização](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png)

3. Clique em **transferir fatura** para visualizar uma cópia da sua fatura em PDF. Se disser **Não disponível**, confira [Por que motivo não vejo uma fatura para o último período de faturação?](#noinvoice)

    ![Captura de ecrã que mostra períodos de faturação, a opção de transferência e o total das cobranças para cada período de faturação](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

4. Também pode ver a sua utilização diária clicando no período de faturação.

Para obter mais informações sobre a sua fatura, consulte [compreender a sua fatura do Microsoft Azure](billing-understand-your-bill.md). Para ajudar a gerir os custos, veja [evitar custos inesperados com a faturação do Azure e a gestão de custos](billing-getting-started.md).

### <a name="download-invoices-for-a-microsoft-customer-agreement"></a>Transferência de faturas para um contrato de cliente da Microsoft

Notas fiscais são gerados para cada [faturação perfil](billing-mca-overview.md#understand-billing-profiles) no contrato de cliente do Microsoft. Tem de ser um perfil de faturação proprietário, a Contribuidor, leitor, ou Gestor de transferência de faturas do portal do Azure de nota fiscal.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Pesquisar nos **custo Management + faturação**.
1. Selecione um perfil de faturação. Consoante o acesso, poderá ter de selecionar primeiro uma conta de cobrança.
1. Selecione **Faturas**.
1. Na grelha de nota fiscal, localize a linha da nota fiscal que pretende transferir.
1. Clique nas reticências (`...`) no final da linha.
    ![Captura de ecrã que mostra o botão de reticências no final da linha](./media/billing-download-azure-invoice/billingprofile-invoicegrid.png)
1. No menu de contexto do download, selecione **nota fiscal**.

    ![Captura de ecrã que mostra o menu de contexto](./media/billing-download-azure-invoice/contextmenu.png)

Se não vir uma nota fiscal para o último período de faturação, consulte [por que motivo não vejo uma nota fiscal para o último período de faturação?](#noinvoice)

## <a name="get-your-invoice-in-email-pdf"></a>Obter a fatura por correio eletrónico (. pdf)

Pode optar ativamente e configurar os destinatários principais para receber sua fatura do Azure por email. Esta funcionalidade não pode estar disponível para determinadas subscrições como ofertas de suporte, contratos de empresa ou do Azure no Open. Se tiver um contrato do Microsoft Customer, veja Get notas fiscais de seu perfil de faturação no e-mail.

### <a name="get-your-subscriptions-invoices-in-email"></a>Obter notas fiscais de sua assinatura no e-mail

1. Selecione a sua subscrição a partir da [página subscrições](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Escolher para cada subscrição que for o proprietário. Clique em **Faturas** , em seguida, **Enviar minha fatura por email**.

    ![Captura de ecrã que mostra o fluxo de participação ativa](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)

2. Clique em **optar ativamente por participar no** e aceite os termos.

    ![Captura de ecrã que mostra a etapa de aceitação fluxo 2](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)
 
3. Depois de ter aceite o contrato, pode configurar os destinatários adicionais. Quando um destinatário for removido, o endereço de e-mail já não é armazenado. Se mudar de ideias, terá de voltar a adicioná-los.

    ![Captura de ecrã que mostra a etapa de aceitação do fluxo 3](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)

Se não receber uma mensagem de e-mail depois de seguir os passos, certifique-se de que o seu endereço de e-mail está correto no [preferências de comunicação no seu perfil](https://account.windowsazure.com/profile).

### <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>Opção de desativar a obter notas fiscais de sua assinatura no e-mail

Pode optar por obter a fatura por e-mail ao seguir os passos acima e clicando em **recusar faturas por e-mail**. Esta opção remove todos os endereços de e-mail que foram configurados para receber faturas por e-mail. Pode reconfigurar os destinatários se optar ativamente por participar de volta.

 ![Captura de ecrã que mostra o fluxo de recusa](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep4.PNG)

### <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>Obter as faturas de contrato de cliente da Microsoft no e-mail

Se tiver um contrato de cliente da Microsoft, pode optar por obter a fatura numa mensagem de e-mail. Todos os faturação perfil proprietários, contribuidores, os leitores e notas fiscais gerentes obterá a nota fiscal por e-mail. Os leitores não é possível atualizar a preferência de nota fiscal de e-mail.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Pesquisar nos **custo Management + faturação**.
1. Selecione um perfil de faturação. Consoante o acesso, poderá ter de selecionar primeiro uma conta de cobrança.
1. Sob **configurações**, selecione **propriedades**.
1. Sob **fatura por E-Mail**, selecione **preferência de nota fiscal de e-mail de atualização**.

    ![Captura de ecrã que mostra as propriedades de nota fiscal de e-mail](./media/billing-download-azure-invoice/billingprofile-email.png)

1. Selecione **optar ativamente por participar no**.
1. Clique em **Atualizar**.

### <a name="opt-out-of-getting-your-microsoft-customer-agreement-invoices-in-email"></a>Opção de desativar a obter as faturas de contrato de cliente da Microsoft no e-mail

Pode optar por obter a fatura por e-mail ao seguir os passos acima e clicando em **para anular**. Todos os proprietários, contribuidores, os leitores e notas fiscais gerentes serão excluídos introdução a nota fiscal por e-mail, também. Se for um leitor, é possível alterar a preferência de nota fiscal de e-mail.

### <a name="noinvoice"></a> Por que motivo não vejo uma fatura para o último período de faturação?

Pode haver vários motivos para não ver uma fatura:

- Passaram menos de 30 dias desde o dia em que subscreveu o Azure.

- A fatura ainda não foi gerada. Aguarde até ao fim do período de faturação.

- Não tem permissão para ver as notas fiscais. Se tiver um contrato de cliente da Microsoft, tem de ser o perfil de faturação proprietário, Contribuidor, leitor, ou Gestor de nota fiscal. Para outras subscrições, poderá não ver faturas antigas se não o administrador de conta. Para saber mais sobre como obter acesso a informações de faturação, veja [Gerir acesso às funções de utilização de faturação do Azure](billing-manage-access.md).

- Se tiver uma avaliação gratuita ou uma quantia de crédito mensal, com a sua subscrição que não foi excedido, não obtém uma nota fiscal a menos que tenha um contrato de cliente da Microsoft.

## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso a um contrato de cliente da Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos

Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre a sua fatura e os custos, veja:

- [Ver e transferir a sua utilização do Microsoft Azure e a cobrança](billing-download-azure-daily-usage.md)
- [Compreender a sua fatura do Microsoft Azure](billing-understand-your-bill.md)
- [Compreender os termos na sua fatura do Azure](billing-understand-your-invoice.md)
- [Compreender os termos na utilização de detalhadas do Microsoft Azure](billing-understand-your-usage.md)
- [Veja os preços do Azure da sua organização](billing-ea-pricing.md)

Se tiver um contrato de cliente da Microsoft, consulte:

- [Compreender os encargos da nota fiscal para o seu perfil de faturação ](billing-mca-understand-your-bill.md)
- [Compreender os termos da nota fiscal para o seu perfil de faturação](billing-mca-understand-your-invoice.md)
- [Compreender o ficheiro de utilização e os encargos do Azure para o seu perfil de faturação](billing-mca-understand-your-usage.md)
- [Ver e transferir documentos de imposto para o seu perfil de faturação](billing-mca-download-tax-document.md)
- [Veja os preços do Azure da sua organização](billing-ea-pricing.md)
