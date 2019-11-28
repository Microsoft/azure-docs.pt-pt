---
title: Visualizar e transferir os preços do Azure para a sua organização
description: Saiba como visualizar e transferir os preços ou estimar custos com os preços da sua organização.
author: bandersmsft
manager: jureid
tags: billing
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: b32c1be93e2fb16da9ce9ebb65b664017e70cac6
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74223946"
---
# <a name="view-and-download-your-organizations-azure-pricing"></a>Visualizar e transferir os preços do Azure para a sua organização

Os clientes do Azure com um Contrato Enterprise (EA) do Azure, Contrato de Cliente da Microsoft (MCA) ou Contrato de Parceiro da Microsoft (MPA) podem visualizar e transferir os preços no portal do Azure. [Saiba como verificar o tipo da conta de faturação](#check-your-billing-account-type).

## <a name="download-pricing-for-an-enterprise-agreement"></a>Transferir os preços de um Contrato Enterprise

Dependendo das políticas definidas para a sua organização pelo Administrador Corporativo, somente determinadas funções administrativas fornecem acesso às informações de preços do EA para a sua organização. Para obter mais informações, veja [Compreender as funções administrativas do Contrato Enterprise no Azure](billing-understand-ea-roles.md).

1. Como Administrador Corporativo, inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Procure *Cost Management + Faturação*.

   ![Captura de ecrã a mostrar a pesquisa no portal do Azure](./media/billing-ea-pricing/portal-cm-billing-search.png)

1. Na conta de faturação, selecione **Utilização + custos**.

   ![Captura de ecrã que mostra a utilização e os custos em Faturação](./media/billing-ea-pricing/ea-pricing-usage-charges-nav.png)

1. Selecione ![Captura de ecrã que mostra a pesquisa no portal do Azure](./media/billing-ea-pricing/download-icon.png) **Transferir** para o mês.

1. Em **Folha de Preços**, selecione **Transferir CSV**.

   ![Captura de ecrã que mostra o botão Transferir CSV da folha de preços](./media/billing-ea-pricing/download-ea-price-sheet.png)

## <a name="download-pricing-for-an-mca-or-mpa-account"></a>Transferir preços de uma conta MCA ou MPA

Se tiver um MCA, terá de ser proprietário, contribuidor, leitor ou gestor de faturas do perfil de faturação para visualizar e transferir os preços. Se tiver um MPA, deverá ter a função de Administrador Global e Agente de Administração na organização parceira para visualizar e transferir os preços.

### <a name="download-price-sheets-for-billed-charges"></a>Transferir folhas de preços para custos faturados

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Procure *Cost Management + Faturação*.
1. Selecione um perfil de faturação. Dependendo do seu acesso, pode ser preciso selecionar primeiro uma conta de faturação.
1. Selecione **Faturas**.
1. Na grelha da fatura, localize o registo da fatura que corresponde à folha de preços que quer transferir.
1. Clique nas reticências (`...`) no final da linha.
![Captura de ecrã que mostra as reticências selecionadas](./media/billing-ea-pricing/billingprofile-invoicegrid.png)

1. Se quiser ver os preços dos serviços na fatura selecionada, selecione **Folha de preços da fatura**.
1. Se quiser ver os preços de todos os serviços do Azure para o período de faturação indicado, selecione **Folha de preços do Azure**.

![Captura de ecrã que mostra o menu de contexto com folhas de preços](./media/billing-ea-pricing/contextmenu-pricesheet.png)

### <a name="download-price-sheets-for-the-current-billing-period"></a>Transferir as folhas de preços para o período de faturação atual

Se tiver um MCA, poderá transferir os preços relativamente ao período de faturação atual.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Procure *Cost Management + Faturação*.
1. Selecione um perfil de faturação. Dependendo do seu acesso, pode ser preciso selecionar primeiro uma conta de faturação.
1. Na área **Descrição Geral**, localize as ligações para transferência por baixo dos custos do mês até à data atual.
1. Selecione **Folha de preços do Azure**.
![Captura de ecrã que mostra a transferência a partir da Descrição geral](./media/billing-ea-pricing/open-pricing.png)

## <a name="estimate-costs-with-the-azure-pricing-calculator"></a>Estimativa dos custos com a calculadora de preços do Azure

Também pode usar os preços da sua organização para estimar os custos com a calculadora de preços do Azure.

1. Vá para a [Calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator).
1. No canto superior direito, selecione **Iniciar sessão**.
1. Em **Programas e Oferta** > **Programa de Licenciamento**, selecione **Contrato Enterprise (EA)** .
1. Em **Programas e Oferta** > **Contrato Selecionado**, selecione **Nenhum selecionado**.

    ![Captura de ecrã que mostra o botão Transferir CSV da folha de preços](./media/billing-ea-pricing/ea-pricing-calculator-estimate.png)

1. Escolha a organização.
1. Selecione **Aplicar**.
1. Pesquise e, em seguida, adicione produtos à sua estimativa.
1. Os preços estimados apresentados baseiam-se nos preços para a organização que selecionou.

## <a name="check-your-billing-account-type"></a>Verificar o tipo da conta de faturação
[!INCLUDE [billing-check-account-type](../../includes/billing-check-account-type.md)]

## <a name="next-steps"></a>Passos seguintes

Se for um cliente do EA, veja:

- [Gerir o acesso às suas informações de faturação do EA para o Azure](billing-manage-access.md)
- [Ver e transferir a sua fatura do Microsoft Azure](billing-download-azure-invoice.md)
- [Ver e transferir a utilização e os custos do Microsoft Azure](billing-download-azure-daily-usage.md)
- [Compreender a sua fatura para clientes do Contrato Enterprise](billing-understand-your-bill-ea.md)

Se tiver um Contrato de Cliente da Microsoft, veja:

- [Compreender os termos na sua folha de preços para um Contrato de Cliente da Microsoft](billing-mca-understand-pricesheet.md)
- [Ver e transferir a sua fatura do Microsoft Azure](billing-download-azure-invoice.md)
- [Ver e transferir a utilização e os custos do Microsoft Azure](billing-download-azure-daily-usage.md)
- [Visualizar e transferir os documentos fiscais para o seu perfil de faturação](billing-mca-download-tax-document.md)
- [Compreender os custos na sua fatura do perfil de faturação](billing-mca-understand-your-bill.md)
