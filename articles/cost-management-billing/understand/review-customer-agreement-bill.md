---
title: Examine a fatura do contrato de cliente da Microsoft-Azure
description: Saiba como revisar a sua conta e o uso de recursos e para verificar cobranças de sua fatura do contrato de cliente da Microsoft.
author: bandersmsft
manager: jureid
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/22/2019
ms.author: banders
ms.openlocfilehash: 3cbc4ab4f0e2ad18c7d1d430ed5501e23b5f5f4d
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75987069"
---
# <a name="tutorial-review-your-microsoft-customer-agreement-invoice"></a>Tutorial: examinar sua fatura do contrato de cliente da Microsoft

Você pode examinar os encargos em sua fatura analisando as transações individuais. Na conta de faturação de um Contrato de Cliente da Microsoft, é gerada uma fatura todos os meses para cada perfil de faturação. A fatura inclui todos os custos do mês anterior. Você pode exibir suas notas fiscais no portal do Azure e comparar os encargos com o arquivo de detalhes de uso.

Este tutorial se aplica somente aos clientes do Azure com um contrato de cliente da Microsoft.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Revisar transações faturadas no portal do Azure
> * Rever custos pendentes para estimar a próxima fatura
> * Analisar os custos de utilização do Azure

## <a name="prerequisites"></a>Pré-requisitos

Você deve ter uma conta de cobrança para um contrato de cliente da Microsoft.

Você deve ter acesso a um contrato do cliente da Microsoft. Você deve ser um proprietário do perfil de cobrança, colaborador, leitor ou Gerenciador de faturas para exibir informações de cobrança e uso. Para saber mais sobre as funções de faturação dos Contratos de Cliente da Microsoft, veja [Funções e tarefas do perfil de faturação](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks).

Deve ser mais de 30 dias a partir do dia em que você se inscreveu no Azure. O Azure cobra-o no final do período da fatura.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

- Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso ao Contrato de Cliente da Microsoft

Verifique o tipo de contrato para determinar se tem acesso a uma conta de faturação de um Contrato de Cliente Microsoft.

Na portal do Azure, digite *Gerenciamento de custos + cobrança* na caixa de pesquisa e selecione **Gerenciamento de custos + cobrança**.

![Captura de ecrã a mostrar a pesquisa da opção cost management + faturação no portal do Azure](./media/review-customer-agreement-bill/billing-search-cost-management-billing.png)

Se tiver acesso a apenas um âmbito de faturação, selecione **Propriedades** no lado esquerdo. Tem acesso a uma conta de faturação de um Contrato de Cliente Microsoft se o tipo de conta de faturação for **Contrato de Cliente Microsoft**.

![Captura de tela que mostra o contrato do cliente da Microsoft na página de propriedades](./media/review-customer-agreement-bill/billing-mca-property.png)

Se tiver acesso a vários âmbitos de faturação, verifique o tipo na coluna da conta de faturação. Tem acesso a uma conta de faturação de um Contrato de Cliente Microsoft se o tipo de conta de faturação de qualquer um dos âmbitos for **Contrato de Cliente Microsoft**.

![Captura de tela que mostra o contrato do cliente da Microsoft na página de lista de contas de cobrança](./media/review-customer-agreement-bill/billing-mca-in-the-list.png)

## <a name="review-invoiced-transactions-in-the-azure-portal"></a>Revisar transações faturadas no portal do Azure

Na portal do Azure, selecione **todas as transações** no lado esquerdo da página. Consoante o seu acesso, poderá ter de selecionar uma conta de faturação, um perfil de faturação ou uma secção de fatura e, em seguida, selecionar **Todas as transações**.

A página Todas as transações apresenta as seguintes informações:

![Captura de ecrã a mostrar a lista de transações cobradas](./media/review-customer-agreement-bill/mca-billed-transactions-list.png)

|Column  |Definição  |
|---------|---------|
|Data     | A data da transação  |
|ID da fatura     | O identificador da fatura onde a transação foi cobrada. Se submeter um pedido de suporte, partilhe o ID com o suporte do Azure para acelerar o pedido |
|Tipo de transação     |  A descrição do tipo de transação, como uma compra, um cancelamento ou custos de utilização  |
|Família de produtos     | A categoria do produto, como uma computação de máquinas virtuais ou uma base de dados SQL do Azure|
|SKU do produto     | Um código exclusivo que identifica a instância do seu produto |
|Montante     |  O montante da transação      |
|Secção de fatura     | A transação é apresentada nesta secção da fatura do perfil de faturação |
|Perfil de faturação     | A transação é apresentada nesta fatura do perfil de faturação |

