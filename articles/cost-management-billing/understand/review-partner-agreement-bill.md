---
title: Analisar a fatura do Contrato de Parceiro Microsoft - Azure
description: Saiba como analisar a faturação e a utilização de recursos e como verificar os custos na fatura do Contrato de Parceiro Microsoft.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: tutorial
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 9e5ecc67fe86afa15c66d175f0705818154588bf
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684359"
---
# <a name="tutorial-review-your-microsoft-partner-agreement-invoice"></a>Tutorial: Analisar a fatura do Contrato de Parceiro Microsoft

 Na conta de faturação de um Contrato de Parceiro da Microsoft, é gerada uma fatura todos os meses para cada perfil de faturação. A fatura inclui todos os custos do cliente relativos ao mês anterior. Pode compreender os custos descritos na fatura ao analisar cada transação no portal do Azure. Também pode ver as faturas no portal do Azure e comparar os custos com o ficheiro de detalhes de utilização.

Para obter mais informações, veja [como transferir faturas do portal do Azure ](download-azure-invoice.md).

Este tutorial aplica-se apenas aos parceiros do Azure com um Contrato de Parceiro Microsoft.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Analisar as transações faturadas no portal do Azure
> * Rever custos pendentes para estimar a próxima fatura
> * Analisar os custos de utilização do Azure

## <a name="prerequisites"></a>Pré-requisitos

Precisa de ter acesso a uma conta de faturação de um Contrato de Parceiro Microsoft.

Passaram mais de 30 dias desde o dia em que subscreveu o Azure. O Azure cobra-o no final do período da fatura.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

- Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso ao Contrato de Cliente da Microsoft

Verifique o tipo de contrato para determinar se tem acesso a uma conta de faturação de um Contrato de Parceiro da Microsoft.

No portal do Azure, escreva *cost management + faturação* na caixa de pesquisa e, em seguida, selecione **Cost Management + Faturação**.

![Captura de ecrã a mostrar a pesquisa no portal do Azure](./media/review-partner-agreement-bill/billing-search-cost-management-billing.png)

Se tiver acesso a apenas um âmbito de faturação, selecione **Propriedades** no lado esquerdo. Terá acesso a uma conta de faturação de um Contrato de Parceiro da Microsoft se o tipo de conta de faturação for **Contrato de Parceiro da Microsoft**.

![Captura de ecrã que mostra o Contrato de Parceiro Microsoft na página de propriedades](./media/review-partner-agreement-bill/billing-account-properties-partner-agreement.png)

Se tiver acesso a vários âmbitos de faturação, verifique o tipo na coluna da conta de faturação. Terá acesso a uma conta de faturação de um Contrato de Parceiro da Microsoft se o tipo de conta de faturação de qualquer um dos âmbitos for **Contrato de Parceiro da Microsoft**.

![Captura de ecrã que mostra o Contrato de Parceiro Microsoft na página com a lista de contas de faturação](./media/review-partner-agreement-bill/mpa-in-the-list.png)

## <a name="review-invoiced-transactions-in-the-azure-portal"></a>Analisar as transações faturadas no portal do Azure

Em Cost Management + Faturação, selecione **Todas as transações** no lado esquerdo da página. Consoante o seu acesso, poderá ter de selecionar uma conta de faturação, um perfil de faturação ou um cliente e, em seguida, selecionar **Todas as transações**.

A página Todas as transações apresenta as seguintes informações:

![Captura de ecrã a mostrar a lista de transações cobradas](./media/review-partner-agreement-bill/all-transactions.png)

|Coluna  |Definição  |
|---------|---------|
|Date     | A data da transação  |
|ID da fatura     | O identificador da fatura onde a transação foi cobrada. Se submeter um pedido de suporte, partilhe o ID com o suporte do Azure para acelerar o pedido |
|Tipo de transação     |  A descrição do tipo de transação, como uma compra, um cancelamento ou custos de utilização  |
|Família de produtos     | A categoria do produto, como uma computação de máquinas virtuais ou uma Base de Dados SQL do Azure|
|SDKU do produto     | Um código exclusivo que identifica a instância do seu produto |
|Montante     |  O montante da transação      |
|Perfil de faturação     | A transação é apresentada nesta fatura do perfil de faturação |

Procure o ID de uma fatura para filtrar as transações da fatura.

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>Rever custos pendentes para estimar a próxima fatura

