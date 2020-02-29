---
title: Como criar um bilhete de apoio
description: Como criar um bilhete de apoio no Azure Synapse Analytics.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 9623a878ea009ec7363501c09c324baa6d0fa7ad
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195708"
---
# <a name="how-to-create-a-support-ticket-for-azure-synapse-analytics"></a>Como criar um bilhete de apoio para a Azure Synapse Analytics
Se você está tendo algum problema com Azure Synapse Analytics, crie um bilhete de apoio para que a equipe de apoio de engenharia possa ajudá-lo.

## <a name="create-a-support-ticket"></a>Criar um pedido de suporte
1. Abra o [Portal do Azure](https://portal.azure.com/).
1. No ecrã principal, clique no separador **Ajuda + suporte**.
   
    ![Ajuda + suporte](./media/sql-data-warehouse-get-started-create-support-ticket/main-page.png)

1. Reveja o seu plano de [apoio Azure.](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/)
   
   * O suporte relativo à **faturação, quota e gestão de subscrições** está disponível em todos os níveis de suporte.
   * O suporte **break-fix** é fornecido através do [Suporte de Desenvolvimento](https://azure.microsoft.com/support/plans/developer/), [Standard](https://azure.microsoft.com/support/plans/standard/), [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/)ou [Premier.](https://azure.microsoft.com/support/plans/premier/) Os problemas de Break-fix são problemas encontrados pelos clientes ao utilizarem o Azure, onde existe uma expetativa razoável de que a Microsoft causou o problema.
   * A **orientação para programadores** e os **serviços de aconselhamento** estão disponíveis nos níveis de [Suporte Direto Profissional](https://azure.microsoft.com/support/plans/prodirect/) e [Premier](https://azure.microsoft.com/support/plans/premier/). 
     
     Se tiver um plano de suporte Premier, também pode comunicar problemas relacionados com o SQL Data Warehouse no [Portal online do Microsoft Premier](https://premier.microsoft.com/). Consulte [os planos](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/) de apoio do Azure para saber mais sobre os vários planos de apoio, incluindo âmbito, tempos de resposta, preços, etc.  Para perguntas frequentes sobre o suporte do Azure, consulte [as FAQs de suporte do Azure.](https://azure.microsoft.com/support/faq/)
1. Na página **Ajuda + Suporte,** selecione **Novo pedido**de suporte . Selecione um tipo de problema a partir do menu drop down. Em seguida, continue a preencher a informação no separador **Basics.** Introduza um **resumo** do seu problema e, em seguida, selecione um **tipo de Problema** do menu e selecione guardar.

    ![Ajuda + suporte](./media/sql-data-warehouse-get-started-create-support-ticket/issue-type.png)

   > [!NOTE]
   > Por predefinição, cada SQL Server (por exemplo, myserver.database.windows.net) tem uma **Quota de DTU** de 45.000. Esta quota é apenas um limite de segurança. Pode aumentar a quota ao criar um pedido de suporte e selecionar *Quota* como o tipo de pedido. Para calcular as suas necessidades de DTU, multiplique 7,5 pelo [DWU](sql-data-warehouse-overview-what-is.md) total necessário. Por exemplo, se pretende alojar dois DW6000s num SQL Server, então deve pedir uma quota de DTU de 90.000.  Pode ver o consumo de DTU atual a partir do painel SQL Server no portal. Tanto as bases de dados em pausa como as que não estão em pausa contam para a quota de DTU. 
   > 

1. Pode ver soluções para ajudar a resolver o seu problema. Se as soluções apresentadas não resolverem o seu problema, selecione **Next:Details**. Envie os detalhes do seu problema e os seus dados de contacto. Selecione **Next:Review + crie**
![Detalhes](./media/sql-data-warehouse-get-started-create-support-ticket/details.png)

    
1. Reveja as suas informações e selecione **Criar** na parte inferior do formulário para submeter o pedido de apoio.

## <a name="monitor-a-support-ticket"></a>Monitorizar um pedido de suporte
Depois de ter apresentado o pedido de apoio, a equipa de apoio do Azure entrará em contacto consigo. Para verificar o estado e os detalhes do pedido, clique em **Todos os pedidos de suporte** no dashboard.

![Verificar o estado](./media/sql-data-warehouse-get-started-create-support-ticket/monitor-ticket.png)

## <a name="other-resources"></a>Outros recursos
Também pode ligar-se à comunidade SQL Data Warehouse no [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw/) ou através do fórum De dados [Azure SQL MSDN](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/).

 