Procure uma ID de fatura para filtrar as transações da fatura.

### <a name="view-transactions-by-invoice-sections"></a>Ver transações por secções de fatura

As secções de fatura ajudam-no a gerir os custos de uma fatura de um perfil de faturação. Para obter mais informações. Quando uma fatura é gerada, os custos de todas as secções no perfil de faturação são apresentados na fatura.

A imagem seguinte mostra os custos da secção de fatura Departamento de Contabilidade numa fatura de exemplo.

![Imagem de exemplo a mostrar os detalhes por informações de secção de fatura](./media/review-customer-agreement-bill/invoicesection-details.png)

Quando tiver identificado os custos de uma secção de fatura, pode ver as transações no portal do Azure para compreender esses custos.

Aceda à página Todas as transações no portal do Azure para ver as transações de uma fatura.

Filtre o nome da secção de fatura para ver as transações.

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>Rever custos pendentes para estimar a próxima fatura

Na conta de cobrança de um contrato de cliente da Microsoft, os encargos são estimados e considerados pendentes até que sejam faturados. Pode ver os custos pendentes no portal do Azure para estimar a sua próxima fatura. Os custos pendentes são estimados e não incluem impostos. Os custos reais na sua próxima fatura vão variar de acordo com os custos pendentes.

### <a name="view-summary-of-pending-charges"></a>Ver um resumo dos custos pendentes

