---
title: Compreender os encargos de serviços externos do Azure | Documentos da Microsoft
description: Saiba mais sobre a faturação de serviços externos, anteriormente conhecido como Marketplace, de custos no Azure.
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
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490356"
---
# <a name="understand-your-azure-external-services-charges"></a>Compreender os encargos de serviços externos do Azure
Serviços externos são publicados por fornecedores de software de terceiros no Azure marketplace. Por exemplo, o SendGrid é um serviço externo que pode comprar no Azure, mas não está publicado pela Microsoft. Alguns produtos da Microsoft são vendidos através do Microsoft Azure marketplace, demasiado.

## <a name="how-external-services-are-billed"></a>Como os serviços externos são faturados

- Se tiver um [contrato de cliente da Microsoft](#check-access), os serviços de terceiros são faturados com o restante dos seus serviços do Azure.
- Se não tiver um contrato de cliente da Microsoft, seus serviços externos são faturados em separado dos seus serviços do Azure.
- Cada serviço externo tem um modelo de faturação diferente. Alguns serviços são faturados de forma pay as you go, enquanto outros corrigiram das cobranças mensais.
- Não é possível utilizar os créditos mensais gratuitos para serviços externos. Se estiver a utilizar uma subscrição do Azure, que inclui [gratuita créditos](https://azure.microsoft.com/pricing/spending-limits/), eles não não possível aplicar a custos de serviços externos. Quando aprovisiona um novo serviço externo ou recurso, é apresentado um aviso:

    ![Aviso de compra do Marketplace](./media/billing-understand-your-azure-marketplace-charges/credit-warning.png)

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

## <a name="view-and-download-invoices"></a>Ver e transferir faturas

Se tiver um [contrato de cliente da Microsoft](#check-access), as cobranças de terceiros são da nota fiscal mesmo como os encargos do Azure. Saiba como [ver e transferir a fatura do Azure](billing-download-azure-invoice.md) do portal do Azure para ver as cobranças de terceiros.

Se não tiver um contrato de cliente da Microsoft, tem de notas fiscais separados para os custos de terceiros. Pode ver e transferir as faturas do Azure Marketplace no portal do Azure ao seguir estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Procure **custo Management + faturação**.
1. No menu da esquerda, selecione **notas fiscais**.
1. Clique nas **Azure Marketplace e as reservas** separador.  ![Imagem do separador de marketplace e as reservas do Azure](./media/billing-understand-your-azure-marketplace-charges/invoice-tabs.png)
1. Na subscrição lista pendente, selecione a subscrição que contém os que pretende ver notas fiscais para os serviços externos.

## <a name="external-spending-for-ea-customers"></a>Externo gastos para clientes com EA

Os clientes com EA podem ver o serviço externo de gastos e transferir relatórios no portal de EA. Ver [do Azure Marketplace para clientes com EA](https://ea.azure.com/helpdocs/azureMarketplace) para começar a utilizar.

## <a name="manage-payment-for-external-services"></a>Gerir o pagamento para serviços externos

Quando compra um serviço externo, escolhe uma subscrição do Azure para o recurso. O método de pagamento da subscrição do Azure selecionada torna-se o método de pagamento para o serviço externo. Para alterar o método de pagamento para um serviço externo, deve [alterar o método de pagamento da subscrição do Azure](billing-how-to-change-credit-card.md) vinculado a esse serviço externo. Pode descobrir qual assinatura do seu pedido de serviço externo que está associado ao seguindo estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Clique em **todos os recursos** no menu de navegação esquerdo.
     ![captura de ecrã do item de menu todos os recursos](./media/billing-understand-your-azure-marketplace-charges/all-resources.png)
1. Pesquisa para o seu serviço externo.
1. Procure o nome da subscrição no **subscrição** coluna.
    ![captura de ecrã do nome da subscrição para o recurso](./media/billing-understand-your-azure-marketplace-charges/sub-selected.png)
1. Clique no nome da subscrição e [atualizar o método de pagamento ativo](billing-how-to-change-credit-card.md).

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
Se quiser cancelar o seu pedido de serviço externo, eliminar o recurso a [portal do Azure](https://portal.azure.com).

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Clique em **todos os recursos** no menu de navegação esquerdo.
    ![captura de ecrã do item de menu todos os recursos](./media/billing-understand-your-azure-marketplace-charges/all-resources.png)
1. Pesquisa para o seu serviço externo.
1. Selecione a caixa junto ao recurso que pretende eliminar.
1. Selecione **eliminar** na barra de comandos.
    ![Captura de ecrã do botão delete](./media/billing-understand-your-azure-marketplace-charges/delete-button.png)
1. Tipo *'Sim'* no painel de confirmação.
    ![Eliminar recurso](./media/billing-understand-your-azure-marketplace-charges/delete-resource.PNG)
1. Clique em **Eliminar**.

## <a name="check-access"></a>Verificação de acesso
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos Seguintes
- [Começar a analisar os custos](../cost-management/quick-acm-cost-analysis.md)
