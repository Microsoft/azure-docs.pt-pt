---
title: Pedido de quota aumenta e obtém apoio
description: Como criar um pedido de apoio no portal Azure para a Azure Synapse Analytics. Solicite aumentos de quota ou obter apoio à resolução de problemas.
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/10/2020
author: kevinvngo
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: acc05d6367a0dc4334bc1d9e3cbb340bdb8d24c0
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98677436"
---
# <a name="request-quota-increases-and-get-support-for-azure-synapse-analytics"></a>Quota de pedido aumenta e obtém apoio para Azure Synapse Analytics

Este artigo descreve como enviar um bilhete de apoio no portal Azure para Azure Synapse Analytics. Este processo permite-lhe solicitar um aumento de quota ou apresentar um pedido de apoio técnico para a equipa de apoio à engenharia.

## <a name="create-a-support-ticket"></a>Criar um pedido de suporte

Utilize os seguintes passos para criar um novo pedido de suporte do portal Azure para a Azure Synapse Analytics.

1. No menu [do portal Azure,](https://portal.azure.com) selecione **Help + support**.

   ![O link de suporte Help +](./media/sql-data-warehouse-get-started-create-support-ticket/help-plus-support.png)


1. No **Suporte Help +**, selecione Novo pedido de **suporte**.

    ![Criar um novo pedido de apoio](./media/sql-data-warehouse-get-started-create-support-ticket/new-support-request.png)

1. Reveja o seu [plano de apoio Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/).

   * O suporte relativo à **faturação, quota e gestão de subscrições** está disponível em todos os níveis de suporte.
   * O suporte **de break-fix** é fornecido através do [suporte de Developer,](https://azure.microsoft.com/support/plans/developer/) [Standard,](https://azure.microsoft.com/support/plans/standard/) [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/)ou [Premier.](https://azure.microsoft.com/support/plans/premier/) Os problemas de Break-fix são problemas encontrados pelos clientes ao utilizarem o Azure, onde existe uma expetativa razoável de que a Microsoft causou o problema.
   * A **orientação para programadores** e os **serviços de aconselhamento** estão disponíveis nos níveis de [Suporte Direto Profissional](https://azure.microsoft.com/support/plans/prodirect/) e [Premier](https://azure.microsoft.com/support/plans/premier/).

   Se tiver um plano de suporte Premier, também pode reportar problemas do Azure Synapse Analytics no portal online da [Microsoft Premier](https://premier.microsoft.com/). Consulte os planos de apoio do [Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/) para saber mais sobre os vários planos de apoio, incluindo âmbito, tempos de resposta, preços, etc.  Para perguntas frequentes sobre o suporte do Azure, consulte [as FAQs de suporte do Azure.](https://azure.microsoft.com/support/faq/)

1. Para **o tipo de emissão**, selecione o tipo de emissão apropriado. Para problemas de correção de rutura, selecione **Técnico**. Para pedidos de aumento de quota, selecione **Limites de Serviço e subscrição (quotas)**.

   ![Selecione um tipo de problema](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-issue-type.png)  

   > [!NOTE]
   > Este remanescente deste artigo centra-se nos pedidos de aumento de quotas. Mas também pode selecionar **Técnico** aqui para pedidos de apoio à resolução de problemas. Se selecionar **Técnico,** é-lhe pedido que forneça um resumo e, em seguida, identifique um tipo de problema selecionando **o tipo de problema Select**. Pode ver soluções para ajudar a resolver o seu problema. Se as soluções apresentadas não resolverem o seu problema, selecione **Next:Details** e preencha o formulário para submeter o bilhete de apoio.

1. Para pedidos de aumento de quota, selecione **Azure Synapse Analytics** para o **tipo quota**. Em seguida, selecione **Seguinte: Soluções >>**.

   ![Selecione um tipo de quota](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-type.png)

1. Na janela **Detalhes,** selecione **Introduzir detalhes** para introduzir informações adicionais.

   ![O link "Fornecer detalhes"](./media/sql-data-warehouse-get-started-create-support-ticket/provide-details-link.png)

## <a name="quota-request-types"></a>Tipos de pedidos de quotas

Selecionar **detalhes do Enter** exibe a janela de detalhes da **Quota** que lhe permite adicionar informações adicionais. As secções seguintes descrevem os diferentes pedidos de quota disponíveis para a Azure Synapse Analytics.

### <a name="synapse-sql-pool-data-warehouse-units-dwus-per-server"></a>Synapse SQL pool Data Warehouse Units (DWUs) por servidor

Utilize os seguintes passos para solicitar um aumento do DWUs por servidor.

1. Selecione o **POOL DWUs synapse por** tipo de quota de servidor.

1. Selecione o **recurso** a que pretende aplicar o aumento da quota utilizando a lista de abandono.

1. Introduza a sua nova quota na secção **de quotas request.**

1. Selecione **Guardar e continuar**.

   ![Detalhes da quota da DWU](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-dwus.png)


### <a name="servers-per-subscription"></a>Servidores por subscrição

Para solicitar um aumento do número de servidores por subscrição, terá de completar os seguintes passos:

1. Selecione os **Servidores SQL por subscrição** como o tipo de quota.

1. Na lista **de Localização,** selecione a região Azure para utilizar. A quota é por subscrição em cada região.

1. No campo **de quotas Request,** insira o seu pedido para o número máximo de servidores naquela região.

   ![Detalhes da quota dos servidores](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-servers.png)



1. Selecione **Guardar e continuar**.

Alguns tipos de oferta não estão disponíveis em todas as regiões. Pode ver o seguinte erro:

![Erro de acesso à região](./media/sql-data-warehouse-get-started-create-support-ticket/region-access-error.png)

### <a name="enable-subscription-access-to-a-region"></a>Permitir o acesso por subscrição a uma região

Para permitir o acesso da região a uma subscrição, terá de completar os seguintes passos:  

1. Selecione o **conjunto Synapse SQL (data warehouse) tipo** de quota de acesso à região.

1. Selecione a região escolhendo uma **localização** da lista de dropdown.

1. Indique o seu requisito de desempenho DWU na secção necessária da **DWU.**

1. Insira a sua **Descrição dos requisitos do negócio**. 

1. Selecione **Guardar e continuar**.

![Acesso à região](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-region.png)


### <a name="for-other-quota-requests"></a>Para outros pedidos de quotas

Selecione **Outro pedido** de quota do menu de entrega do tipo de quota para outros tipos de pedido de quota:

![Outros detalhes da quota](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details.png)

## <a name="submit-your-request"></a>Submeter o pedido

O passo final é preencher os restantes detalhes do seu pedido de suporte sql Database. Em seguida, selecione **Seguinte: Revisão + criar>>**.

![Rever criar detalhes](./media/sql-data-warehouse-get-started-create-support-ticket/review-create-details.png)

Depois de rever os dados do pedido, selecione **Criar** para submeter o pedido.

![Criar bilhete](./media/sql-data-warehouse-get-started-create-support-ticket/create-ticket.png)

## <a name="monitor-a-support-ticket"></a>Monitorizar um pedido de suporte

Depois de ter apresentado o pedido de apoio, a equipa de apoio do Azure entrará em contacto consigo. Para verificar o estado do seu pedido e detalhes, selecione Todos os pedidos de suporte no painel de **instrumentos.**

![Verificar o estado](./media/sql-data-warehouse-get-started-create-support-ticket/monitor-ticket.png)

## <a name="other-resources"></a>Outros recursos

Também pode ligar-se à comunidade Azure Synapse Analytics no [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-synapse+or+azure-sql-data-warehouse) ou através da página de perguntas do Microsoft Q&Uma página de perguntas para [a Azure Synapse Analytics](/answers/topics/azure-synapse-analytics.html).