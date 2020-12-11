---
title: Analisar a fatura do Contrato de Cliente Microsoft – Azure
description: Saiba como analisar a faturação e a utilização de recursos e como verificar os custos na fatura do Contrato de Cliente Microsoft.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: tutorial
ms.date: 12/02/2020
ms.author: banders
ms.openlocfilehash: b5de81f5eaf76c16c1ea8fc78075c4567c77aeee
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96531989"
---
# <a name="tutorial-review-your-microsoft-customer-agreement-invoice"></a>Tutorial: Analisar a fatura do Contrato de Cliente Microsoft

Pode rever os custos descritos na fatura ao analisar cada transação. Na conta de faturação de um Contrato de Cliente da Microsoft, é gerada uma fatura todos os meses para cada perfil de faturação. A fatura inclui todos os custos do mês anterior. Pode ver as faturas no portal do Azure e comparar os custos com o ficheiro de detalhes de utilização.

Este tutorial aplica-se apenas aos clientes do Azure com um Contrato de Cliente Microsoft.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Analisar as transações faturadas no portal do Azure
> * Rever custos pendentes para estimar a próxima fatura
> * Analisar os custos de utilização do Azure

## <a name="prerequisites"></a>Pré-requisitos

Precisa de ter uma conta de faturação para um Contrato de Cliente Microsoft.

Precisa de ter acesso a um Contrato de Cliente Microsoft. Tem de ser Proprietário, Contribuidor, Leitor ou Gestor de faturas de um perfil de faturação para ver as informações de utilização e de faturação. Para saber mais sobre as funções de faturação dos Contratos de Cliente da Microsoft, veja [Funções e tarefas do perfil de faturação](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks).

Passaram mais de 30 dias desde o dia em que subscreveu o Azure. O Azure cobra-o no final do período da fatura.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

- Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso ao Contrato de Cliente da Microsoft

Verifique o tipo de contrato para determinar se tem acesso a uma conta de faturação de um Contrato de Cliente Microsoft.

No portal do Azure, escreva *cost management + faturação* na caixa de pesquisa e, em seguida, selecione **Cost Management + Faturação**.

![Captura de ecrã que mostra a pesquisa da opção cost management + faturação no portal do Azure.](./media/review-customer-agreement-bill/billing-search-cost-management-billing.png)

Se tiver acesso a apenas um âmbito de faturação, selecione **Propriedades** no lado esquerdo. Tem acesso a uma conta de faturação de um Contrato de Cliente Microsoft se o tipo de conta de faturação for **Contrato de Cliente Microsoft**.

![Captura de ecrã que mostra o Contrato de Cliente Microsoft na página de propriedades](./media/review-customer-agreement-bill/billing-mca-property.png)

Se tiver acesso a vários âmbitos de faturação, verifique o tipo na coluna da conta de faturação. Tem acesso a uma conta de faturação de um Contrato de Cliente Microsoft se o tipo de conta de faturação de qualquer um dos âmbitos for **Contrato de Cliente Microsoft**.

Na página dos âmbitos de faturação, selecione **Âmbitos de faturação** e a conta de faturação que seria utilizada para pagar a utilização das subscrições. A conta de faturação deverá ser do tipo **Contrato de Cliente Microsoft**.

![Captura de ecrã que mostra o Contrato de Cliente Microsoft na página com a lista de contas de faturação](./media/review-customer-agreement-bill/billing-mca-in-the-list.png)

## <a name="review-invoiced-transactions-in-the-azure-portal"></a>Analisar as transações faturadas no portal do Azure

No portal do Azure, selecione **Todas as transações** no lado esquerdo da página. Consoante o seu acesso, poderá ter de selecionar uma conta de faturação, um perfil de faturação ou uma secção de fatura e, em seguida, selecionar **Todas as transações**.

A página Todas as transações apresenta as seguintes informações:

![Captura de ecrã a mostrar a lista de transações cobradas](./media/review-customer-agreement-bill/mca-billed-transactions-list.png)

