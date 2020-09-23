---
title: Migração mínima para a base de dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve como realizar uma migração mínima de tempo de inatividade de uma base de dados PostgreSQL para Azure Database for PostgreSQL - Single Server utilizando o Serviço de Migração da Base de Dados de Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 8bdafb73c4b791f059f8c38233ae8f9cc5945039
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90881972"
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql---single-server"></a>Migração mínima para a base de dados Azure para PostgreSQL - Servidor Único
[!INCLUDE[applies-to-postgres-single-flexible-server](includes/applies-to-postgres-single-flexible-server-hyperscale.md)]

Pode executar migrações PostgreSQL para Azure Database for PostgreSQL com tempo mínimo de inatividade utilizando a **capacidade de sincronização contínua** recentemente introduzida para o Serviço de Migração da Base de Dados de [Azure](https://aka.ms/get-dms) (DMS). Esta funcionalidade limita a quantidade de tempo de inatividade que é incorrido pela aplicação.

## <a name="overview"></a>Descrição geral
O Azure DMS realiza uma carga inicial das suas instalações para a Azure Database para PostgreSQL e, em seguida, sincroniza continuamente quaisquer novas transações para o Azure enquanto a aplicação permanece em execução. Depois de os dados recuperarem o lado alvo do Azure, paras a aplicação por um breve momento (tempo mínimo de inatividade), esperas o último lote de dados (a partir do momento em que parares a aplicação até que a aplicação esteja efetivamente indisponível para qualquer novo tráfego) para apanhar o alvo e, em seguida, atualiza a tua cadeia de ligação para apontar para Azure. Quando terminar, a sua candidatura será transmitida em direto no Azure!

:::image type="content" source="./media/howto-migrate-online/ContinuousSync.png" alt-text="Sincronização contínua com o Serviço de Migração da Base de Dados Azure":::

## <a name="next-steps"></a>Passos seguintes
- Veja a modernização da aplicação de vídeo [com o Microsoft Azure,](https://medius.studios.ms/Embed/Video/BRK2102?sid=BRK2102)que contém uma demonstração que mostra como migrar aplicações PostgreSQL para Azure Database for PostgreSQL.
- Consulte o tutorial [Migrar PostgreSQL para Azure Database para PostgreSQL on-line usando DMS](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online).
