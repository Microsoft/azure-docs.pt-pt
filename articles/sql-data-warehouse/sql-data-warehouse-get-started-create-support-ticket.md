---
title: Como criar um bilhete de apoio
description: Como criar um pedido de suporte no Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 04fd6267782ab89e12288dadb64d28d1ef1746b5
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717833"
---
# <a name="how-to-create-a-support-ticket-for-sql-data-warehouse"></a>Como criar um pedido de suporte para o SQL Data Warehouse
Se você está tendo algum problema com seu Armazém de Dados SQL, crie um bilhete de suporte para que a equipe de suporte de engenharia possa ajudá-lo.

## <a name="create-a-support-ticket"></a>Criar um pedido de suporte
1. Abra o [Portal do Azure](https://portal.azure.com/).
2. No ecrã principal, clique no separador **Ajuda + suporte**.
   
    ![Ajuda + suporte](./media/sql-data-warehouse-get-started-create-support-ticket/MainPage.PNG)
3. No painel Ajuda + Suporte, clique em **Novo pedido de suporte** e preencher no painel **Noções básicas**.

   Selecione o seu [Plano de suporte do Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/).
   
   * O suporte relativo à **faturação, quota e gestão de subscrições** está disponível em todos os níveis de suporte.
   * O suporte **break-fix** é fornecido através do [Suporte de Desenvolvimento](https://azure.microsoft.com/support/plans/developer/), [Standard](https://azure.microsoft.com/support/plans/standard/), [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/)ou [Premier.](https://azure.microsoft.com/support/plans/premier/) Questões de break-fix são problemas que os clientes tenham durante a utilização do Azure quando existir a probabilidade razoável de a Microsoft ter causado o problema.
   * A **orientação para programadores** e os **serviços de aconselhamento** estão disponíveis nos níveis de [Suporte Direto Profissional](https://azure.microsoft.com/support/plans/prodirect/) e [Premier](https://azure.microsoft.com/support/plans/premier/). 
     
     Se tiver um plano de suporte Premier, também pode comunicar problemas relacionados com o SQL Data Warehouse no [Portal online do Microsoft Premier](https://premier.microsoft.com/). Consulte [os planos](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/) de apoio do Azure para saber mais sobre os vários planos de apoio, incluindo âmbito, tempos de resposta, preços, etc.  Para perguntas frequentes sobre o suporte do Azure, consulte [as FAQs de suporte do Azure.](https://azure.microsoft.com/support/faq/)  
        
     ![Painel Noções básicas](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_1.PNG)
     ![Noções básicas blade1](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_2.PNG)
4. Preencha o painel **Problema**.

    ![Problem_blade](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_3.PNG)
   
   > [!NOTE]
   > Por predefinição, cada SQL Server (por exemplo, myserver.database.windows.net) tem uma **Quota de DTU** de 45.000. Esta quota é apenas um limite de segurança. Pode aumentar a quota ao criar um pedido de suporte e selecionar *Quota* como o tipo de pedido. Para calcular as suas necessidades de DTU, multiplique 7,5 pelo [DWU](sql-data-warehouse-overview-what-is.md) total necessário. Por exemplo, se pretende alojar dois DW6000s num SQL Server, então deve pedir uma quota de DTU de 90.000.  Pode ver o consumo de DTU atual a partir do painel SQL Server no portal. Tanto as bases de dados em pausa como as que não estão em pausa contam para a quota de DTU. 
   > 
   > 
   
5. Preencha as **informações de contacto**.

   ![Contact_information](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_4.PNG)

    
6. Clique em **Criar** para submeter o pedido de suporte.

## <a name="monitor-a-support-ticket"></a>Monitorizar um pedido de suporte
Depois de ter apresentado o pedido de apoio, a equipa de apoio do Azure entrará em contacto consigo. Para verificar o estado e os detalhes do pedido, clique em **Todos os pedidos de suporte** no dashboard.

![Verificar o estado](./media/sql-data-warehouse-get-started-create-support-ticket/Monitor_ticket.PNG)

## <a name="other-resources"></a>Outros recursos
Também pode ligar-se à comunidade SQL Data Warehouse no [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw/) ou através do fórum De dados [Azure SQL MSDN](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/).

 
