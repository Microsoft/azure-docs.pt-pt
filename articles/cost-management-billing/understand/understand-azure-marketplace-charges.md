---
title: Compreender os custos de serviços externos do Azure | Microsoft Docs
description: Saiba mais sobre a faturação de serviços externos, anteriormente conhecidos como custos de Azure Marketplace.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7955e9bc75b2a27ac42d381df1e686ec8a0ed04b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "77587816"
---
# <a name="understand-your-azure-external-services-charges"></a>Compreender os custos de serviços externos do Azure
Os serviços externos são publicados por fornecedores de software terceiros no Azure Marketplace. Por exemplo, SendGrid é um serviço externo que pode comprar no Azure, mas não é publicado pela Microsoft. Alguns produtos da Microsoft também são vendidos por meio do Azure Marketplace.

## <a name="how-external-services-are-billed"></a>Como os serviços externos são faturados

- Se tiver um Contrato de Cliente da Microsoft (MCA) ou um Contrato de Parceiro da Microsoft (MPA), os serviços de terceiros serão faturados com o restante serviços do Azure. [Verifique o tipo de conta de faturação](#check-billing-account-type) para ver se tem acesso a um MCA ou MPA.
- Se não tiver um MCA ou MPA, os serviços externos serão faturados em separado dos serviços do Azure. Receberá duas faturas em cada período de faturação, uma para os serviços do Azure e outra para compras no Marketplace.
- Cada serviço externo tem um modelo de faturação diferente. Alguns serviços são faturados como pay as you go, enquanto outros têm custos mensais fixos.
- Não pode usar créditos gratuitos mensais para serviços externos. Se estiver a utilizar uma subscrição do Azure que inclui [créditos gratuitos](https://azure.microsoft.com/pricing/spending-limits/), não poderão ser aplicados a custos de serviços externos. Quando provisiona um novo serviço ou recurso externo, é apresentado um aviso:

    ![Aviso de compra no Marketplace](./media/understand-azure-marketplace-charges/credit-warning.png)

<!-- ## View external service spending and history in the Azure portal
You can view a list of the external services that are on each subscription within the [Azure portal](https://portal.azure.com/):

1. Sign in to the [Azure portal](https://portal.azure.com/) as the account administrator.
2. In the Hub menu, select **Subscriptions**.

    ![Select Subscriptions in the Hub menu](./media/understand-azure-marketplace-charges/sub-button.png)
3. In the **Subscriptions** blade, select the subscription that you want to view, and then select **External services**.

    ![Select a subscription in the billing blade](./media/understand-azure-marketplace-charges/select-sub-external-services.png)
4. You should see each of your external service orders, the publisher name, service tier you bought, name you gave the resource, and the current order status. To see past bills, select an external service.

    ![Select an external service](./media/understand-azure-marketplace-charges/external-service-blade2.png)
5. From here, you can view past bill amounts including the tax breakdown.

    ![View external services billing history](./media/understand-azure-marketplace-charges/billing-overview-blade.png) -->

## <a name="external-spending-for-ea-customers"></a>Despesas externas para clientes do EA

Os clientes do EA podem ver as despesas de serviço externo e transferir relatórios no portal de EA. Veja [Azure Marketplace para clientes do EA](https://ea.azure.com/helpdocs/azureMarketplace) para começar.

## <a name="view-and-download-invoices-for-external-services"></a>Visualizar e transferir as faturas dos serviços externos

Se tiver um Contrato de Cliente Microsoft (MCA) ou um Contrato de Parceiro da Microsoft (MPA), os serviços de terceiros serão faturados com os restantes serviços do Azure numa única fatura. [Verifique o tipo de conta de faturação](#check-billing-account-type) para ver se tem acesso a um MCA ou MPA. Se tiver, veja [Visualizar e transferir faturas no do portal do Azure](download-azure-invoice.md) para ver os custos de terceiros.

Se não tiver um MCA ou MPA, terá faturas separadas para os custos de terceiros. 

Os custos do Azure Marketplace são apresentados na sua moeda local.

Pode visualizar e transferir as suas faturas do Azure Marketplace a partir do portal do Azure seguindo estes passos:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. Procure **Cost Management + Faturação**.
1. No menu do lado esquerdo, selecione **Faturas**.
1. No menu pendente de subscrições, selecione a subscrição associada aos seus serviços do Marketplace.
1. Veja a coluna **Tipo** na lista de faturas. Se a fatura for relativa a um serviço do Marketplace, o tipo será **Azure Marketplace e Reservas**. 

    ![captura de ecrã do tipo Azure Marketplace na grelha de faturas](./media/understand-azure-marketplace-charges/marketplace-type-twd.png)

1. Para filtrar por tipo, de modo a ver apenas faturas do Azure Marketplace e das Reservas, selecione o filtro **Tipo**. Em seguida, selecione **Azure Marketplace e Reservas** no menu pendente.

    ![captura de ecrã do filtro Tipo selecionado, que mostra Azure Marketplace e Reservas selecionado no menu pendente](./media/understand-azure-marketplace-charges/type-filter.png)

1. Selecione o ícone de transferência no lado direito da fatura que pretende transferir.

    ![captura de ecrã que mostra o ícone de transferência selecionado para a fatura](./media/understand-azure-marketplace-charges/download-icon-marketplace.png)

1. Em **Fatura**, selecione o botão **Transferir**.

    ![captura de ecrã que mostra o botão Transferir da fatura no painel de contexto](./media/understand-azure-marketplace-charges/invoice-download-marketplace.png)

## <a name="pay-for-external-services-in-the-azure-portal"></a>Pagar serviços externos no portal do Azure

Se tiver um Contrato de Cliente da Microsoft (MCA) ou um Contrato de Parceiro da Microsoft (MPA), os serviços de terceiros serão faturados com o restante serviços do Azure. [Verifique o tipo de conta de faturação](#check-billing-account-type) para ver se tem acesso a um MCA ou MPA. Se tiver um MCA ou um MPA, pode pagar toda a fatura no portal do Azure. Para tal, siga os passos em [Pagar a fatura do Microsoft Azure](pay-bill.md).

Se não tiver um MCA ou um MPA, pode pagar as faturas do Marketplace no portal do Azure mediante os passos seguintes:

1. Siga os passos da secção anterior, [Ver e transferir faturas de serviços externos](#view-and-download-invoices-for-external-services) para localizar as faturas do Marketplace.
1. Selecione a ligação **Pagar agora** relativa à fatura que quer pagar.

    ![captura de ecrã que mostra a ligação Pagar agora selecionada na grelha de faturas](./media/understand-azure-marketplace-charges/pay-now-twd.png)

    >[!NOTE]
    > Só verá a ligação **Pagar agora** se o tipo da fatura for **Azure Marketplace e Reservas** e o estado de pagamento da mesma for devido ou vencido.

1. Na página nova, clique na ligação azul **Selecionar método de pagamento**.

    ![captura de ecrã que mostra a ligação Selecionar método de pagamento](./media/understand-azure-marketplace-charges/select-payment-method-pay-now-twd.png)

1. Depois de selecionar um método de pagamento, clique no botão **Pagar agora** no canto inferior esquerdo da página.
    ![captura de ecrã que mostra o botão Pagar agora selecionado](./media/understand-azure-marketplace-charges/pay-now-button-twd.png)

## <a name="change-default-payment-for-external-services"></a>Alterar o pagamento predefinido de serviços externos

Ao comprar um serviço externo, escolhe uma subscrição do Azure para o recurso. O método de pagamento da subscrição do Azure selecionada torna-se o método de pagamento para o serviço externo. Para alterar o método de pagamento de um serviço externo, tem de [alterar o método de pagamento da subscrição do Azure](../manage/change-credit-card.md) vinculada a esse serviço externo. Pode descobrir a qual subscrição o seu pedido de serviço externo está vinculada seguindo estes passos:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. Clique em **Todos os recursos** no menu de navegação à esquerda.
     ![captura de ecrã do item de menu Todos os recursos](./media/understand-azure-marketplace-charges/all-resources.png)
1. Pesquise o seu serviço externo.
1. Procure o nome da subscrição na coluna **Subscrição**.
    ![captura de ecrã do nome da subscrição do recurso](./media/understand-azure-marketplace-charges/sub-selected.png)
1. Clique no nome da subscrição e [atualize o método de pagamento ativo](../manage/change-credit-card.md).

## <a name="cancel-an-external-service-order"></a>Cancelar um pedido de serviço externo

Se quiser cancelar o pedido de serviço externo, elimine o recurso no [portal do Azure](https://portal.azure.com).

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. Clique em **Todos os recursos** no menu de navegação à esquerda.
    ![Captura de ecrã do item de menu Todos os recursos](./media/understand-azure-marketplace-charges/all-resources.png)
1. Pesquise o seu serviço externo.
1. Selecione a caixa junto do recurso que quer eliminar.
1. Selecione **Eliminar** na barra de comandos.
    ![Captura de ecrã do botão Eliminar](./media/understand-azure-marketplace-charges/delete-button.png)
1. Digite *“Sim”* na folha de confirmação.
    ![Eliminar recurso](./media/understand-azure-marketplace-charges/delete-resource.PNG)
1. Clique em **Eliminar**.

## <a name="check-billing-account-type"></a>Verificar o tipo da conta de faturação
[!INCLUDE [billing-check-account-type](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes
- [Começar a analisar os custos](../costs/quick-acm-cost-analysis.md)
