---
title: Compreender os custos de serviços externos do Azure | Microsoft Docs
description: Saiba mais sobre a faturação de serviços externos, anteriormente conhecidos como custos de Azure Marketplace.
author: jureid
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 278e873d01eb3dd7d614d771e5b50b8fe624800a
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "67490356"
---
# <a name="understand-your-azure-external-services-charges"></a>Compreender os custos de serviços externos do Azure
Os serviços externos são publicados por fornecedores de software terceiros no Azure Marketplace. Por exemplo, SendGrid é um serviço externo que pode comprar no Azure, mas não é publicado pela Microsoft. Alguns produtos da Microsoft também são vendidos por meio do Azure Marketplace.

## <a name="how-external-services-are-billed"></a>Como os serviços externos são faturados

- Se tiver um [Contrato de Cliente da Microsoft](#check-access), os seus serviços de terceiros são faturados com o restante dos seus serviços do Azure.
- Se não tiver um Contrato de Cliente da Microsoft, os seus serviços externos são faturados em separado dos seus serviços do Azure.
- Cada serviço externo tem um modelo de faturação diferente. Alguns serviços são faturados como pay as you go, enquanto outros têm custos mensais fixos.
- Não pode usar créditos gratuitos mensais para serviços externos. Se estiver a utilizar uma subscrição do Azure que inclui [créditos gratuitos](https://azure.microsoft.com/pricing/spending-limits/), não poderão ser aplicados a custos de serviços externos. Quando provisiona um novo serviço ou recurso externo, é apresentado um aviso:

    ![Aviso de compra no Marketplace](./media/billing-understand-your-azure-marketplace-charges/credit-warning.png)

<!-- ## View external service spending and history in the Azure portal
You can view a list of the external services that are on each subscription within the [Azure portal](https://portal.azure.com/):

1. Sign in to the [Azure portal](https://portal.azure.com/) as the account administrator.
2. In the Hub menu, select **Subscriptions**.

    ![Select Subscriptions in the Hub menu](./media/billing-understand-your-azure-marketplace-charges/sub-button.png)
3. In the **Subscriptions** blade, select the subscription that you want to view, and then select **External services**.

    ![Select a subscription in the billing blade](./media/billing-understand-your-azure-marketplace-charges/select-sub-external-services.png)
4. You should see each of your external service orders, the publisher name, service tier you bought, name you gave the resource, and the current order status. To see past bills, select an external service.

    ![Select an external service](./media/billing-understand-your-azure-marketplace-charges/external-service-blade2.png)
5. From here, you can view past bill amounts including the tax breakdown.

    ![View external services billing history](./media/billing-understand-your-azure-marketplace-charges/billing-overview-blade.png) -->

## <a name="view-and-download-invoices"></a>Visualizar e transferir faturas

Se tiver um [Contrato de Cliente da Microsoft](#check-access), os seus custos de terceiros estarão na mesma fatura que os custos do Azure. Saiba como [visualizar e transferir a sua fatura do Azure](billing-download-azure-invoice.md) do portal do Azure para ver os custos de terceiros.

Se não tiver um Contrato de Cliente da Microsoft, terá faturas separadas para custos de terceiros. Pode visualizar e transferir as suas faturas do Azure Marketplace a partir do portal do Azure seguindo estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Procure **Cost Management + Faturação**.
1. No menu do lado esquerdo, selecione **Faturas**.
1. Clique no separador **Azure Marketplace e reservas**.  ![Imagem do separador do Azure Marketplace e reservas](./media/billing-understand-your-azure-marketplace-charges/invoice-tabs.png)
1. Na lista suspensa de subscrições, selecione a subscrição que contém os serviços externos dos quais pretende ver faturas.

## <a name="external-spending-for-ea-customers"></a>Despesas externas para clientes do EA

Os clientes do EA podem ver as despesas de serviço externo e transferir relatórios no portal de EA. Veja [Azure Marketplace para clientes do EA](https://ea.azure.com/helpdocs/azureMarketplace) para começar.

## <a name="manage-payment-for-external-services"></a>Gerir o pagamento de serviços externos

Ao comprar um serviço externo, escolhe uma subscrição do Azure para o recurso. O método de pagamento da subscrição do Azure selecionada torna-se o método de pagamento para o serviço externo. Para alterar o método de pagamento de um serviço externo, tem de [alterar o método de pagamento da subscrição do Azure](billing-how-to-change-credit-card.md) vinculada a esse serviço externo. Pode descobrir a qual subscrição o seu pedido de serviço externo está vinculada seguindo estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Clique em **Todos os recursos** no menu de navegação à esquerda.
     ![captura de ecrã do item de menu Todos os recursos](./media/billing-understand-your-azure-marketplace-charges/all-resources.png)
1. Pesquise o seu serviço externo.
1. Procure o nome da subscrição na coluna **Subscrição**.
    ![captura de ecrã do nome da subscrição do recurso](./media/billing-understand-your-azure-marketplace-charges/sub-selected.png)
1. Clique no nome da subscrição e [atualize o método de pagamento ativo](billing-how-to-change-credit-card.md).

<!-- Update your payment methods for external service orders from the [Account Center](https://account.windowsazure.com/).

> [!NOTE]
> If you purchased your subscription with a Work or School account, [contact support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) to make changes to your payment method.

1. Sign in to the [Account Center](https://account.windowsazure.com/) and [navigate to the **marketplace** tab](https://account.windowsazure.com/Store)

    ![Select marketplace in the account center](./media/billing-understand-your-azure-marketplace-charges/select-marketplace.png)
2. Select the external service you want to manage

    ![Select the external service you want to manage](./media/billing-understand-your-azure-marketplace-charges/select-ext-service.png)
3. Click **Change payment method** on the right side of the page. This link brings you to a different portal to manage your payment method.

    ![Order summary](./media/billing-understand-your-azure-marketplace-charges/change-payment.PNG)
4. Click **Edit info** and follow instructions to update your payment information.

    ![Select edit info](./media/billing-understand-your-azure-marketplace-charges/edit-info.png) -->

## <a name="cancel-an-external-service-order"></a>Cancelar um pedido de serviço externo
Se quiser cancelar o pedido de serviço externo, elimine o recurso no [portal do Azure](https://portal.azure.com).

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Clique em **Todos os recursos** no menu de navegação à esquerda.
    ![Captura de ecrã do item de menu Todos os recursos](./media/billing-understand-your-azure-marketplace-charges/all-resources.png)
1. Pesquise o serviço externo.
1. Selecione a caixa junto do recurso que quer eliminar.
1. Selecione **Eliminar** na barra de comandos.
    ![Captura de ecrã do botão Eliminar](./media/billing-understand-your-azure-marketplace-charges/delete-button.png)
1. Digite *“Sim”* na folha de confirmação.
    ![Eliminar recurso](./media/billing-understand-your-azure-marketplace-charges/delete-resource.PNG)
1. Clique em **Eliminar**.

## <a name="check-access"></a>Verificar acesso
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes
- [Começar a analisar os custos](../cost-management/quick-acm-cost-analysis.md)
