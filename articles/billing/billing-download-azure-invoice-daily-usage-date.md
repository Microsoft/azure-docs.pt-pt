---
title: Transferir fatura do Azure e dados de uso diário | Microsoft Docs
description: Descreve como transferir ou visualizar seus dados do Azure faturas da nota fiscal e diário utilização.
keywords: cobrança de nota fiscal, a transferência de faturas, a fatura do azure, utilização do azure
services: billing
documentationcenter: ''
author: genlin
manager: adpick
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: 52e227b933533bf60a474d0417c57e4b75c7978f
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58224639"
---
# <a name="download-or-view-your-azure-billing-invoice-and-daily-usage-data"></a>Baixe ou exiba sua fatura do Azure e dados de utilização diária

Para a maioria das subscrições, pode transferir a fatura do [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) ou enviá-lo no e-mail. Se for um cliente do Azure com um Enterprise Agreement (cliente EA), não é possível transferir as notas fiscais de sua organização. Notas fiscais são enviadas para quem está configurado para receber as notas fiscais para a inscrição.

Se estiver num cliente EA ou têm um [contrato de cliente do Microsoft](#check-access-to-a-microsoft-customer-agreement), pode transferir utilização no [portal do Azure](https://portal.azure.com/). Para outras subscrições, vá para o [Centro de contas do Azure](https://account.azure.com/Subscriptions) para transferir utilização.

Apenas determinadas funções do utilizador tem permissão para obter informações de fatura e utilização, como o administrador Corporativo ou de administrador de conta de faturação. Para saber mais sobre como obter acesso a informações de faturação, veja [Gerir acesso às funções de utilização de faturação do Azure](billing-manage-access.md).

Se tiver um contrato de cliente da Microsoft, tem de ser um perfil de faturação proprietário, Contribuidor, leitor, ou manager para ver informações de faturação e utilização de nota fiscal. Para saber mais sobre as funções de faturas para contratos de cliente da Microsoft, consulte [funções de perfis e tarefas de faturação](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

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

1. Pesquisar nos **custo Management + faturação**.
2. Selecione um perfil de faturação.
3. Selecione **Faturas**.
4. Na grelha de nota fiscal, localize a linha da nota fiscal que pretende transferir.
5. Clique nas reticências (`...`) no final da linha.
6. No menu de contexto do download, selecione **nota fiscal**.

Se não vir uma nota fiscal para o último período de faturação, consulte **informações adicionais**. <!-- Fix this -->
### <a name="noinvoice"></a> Por que motivo não vejo uma fatura para o último período de faturação?

Pode haver vários motivos para não ver uma fatura:

- Passaram menos de 30 dias desde o dia em que subscreveu o Azure.

- A fatura ainda não foi gerada. Aguarde até ao fim do período de faturação.

- Não tem permissão para ver as notas fiscais. Se tiver um contrato de cliente da Microsoft, tem de ser o perfil de faturação proprietário, Contribuidor, leitor, ou Gestor de nota fiscal. Para outras subscrições, poderá não ver faturas antigas se não o administrador de conta. Para saber mais sobre como obter acesso a informações de faturação, veja [Gerir acesso às funções de utilização de faturação do Azure](billing-manage-access.md).

- Se tiver uma avaliação gratuita ou uma quantia de crédito mensal, com a sua subscrição que não foi excedido, não obtém uma nota fiscal a menos que tenha um contrato de cliente da Microsoft.

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

1. Pesquisar nos **custo Management + faturação**.
1. Selecione um perfil de faturação.
1. Sob **configurações**, selecione **propriedades**.
1. Sob **fatura por E-Mail**, selecione **preferência de nota fiscal de e-mail de atualização**.
1. Selecione **optar ativamente por participar no**.
1. Clique em **Atualizar**.

### <a name="opt-out-of-getting-your-billing-profile-invoices-in-email"></a>Opção de desativar a obter as faturas de perfil de faturação no e-mail

Pode optar por obter a fatura por e-mail ao seguir os passos acima e clicando em **para anular**. Todos os proprietários, contribuidores, os leitores e notas fiscais gerentes serão excluídos introdução a nota fiscal por e-mail, também. Se for um leitor, é possível alterar a preferência de nota fiscal de e-mail.

## <a name="download-usage"></a>Transferir utilização

 Para a maioria das subscrições, encontre o ficheiro de utilização diária no [Centro de contas do Azure](https://account.azure.com/Subscriptions). Se for um cliente EA ou têm um contrato de cliente da Microsoft, pode transferir utilização no [portal do Azure](https://portal.azure.com/). <!-- TO DO: update PayG experience to Ibiza once it ships-->

### <a name="download-usage-from-the-account-center-csv"></a>Transferir utilização a partir do Centro de contas (. csv)

1. Inicie sessão no [Centro de contas do Azure](https://account.windowsazure.com/subscriptions) como o administrador de conta.

2. Selecione a subscrição que pretende que as informações da fatura e utilização.

3. Selecione **histórico de FATURAÇÃO**.

    ![Captura de ecrã que mostra a opção de histórico de faturação](./media/billing-download-azure-invoice-daily-usage-date/Billinghisotry.png)

4. Pode ver as suas instruções para os últimos seis períodos de faturas e o período de faturação por faturar atual.

    ![Captura de ecrã que mostra períodos de faturação, as opções a transferir fatura e utilização diária e total das cobranças para cada período de faturação](./media/billing-download-azure-invoice-daily-usage-date/billingSum.png)

5. Selecione **Ver Extrato Atual** para ver uma estimativa dos custos no momento em que a estimativa foi gerada. Esta informação é atualizada diariamente apenas e não pode incluir toda a sua utilização. A fatura mensal poderá divergir desta estimativa.

    ![Captura de ecrã que mostra a opção de ver extrato atual](./media/billing-download-azure-invoice-daily-usage-date/billingSum2.png)

    ![Captura de ecrã que mostra a estimativa de custos atuais](./media/billing-download-azure-invoice-daily-usage-date/billingSum3.png)

6. Selecione **Transferir Utilização** para transferir os dados de utilização diária como um ficheiro CSV. Se vir duas versões disponíveis, transfira a versão 2.

    ![Captura de ecrã que mostra a opção de transferir utilização](./media/billing-download-azure-invoice-daily-usage-date/DLusage.png)

Apenas o administrador de conta pode aceder ao centro de contas do Azure. Outros administradores de faturas como, por exemplo, um proprietário, podem obter a utilização de informações de utilização a [APIs de faturação](billing-usage-rate-card-overview.md).

Para obter mais informações sobre a sua utilização diária, veja [Compreender a fatura do Microsoft Azure](billing-understand-your-bill.md). Para ajudar a gerir os custos, veja [evitar custos inesperados com a faturação do Azure e a gestão de custos](billing-getting-started.md).

### <a name="download-usage-for-ea-customers"></a>Transferir utilização para clientes com EA

Para ver e transferir dados de utilização como um cliente EA, tem de ser um administrador empresarial, o proprietário da conta, ou administrador do departamento com o modo de exibição cobra política ativada.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Procure *custo Management + faturação*.

    ![Captura de ecrã que mostra a pesquisa de portal do Azure](./media/billing-download-azure-invoice-daily-usage-date/portal-cm-billing-search.png)

1. Selecione **utilização e custos**.
1. Para o mês em que deseja baixar, selecione **transferir**.

### <a name="download-usage-for-your-microsoft-customer-agreement"></a>Transferir utilização para o seu contrato de cliente da Microsoft

Para ver e transferir dados de utilização para um perfil de faturação, tem de ser um perfil de faturação proprietário, Contribuidor, leitor, ou Gestor de nota fiscal.

#### <a name="download-usage-for-billed-charges"></a>Transferir utilização para os custos de faturação

1. Pesquisar nos **custo Management + faturação**.
2. Selecione um perfil de faturação.
3. Selecione **Faturas**.
4. Na grelha de nota fiscal, localize a linha da nota fiscal correspondente à utilização de que pretende transferir.
5. Clique nas reticências (`...`) no final da linha.
6. No menu de contexto do download, selecione **utilização do Azure e os encargos**.

#### <a name="download-usage-for-open-charges"></a>Transferir utilização para os custos da open

Também pode transferir utilização mês até à data para o período de faturação atual, que significa que os custos de não tem sido faturados ainda.

1. Pesquisar nos **custo Management + faturação**.
2. Selecione um perfil de faturação.
3. Na **descrição geral** painel, clique em **Azure transferir utilização e a cobrança**.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso a um contrato de cliente da Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos

Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre a sua fatura e os custos, veja:

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
