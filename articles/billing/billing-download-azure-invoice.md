---
title: Ver e transferir a fatura do Microsoft Azure
description: Descreve como exibir e baixar sua fatura do Microsoft Azure.
keywords: fatura de cobrança, download de fatura, fatura do Azure, uso do Azure
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
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68321768"
---
# <a name="view-and-download-your-microsoft-azure-invoice"></a>Ver e transferir a fatura do Microsoft Azure

Para a maioria das assinaturas, você pode baixar sua fatura do [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) ou enviá-la por email. Se você for um cliente do Azure com um Enterprise Agreement (cliente EA), não poderá baixar as faturas da sua organização. As notas fiscais são enviadas para quem está configurada para receber faturas para o registro.

Somente determinadas funções têm permissão para exibir faturas. Por exemplo, o administrador da conta ou o administrador corporativo. Para obter mais informações sobre como obter acesso a informações de cobrança, consulte [gerenciar o acesso à cobrança do Azure usando funções](billing-manage-access.md).

Se você tiver um [contrato](#check-your-access-to-a-microsoft-customer-agreement)com o cliente da Microsoft, deverá ter uma das seguintes funções para obter suas faturas:

- Proprietário do perfil de cobrança
- Contribuinte
- Leitor
- Gerenciador de faturas

Para obter mais informações sobre as funções de cobrança para contratos de clientes da Microsoft, consulte [funções e tarefas de perfil de cobrança](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="noinvoice"></a>Por que você não pode obter uma fatura

Pode haver vários motivos para não ver uma fatura:

- Passaram menos de 30 dias desde o dia em que subscreveu o Azure.

- O Azure cobra você no final do período da fatura. Portanto, uma fatura pode não ter sido gerada ainda. Aguarde até ao fim do período de faturação.

- Você não tem permissão para exibir faturas. Se você tiver um contrato de cliente da Microsoft, deverá ser o proprietário do perfil de cobrança, o colaborador, o leitor ou o Gerenciador de faturas. Para outras assinaturas, talvez você não veja as faturas antigas se não for o administrador da conta. Para saber mais sobre como obter acesso a informações de faturação, veja [Gerir acesso às funções de utilização de faturação do Azure](billing-manage-access.md).

- Se você tiver uma avaliação gratuita ou um valor de crédito mensal com sua assinatura, receberá apenas uma fatura quando exceder o valor de crédito mensal. Se você tiver um contrato com o cliente da Microsoft, sempre receberá uma fatura.

## <a name="download-your-azure-invoices-pdf"></a>Baixe suas faturas do Azure (. pdf)

Para a maioria das assinaturas, você pode baixar sua fatura do portal do Azure. Se você tiver um contrato com o cliente da Microsoft, consulte [baixar faturas para um contrato de cliente da Microsoft](#download-invoices-for-a-microsoft-customer-agreement).

### <a name="download-invoices-for-an-individual-subscription"></a>Baixar faturas para uma assinatura individual

1. Selecione sua assinatura na [página assinaturas](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) na portal do Azure como [um usuário com acesso a faturas](billing-manage-access.md).

2. Selecione **Faturas**.

    ![Captura de tela que mostra a opção de uso de & de cobrança](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png)

3. Clique em **baixar fatura** para exibir uma cópia de sua fatura em PDF. Se disser **Não disponível**, confira [Por que motivo não vejo uma fatura para o último período de faturação?](#noinvoice)

    ![Captura de tela que mostra os períodos de cobrança, a opção de download e os encargos totais de cada período de cobrança](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

4. Você também pode exibir seu uso diário clicando no período de cobrança.

Para obter mais informações sobre sua fatura, consulte [entender sua fatura por Microsoft Azure](billing-understand-your-bill.md). Para obter ajuda com o gerenciamento de seus custos, confira [evitar custos inesperados com o gerenciamento de custos e cobrança do Azure](billing-getting-started.md).

### <a name="download-invoices-for-a-microsoft-customer-agreement"></a>Baixar faturas para um contrato de cliente da Microsoft

As notas fiscais são geradas para cada [perfil de cobrança](billing-mca-overview.md#billing-profiles) no contrato do cliente da Microsoft. Você deve ser um proprietário do perfil de cobrança, colaborador, leitor ou Gerenciador de faturas para baixar faturas do portal do Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Procure *Gerenciamento de custos + cobrança*.
1. Selecione um perfil de cobrança. Dependendo do seu acesso, talvez seja necessário selecionar primeiro uma conta de cobrança.
1. Selecione **Faturas**.
1. Na grade da nota fiscal, localize a linha da fatura que você deseja baixar.
1. Clique nas reticências`...`() no final da linha.
    ![Captura de tela que mostra as reticências no final da linha](./media/billing-download-azure-invoice/billingprofile-invoicegrid.png)
1. No menu de contexto baixar, selecione **fatura**.

    ![Captura de tela que mostra o menu de contexto](./media/billing-download-azure-invoice/contextmenu.png)

Se você não vir uma fatura para o último período de cobrança, confira [por que não vejo uma fatura para o último período de cobrança?](#noinvoice)

## <a name="get-your-invoice-in-email-pdf"></a>Obter sua fatura por email (. pdf)

Pode optar ativamente e configurar os destinatários principais para receber sua fatura do Azure por email. Esse recurso pode não estar disponível para determinadas assinaturas, como ofertas de suporte, Enterprise Agreements ou Azure via Open. Se você tiver um contrato com o cliente da Microsoft, consulte a próxima seção, [obter suas faturas de perfil de cobrança por email](#get-your-subscriptions-invoices-in-email).

### <a name="get-your-subscriptions-invoices-in-email"></a>Obter as faturas de sua assinatura no email

1. Selecione sua assinatura na [página assinaturas](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Aceite para cada assinatura que você possui. Clique em **Faturas** , em seguida, **Enviar minha fatura por email**.

    ![Captura de tela que mostra o fluxo de aceitação](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)

2. Clique **em** aceitar e aceite os termos.

    ![Captura de tela que mostra o fluxo de aceitação etapa 2](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)

3. Quando você aceitar o contrato, poderá configurar destinatários adicionais. Quando um destinatário é removido, o endereço de email não é mais armazenado. Se você mudar de ideia, precisará lê-las.

    ![Captura de tela que mostra o fluxo de aceitação etapa 3](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)

Se você não receber um email depois de seguir as etapas, verifique se seu endereço de email está correto nas [preferências de comunicação em seu perfil](https://account.windowsazure.com/profile).

### <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>Recusar a obtenção de faturas de sua assinatura no email

Para cancelar a obtenção de sua fatura por email, siga as etapas anteriores e clique em **recusar faturas por email**. Esta opção remove todos os endereços de e-mail que foram configurados para receber faturas por e-mail. Você poderá reconfigurar os destinatários se optar por voltar.

 ![Captura de tela que mostra o fluxo de recusa](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep4.PNG)

### <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>Obter suas faturas de contrato de cliente da Microsoft por email

Se você tiver um contrato com o cliente da Microsoft, poderá optar por obter sua fatura em um email. Todos os proprietários do perfil de cobrança, colaboradores, leitores e gerentes de fatura receberão a fatura por email. Os leitores não podem atualizar a preferência de fatura de email.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Procure **Gerenciamento de custos + cobrança**.
1. Selecione um perfil de cobrança. Dependendo do seu acesso, talvez seja necessário selecionar primeiro uma conta de cobrança.
1. Em **configurações**, selecione **Propriedades**.
1. Em **fatura de email**, selecione **Atualizar preferência de fatura de email**.

    ![Captura de tela que mostra as propriedades de fatura de email](./media/billing-download-azure-invoice/billingprofile-email.png)

1. Selecione **aceitar**.
1. Clique em **Atualizar**.

### <a name="opt-out-of-getting-your-microsoft-customer-agreement-invoices-in-email"></a>Recusar o recebimento de suas faturas de contrato do cliente da Microsoft por email

Para cancelar a obtenção de sua fatura por email, siga as etapas anteriores e clique em **recusar**. Todos os proprietários, colaboradores, leitores e gerentes de fatura são recusados de obter a fatura por email também. Se você for um leitor, não poderá alterar a preferência de fatura de email.



## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>Verifique seu acesso a um contrato de cliente da Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a fatura e os encargos, confira:

- [Exibir e baixar seu uso de Microsoft Azure e encargos](billing-download-azure-daily-usage.md)
- [Understand your bill for Microsoft Azure](billing-understand-your-bill.md) (Compreender a sua fatura do Microsoft Azure)
- [Entender os termos em sua fatura do Azure](billing-understand-your-invoice.md)
- [Entenda os termos em seu Microsoft Azure uso detalhado](billing-understand-your-usage.md)
- [Exibir os preços do Azure de sua organização](billing-ea-pricing.md)

Se você tiver um contrato com o cliente da Microsoft, consulte:

- [Entender os encargos na fatura do seu perfil de cobrança](billing-mca-understand-your-bill.md)
- [Entender os termos da fatura do seu perfil de cobrança](billing-mca-understand-your-invoice.md)
- [Entender o arquivo de custos e de uso do Azure para seu perfil de cobrança](billing-mca-understand-your-usage.md)
- [Exibir e baixar documentos de impostos para seu perfil de cobrança](billing-mca-download-tax-document.md)
- [Exibir os preços do Azure de sua organização](billing-ea-pricing.md)
