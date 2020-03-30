---
title: Solicitar aumentos de quotas e obter apoio
description: Como criar um pedido de apoio no portal Azure para a Azure Synapse Analytics. Solicitar aumentos de quotas ou obter apoio de resolução de problemas.
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/10/2020
author: kevinvngo
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: eaa6ceded9893b278b28d517cbddb303f8469e10
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350886"
---
# <a name="request-quota-increases-and-get-support-for-azure-synapse-analytics"></a>Pedidos de quota aumenta e obter apoio para Azure Synapse Analytics

Este artigo descreve como enviar um bilhete de apoio no portal Azure para a Azure Synapse Analytics. Este processo permite-lhe solicitar um aumento de quota ou apresentar um pedido de apoio técnico à equipa de apoio à engenharia.

## <a name="create-a-support-ticket"></a>Criar um pedido de suporte

Utilize os seguintes passos para criar um novo pedido de apoio do portal Azure para a Azure Synapse Analytics.

1. No menu [do portal Azure,](https://portal.azure.com) selecione **Ajuda + suporte**.

   ![O link de ajuda + suporte](./media/sql-data-warehouse-get-started-create-support-ticket/help-plus-support.png)


1. Em **Ajuda + suporte,** selecione **Novo pedido de suporte**.

    ![Criar um novo pedido de apoio](./media/sql-data-warehouse-get-started-create-support-ticket/new-support-request.png)

1. Reveja o seu plano de [apoio Azure.](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/)

   * O suporte relativo à **faturação, quota e gestão de subscrições** está disponível em todos os níveis de suporte.
   * O suporte **break-fix** é fornecido através do [Suporte de Desenvolvimento](https://azure.microsoft.com/support/plans/developer/), [Standard](https://azure.microsoft.com/support/plans/standard/), [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/)ou [Premier.](https://azure.microsoft.com/support/plans/premier/) Os problemas de Break-fix são problemas encontrados pelos clientes ao utilizarem o Azure, onde existe uma expetativa razoável de que a Microsoft causou o problema.
   * A **orientação para programadores** e os **serviços de aconselhamento** estão disponíveis nos níveis de [Suporte Direto Profissional](https://azure.microsoft.com/support/plans/prodirect/) e [Premier](https://azure.microsoft.com/support/plans/premier/).

   Se tiver um plano de suporte Premier, também pode reportar problemas da Azure Synapse Analytics no [portal online Microsoft Premier](https://premier.microsoft.com/). Consulte [os planos](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/) de apoio do Azure para saber mais sobre os vários planos de apoio, incluindo âmbito, tempos de resposta, preços, etc.  Para perguntas frequentes sobre o suporte do Azure, consulte [as FAQs de suporte do Azure.](https://azure.microsoft.com/support/faq/)

1. Para **o tipo de problema,** selecione o tipo de problema apropriado. Para problemas de correção de rutura, selecione **Técnico**. Para pedidos de aumento de quotas, selecione limites de **serviço e subscrição (quotas)**.

   ![Selecione um tipo de problema](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-issue-type.png)  

   > [!NOTE]
   > Este resto deste artigo centra-se nos pedidos de aumento de quotas. Mas também pode selecionar **Técnico** aqui para pedidos de apoio à resolução de problemas. Se selecionar **Técnico,** é-lhe pedido que forneça um resumo e, em seguida, identifique um tipo de problema selecionando o tipo de **problema Select**. Pode ver soluções para ajudar a resolver o seu problema. Se as soluções apresentadas não resolverem o seu problema, selecione **Next:Details** e preencha o formulário para submeter o bilhete de apoio.

1. Para pedidos de aumento de quota, **selecione Azure Synapse Analytics** para o **tipo quota**. Em seguida, selecione **Seguinte: Soluções >>**.

   ![Selecione um tipo de quota](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-type.png)

1. Na janela **Detalhes,** selecione **Fornecer detalhes** para introduzir informações adicionais.

   ![O link "Fornecer detalhes"](./media/sql-data-warehouse-get-started-create-support-ticket/provide-details-link.png)

## <a name="quota-request-types"></a>Tipos de pedidos de quotas

Clique **em Fornecer detalhes** mostra a janela de **detalhes da Quota** que lhe permite adicionar informações adicionais. As seguintes secções descrevem os diferentes pedidos de quota disponíveis para a Azure Synapse Analytics.

### <a name="data-warehouse-units-dwus-per-server"></a>Unidades de Armazém de Dados (DWUs) por servidor

Utilize os seguintes passos para solicitar um aumento das DWUs por servidor.

1. Selecione as Unidades de Depósito de **Dados (DTUs) por** tipo de quota do servidor.

1. Na lista **de Recursos,** selecione o recurso para o alvo.

1. No campo de **quotas de Pedido,** insira o novo limite de DWU que está a solicitar.

   ![Detalhes da quota da DWU](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-dwus.png)

### <a name="servers-per-subscription"></a>Servidores por subscrição

Utilize os seguintes passos para solicitar um aumento do número de servidores por subscrição.

1. Selecione os Servidores por tipo de quota **de subscrição.**

1. Na lista **de localização,** selecione a região azure para utilizar. A quota é por subscrição em cada região.

1. No **campo de cotações New,** insira o seu pedido para o número máximo de servidores naquela região.

   ![Detalhes da quota dos servidores](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-servers.png)

### <a name="enable-subscription-access-to-a-region"></a>Permitir o acesso por subscrição a uma região

Alguns tipos de ofertas não estão disponíveis em todas as regiões. Pode ver um erro como o seguinte:

`This location is not available for subscription`

Se a sua subscrição necessitar de acesso numa determinada região, utilize a opção de pedido de **outra quota** para solicitar acesso. No seu pedido, especifique a oferta e os detalhes sKU que pretende ativar para a região. Para explorar a oferta e as opções SKU, consulte o preço da [Azure Synapse Analytics.](https://azure.microsoft.com/pricing/details/synapse-analytics/)

![Outros detalhes da quota](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-whitelisting.png)

## <a name="submit-your-request"></a>Submeta o seu pedido

O passo final é preencher os detalhes restantes do seu pedido de suporte da Base de Dados SQL. Em seguida, selecione **Next: Revê + criar>>**, e depois de rever os detalhes do pedido, clique em **Criar** para submeter o pedido.

## <a name="monitor-a-support-ticket"></a>Monitorizar um pedido de suporte

Depois de ter apresentado o pedido de apoio, a equipa de apoio do Azure entrará em contacto consigo. Para verificar o estado e os detalhes do pedido, clique em **Todos os pedidos de suporte** no dashboard.

![Verificar o estado](./media/sql-data-warehouse-get-started-create-support-ticket/monitor-ticket.png)

## <a name="other-resources"></a>Outros recursos

Também pode conectar-se com a comunidade Azure Synapse Analytics no [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-synapse+or+azure-sql-data-warehouse) ou através do [fórum Azure SQL Data Warehouse MSDN](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/).

