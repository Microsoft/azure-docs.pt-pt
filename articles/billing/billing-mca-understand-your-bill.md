---
title: Compreender os encargos na fatura de seu contrato de cliente de Microsoft - Azure | Documentos da Microsoft
description: Saiba como ler e compreender os encargos na sua fatura
services: ''
documentationcenter: ''
author: jureid
manager: jureid
editor: ''
tags: billing
ms.assetid: 32eea268-161c-4b93-8774-bc435d78a8c9
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: 3c5f5778d68b8df4d4b3132ff96bedb9d5da24ee
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57249231"
---
# <a name="understand-the-charges-on-your-microsoft-customer-agreements-invoice"></a>Compreender os encargos na fatura do seu contrato cliente da Microsoft

Poderá reconciliar os custos na sua fatura, ao analisar as transações individuais da nota fiscal.

A conta de faturação de um contrato de cliente da Microsoft, uma nota fiscal é gerada por mês para cada perfil de faturação. A nota fiscal inclui todos os encargos do mês anterior. Pode ver as faturas no portal do Azure. Para obter mais informações, consulte [View e o download do Microsoft Azure nota fiscal](billing-download-azure-invoice.md).

Este artigo aplica-se a uma conta de cobrança para um contrato de cliente da Microsoft. [Verifique se tem acesso a um contrato de cliente da Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="view-transactions-for-an-invoice-in-the-azure-portal"></a>Ver transações para uma nota fiscal no portal do Azure

1. Inicie sessão no [portal do Azure](https://www.azure.com).

2. Pesquisar nos **custo Management + faturação**.

    ![Captura de ecrã que mostra a pesquisa de portal do Azure para gestão de custos + faturação](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Selecione **todas as transações** do lado esquerdo do ecrã. Dependendo do seu acesso poderá ter de selecionar uma conta de cobrança ou um perfil de faturação, em seguida, selecione **todas as transações**.

4. Página de todas as transações apresenta as seguintes informações:

    ![Captura de ecrã que mostra a lista de transações faturadas](./media/billing-understand-your-bill-mca/mca-billed-transactions-list.png)

    |Coluna  |Definição  |
    |---------|---------|
    |Date     | A data da transação  |
    |ID da nota fiscal     | O identificador da nota fiscal no qual a transação foi cobrada. Se submeter um pedido de suporte, partilhar o ID com o suporte do Azure para acelerar o seu pedido de suporte |
    |Tipo de transação     |  O tipo de transação, como cobranças de compra, Cancelar e a utilização  |
    |Família de produtos     | A categoria de produto, como a computação para as máquinas virtuais ou base de dados para a base de dados SQL do Azure|
    |Sku do produto     | Um código único, identificar a instância do seu produto |
    |Montante     |  A quantidade de transação      |
    |Secção de faturas     | A transação é exibido nesta secção da nota fiscal do perfil de faturação |
    |Perfil de faturação     | A transação é exibido numa das faturas este perfil de faturação |

5. Procure o ID da nota fiscal para filtrar as transações da nota fiscal.

### <a name="view-transactions-by-invoice-sections"></a>Ver transações por secções de nota fiscal

As secções de nota fiscal permitem-lhe organizar os custos na fatura de um perfil de faturação. Para obter mais informações, consulte [secção de nota fiscal de compreender](billing-mca-overview.md#understand-invoice-sections). Quando uma nota fiscal é gerada, custos de todas as seções no perfil de faturação, aparecem da nota fiscal.

A imagem seguinte mostra os custos para a secção de nota fiscal do departamento de TI numa nota fiscal de exemplo.

![Imagem de exemplo que mostra os detalhes por informações da secção de nota fiscal](./media/billing-understand-your-bill-mca/invoicesection-details.png)

Depois de ter identificado os encargos de uma seção de nota fiscal, pode ver as transações no portal do Azure para reconciliar os custos.

1. Aceda à página todas as transações no portal do Azure para exibir transações para uma nota fiscal. Para obter mais informações, consulte [ver transações para uma nota fiscal no portal do Azure](#view-transactions-for-an-invoice-in-the-azure-portal).

2. Filtrar por nome de secção de notas fiscais para exibir transações para a secção de nota fiscal.

## <a name="understand-pending-charges-to-estimate-your-next-invoice"></a>Compreender pendentes custos para calcular a sua próxima nota fiscal

Na conta de faturação para um contrato de cliente da Microsoft, até que os custos são faturados, eles são estimativa e considerados pendentes. Eles são finalizados e considerados cobradas quando mostram da nota fiscal. Pode ver pendentes custos para um perfil de faturação estimar os custos na sua fatura seguinte.

### <a name="view-charges-summary"></a>Ver resumo de custos

1. Inicie sessão no [portal do Azure](https://www.azure.com).

2. Pesquisar nos **custo Management + faturação**.

   ![Captura de ecrã que mostra a pesquisa de portal do Azure para gestão de custos + faturação](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Selecione um perfil de faturação. Consoante o acesso, poderá ter de selecionar uma conta de cobrança. A partir da conta de cobrança, selecione **perfis de faturação** , em seguida, selecione um perfil de faturação.

4. Selecione **resumo** separador na parte superior do ecrã.

5. A secção encargos exibe a mês até à data e encargos do mês passado.

   ![Captura de ecrã que mostra a pesquisa de portal do Azure para gestão de custos + faturação](./media/billing-understand-your-bill-mca/mca-billing-profile-summary.png)

Os encargos do mês até à data são os encargos pendentes para o mês atual e são cobrados quando a nota fiscal é gerada para o mês. Se a nota fiscal do último mês não ainda é gerada, então encargos do mês passado também estão pendentes.

### <a name="view-pending-transactions"></a>Ver pendentes de transações

Depois de identificar pendentes Cobranças, pode ver as transações individuais que contribuem para os encargos na página de todas as transações. Neste momento, pendentes utilização encargos não são apresentados na página de todas as transações. Pode ver os custos de utilização pendente na página de subscrições do Azure. Para obter mais informações, consulte [vista pendentes custos de utilização](#view-pending-usage-charges)

1. Inicie sessão no [portal do Azure](https://www.azure.com).

2. Pesquisar nos **custo Management + faturação**.

   ![Captura de ecrã que mostra a pesquisa de portal do Azure para gestão de custos + faturação](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Selecione um perfil de faturação. Consoante o acesso, poderá ter de selecionar uma conta de cobrança. A partir da conta de cobrança, selecione **perfis de faturação** , em seguida, selecione um perfil de faturação.

4. Selecione **todas as transações** do lado esquerdo do ecrã.

5. Procure **pendente**. Utilize o **Timespan** filtro para ver pendentes custos atual ou no mês passado.

   ![Captura de ecrã que mostra a lista de transações pendentes](./media/billing-understand-your-bill-mca/mca-pending-transactions-list.png)

### <a name="view-pending-usage-charges"></a>Ver os custos de utilização pendente

1. Inicie sessão no [portal do Azure](https://www.azure.com).

2. Pesquisar nos **custo Management + faturação**.

   ![Captura de ecrã que mostra a pesquisa de portal do Azure para gestão de custos + faturação](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Selecione um perfil de faturação. Consoante o acesso, poderá ter de selecionar uma conta de cobrança. A partir da conta de cobrança, selecione **perfis de faturação** , em seguida, selecione um perfil de faturação.

4. Selecione **todas as subscrições** do lado esquerdo do ecrã.

5. A página de subscrições do Azure atual e os encargos do mês passado para cada subscrição é apresentada no perfil de faturação. Os encargos do mês até à data são os encargos pendentes para o mês atual e são cobrados quando a nota fiscal é gerada para o mês. Se a nota fiscal do último mês não ainda é gerada, então encargos do mês passado também estão pendentes.

    ![Captura de ecrã que mostra a lista de subscrições do Azure para o perfil de faturação](./media/billing-understand-your-bill-mca/mca-billing-profile-subscriptions-list.png)

Pode ver os custos de utilização detalhada usando o arquivo de utilização e os encargos do Azure. Para obter mais informações, consulte a secção seguinte.

## <a name="analyze-your-azure-usage-charges"></a>Analisar as cobranças de utilização do Azure

Utilize o ficheiro de csv de utilização e os encargos do Azure para analisar as cobranças de utilização com base. Pode transferir o ficheiro para uma nota fiscal ou para pendentes encargos. Para obter mais informações, consulte [obter a faturação da nota fiscal e diário de dados de utilização do Azure](billing-download-azure-invoice-daily-usage-date.md).

### <a name="view-detailed-usage-by-invoice-section"></a>Ver a utilização detalhada pela secção de nota fiscal

Pode filtrar o ficheiro de utilização e os encargos do Azure para reconciliar os custos de utilização de suas seções de nota fiscal.

Os seguintes passos guiá-lo por meio de reconciliar os custos de computação para a secção de nota fiscal do departamento de contabilidade:

![Imagem de exemplo que mostra os detalhes por informações da secção de nota fiscal](./media/billing-understand-your-bill-mca/invoicesection-details.png)

 | Nota fiscal de PDF | Utilização do Azure e os encargos de CSV |
 | --- | --- |
 |Departamento de TI |invoiceSectionName |
 |Custos de utilização - Standard do Azure |productOrderName |
 |Armazenamento |serviceFamily |

1. Filtro da **invoiceSectionName** coluna no ficheiro CSV para **departamento de contabilidade**.
2. Filtro da **productOrderName** coluna no ficheiro CSV para **planear do Microsoft Azure**.
3. Filtro da **serviceFamily** coluna no ficheiro CSV para **Microsoft. Compute**.

![Captura de ecrã que mostra a utilização e os custos de ficheiro filtrado pela secção de nota fiscal](./media/billing-understand-your-bill-mca/billing-usage-file-filtered-by-invoice-section.png)

<!--Todo Add screenshot of usage file -->

### <a name="view-detailed-usage-by-subscription"></a>Ver a utilização detalhada por subscrição

Pode filtrar os ficheiros de csv de utilização e custos do Azure para reconciliar os custos de utilização das suas subscrições. Para ver todas as subscrições num perfil de faturação, consulte [vista pendentes custos de utilização](#view-pending-usage-charges).

Depois de identificar custos para uma subscrição, utilize o ficheiro de csv de utilização e os encargos do Azure para analisar os custos.

Captura de ecrã seguinte mostra a lista de subscrições no portal do Azure.

![Captura de ecrã que mostra a lista de subscrições do Azure para o perfil de faturação](./media/billing-understand-your-bill-mca/mca-billing-profile-subscriptions-list-highlighted.png)

Filtro da **subscriptionName** coluna ao ficheiro de CSV de utilização e os encargos do Azure para **WA_Subscription** para ver os custos de utilização detalhada para WA_Subscription.

![Captura de ecrã que mostra a utilização e custos de ficheiro filtrado pela subscrição](./media/billing-understand-your-bill-mca/billing-usage-file-filtered-by-subscription.png)

## <a name="pay-your-bill"></a>Pagar a sua fatura

Instruções de pagamento de sua fatura são mostradas na parte inferior da nota fiscal. [Saiba como pagar](billing-mca-understand-your-invoice.md#how-to-pay).

Se já pagou sua fatura, pode verificar o status de pagamento na página de notas fiscais no portal do Azure. 

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso a um contrato de cliente da Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos

Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre a sua fatura e utilização detalhada, consulte:

- [Como obter a faturação e os dados de utilização diária do Azure](billing-download-azure-invoice-daily-usage-date.md)
- [Compreender os termos na sua fatura de contrato de cliente da Microsoft](billing-mca-understand-your-invoice.md)
- [Compreender os termos na sua utilização de contrato de cliente da Microsoft CSV](billing-mca-understand-your-usage.md)
