---
title: Desativar geo-backups
description: Como guiar para desativar geo-backups para uma piscina SQL dedicada (anteriormente SQL DW) em Azure Synapse Analytics
services: synapse-analytics
author: joannapea
manager: igorstan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 01/06/2021
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 395d5f0697138155b0bb0c629461aada9e9c18c6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98739147"
---
# <a name="disable-geo-backups-for-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Desativar geo-backups para uma piscina SQL dedicada (anteriormente SQL DW) em Azure Synapse Analytics

Neste artigo, você aprende a desativar geo-backups para o seu dedicado portal SQL DW (ex-SQL DW).

## <a name="disable-geo-backups-through-azure-portal"></a>Desativar geo-backups através do portal Azure

Siga estes passos para desativar geo-backups para a sua piscina SQL dedicada (anteriormente SQL DW):

> [!NOTE]
> Se desativar geo-backups, deixará de ser capaz de recuperar a sua piscina SQL dedicada (anteriormente SQL DW) para outra região do Azure. 
>

1. Inscreva-se na sua conta [do portal Azure.](https://portal.azure.com/)
1. Selecione o recurso de piscina SQL (anteriormente SQL DW) para o quais gostaria de desativar geo-backups. 
1. Em **Definições** no painel de navegação à esquerda, selecione **a política de geo-backup**.

   ![Desativar a cópia de segurança](./media/sql-data-warehouse-restore-from-geo-backup/disable-geo-backup-1.png)

1. Para desativar as geo-cópias de segurança, **selecione Desativado**. 

   ![Geo-backup desativado](./media/sql-data-warehouse-restore-from-geo-backup/disable-geo-backup-2.png)

1. *Selecione Guardar* para garantir que as suas definições estão guardadas. 

   ![Guardar definições de geo-backup](./media/sql-data-warehouse-restore-from-geo-backup/disable-geo-backup-3.png)

## <a name="next-steps"></a>Passos seguintes

- [Restaurar uma piscina SQL dedicada existente (anteriormente SQL DW)](sql-data-warehouse-restore-active-paused-dw.md)
- [Restaurar uma piscina SQL dedicada eliminada (anteriormente SQL DW)](sql-data-warehouse-restore-deleted-dw.md)