Inicie sessão no [Portal do Azure](https://portal.azure.com).

Selecione um perfil de faturação. Dependendo do seu acesso, poderá ter de selecionar uma conta de faturação. Na conta de faturação, selecione **Perfis de faturação** e, em seguida, selecione um perfil de faturação.

Selecione a guia **Resumo** na parte superior da tela.

A secção Encargos apresenta os custo do mês acumulados até à data atual e do último mês.

![Captura de ecrã a mostrar a pesquisa da opção cost management + faturação no portal do Azure](./media/review-customer-agreement-bill/mca-billing-profile-summary.png)

Os custos do mês acumuladas até à data são os custos pendentes relativamente ao mês atual e estes são cobrados quando a fatura é gerada para esse mês. Se a fatura do último mês ainda não tiver sido gerada, os custos desse mês também estarão pendentes e serão apresentados na sua próxima fatura.

### <a name="view-pending-transactions"></a>Ver transações pendentes

Ao identificar custos pendentes, conseguirá compreender os custos ao analisar individualmente cada transação que contribuiu para esses custos. Neste momento, os custos de utilização pendentes não serão apresentados na página Todas as transações. Pode ver os custos de utilização pendentes na página Subscrições do Azure.

Inicie sessão no [Portal do Azure](https://portal.azure.com).

Na portal do Azure, digite *Gerenciamento de custos + cobrança* na caixa de pesquisa e selecione **Gerenciamento de custos + cobrança**.

Selecione um perfil de faturação. Dependendo do seu acesso, poderá ter de selecionar uma conta de faturação. Na conta de faturação, selecione **Perfis de faturação** e, em seguida, selecione um perfil de faturação.

Selecione **Todas as transações** no lado esquerdo da página.

Escreva a palavra *pendentes* na caixa de pesquisa. Utilize o filtro **Período de tempo** para ver os custos pendentes do mês até à data ou do último mês.

![Captura de ecrã a mostrar a lista de transações pendentes](./media/review-customer-agreement-bill/mca-pending-transactions-list.png)

### <a name="view-pending-usage-charges"></a>Ver custos de utilização pendentes

Inicie sessão no [Portal do Azure](https://portal.azure.com).

Na portal do Azure, digite *Gerenciamento de custos + cobrança* na caixa de pesquisa e selecione **Gerenciamento de custos + cobrança**.

Selecione um perfil de faturação. Dependendo do seu acesso, poderá ter de selecionar uma conta de faturação. Na conta de faturação, selecione **Perfis de faturação** e, em seguida, selecione um perfil de faturação.

Selecione **Todas as subscrições** no lado esquerdo da página.

A página Subscrições do Azure apresenta os custos do mês até à data e do último mês para cada subscrição no perfil de faturação. Os custos do mês acumuladas até à data são os custos pendentes relativamente ao mês atual e estes são cobrados quando a fatura é gerada para esse mês. Se a fatura do último mês ainda não foi gerada, os custos desse mês também estarão pendentes.

![Captura de ecrã a mostrar a lista de subscrições do Azure do perfil de faturação](./media/review-customer-agreement-bill/mca-billing-profile-subscriptions-list.png)

## <a name="analyze-your-azure-usage-charges"></a>Analisar os custos de utilização do Azure

Utilize o ficheiro CSV Utilização e custos do Azure para analisar os seus custos de utilização. Pode transferir o ficheiro para uma fatura ou para custos pendentes.

### <a name="download-your-invoice-and-usage-details"></a>Baixar a fatura e os detalhes de uso

Dependendo do seu acesso, talvez seja necessário selecionar uma conta de cobrança ou um perfil de cobrança em gerenciamento de custos + cobrança. No menu esquerdo, selecione **Faturas** em **Faturação**. Na grelha da fatura, localize a linha da fatura que quer transferir. Clique no símbolo de download ou nas reticências (...) no final da linha. Na caixa de **Download** , baixe o arquivo de detalhes de uso e fatura.

### <a name="view-detailed-usage-by-invoice-section"></a>Ver a secção de utilização detalhada por fatura

Pode filtrar o ficheiro Utilização e custos do Azure para reconciliar os custos de utilização das suas secções de fatura.

As informações a seguir orientam você pela reconciliação de encargos de computação para a seção fatura do departamento de contabilidade:

![Imagem de exemplo a mostrar os detalhes por informações de secção de fatura](./media/review-customer-agreement-bill/invoicesection-details.png)

| PDF da fatura | Ficheiro CSV Utilização e custos do Azure |
| --- | --- |
|Departamento de Contabilidade |invoiceSectionName |
|Custos de Utilização – Plano do Microsoft Azure |productOrderName |
|Computação |serviceFamily |

Filtre a coluna **invoiceSectionName** no arquivo CSV para o **departamento de contabilidade**. Em seguida, filtre a coluna **productOrderName** no arquivo CSV para **Microsoft Azure plano**. Em seguida, filtre a coluna da **imfamily** no arquivo CSV para **Microsoft. Compute**.

![Captura de ecrã a mostrar o ficheiro de utilização e custos filtrado por secção de fatura](./media/review-customer-agreement-bill/billing-usage-file-filtered-by-invoice-section.png)

### <a name="view-detailed-usage-by-subscription"></a>Ver utilização detalhada por subscrição

Pode filtrar o ficheiro CSV Utilização e custos do Azure para reconciliar os custos de utilização das suas secções de fatura. Ao identificar os custos de uma subscrição, utilize o ficheiro CSV Utilização e custos do Azure para analisar os custos.

A imagem seguinte mostra a lista de subscrições no portal do Azure.

![Captura de ecrã a mostrar a lista de subscrições do Azure do perfil de faturação](./media/review-customer-agreement-bill/mca-billing-profile-subscriptions-list-highlighted.png)

Filtre a coluna **subscriptionName** no ficheiro CSV Utilização e custos do Azure para **WA_Subscription** de modo a ver os custos de utilização detalhados para o campo WA_Subscription.

![Captura de ecrã a mostrar o ficheiro de utilização e custos filtrado por subscrição](./media/review-customer-agreement-bill/billing-usage-file-filtered-by-subscription.png)

## <a name="pay-your-bill"></a>Pagar a sua fatura

As instruções para pagar a sua fatura são apresentadas na parte inferior da fatura. [Saiba como pagar](mca-understand-your-invoice.md#how-to-pay).

Se já pagou a sua fatura, pode verificar o estado do pagamento na página Faturas no portal do Azure.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Revisar transações faturadas no portal do Azure
> * Rever custos pendentes para estimar a próxima fatura
> * Analisar os custos de utilização do Azure

Conclua o guia de início rápido para começar a usar a análise de custo.

> [!div class="nextstepaction"]
> [Começar a analisar os custos](../costs/quick-acm-cost-analysis.md)
