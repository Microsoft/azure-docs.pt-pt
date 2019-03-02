---
title: Veja e transfira os preços do Azure da sua organização | Documentos da Microsoft
description: Saiba como ver e transferir a preços ou estimar os custos com preços da sua organização.
services: ''
documentationcenter: ''
author: adpick
manager: jureid
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 88d94492cb6ed3191e146f117a648293bc9e50f9
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57243725"
---
# <a name="view-and-download-your-organizations-azure-pricing"></a>Veja e transfira os preços do Azure da sua organização

Os clientes do Azure com um contrato Enterprise (EA) ou [contrato de cliente da Microsoft](#check-your-access-to-a-microsoft-customer-agreement) podem ver e transferir os respetivos preços do portal do Azure. Se tiver um contrato de cliente da Microsoft, consulte [modo de exibição e preços de transferência para o contrato do cliente Microsoft](#View-and-download-pricing-for-your-billing-profile).

## <a name="view-and-download-ea-pricing"></a>Veja e transfira a preços de EA

Consoante as políticas definidas para a sua organização, o administrador da empresa, apenas determinadas funções administrativas fornecem acesso a informações de preços de EA da sua organização. Para obter mais informações, consulte [funções administrativas do Azure Enterprise Agreement compreender no Azure](billing-understand-ea-roles.md).

1. Como um administrador empresarial, inicie sessão para o [portal do Azure](https://portal.azure.com/).
1. Pesquisar nos **custo Management + faturação**.

   ![Captura de ecrã que mostra a pesquisa de portal do Azure](./media/billing-ea-pricing/portal-cm-billing-search.png)

1. Sob a conta de cobrança, selecione **utilização e custos**.

   ![Captura de ecrã que mostra a utilização e custos de faturação](./media/billing-ea-pricing/ea-pricing-usage-charges-nav.png)

1. Selecione ![captura de ecrã que mostra a pesquisa de portal do Azure](./media/billing-ea-pricing/download-icon.png) **transferir** durante o mês.
1. Sob **folha de preços**, selecione **transferir csv**.

   ![Botão de csv de transferir a captura de ecrã que mostra a folha de preços](./media/billing-ea-pricing/download-ea-price-sheet.png)

## <a name="view-and-download-pricing-for-your-microsoft-customer-agreement"></a>Veja e transfira os preços para o seu contrato de cliente da Microsoft

Tem de ser o proprietário de perfil de faturação, Contribuidor, leitor ou Gerenciador de faturas para ver e transferir a preços. Para saber mais sobre as funções de faturas para contratos de cliente da Microsoft, consulte [funções de perfis e tarefas de faturação](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

### <a name="download-price-sheets-for-the-current-billing-period"></a>Transferir as folhas de preços para o período de faturação atual

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Pesquisar nos **custo Management + faturação**.
1. Selecione um perfil de faturação. Consoante o acesso, poderá ter de selecionar primeiro uma conta de cobrança.
1. Na **descrição geral** painel, encontra os links de download abaixo os encargos do mês até à data.
1. Selecione **folha de preços do Azure**.
![Captura de ecrã que mostra o download da descrição geral](./media/billing-ea-pricing/open-pricing.png)

### <a name="download-price-sheets-for-billed-charges"></a>Transferir as folhas de preços para os custos de faturação

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Pesquisar nos **custo Management + faturação**.
1. Selecione um perfil de faturação. Consoante o acesso, poderá ter de selecionar primeiro uma conta de cobrança.
1. Selecione **Faturas**.
1. Na grelha de nota fiscal, localize a linha da nota fiscal correspondente à folha de preços que pretende transferir.
1. Clique nas reticências (`...`) no final da linha.
![Captura de ecrã que mostra o botão de reticências selecionado](./media/billing-ea-pricing/billingprofile-invoicegrid.png)

1. Se quiser ver os preços para os serviços da nota fiscal selecionado, selecione **folha de preços de nota fiscal**.
1. Se quiser ver os preços para todos os serviços do Azure para o determinado período de faturação, selecione **folha de preços do Azure**.

![Captura de ecrã que mostra o menu de contexto com folhas de preços](./media/billing-ea-pricing/contextmenu-pricesheet.png)

## <a name="estimate-costs-with-the-azure-pricing-calculator"></a>Estimar os custos com a Calculadora de preços do Azure

Também pode usar o preço da sua organização para estimar os custos com a Calculadora de preços do Azure.

1. Vá para o [Calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator).
1. No canto superior direito, selecione **iniciar sessão**.
1. Sob **programas e oferta** > **programa de licenciamento**, selecione **Enterprise Agreement (EA)**.
1. Sob **programas e oferta** > **selecionado contrato**, selecione **nenhum selecionado**.

    ![Botão de csv de transferir a captura de ecrã que mostra a folha de preços](./media/billing-ea-pricing/ea-pricing-calculator-estimate.png)

1. Escolha a organização.
1. Selecione **Aplicar**.
1. Procurar e adicionar produtos à sua estimativa.
1. Os preços estimados apresentados baseiam-se sobre os preços para a organização que selecionou.

## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso a um contrato de cliente da Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="next-steps"></a>Passos Seguintes

Se estiver num cliente EA, consulte:

- [Gerir o acesso às suas informações de faturas de EA para o Azure](billing-manage-access.md)
- [Ver e transferir a fatura do Microsoft Azure](billing-download-azure-invoice.md)
- [Ver e transferir a sua utilização do Microsoft Azure e a cobrança](billing-download-azure-daily-usage.md)
- [Compreender a sua fatura para clientes com contrato Enterprise](billing-understand-your-bill-ea.md)

Se tiver um contrato de cliente da Microsoft, consulte:

- [Compreender os termos na sua folha de preços para um contrato de cliente da Microsoft](billing-mca-understand-pricesheet.md)
- [Ver e transferir a fatura do Microsoft Azure](billing-download-azure-invoice.md)
- [Ver e transferir a sua utilização do Microsoft Azure e a cobrança](billing-download-azure-daily-usage.md)
- [Ver e transferir documentos de imposto para o seu perfil de faturação](billing-mca-download-tax-document.md)
- [Compreender os encargos na fatura de seu perfil de faturação](billing-mca-understand-your-bill.md)
