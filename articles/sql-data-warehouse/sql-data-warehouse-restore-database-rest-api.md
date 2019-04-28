---
title: Restaurar um armazém de dados SQL do Azure - REST API | Documentos da Microsoft
description: Restaure um armazém de dados SQL do Azure através de REST APIs.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: daf013472e5fa533912920e4c14a552905b5d333
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60935692"
---
# <a name="restore-an-azure-sql-data-warehouse-with-rest-apis"></a>Restaurar um armazém de dados SQL do Azure com as APIs REST
Restaure um armazém de dados SQL do Azure através de REST APIs.

## <a name="before-you-begin"></a>Antes de começar
**Verifique a sua capacidade DTU.** Cada SQL Data Warehouse é hospedado por um servidor lógico SQL (por exemplo, myserver.database.windows.net) que tem uma predefinição [quota de DTU](../sql-database/sql-database-what-is-a-dtu.md).  Antes de pode restaurar um armazém de dados SQL, certifique-se de que o seu SQL server tem quota suficiente DTU restante para a base de dados a ser restaurada. Para pedir DTU mais, pode [criar um pedido de suporte](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-an-active-or-paused-data-warehouse"></a>Restaurar um armazém de dados do Active Directory ou em pausa
Para restaurar um armazém de dados:

1. Obter a lista de pontos de restauro de base de dados usando a operação obter pontos de restauro de base de dados.
2. Iniciar o restauro utilizando o [criar pedido de restauro de base de dados](https://msdn.microsoft.com/library/azure/dn509571.aspx) operação.
3. Controlar o estado do restauro com o [estado da operação de base de dados](https://msdn.microsoft.com/library/azure/dn720371.aspx) operação.

> [!NOTE]
> Depois do restauro foi concluída, pode configurar o seu armazém de dados recuperados seguindo [configurar a sua base de dados após a recuperação](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).
> 
> 

## <a name="restore-a-deleted-data-warehouse"></a>Restaurar um armazém de dados eliminados
Para restaurar um armazém de dados eliminados:

1. Listar todos os armazéns de dados eliminada restaurável utilizando o [lista restaurável remover bases de dados](https://msdn.microsoft.com/library/azure/dn509562.aspx) operação.
2. Obtenha os detalhes para o armazém de dados eliminada que pretende restaurar com a operação de [Get restaurável base de dados] [Get restaurável base de dados].
3. Iniciar o restauro utilizando o [criar pedido de restauro de base de dados](https://msdn.microsoft.com/library/azure/dn509571.aspx) operação.
4. Controlar o estado do restauro com o [estado da operação de base de dados](https://msdn.microsoft.com/library/azure/dn720371.aspx) operação.

> [!NOTE]
> Para configurar o seu armazém de dados após o restauro foi concluída, consulte [configurar a sua base de dados após a recuperação](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).
> 
> 

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre as funcionalidades de continuidade de negócio das edições do SQL Database do Azure, leia os [descrição geral da continuidade de negócio da linha de base de dados do Azure SQL](../sql-database/sql-database-business-continuity.md).
