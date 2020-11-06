---
title: Transferir a faturação e os dados de utilização diária do Azure
description: Descreve como transferir ou ver os dados da utilização diária e da faturação do Azure.
keywords: faturação, transferência da fatura, fatura do Azure, utilização do Azure
author: genlin
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 10/28/2020
ms.author: banders
ms.openlocfilehash: e380aa1f4d50e6ced34254ceca9d899022142f6d
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2020
ms.locfileid: "92911295"
---
# <a name="download-or-view-your-azure-billing-invoice-and-daily-usage-data"></a>Transferir ou ver os dados da utilização diária e da faturação do Azure

Para a maioria das subscrições, pode transferir a fatura a partir do [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) ou recebê-la por e-mail. Se for um cliente do Azure com um Contrato Enterprise (cliente EA), não poderá transferir as faturas da sua organização. As faturas são enviadas para quem está configurado para receber faturas para a inscrição.

Se for cliente do EA ou tiver um [Contrato de Cliente da Microsoft](#check-access-to-a-microsoft-customer-agreement), poderá transferir a utilização no [portal do Azure](https://portal.azure.com/).

Apenas determinadas funções têm permissão para receber informações de utilização e faturação, como o Administrador de Conta ou o Administrador do Enterprise. Para saber mais sobre como obter acesso a informações de faturação, veja [Gerir acesso às funções de utilização de faturação do Azure](manage-billing-access.md).

Se tiver um Contrato de Cliente da Microsoft, terá de ser Proprietário, Contribuidor, Leitor ou Gestor de faturas de um perfil de faturação para ver as informações de utilização e de faturação. Para saber mais sobre as funções de faturação dos Contratos de Cliente da Microsoft, veja [Funções e tarefas do perfil de faturação](understand-mca-roles.md#billing-profile-roles-and-tasks).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="download-your-azure-invoices-pdf"></a>Transferir as faturas do Azure (.pdf)

Para a maioria das subscrições, pode transferir a fatura a partir do portal do Azure. Se tiver um Contrato de Cliente da Microsoft, veja Transferir faturas de um perfil de faturação.

### <a name="download-invoices-for-an-individual-subscription"></a>Transferir faturas de uma subscrição individual

1. Selecione a sua subscrição na [página Subscrições](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) no portal do Azure como [utilizador com acesso às faturas](manage-billing-access.md).

2. Selecione **Faturas**.

    ![Captura de ecrã que mostra a opção Faturação e utilização](./media/download-azure-invoice-daily-usage-date/billingandusage.png)

3. Clique no botão de transferência para transferir uma cópia da sua fatura em PDF e, em seguida, selecione **Transferir fatura**. Se indicar **Não disponível** , veja [Por que motivo não vejo uma fatura para o último período de faturação?](#noinvoice)

    ![Captura de ecrã que mostra os períodos de faturação, a opção de transferência e os custos totais de cada período de faturação](./media/download-azure-invoice-daily-usage-date/downloadinvoice.png)

4. Também pode transferir uma discriminação diária das quantidades consumidas e despesas estimadas ao clicar em **Transferir csv**.

    ![Captura de ecrã que mostra a página Transferir fatura e utilização](./media/download-azure-invoice-daily-usage-date/usageandinvoice.png)

Para obter mais informações sobre a fatura, veja [Compreender a fatura do Microsoft Azure](../understand/review-individual-bill.md). Para obter ajuda para a gestão dos seus custos, veja [Analisar custos inesperados](../understand/analyze-unexpected-charges.md).

### <a name="download-invoices-for-a-microsoft-customer-agreement"></a>Transferir as faturas de um Contrato de Cliente da Microsoft

São geradas faturas para cada [perfil de faturação](../understand/mca-overview.md#billing-profiles) no Contrato de Cliente da Microsoft. Tem de ser Proprietário, Contribuidor, Leitor ou Gestor de faturas do perfil de faturação para transferir faturas do portal do Azure.

1. Procure **Cost Management + Faturação**.
2. Selecione um perfil de faturação.
3. Selecione **Faturas**.
4. Na grelha da fatura, localize a linha da fatura que quer transferir.
5. Clique no botão de transferência no final da linha.
6. No menu de contexto da transferência, selecione **Fatura**.

Se não vir nenhuma fatura para o último período de faturação, veja **Informações adicionais**. <!-- Fix this -->
### <a name="why-dont-i-see-an-invoice-for-the-last-billing-period"></a><a name="noinvoice"></a> Por que motivo não vejo uma fatura para o último período de faturação?

Podem existir vários motivos para não ver uma fatura:

- Passaram menos de 30 dias desde o dia em que subscreveu o Azure.

- A fatura ainda não foi gerada. Aguarde até ao fim do período de faturação.

- Não tem permissão para ver faturas. Se tiver um Contrato de Cliente da Microsoft, tem de ser Proprietário, Contribuidor, Leitor ou Gestor de faturas de um perfil de faturação. Para as outras subscrições, pode não ver as faturas antigas se não for o Administrador de Conta. Para saber mais sobre como obter acesso a informações de faturação, veja [Gerir acesso às funções de utilização de faturação do Azure](manage-billing-access.md).

- Se tiver uma Avaliação Gratuita ou um montante de crédito mensal com a sua subscrição que não excedeu, não receberá nenhuma fatura, a menos que tenha um Contrato de Cliente da Microsoft.

## <a name="get-your-invoice-in-email-pdf"></a>Obter a fatura por e-mail (.pdf)

Pode ativar e configurar destinatários adicionais para receber a fatura do Azure por e-mail. Esta funcionalidade pode não estar disponível para determinadas subscrições, como as ofertas de suporte, os Contratos Enterprise ou o Azure no Open. Se tiver um Contrato de Cliente da Microsoft, veja Obter as faturas do perfil de faturação por e-mail.

### <a name="get-your-subscriptions-invoices-in-email"></a>Obter as faturas da subscrição por e-mail

1. Selecione a sua subscrição na [página Subscrições](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Escolha Optar por receber para cada subscrição que tiver. Clique em **Faturas** e, em seguida, **Enviar a minha fatura por e-mail**.

    ![Captura de ecrã que mostra o fluxo de optar por receber](./media/download-azure-invoice-daily-usage-date/invoicesdeeplink01.png)

2. Clique em **Optar por receber** e aceite os termos.

    ![Captura de ecrã que mostra o passo 2 do fluxo de optar por receber](./media/download-azure-invoice-daily-usage-date/invoicearticlestep02.png)

3. Depois de aceitar o contrato, poderá configurar destinatários adicionais. Quando um destinatário é removido, o endereço de e-mail deixa de ser armazenado. Se mudar de ideia, terá de os adicionar novamente.

    ![Captura de ecrã que mostra o passo 3 do fluxo de optar por receber](./media/download-azure-invoice-daily-usage-date/invoicearticlestep03.png)

Se não receber um e-mail depois de seguir os passos, confirme que o endereço de e-mail está correto nas [preferências de comunicação do seu perfil](https://account.windowsazure.com/profile).

### <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>Optar por não receber as faturas da subscrição por e-mail

Pode optar por não receber as faturas por e-mail. Para tal, siga os passos acima e clique em **Optar por não receber faturas por e-mail**. Esta opção remove todos os endereços de e-mail que foram configurados para receber faturas por e-mail. Pode reconfigurar os destinatários se optar por voltar a receber faturas.

 ![Captura de ecrã que mostra o fluxo de optar por não receber](./media/download-azure-invoice-daily-usage-date/invoicearticlestep04.png)

### <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>Obter as faturas do Contrato de Cliente da Microsoft por e-mail

Se tiver um Contrato de Cliente da Microsoft, poderá optar por receber a fatura por e-mail. Todos os Proprietários, Contribuidores, Leitores e Gestores de faturas do perfil de faturação receberão a fatura por e-mail. Os leitores não podem atualizar a preferência de fatura por e-mail.

1. Procure **Cost Management + Faturação**.
1. Selecione um perfil de faturação.
1. Em **Definições** , selecione **Propriedades**.
1. Em **Fatura por E-mail** , selecione **Atualizar preferência de fatura por e-mail**.
1. Selecione **Optar por receber**.
1. Clique em **Atualizar**.

### <a name="opt-out-of-getting-your-billing-profile-invoices-in-email"></a>Optar por não receber as faturas do perfil de faturação por e-mail

Pode optar por não receber a fatura por e-mail. Para tal, siga os passos acima e clique em **Optar por não receber**. Todos os Proprietários, Contribuidores, Leitores e Gestores de faturas também deixarão de receber a fatura por e-mail. Se for um Leitor, não poderá alterar a preferência de fatura por e-mail.

## <a name="download-usage-in-azure-portal"></a>Transferir a utilização no portal do Azure

 Para a maioria das subscrições, siga estes passos para saber qual é a sua utilização diária:

1. Selecione a sua subscrição na [página Subscrições](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) no portal do Azure como [utilizador com acesso às faturas](manage-billing-access.md).

2. Selecione **Faturas**.

    ![Captura de ecrã que mostra a opção Faturação e utilização](./media/download-azure-invoice-daily-usage-date/billingandusage.png)

3. Clique no botão de transferência de um período de faturação que queira verificar.

4. Transfira uma discriminação diária das quantidades consumidas e despesas estimadas ao clicar em **Transferir csv**.  A preparação do ficheiro csv pode demorar alguns minutos.

### <a name="download-usage-for-ea-customers"></a>Transferir a utilização para clientes do EA

Para ver e transferir os dados de utilização como um cliente do EA, tem de ser um Administrador da Empresa, o Proprietário da Conta ou o Administrador do Departamento com a política de visualização de custos ativada.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Procure *Cost Management + Faturação*.
1. Se tiver acesso a várias contas de faturação, selecione o âmbito de faturação da sua conta de faturação do EA.
1. Selecione **Utilização + custos**.
1. Para o mês que pretende transferir, selecione **Transferir**.

### <a name="download-usage-for-your-microsoft-customer-agreement"></a>Transferir a utilização do Contrato de Cliente da Microsoft

Para ver e transferir dados de utilização de um perfil de faturação, tem de ser Proprietário, Contribuidor, Leitor ou Gestor de faturas do perfil de faturação.

#### <a name="download-usage-for-billed-charges"></a>Transferir utilização para custos faturados

1. Procure **Cost Management + Faturação**.
2. Selecione um perfil de faturação.
3. Selecione **Faturas**.
4. Na grelha da fatura, localize a linha da fatura que corresponde à utilização que quer transferir.
5. Clique nas reticências (`...`) no final da linha.
6. No menu de contexto da transferência, selecione **Utilização e custos do Azure**.

#### <a name="download-usage-for-open-charges"></a>Transferir a utilização dos custos em aberto

Também pode transferir a utilização acumulada até hoje para o período de faturação atual, o que significa que os custos não foram ainda faturados.

1. Procure **Cost Management + Faturação**.
2. Selecione um perfil de faturação.
3. No painel **Descrição geral** , clique em **Transferir utilização e custos do Azure**.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso ao Contrato de Cliente da Microsoft
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a fatura e os custos, veja:

- [Understand your bill for Microsoft Azure](../understand/review-individual-bill.md) (Compreender a sua fatura do Microsoft Azure)
- [Compreender os termos na fatura do Azure](../understand/understand-invoice.md)
- [Compreender os termos na utilização diária do Microsoft Azure](../understand/understand-usage.md)
- [Ver os preços do Azure da sua organização](ea-pricing.md)

Se tiver um Contrato de Cliente da Microsoft, veja:

- [Compreender os custos na fatura para o seu perfil de faturação](../understand/review-customer-agreement-bill.md)
- [Compreender os termos na fatura para o seu perfil de faturação](../understand/mca-understand-your-invoice.md)
- [Compreender o ficheiro de utilização e de custos do Azure para o seu perfil de faturação](../understand/mca-understand-your-usage.md)
- [Ver e transferir os documentos fiscais para o seu perfil de faturação](../understand/mca-download-tax-document.md)
- [Ver os preços do Azure da sua organização](ea-pricing.md)