Os custos são estimados e considerados pendentes até que sejam faturados. Pode visualizar os custos pendentes para o seu perfil de faturação do Contrato de Parceiro da Microsoft no portal do Azure para calcular a próxima fatura. Os custos pendentes são estimados e não incluem impostos. Os custos reais na sua próxima fatura vão variar de acordo com os custos pendentes.

### <a name="view-pending-transactions"></a>Ver transações pendentes

Ao identificar custos pendentes, conseguirá compreender os custos ao analisar individualmente cada transação que contribuiu para esses custos. Neste momento, os custos de utilização pendentes não serão apresentados na página Todas as transações. Pode ver os custos de utilização pendentes na página Subscrições do Azure.

Em Cost Management + Faturação, selecione um perfil de faturação. Dependendo do seu acesso, poderá ter de selecionar uma conta de faturação. Na conta de faturação, selecione **Perfis de faturação** e, em seguida, selecione um perfil de faturação.

Selecione **Todas as transações** no lado esquerdo da página.

Escreva a palavra *pendentes* na caixa de pesquisa. Utilize o filtro **Período de tempo** para ver os custos pendentes do mês até à data ou do último mês.

<!-- ![Screenshot that shows the pending transactions list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-pending-transactions.png) -->

### <a name="view-pending-charges-by-customer"></a>Ver os custos pendentes por cliente

Em Cost Management + Faturação, selecione um perfil de faturação. Dependendo do seu acesso, poderá ter de selecionar uma conta de faturação. Na conta de faturação, selecione **Perfis de faturação** e, em seguida, selecione um perfil de faturação.

Selecione **Clientes** no lado esquerdo da página.

<!-- ![screenshot of billing profile customers list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-customers.png) -->

A página Clientes apresenta os custos do mês até à data e do último mês para cada cliente associado ao perfil de faturação. Os custos do mês acumuladas até à data são os custos pendentes relativamente ao mês atual e estes são cobrados quando a fatura é gerada para esse mês. Se a fatura do último mês ainda não foi gerada, os custos desse mês também estarão pendentes.

### <a name="view-pending-usage-charges"></a>Ver custos de utilização pendentes

Em Cost Management + Faturação, selecione um perfil de faturação. Dependendo do seu acesso, poderá ter de selecionar uma conta de faturação. Na conta de faturação, selecione **Perfis de faturação** e, em seguida, selecione um perfil de faturação.

Selecione **Subscrições do Azure** no lado esquerdo da página. A página Subscrições do Azure apresenta os custos do mês até à data e do último mês para cada subscrição no perfil de faturação. Os custos do mês acumuladas até à data são os custos pendentes relativamente ao mês atual e estes são cobrados quando a fatura é gerada para esse mês. Se a fatura do último mês ainda não foi gerada, os custos desse mês também estarão pendentes.

<!--     ![Screenshot that shows the Azure subscriptions list for MPA billing profile](./media/billing-mpa-understand-your-bill/mpa-billing-profile-subscriptions-list.png) -->

## <a name="analyze-your-azure-usage-charges"></a>Analisar os custos de utilização do Azure

Utilize o ficheiro CSV Utilização e custos do Azure para analisar os seus custos de utilização. Pode filtrar o ficheiro de utilização e custos do Azure para reconciliar os custos de utilização de cada produto listado no pdf da fatura. Para visualizar os custos detalhados da utilização de um produto específico, filtre a coluna **produto** no ficheiro CSV de utilização e custos do Azure de modo a incluir apenas o nome do produto.

Também pode filtrar a coluna **customerName**  no ficheiro CSV de utilização e custos do Azure para ver os custos diários de utilização de cada um dos seus clientes. Se quiser visualizar os custos diários de utilização por subscrição do Azure, filtre a coluna **subscriptionName** .

## <a name="pay-your-bill"></a>Pagar a sua fatura

As instruções para pagar a sua fatura são apresentadas na parte inferior da fatura. Pode pagar por transferência bancária ou por cheque no prazo de 60 dias da data da fatura.

Se já pagou a sua fatura, pode verificar o estado do pagamento na página Faturas no portal do Azure.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Analisar as transações faturadas no portal do Azure
> * Rever custos pendentes para estimar a próxima fatura
> * Analisar os custos de utilização do Azure

Saiba mais acerca da utilização do Azure Cost Management para parceiros.

> [!div class="nextstepaction"]
> [Começar a utilizar o Azure Cost Management para parceiros](../costs/get-started-partners.md)
