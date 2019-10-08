---
title: Compreender os custos na fatura do Contrato de Cliente da Microsoft – Azure
description: Saiba como ler e compreender os custos descritos na sua fatura.
author: jureid
manager: jureid
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 8ab6cf061531a1ef3c72b2f36d25932c7498d291
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71345281"
---
# <a name="understand-charges-on-your-microsoft-customer-agreement-invoice"></a>Compreender os custos na fatura do Contrato de Cliente da Microsoft

Pode compreender os custos descritos na sua fatura ao analisar cada transação. Na conta de faturação de um Contrato de Cliente da Microsoft, é gerada uma fatura todos os meses para cada perfil de faturação. A fatura inclui todos os custos do mês anterior. Pode ver as suas faturas no portal do Azure. Para obter mais informações, veja [Transferir as faturas para um Contrato de Cliente da Microsoft](billing-download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement).

Este artigo aplica-se a uma conta de faturação para um Contrato de Cliente da Microsoft. [Verificar se tem acesso a um Contrato de Cliente da Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="view-transactions-for-an-invoice-in-the-azure-portal"></a>Ver as transações de uma fatura no portal do Azure

1. Inicie sessão no [portal do Azure](https://www.azure.com).

2. Escreva **Gestão de Custos + Faturação**.

    ![Captura de ecrã a mostrar a pesquisa da opção Gestão de Custos + Faturação no portal do Azure](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Selecione **Todas as transações** no lado esquerdo da página. Consoante o seu acesso, poderá ter de selecionar uma conta de faturação, um perfil de faturação ou uma secção de fatura e, em seguida, selecionar **Todas as transações**.

4. A página Todas as transações apresenta as seguintes informações:

    ![Captura de ecrã a mostrar a lista de transações cobradas](./media/billing-understand-your-bill-mca/mca-billed-transactions-list.png)

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

5. Procure ID da fatura para filtrar as transações da fatura.

### <a name="view-transactions-by-invoice-sections"></a>Ver transações por secções de fatura

As secções de fatura ajudam-no a gerir os custos de uma fatura de um perfil de faturação. Para obter mais informações, veja [Compreender a secção de fatura](billing-mca-overview.md#invoice-sections). Quando uma fatura é gerada, os custos de todas as secções no perfil de faturação são apresentados na fatura.

A imagem seguinte mostra os custos da secção de fatura Departamento de Contabilidade numa fatura de exemplo.

![Imagem de exemplo a mostrar os detalhes por informações de secção de fatura](./media/billing-understand-your-bill-mca/invoicesection-details.png)

Quando tiver identificado os custos de uma secção de fatura, pode ver as transações no portal do Azure para compreender esses custos.

1. Aceda à página Todas as transações no portal do Azure para ver as transações de uma fatura. Para obter mais informações, veja a secção [Ver as transações de uma fatura no portal do Azure](#view-transactions-for-an-invoice-in-the-azure-portal).

2. Filtre o nome da secção de fatura para ver as transações.

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>Rever custos pendentes para estimar a próxima fatura

Na conta de faturação de um Contrato de Cliente da Microsoft, os custos são estimados e considerados como pendentes até que sejam faturados. Pode ver os custos pendentes no portal do Azure para estimar a sua próxima fatura. Os custos pendentes são estimados e não incluem impostos. Os custos reais na sua próxima fatura vão variar de acordo com os custos pendentes.

### <a name="view-summary-of-pending-charges"></a>Ver um resumo dos custos pendentes

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Escreva **Gestão de Custos + Faturação**.

   ![Captura de ecrã a mostrar a pesquisa da opção Gestão de Custos + Faturação no portal do Azure](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Selecione um perfil de faturação. Dependendo do seu acesso, poderá ter de selecionar uma conta de faturação. Na conta de faturação, selecione **Perfis de faturação** e, em seguida, selecione um perfil de faturação.

4. Selecione o separador **Resumo** na parte superior do ecrã.

5. A secção Encargos apresenta os custo do mês acumulados até à data atual e do último mês.

   ![Captura de ecrã a mostrar a pesquisa da opção Gestão de Custos + Faturação no portal do Azure](./media/billing-understand-your-bill-mca/mca-billing-profile-summary.png)

Os custos do mês acumuladas até à data são os custos pendentes relativamente ao mês atual e estes são cobrados quando a fatura é gerada para esse mês. Se a fatura do último mês ainda não tiver sido gerada, os custos desse mês também estarão pendentes e serão apresentados na sua próxima fatura.

### <a name="view-pending-transactions"></a>Ver transações pendentes

Ao identificar custos pendentes, conseguirá compreender os custos ao analisar individualmente cada transação que contribuiu para esses custos. Neste momento, os custos de utilização pendentes não serão apresentados na página Todas as transações. Pode ver os custos de utilização pendentes na página Subscrições do Azure. Para obter mais informações, veja [Ver custos de utilização pendentes](#view-pending-usage-charges).

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Escreva **Gestão de Custos + Faturação**.

   ![Captura de ecrã a mostrar a pesquisa da opção Gestão de Custos + Faturação no portal do Azure](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Selecione um perfil de faturação. Dependendo do seu acesso, poderá ter de selecionar uma conta de faturação. Na conta de faturação, selecione **Perfis de faturação** e, em seguida, selecione um perfil de faturação.

4. Selecione **Todas as transações** no lado esquerdo da página.

5. Escreva a palavra *pendentes* na caixa de pesquisa. Utilize o filtro **Período de tempo** para ver os custos pendentes do mês até à data ou do último mês.

   ![Captura de ecrã a mostrar a lista de transações pendentes](./media/billing-understand-your-bill-mca/mca-pending-transactions-list.png)

### <a name="view-pending-usage-charges"></a>Ver custos de utilização pendentes

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Escreva *Gestão de Custos + Faturação*.

   ![Captura de ecrã a mostrar a pesquisa da opção Gestão de Custos + Faturação no portal do Azure](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Selecione um perfil de faturação. Dependendo do seu acesso, poderá ter de selecionar uma conta de faturação. Na conta de faturação, selecione **Perfis de faturação** e, em seguida, selecione um perfil de faturação.

4. Selecione **Todas as subscrições** no lado esquerdo da página.

5. A página Subscrições do Azure apresenta os custos do mês até à data e do último mês para cada subscrição no perfil de faturação. Os custos do mês acumuladas até à data são os custos pendentes relativamente ao mês atual e estes são cobrados quando a fatura é gerada para esse mês. Se a fatura do último mês ainda não foi gerada, os custos desse mês também estarão pendentes.

    ![Captura de ecrã a mostrar a lista de subscrições do Azure do perfil de faturação](./media/billing-understand-your-bill-mca/mca-billing-profile-subscriptions-list.png)

## <a name="analyze-your-azure-usage-charges"></a>Analisar os custos de utilização do Azure

Utilize o ficheiro CSV Utilização e custos do Azure para analisar os seus custos de utilização. Pode transferir o ficheiro para uma fatura ou para custos pendentes. Para obter mais informações, veja [Obter a faturação e os dados de utilização diária do Azure](billing-download-azure-invoice-daily-usage-date.md).

### <a name="view-detailed-usage-by-invoice-section"></a>Ver a secção de utilização detalhada por fatura

Pode filtrar o ficheiro Utilização e custos do Azure para reconciliar os custos de utilização das suas secções de fatura.

Os passos seguintes explicam como pode reconciliar os custos de computação na secção de fatura Departamento de Contabilidade:

![Imagem de exemplo a mostrar os detalhes por informações de secção de fatura](./media/billing-understand-your-bill-mca/invoicesection-details.png)

 | PDF da fatura | Ficheiro CSV Utilização e custos do Azure |
 | --- | --- |
 |Departamento de Contabilidade |invoiceSectionName |
 |Custos de Utilização – Plano do Microsoft Azure |productOrderName |
 |Computação |serviceFamily |

1. Filtre a coluna **invoiceSectionName** no ficheiro CSV para **Accounting Dept** (Departamento de Contabilidade).
2. Filtre a coluna **productOrderName** no ficheiro CSV para **Microsoft Azure Plan** (Plano do Microsoft Azure).
3. Filtre a coluna **serviceFamily** no ficheiro CSV para **Microsoft.Compute**.

![Captura de ecrã a mostrar o ficheiro de utilização e custos filtrado por secção de fatura](./media/billing-understand-your-bill-mca/billing-usage-file-filtered-by-invoice-section.png)


### <a name="view-detailed-usage-by-subscription"></a>Ver utilização detalhada por subscrição

Pode filtrar o ficheiro CSV Utilização e custos do Azure para reconciliar os custos de utilização das suas secções de fatura. Para ver todas as subscrições num perfil de faturação, veja a secção [Ver custos de utilização pendentes](#view-pending-usage-charges).

Ao identificar os custos de uma subscrição, utilize o ficheiro CSV Utilização e custos do Azure para analisar os custos.

A imagem seguinte mostra a lista de subscrições no portal do Azure.

![Captura de ecrã a mostrar a lista de subscrições do Azure do perfil de faturação](./media/billing-understand-your-bill-mca/mca-billing-profile-subscriptions-list-highlighted.png)

Filtre a coluna **subscriptionName** no ficheiro CSV Utilização e custos do Azure para **WA_Subscription** de modo a ver os custos de utilização detalhados para o campo WA_Subscription.

![Captura de ecrã a mostrar o ficheiro de utilização e custos filtrado por subscrição](./media/billing-understand-your-bill-mca/billing-usage-file-filtered-by-subscription.png)

## <a name="pay-your-bill"></a>Pagar a sua fatura

As instruções para pagar a sua fatura são apresentadas na parte inferior da fatura. [Saiba como pagar](billing-mca-understand-your-invoice.md#how-to-pay).

Se já pagou a sua fatura, pode verificar o estado do pagamento na página Faturas no portal do Azure.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso ao Contrato de Cliente da Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a sua fatura e a sua utilização em detalhe, veja:

- [Como obter a faturação e os dados de utilização diária do Azure](billing-download-azure-invoice-daily-usage-date.md)
- [Compreender os termos na fatura do Contrato de Cliente da Microsoft](billing-mca-understand-your-invoice.md)
- [Compreender os termos no ficheiro CSV de utilização do Contrato de Cliente da Microsoft](billing-mca-understand-your-usage.md)
