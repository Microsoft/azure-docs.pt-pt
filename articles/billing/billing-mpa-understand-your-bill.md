---
title: Compreender os custos na fatura do Contrato de Parceiro da Microsoft – Azure
description: Saiba como ler e compreender os custos descritos na sua fatura.
author: jureid
manager: jureid
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2019
ms.author: banders
ms.openlocfilehash: 9994ef6da379d0063930912a4d198b81623ddbe8
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74223702"
---
# <a name="understand-charges-on-your-microsoft-partner-agreement-invoice"></a>Compreender os custos na fatura do Contrato de Parceiro da Microsoft

 Na conta de faturação de um Contrato de Parceiro da Microsoft, é gerada uma fatura todos os meses para cada perfil de faturação. A fatura inclui todos os custos do cliente relativos ao mês anterior. Pode compreender os custos descritos na fatura ao analisar cada transação no portal do Azure. Também pode ver as faturas no portal do Azure. Para obter mais informações, veja [como transferir faturas do portal do Azure ](billing-download-azure-invoice.md).

Este artigo aplica-se a uma conta de faturação de um Contrato de Parceiro da Microsoft. [Verifique se tem acesso a um Contrato de Parceiro da Microsoft](#check-access-to-a-microsoft-partner-agreement).

## <a name="view-transactions-for-an-invoice-in-the-azure-portal"></a>Ver as transações de uma fatura no portal do Azure

1. Inicie sessão no [portal do Azure](https://www.azure.com).

2. Procure *Cost Management + Faturação*.

   <!--  ![Screenshot that shows Azure portal search for cost management + billing](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png) -->

3. Selecione **Todas as transações** no lado esquerdo da página. Consoante o seu acesso, poderá ter de selecionar uma conta de faturação, um perfil de faturação ou um cliente e, em seguida, selecionar **Todas as transações**.

4. A página Todas as transações apresenta as seguintes informações:

    <!-- ![Screenshot that shows the billed transactions list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-all-transactions.png) -->

    |Coluna  |Definição  |
    |---------|---------|
    |Date     | A data da transação  |
    |ID da fatura     | O identificador da fatura onde a transação foi cobrada. Se submeter um pedido de suporte, partilhe o ID com o suporte do Azure para acelerar o pedido |
    |Tipo de transação     |  A descrição do tipo de transação, como uma compra, um cancelamento ou custos de utilização  |
    |Família de produtos     | A categoria do produto, como uma computação de máquinas virtuais ou uma base de dados SQL do Azure|
    |SKU do produto     | Um código exclusivo que identifica a instância do seu produto |
    |Montante     |  O montante da transação      |
    |Perfil de faturação     | A transação é apresentada nesta fatura do perfil de faturação |

5. Procure ID da fatura para filtrar as transações da fatura.

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>Rever custos pendentes para estimar a próxima fatura

Os custos são estimados e considerados pendentes até que sejam faturados. Pode visualizar os custos pendentes para o seu perfil de faturação do Contrato de Parceiro da Microsoft no portal do Azure para calcular a próxima fatura. Os custos pendentes são estimados e não incluem impostos. Os custos reais na sua próxima fatura vão variar de acordo com os custos pendentes.

### <a name="view-pending-transactions"></a>Ver transações pendentes

Ao identificar custos pendentes, conseguirá compreender os custos ao analisar individualmente cada transação que contribuiu para esses custos. Neste momento, os custos de utilização pendentes não serão apresentados na página Todas as transações. Pode ver os custos de utilização pendentes na página Subscrições do Azure. Para obter mais informações, veja [Ver custos de utilização pendentes](#view-pending-usage-charges).

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Procure *Cost Management + Faturação*.

   <!-- ![Screenshot that shows Azure portal search for cost management + billing](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png) -->

3. Selecione um perfil de faturação. Dependendo do seu acesso, poderá ter de selecionar uma conta de faturação. Na conta de faturação, selecione **Perfis de faturação** e, em seguida, selecione um perfil de faturação.

4. Selecione **Todas as transações** no lado esquerdo da página.

5. Escreva a palavra *pendentes* na caixa de pesquisa. Utilize o filtro **Período de tempo** para ver os custos pendentes do mês até à data ou do último mês.

   <!-- ![Screenshot that shows the pending transactions list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-pending-transactions.png) -->

### <a name="view-pending-charges-by-customer"></a>Ver os custos pendentes por cliente

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Procure *Cost Management + Faturação*.

3. Selecione um perfil de faturação. Dependendo do seu acesso, poderá ter de selecionar uma conta de faturação. Na conta de faturação, selecione **Perfis de faturação** e, em seguida, selecione um perfil de faturação.

4. Selecione **Clientes** no lado esquerdo da página.

    <!-- ![screenshot of billing profile customers list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-customers.png) -->

5. A página Clientes apresenta os custos do mês até à data e do último mês para cada cliente associado ao perfil de faturação. Os custos do mês acumuladas até à data são os custos pendentes relativamente ao mês atual e estes são cobrados quando a fatura é gerada para esse mês. Se a fatura do último mês ainda não foi gerada, os custos desse mês também estarão pendentes.

### <a name="view-pending-usage-charges"></a>Ver custos de utilização pendentes

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Procure *Cost Management + Faturação*.

3. Selecione um perfil de faturação. Dependendo do seu acesso, poderá ter de selecionar uma conta de faturação. Na conta de faturação, selecione **Perfis de faturação** e, em seguida, selecione um perfil de faturação.

4. Selecione **Subscrições do Azure** no lado esquerdo da página.

5. A página Subscrições do Azure apresenta os custos do mês até à data e do último mês para cada subscrição no perfil de faturação. Os custos do mês acumuladas até à data são os custos pendentes relativamente ao mês atual e estes são cobrados quando a fatura é gerada para esse mês. Se a fatura do último mês ainda não foi gerada, os custos desse mês também estarão pendentes.

<!--     ![Screenshot that shows the Azure subscriptions list for MPA billing profile](./media/billing-mpa-understand-your-bill/mpa-billing-profile-subscriptions-list.png) -->

## <a name="analyze-your-azure-usage-charges"></a>Analisar os custos de utilização do Azure

Utilize o ficheiro CSV Utilização e custos do Azure para analisar os seus custos de utilização. Pode [transferir o CSV de utilização e custos do Azure do portal do Azure](billing-download-azure-daily-usage.md).

Pode filtrar o ficheiro de utilização e custos do Azure para reconciliar os custos de utilização de cada produto listado no pdf da fatura. Para visualizar os custos detalhados da utilização de um produto específico, filtre a coluna **produto** no ficheiro CSV de utilização e custos do Azure de modo a incluir apenas o nome do produto.

Também pode filtrar a coluna **customerName**  no ficheiro CSV de utilização e custos do Azure para ver os custos diários de utilização de cada um dos seus clientes. Se quiser visualizar os custos diários de utilização por subscrição do Azure, filtre a coluna **subscriptionName** .


## <a name="pay-your-bill"></a>Pagar a sua fatura

As instruções para pagar a sua fatura são apresentadas na parte inferior da fatura. Pode pagar por transferência bancária ou por cheque no prazo de 60 dias da data da fatura.

Se já pagou a sua fatura, pode verificar o estado do pagamento na página Faturas no portal do Azure.

## <a name="check-access-to-a-microsoft-partner-agreement"></a>Verificar o acesso a um Contrato de Parceiro da Microsoft
[!INCLUDE [billing-check-mpa](../../includes/billing-check-mpa.md)]

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a sua fatura e a sua utilização em detalhe, veja:
