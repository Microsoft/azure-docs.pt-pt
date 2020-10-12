---
title: Migração mínima - Base de Dados Azure para o MySQL
description: Este artigo descreve como realizar uma migração mínima de tempo de inatividade de uma base de dados MySQL para Azure Database for MySQL utilizando o Serviço de Migração da Base de Dados Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 978312c6238315cf836cf7c26a2ee63e452d3b85
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90884637"
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>Migração mínima para a Base de Dados Azure para o MySQL
[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

Pode executar migrações MySQL para Azure Database para MySQL com tempo mínimo de inatividade utilizando a **capacidade de sincronização contínua** recentemente introduzida para o Serviço de Migração de Bases de Dados (DMS) da [Azure Database.](https://aka.ms/get-dms) Esta funcionalidade limita a quantidade de tempo de inatividade que é incorrido pela aplicação.

## <a name="overview"></a>Descrição geral
O Azure DMS realiza uma carga inicial das suas instalações para a Azure Database para o MySQL e, em seguida, sincroniza continuamente quaisquer novas transações para o Azure enquanto a aplicação permanece em execução. Depois de os dados recuperarem o lado alvo do Azure, paras a aplicação por um breve momento (tempo mínimo de inatividade), esperas o último lote de dados (a partir do momento em que parares a aplicação até que a aplicação esteja efetivamente indisponível para qualquer novo tráfego) para apanhar o alvo e, em seguida, atualiza a tua cadeia de ligação para apontar para Azure. Quando terminar, a sua candidatura será transmitida em direto no Azure!

:::image type="content" source="./media/howto-migrate-online/ContinuousSync.png" alt-text="Sincronização contínua com o Serviço de Migração da Base de Dados Azure":::

## <a name="next-steps"></a>Passos seguintes
- Veja o vídeo [Migrar facilmente as aplicações MySQL/PostgreSQL para o serviço gerido Azure](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201), que contém uma demonstração que mostra como migrar as aplicações mySQL para Azure Database para o MySQL.
- Consulte o tutorial [Migrate MySQL para Azure Database for MySQL on-line usando DMS](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online).