|Coluna  |Definição  |
|---------|---------|
|Date     | A data da transação  |
|ID da fatura     | O identificador da fatura onde a transação foi cobrada. Se submeter um pedido de suporte, partilhe o ID com o suporte do Azure para acelerar o pedido |
|Tipo de transação     |  A descrição do tipo de transação, como uma compra, um cancelamento ou custos de utilização  |
|Família de produtos     | A categoria do produto, como uma computação de máquinas virtuais ou uma base de dados SQL do Azure|
|SKU do produto     | Um código exclusivo que identifica a instância do seu produto |
|Montante     |  O montante da transação      |
|Secção de fatura     | A transação é apresentada nesta secção da fatura do perfil de faturação |
|Perfil de faturação     | A transação é apresentada nesta fatura do perfil de faturação |

Procure o ID de uma fatura para filtrar as transações da fatura.

### <a name="view-transactions-by-invoice-sections"></a>Ver transações por secções de fatura

As secções de fatura ajudam-no a gerir os custos de uma fatura de um perfil de faturação. Para obter mais informações. Quando uma fatura é gerada, os custos de todas as secções no perfil de faturação são apresentados na fatura.

A imagem seguinte mostra os custos da secção de fatura Departamento de Contabilidade numa fatura de exemplo.

![Imagem de exemplo a mostrar os detalhes por informações de secção de fatura](./media/review-customer-agreement-bill/invoicesection-details.png)

Quando tiver identificado os custos de uma secção de fatura, pode ver as transações no portal do Azure para compreender esses custos.

Aceda à página Todas as transações no portal do Azure para ver as transações de uma fatura.

Filtre o nome da secção de fatura para ver as transações.

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>Rever custos pendentes para estimar a próxima fatura

Na conta de faturação de um Contrato de Cliente Microsoft, os custos são estimados e considerados como pendentes até que sejam faturados. Pode ver os custos pendentes no portal do Azure para estimar a sua próxima fatura. Os custos pendentes são estimados e não incluem impostos. Os custos reais na sua próxima fatura vão variar de acordo com os custos pendentes.

### <a name="view-summary-of-pending-charges"></a>Ver um resumo dos custos pendentes

