---
title: Restaurar o Azure SQL Data Warehouse (portal do Azure) | Documentos da Microsoft
description: Tarefas do portal do Azure para restaurar o Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 2d59235b067d9571bc8b64c33799431be6489502
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58087215"
---
# <a name="restore-azure-sql-data-warehouse-portal"></a>Restaurar o Azure SQL Data Warehouse (portal)
> [!div class="op_single_selector"]
> * [Descrição geral][Overview]
> * [Portal][Portal]
> * [PowerShell][PowerShell]
> * [REST][REST]
> 
> 
> Neste artigo, irá aprender a restaurar o Azure SQL Data Warehouse com o portal do Azure.

## <a name="before-you-begin"></a>Antes de começar
**Verifique a sua capacidade DTU.** Cada instância do SQL Data Warehouse é hospedada por um SQL server (por exemplo, myserver.database.windows.net), que tem uma quota de unidade (DTU) de débito de dados predefinida. Antes de pode restaurar o SQL Data Warehouse, certifique-se de que o seu SQL server tem quota de DTU suficientemente restante para a base de dados que está a restaurar. Para saber como calcular a quota de DTU ou para pedir mais DTUs, consulte [solicitar uma alteração de quota DTU][Request a DTU quota change].

## <a name="restore-an-active-or-paused-database"></a>Restaurar uma base de dados do Active Directory ou em pausa
Para restaurar uma base de dados:

1. Inicie sessão no [Portal do Azure][Azure portal].
2. No painel esquerdo, selecione **navegue**e, em seguida, selecione **servidores SQL**.

    ![Selecione Procurar > servidores SQL](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)
3. Localize o seu servidor e, em seguida, selecioná-lo.

    ![Selecione o seu servidor](./media/sql-data-warehouse-restore-database-portal/01-select-server.png)
4. Localizar a instância do SQL Data Warehouse que pretende restaurar a partir de e, em seguida, selecioná-lo.

    ![Selecione a instância do SQL Data Warehouse para restaurar](./media/sql-data-warehouse-restore-database-portal/01-select-active-dw.png)
5. Na parte superior do painel do armazém de dados, selecione **restaurar**.

    ![Selecione o restauro](./media/sql-data-warehouse-restore-database-portal/01-select-restore-from-active.png)
6. Especifique um novo **nome da base de dados**.
7. Selecione a versão mais recente **ponto de restauro**.

   Certifique-se de que escolha o ponto de restauro mais recente. Uma vez que os pontos de restauro são apresentados na hora Universal Coordenada (UTC), a opção padrão pode não ser o ponto de restauro mais recente.

      ![Selecione um ponto de restauro](./media/sql-data-warehouse-restore-database-portal/01-restore-blade-from-active.png)
8. Selecione **OK**.
9. O processo de restauro de base de dados será iniciado e pode usar **notificações** para monitorizar o processo.

> [!NOTE]
> Após concluir o restauro, pode configurar a base de dados recuperada seguindo [configurar a sua base de dados após a recuperação][Configure your database after recovery].
>
>

## <a name="restore-a-deleted-database"></a>Restaurar uma base de dados eliminada
Para restaurar uma base de dados eliminada:

1. Inicie sessão no [Portal do Azure][Azure portal].
2. No painel esquerdo, selecione **navegue**e, em seguida, selecione **servidores SQL**.

    ![Selecione Procurar > servidores SQL](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)
3. Localize o seu servidor e, em seguida, selecioná-lo.

    ![Selecione o seu servidor](./media/sql-data-warehouse-restore-database-portal/02-select-server.png)
4. Desloque para baixo para o **operações** secção no painel do seu servidor.
5. Selecione o **bases de dados eliminadas** mosaico.

    ![Selecione o mosaico de bases de dados eliminadas](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dws.png)
6. Selecione a base de dados eliminada que pretende restaurar.

    ![Selecione uma base de dados para restaurar](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dw.png)
7. Especifique um novo **nome da base de dados**.

    ![Adicionar um nome para a base de dados](./media/sql-data-warehouse-restore-database-portal/02-restore-blade-from-deleted.png)
8. Selecione **OK**.
9. O processo de restauro de base de dados será iniciado e pode usar **notificações** para monitorizar o processo.

> [!NOTE]
> Para configurar a sua base de dados após terminar o restauro, veja [configurar a sua base de dados após a recuperação][Configure your database after recovery].
>
>

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre as funcionalidades de continuidade de negócio das edições do SQL Database do Azure, leia os [descrição geral da continuidade de negócio da linha de base de dados do Azure SQL][Azure SQL Database business continuity overview].

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md

<!--MSDN references-->

<!--Blog references-->

<!--Other Web references-->
[Azure portal]: https://portal.azure.com/
