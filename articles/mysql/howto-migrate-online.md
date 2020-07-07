---
title: Migração mínima - Base de Dados Azure para o MySQL
description: Este artigo descreve como realizar uma migração mínima de tempo de inatividade de uma base de dados MySQL para Azure Database for MySQL utilizando o Serviço de Migração da Base de Dados Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: df818fa4106aec341607d8142b2a672699b8e9d3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80063349"
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>Migração mínima para a Base de Dados Azure para o MySQL
Pode executar migrações MySQL para Azure Database para MySQL com tempo mínimo de inatividade utilizando a **capacidade de sincronização contínua** recentemente introduzida para o Serviço de Migração de Bases de Dados (DMS) da [Azure Database.](https://aka.ms/get-dms) Esta funcionalidade limita a quantidade de tempo de inatividade que é incorrido pela aplicação.

## <a name="overview"></a>Descrição Geral
O Azure DMS realiza uma carga inicial das suas instalações para a Azure Database para o MySQL e, em seguida, sincroniza continuamente quaisquer novas transações para o Azure enquanto a aplicação permanece em execução. Depois de os dados recuperarem o lado alvo do Azure, paras a aplicação por um breve momento (tempo mínimo de inatividade), esperas o último lote de dados (a partir do momento em que parares a aplicação até que a aplicação esteja efetivamente indisponível para qualquer novo tráfego) para apanhar o alvo e, em seguida, atualiza a tua cadeia de ligação para apontar para Azure. Quando terminar, a sua candidatura será transmitida em direto no Azure!

![Sincronização contínua com o Serviço de Migração da Base de Dados Azure](./media/howto-migrate-online/ContinuousSync.png)

## <a name="next-steps"></a>Passos seguintes
- Veja o vídeo [Migrar facilmente as aplicações MySQL/PostgreSQL para o serviço gerido Azure](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201), que contém uma demonstração que mostra como migrar as aplicações mySQL para Azure Database para o MySQL.
- Consulte o tutorial [Migrate MySQL para Azure Database for MySQL on-line usando DMS](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online).