Inicie sessão no [portal do Azure](https://portal.azure.com).

Selecione um perfil de faturação. Dependendo do seu acesso, poderá ter de selecionar uma conta de faturação. Na conta de faturação, selecione **Perfis de faturação** e, em seguida, selecione um perfil de faturação.

Selecione o separador **Resumo** na parte superior do ecrã.

A secção Encargos apresenta os custo do mês acumulados até à data atual e do último mês.

![Captura de ecrã que mostra o resumo de um perfil de faturação.](./media/review-customer-agreement-bill/mca-billing-profile-summary.png)

Os custos do mês acumuladas até à data são os custos pendentes relativamente ao mês atual e estes são cobrados quando a fatura é gerada para esse mês. Se a fatura do último mês ainda não tiver sido gerada, os custos desse mês também estarão pendentes e serão apresentados na sua próxima fatura.

### <a name="view-pending-transactions"></a>Ver transações pendentes

Ao identificar custos pendentes, conseguirá compreender os custos ao analisar individualmente cada transação que contribuiu para esses custos. Neste momento, os custos de utilização pendentes não serão apresentados na página Todas as transações. Pode ver os custos de utilização pendentes na página Subscrições do Azure.

Inicie sessão no [portal do Azure](https://portal.azure.com).

No portal do Azure, escreva *cost management + faturação* na caixa de pesquisa e, em seguida, selecione **Cost Management + Faturação**.

Selecione um perfil de faturação. Dependendo do seu acesso, poderá ter de selecionar uma conta de faturação. Na conta de faturação, selecione **Perfis de faturação** e, em seguida, selecione um perfil de faturação.

Selecione **Todas as transações** no lado esquerdo da página.

Escreva a palavra *pendentes* na caixa de pesquisa. Utilize o filtro **Período de tempo** para ver os custos pendentes do mês até à data ou do último mês.

![Captura de ecrã a mostrar a lista de transações pendentes](./media/review-customer-agreement-bill/mca-pending-transactions-list.png)

### <a name="view-pending-usage-charges"></a>Ver custos de utilização pendentes

Inicie sessão no [portal do Azure](https://portal.azure.com).

No portal do Azure, escreva *cost management + faturação* na caixa de pesquisa e, em seguida, selecione **Cost Management + Faturação**.

Selecione um perfil de faturação. Dependendo do seu acesso, poderá ter de selecionar uma conta de faturação. Na conta de faturação, selecione **Perfis de faturação** e, em seguida, selecione um perfil de faturação.

Selecione **Todas as subscrições** no lado esquerdo da página.

A página Subscrições do Azure apresenta os custos do mês até à data e do último mês para cada subscrição no perfil de faturação. Os custos do mês acumuladas até à data são os custos pendentes relativamente ao mês atual e estes são cobrados quando a fatura é gerada para esse mês. Se a fatura do último mês ainda não foi gerada, os custos desse mês também estarão pendentes.

![Captura de ecrã que mostra subscrições com cobranças do mês acumuladas até hoje e as cobranças do último mês.](./media/review-customer-agreement-bill/mca-billing-profile-subscriptions-list.png)

## <a name="analyze-your-azure-usage-charges"></a>Analisar os custos de utilização do Azure

Utilize o ficheiro CSV Utilização e custos do Azure para analisar os seus custos de utilização. Pode transferir o ficheiro para uma fatura ou para custos pendentes.

### <a name="download-your-invoice-and-usage-details"></a>Transferir a fatura e os detalhes de utilização

Dependendo do acesso, pode precisar de selecionar uma conta de faturação ou um perfil de faturação em Cost Management + Faturação. No menu esquerdo, selecione **Faturas** em **Faturação**. Na grelha da fatura, localize a linha da fatura que quer transferir. Selecione o símbolo de transferência ou as reticências (...) no final da linha. Na caixa **Transferir**, transfira os detalhes de utilização e a fatura.

### <a name="view-detailed-usage-by-invoice-section"></a>Ver a secção de utilização detalhada por fatura

Pode filtrar o ficheiro Utilização e custos do Azure para reconciliar os custos de utilização das suas secções de fatura.

As informações seguintes explicam como pode reconciliar os custos de computação na secção de fatura Departamento de Contabilidade:

![Imagem de exemplo a mostrar os detalhes por informações de secção de fatura](./media/review-customer-agreement-bill/invoicesection-details.png)

| PDF da fatura | Ficheiro CSV Utilização e custos do Azure |
| --- | --- |
|Departamento de Contabilidade |invoiceSectionName |
|Custos de Utilização – Plano do Microsoft Azure |productOrderName |
|Computação |serviceFamily |

Filtre a coluna **invoiceSectionName** no ficheiro CSV para **Accounting Dept** (Departamento de Contabilidade). Em seguida, filtre a coluna **productOrderName** no ficheiro CSV para **Microsoft Azure Plan** (Plano do Microsoft Azure). Depois, filtre a coluna **serviceFamily** no ficheiro CSV para **Microsoft.Compute**.

![Captura de ecrã a mostrar o ficheiro de utilização e custos filtrado por secção de fatura](./media/review-customer-agreement-bill/billing-usage-file-filtered-by-invoice-section.png)

### <a name="view-detailed-usage-by-subscription"></a>Ver utilização detalhada por subscrição

Pode filtrar o ficheiro CSV Utilização e custos do Azure para reconciliar os custos de utilização das suas secções de fatura. Ao identificar os custos de uma subscrição, utilize o ficheiro CSV Utilização e custos do Azure para analisar os custos.

A imagem seguinte mostra a lista de subscrições no portal do Azure.

![Captura de ecrã que mostra a lista de subscrições no portal do Azure com uma subscrição em destaque.](./media/review-customer-agreement-bill/mca-billing-profile-subscriptions-list-highlighted.png)

Filtre a coluna **subscriptionName** no ficheiro CSV Utilização e custos do Azure para **WA_Subscription** de modo a ver os custos de utilização detalhados para o campo WA_Subscription.

![Captura de ecrã a mostrar o ficheiro de utilização e custos filtrado por subscrição](./media/review-customer-agreement-bill/billing-usage-file-filtered-by-subscription.png)

## <a name="pay-your-bill"></a>Pagar a sua fatura

As instruções para pagar a sua fatura são apresentadas na parte inferior da fatura. [Saiba como pagar](mca-understand-your-invoice.md#how-to-pay).

Se já pagou a sua fatura, pode verificar o estado do pagamento na página Faturas no portal do Azure.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Analisar as transações faturadas no portal do Azure
> * Rever custos pendentes para estimar a próxima fatura
> * Analisar os custos de utilização do Azure

Conclua o início rápido para começar a utilizar a análise de custos.

> [!div class="nextstepaction"]
> [Começar a analisar os custos](../costs/quick-acm-cost-analysis.md)
