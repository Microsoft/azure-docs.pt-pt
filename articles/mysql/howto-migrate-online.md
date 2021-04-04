---
title: Migração mínima - Base de Dados Azure para o MySQL
description: Este artigo descreve como realizar uma migração mínima de tempo de inatividade de uma base de dados MySQL para Azure Database for MySQL utilizando o Serviço de Migração da Base de Dados Azure.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 10/30/2020
ms.openlocfilehash: 75b548b53de4c0f4ea39959c3d70e9ddb361d64c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94540336"
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>Migração mínima para a Base de Dados Azure para o MySQL
[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

Pode executar migrações MySQL para Azure Database para MySQL com tempo mínimo de inatividade utilizando a **capacidade de sincronização contínua** recentemente introduzida para o Serviço de Migração de Bases de Dados (DMS) da [Azure Database.](https://aka.ms/get-dms) Esta funcionalidade limita a quantidade de tempo de inatividade que é incorrido pela aplicação.

Também pode consultar o [Guia de Migração da Base de Dados](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide) para obter informações detalhadas e utilizar casos sobre bases de dados migratórias para a Base de Dados Azure para o MySQL. Este guia fornece orientações que conduzirão ao planeamento e execução bem-sucedidos de uma migração MySQL para Azure.

## <a name="overview"></a>Descrição Geral
O Azure DMS realiza uma carga inicial das suas instalações para a Azure Database para o MySQL e, em seguida, sincroniza continuamente quaisquer novas transações para o Azure enquanto a aplicação permanece em execução. Depois de os dados recuperarem o lado alvo do Azure, paras a aplicação por um breve momento (tempo mínimo de inatividade), esperas o último lote de dados (a partir do momento em que parares a aplicação até que a aplicação esteja efetivamente indisponível para qualquer novo tráfego) para apanhar o alvo e, em seguida, atualiza a tua cadeia de ligação para apontar para Azure. Quando terminar, a sua candidatura será transmitida em direto no Azure!

:::image type="content" source="./media/howto-migrate-online/ContinuousSync.png" alt-text="Sincronização contínua com o Serviço de Migração da Base de Dados Azure":::

## <a name="next-steps"></a>Passos seguintes
- Para obter mais informações sobre bases de dados migratórias para a Base de Dados Azure para o MySQL, consulte o [Guia de Migração da Base de Dados.](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide)
- Veja o vídeo [Migrar facilmente as aplicações MySQL/PostgreSQL para o serviço gerido Azure](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201), que contém uma demonstração que mostra como migrar as aplicações mySQL para Azure Database para o MySQL.
- Consulte o tutorial [Migrate MySQL para Azure Database for MySQL on-line usando DMS](../dms/tutorial-mysql-azure-mysql-online.md).