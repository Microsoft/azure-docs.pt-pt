---
title: Como criar um pedido de suporte do Azure SQL Data Warehouse | Documentos da Microsoft
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
ms.openlocfilehash: b3ffc9794967f44708d8330d4dc5d5de11b794d6
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58079184"
---
# <a name="how-to-create-a-support-ticket-for-sql-data-warehouse"></a>Como criar um pedido de suporte para o SQL Data Warehouse
Se estiver a ter problemas com o SQL Data Warehouse, crie um pedido de suporte para que a equipa técnica possa ajudá-lo.

## <a name="create-a-support-ticket"></a>Criar um pedido de suporte
1. Abra o [portal do Azure][Azure portal].
2. No ecrã principal, clique no separador **Ajuda + suporte**.
   
    ![Ajuda + suporte](./media/sql-data-warehouse-get-started-create-support-ticket/MainPage.PNG)
3. No painel Ajuda + Suporte, clique em **Novo pedido de suporte** e preencher no painel **Noções básicas**.

   Selecione o seu [plano de suporte do Azure][Azure support plan].
   
   * O suporte relativo à **faturação, quota e gestão de subscrições** está disponível em todos os níveis de suporte.
   * O suporte **com garantia de reparação e assistência** é fornecido através de suporte de [Programador][Developer], [Standard][Standard], [Suporte Direto Profissional][Professional Direct] ou [Premier][Premier]. Os problemas de Break-fix são problemas encontrados pelos clientes ao utilizarem o Azure, onde existe uma expetativa razoável de que a Microsoft causou o problema.
   * A **orientação para programadores** e os **serviços de aconselhamento** estão disponíveis nos níveis de [Suporte Direto Profissional][Professional Direct] e [Premier][Premier]. 
     
     Se tiver um plano de suporte Premier, também pode comunicar problemas relacionados com o SQL Data Warehouse no [Portal online do Microsoft Premier][Microsoft Premier online portal].  Veja os [Planos de suporte do Azure][Azure support plan] para saber mais sobre os vários planos de suporte, incluindo o âmbito, os tempos de resposta, os preços, etc.  Para perguntas mais frequentes sobre o suporte do Azure, veja [FAQ do Suporte do Azure][Azure support FAQs].  
        
     ![Painel Noções básicas](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_1.PNG)
     ![Noções básicas blade1](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_2.PNG)
4. Preencha o painel **Problema**.
    ![Problem_blade](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_3.PNG)
   
   > [!NOTE]
   > Por predefinição, cada SQL Server (por exemplo, myserver.database.windows.net) tem uma **Quota de DTU** de 45.000. Esta quota é apenas um limite de segurança. Pode aumentar a quota ao criar um pedido de suporte e selecionar *Quota* como o tipo de pedido. Para calcular as necessidades de DTU, multiplique 7,5 pelo total de [DWU][DWU] necessários. Por exemplo, se pretende alojar dois DW6000s num SQL Server, então deve pedir uma quota de DTU de 90.000.  Pode ver o consumo de DTU atual a partir do painel SQL Server no portal. Tanto as bases de dados em pausa como as que não estão em pausa contam para a quota de DTU. 
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

