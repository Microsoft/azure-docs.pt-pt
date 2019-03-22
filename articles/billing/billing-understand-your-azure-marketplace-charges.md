---
title: Compreender os encargos de serviços externos do Azure | Documentos da Microsoft
description: Saiba mais sobre a faturação de serviços externos, anteriormente conhecido como Marketplace, de custos no Azure.
services: ''
documentationcenter: ''
author: adpick
manager: adpick
editor: ''
tags: billing
ms.assetid: 5e0e2a3c-d111-4054-8508-0c111c1b749b
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ae9c2c975bf49725be1858ad02a1c4b90ef58a7f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57871267"
---
# <a name="understand-your-azure-billing-for-external-service-charges"></a>Compreender a faturação do Azure para encargos de serviços externos
Serviços externos são publicados por fornecedores de software de terceiros no Azure marketplace. Por exemplo, o SendGrid é um serviços externos, que pode comprar no Azure, mas não está publicado pela Microsoft.

Quando aprovisiona um novo serviço externo ou recurso, é apresentado um aviso:

![Aviso de compra do Marketplace](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

> [!NOTE]
> Serviços externos são publicados por empresas que não sejam Microsoft, mas, às vezes, os produtos da Microsoft também são categorizados como serviços externos.
> 
> 

## <a name="how-external-services-are-billed"></a>Como os serviços externos são faturados
- Serviços externos são faturados em separado. Eles são tratados como pedidos individuais na sua subscrição do Azure. O período de faturação para cada serviço é definido quando comprar o serviço. Não deve ser confundido com o período de faturação da subscrição na qual comprou. Também deve receber faturas separadas e seu cartão de crédito é cobrado separadamente.
- Cada serviço externo tem um modelo de faturação diferente. Alguns serviços são cobrados de forma pay as you go, enquanto outros utilizam um modelo de pagamento com base mensal. Precisa de um cartão de crédito para serviços externos do Azure, não é possível comprar serviços externos com o pagamento de nota fiscal.
- Não é possível utilizar os créditos mensais gratuitos para serviços externos. Se estiver a utilizar uma subscrição do Azure, que inclui [gratuita créditos](https://azure.microsoft.com/pricing/spending-limits/), não pode ser aplicadas a contas de serviço externo. Utilize um cartão de crédito para comprar serviços externos.

## <a name="view-external-service-spending-and-history-in-the-azure-portal"></a>Gastos de serviço externo de exibição e o histórico no portal do Azure
Pode exibir uma lista dos serviços externos que se encontram em cada subscrição dentro de [portal do Azure](https://portal.azure.com/): 

1. Inicie sessão para o [portal do Azure](https://portal.azure.com/) como administrador de conta.
2. No Hub menu, selecione **subscrições**.
   
    ![Selecione as subscrições no Hub menu](./media/billing-understand-your-azure-marketplace-charges/sub-button.png) 
3. Na **subscrições** painel, selecione a subscrição que pretende visualizar e, em seguida, selecione **serviços externos**.
   
    ![Selecione uma subscrição no painel de faturação](./media/billing-understand-your-azure-marketplace-charges/select-sub-external-services.png)
4. Deverá ver cada um dos seus pedidos de serviço externo, o nome do publicador, a camada de serviços comprou, nome que deu o recurso e o atual estado da encomenda. Para ver faturas antigas, selecione um serviço externo.
   
    ![Selecione um serviço externo](./media/billing-understand-your-azure-marketplace-charges/external-service-blade2.png)
5. A partir daqui, pode ver anteriores quantidades de fatura, incluindo a divisão de impostos.
   
    ![Ver serviços externos, histórico de faturação](./media/billing-understand-your-azure-marketplace-charges/billing-overview-blade.png)

## <a name="view-external-service-spending-for-enterprise-agreement-ea-customers"></a>Vista de serviço externo de gastos para clientes Enterprise Agreement (EA)
Os clientes com EA podem ver o serviço externo de gastos e transferir relatórios no portal de EA. Ver [do Azure Marketplace para clientes com EA](https://ea.azure.com/helpdocs/azureMarketplace) para começar a utilizar.

## <a name="manage-payment-methods-for-external-service-orders"></a>Gerir métodos de pagamento para pedidos de serviço externo
Atualizar os métodos de pagamento para pedidos de serviço externo a partir da [Centro de contas](https://account.windowsazure.com/).

> [!NOTE]
> Se tiver adquirido sua assinatura com uma conta profissional ou escolar [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para fazer alterações ao seu método de pagamento.
> 
> 

1. Inicie sessão para o [Centro de contas](https://account.windowsazure.com/) e [navegue para o **marketplace** separador](https://account.windowsazure.com/Store)
   
    ![Selecione o marketplace no Centro de contas](./media/billing-understand-your-azure-marketplace-charges/select-marketplace.png)
2. Selecione o serviço externo que pretende gerir
   
    ![Selecione o serviço externo que pretende gerir](./media/billing-understand-your-azure-marketplace-charges/select-ext-service.png)
3. Clique em **alterar método de pagamento** no lado direito da página. Esta ligação traz até um portal diferente para gerir o seu método de pagamento.
   
    ![Resumo de ordem](./media/billing-understand-your-azure-marketplace-charges/change-payment.PNG)
4. Clique em **editar as informações** e siga as instruções para atualizar suas informações de pagamento.
   
    ![Selecione editar as informações](./media/billing-understand-your-azure-marketplace-charges/edit-info.png)

## <a name="cancel-an-external-service-order"></a>Cancelar um pedido de serviço externo
Se quiser cancelar o seu pedido de serviço externo, eliminar o recurso a [portal do Azure](https://portal.azure.com).

![Eliminar Recurso](./media/billing-understand-your-azure-marketplace-charges/deleteMarketplaceOrder.PNG)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

