---
title: Como criar um tíquete de suporte
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
ms.openlocfilehash: 12a977e26f458fa8ee8a58c44985d9d1ae47d5c3
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692735"
---
# <a name="how-to-create-a-support-ticket-for-sql-data-warehouse"></a>Como criar um pedido de suporte para o SQL Data Warehouse
Se estiver a ter problemas com o SQL Data Warehouse, crie um pedido de suporte para que a equipa técnica possa ajudá-lo.

## <a name="create-a-support-ticket"></a>Criar um pedido de suporte
1. Abra o [Portal do Azure][Azure portal].
2. No ecrã principal, clique no separador **Ajuda + suporte**.
   
    ![Ajuda + suporte](./media/sql-data-warehouse-get-started-create-support-ticket/MainPage.PNG)
3. No painel Ajuda + Suporte, clique em **Novo pedido de suporte** e preencher no painel **Noções básicas**.

   Selecione o seu [Plano de suporte do Azure][Azure support plan].
   
   * O suporte relativo à **faturação, quota e gestão de subscrições** está disponível em todos os níveis de suporte.
   * O suporte à **correção de** conserto é fornecido por meio do suporte [Developer][Developer], [Standard][Standard], [Professional Direct][Professional Direct]ou [Premier][Premier] . Os problemas de Break-fix são problemas encontrados pelos clientes ao utilizarem o Azure, onde existe uma expetativa razoável de que a Microsoft causou o problema.
   * A **orientação para programadores** e os **serviços de aconselhamento** estão disponíveis nos níveis de [Suporte Direto Profissional][Professional Direct] e [Premier][Premier]. 
     
     Se tiver um plano de suporte Premier, também pode comunicar problemas relacionados com o SQL Data Warehouse no [Portal online do Microsoft Premier][Microsoft Premier online portal].  Consulte [planos de suporte do Azure][Azure support plan] para saber mais sobre os vários planos de suporte, incluindo escopo, tempos de resposta, preços, etc.  Para perguntas frequentes sobre o suporte do Azure, consulte [perguntas frequentes sobre o suporte do Azure][Azure support FAQs].  
        
     ![Painel Noções básicas](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_1.PNG)
     ![Noções básicas blade1](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_2.PNG)
4. Preencha o painel **Problema**.
    ![Problem_blade](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_3.PNG)
   
   > [!NOTE]
   > Por predefinição, cada SQL Server (por exemplo, myserver.database.windows.net) tem uma **Quota de DTU** de 45.000. Esta quota é apenas um limite de segurança. Pode aumentar a quota ao criar um pedido de suporte e selecionar *Quota* como o tipo de pedido. Para calcular suas necessidades de DTU, multiplique 7,5 pelo total de [DWU][DWU] necessário. Por exemplo, se pretende alojar dois DW6000s num SQL Server, então deve pedir uma quota de DTU de 90.000.  Pode ver o consumo de DTU atual a partir do painel SQL Server no portal. Tanto as bases de dados em pausa como as que não estão em pausa contam para a quota de DTU. 
   > 
   > 
   
5. Preencha as **informações de contacto**.
   ![Contact_information](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_4.PNG)

    
6. Clique em **Criar** para submeter o pedido de suporte.

## <a name="monitor-a-support-ticket"></a>Monitorizar um pedido de suporte
Depois de ter submetido o pedido de suporte, a equipa de suporte do Azure irá contactá-lo. Para verificar o estado e os detalhes do pedido, clique em **Todos os pedidos de suporte** no dashboard.

![Verificar o estado](./media/sql-data-warehouse-get-started-create-support-ticket/Monitor_ticket.PNG)

## <a name="other-resources"></a>Outros recursos
Além disso, pode ligar-se à comunidade do SQL Data Warehouse no [Stack Overflow][Stack Overflow] ou no [Fórum MSDN do Azure SQL Data Warehouse][Azure SQL Data Warehouse MSDN forum].

<!--Image references--> 

<!--Article references--> 
[DWU]: ./sql-data-warehouse-overview-what-is.md

<!--MSDN references--> 

<!--Other web references--> 
[Azure portal]: https://portal.azure.com/
[Azure support plan]: https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/  
[Developer]: https://azure.microsoft.com/support/plans/developer/  
[Standard]: https://azure.microsoft.com/support/plans/standard/  
[Professional Direct]: https://azure.microsoft.com/support/plans/prodirect/  
[Premier]: https://azure.microsoft.com/support/plans/premier/  
[Azure support FAQs]: https://azure.microsoft.com/support/faq/
[Microsoft Premier online portal]: https://premier.microsoft.com/
[Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw/
[Azure SQL Data Warehouse MSDN forum]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/

