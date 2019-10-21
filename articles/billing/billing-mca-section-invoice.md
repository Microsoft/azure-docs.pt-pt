---
title: Criar secções na fatura para organizar os custos – Azure
description: Aprenda a organizar os custos com secções de fatura.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.topic: conceptual
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 77dd8a5e54697a37a2039238ecdedb3e2a2fa326
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72375467"
---
# <a name="create-sections-on-your-invoice-to-organize-your-costs"></a>Crie secções na fatura para organizar os custos

Crie secções na fatura para organizar os custos por departamento, ambiente de desenvolvimento ou com base nas necessidades da sua organização. Em seguida, dê permissão a outras pessoas para criarem subscrições do Azure que são faturadas na secção. Todos os custos de utilização e compras relativos às subscrições são, então, faturados na secção. Pode visualizar o total de custos da secção na sua fatura, no portal do Azure ou revê-los na análise de custos do Azure. Para obter mais informações, veja [visualizar transações por secções de fatura](billing-mca-understand-your-bill.md#view-transactions-by-invoice-sections).

Este artigo aplica-se à conta de faturação de um Contrato de Cliente da Microsoft. [Verifique se tem acesso a um Contrato de Cliente da Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="create-an-invoice-section-in-the-azure-portal"></a>Crie uma secção de fatura no portal do Azure

Para criar uma secção de fatura, precisa de ser **proprietário do perfil de faturação** ou **contribuidor do perfil de faturação**. Para obter mais informações, veja [gerir secções de fatura para o perfil de faturação](billing-understand-mca-roles.md#manage-invoice-sections-for-billing-profile).

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Procure **Cost Management + Faturação**.

   ![Captura de ecrã que mostra a pesquisa no portal do Azure](./media/billing-mca-section-invoice/billing-search-cost-management-billing.png)

3. Selecione **Secções de fatura** no painel do lado esquerdo. Dependendo do seu acesso, pode ser preciso selecionar um perfil de faturação ou uma conta de faturação e, em seguida, selecionar **Secções de fatura**.

   ![Captura de ecrã que mostra a lista das secções de fatura](./media/billing-mca-section-invoice/mca-select-invoice-sections.png)

4. Na parte superior da página, selecione **Adicionar**.

5. Introduza um nome para a secção de fatura e selecione um perfil de faturação. Verá que esta secção na fatura do perfil de faturação reflete a utilização de cada subscrição e as compras que atribuiu à mesma. 

   ![Captura de ecrã que mostra a página de criação das secções de fatura](./media/billing-mca-section-invoice/mca-create-invoice-section.png)

6. Selecione **Criar**.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso ao Contrato de Cliente da Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contactar o suporte

Se precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema rapidamente.

## <a name="next-steps"></a>Passos seguintes

- [Criar uma subscrição do Azure adicional para o Contrato de Cliente da Microsoft](billing-mca-create-subscription.md)
- [Gerir funções de faturação no portal do Azure](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)
- [Obter a propriedade da faturação de subscrições do Azure de outros utilizadores noutras contas de faturação](billing-mca-request-billing-ownership.md)
