---
title: Migração mínima para a base de dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve como realizar uma migração mínima de tempo de inatividade de uma base de dados PostgreSQL para Azure Database for PostgreSQL - Single Server utilizando o Serviço de Migração da Base de Dados de Azure.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 27da5f1b731b2cdb0604f91f7f9e78b19ee2908b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92489800"
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql---single-server"></a>Migração mínima para a base de dados Azure para PostgreSQL - Servidor Único
[!INCLUDE[applies-to-postgres-single-flexible-server](includes/applies-to-postgres-single-flexible-server-hyperscale.md)]

Pode executar migrações PostgreSQL para Azure Database for PostgreSQL com tempo mínimo de inatividade utilizando a **capacidade de sincronização contínua** recentemente introduzida para o Serviço de Migração da Base de Dados de [Azure](https://aka.ms/get-dms) (DMS). Esta funcionalidade limita a quantidade de tempo de inatividade que é incorrido pela aplicação.

## <a name="overview"></a>Descrição geral
O Azure DMS realiza uma carga inicial das suas instalações para a Azure Database para PostgreSQL e, em seguida, sincroniza continuamente quaisquer novas transações para o Azure enquanto a aplicação permanece em execução. Depois de os dados recuperarem o lado alvo do Azure, paras a aplicação por um breve momento (tempo mínimo de inatividade), esperas o último lote de dados (a partir do momento em que parares a aplicação até que a aplicação esteja efetivamente indisponível para qualquer novo tráfego) para apanhar o alvo e, em seguida, atualiza a tua cadeia de ligação para apontar para Azure. Quando terminar, a sua candidatura será transmitida em direto no Azure!

:::image type="content" source="./media/howto-migrate-online/ContinuousSync.png" alt-text="Sincronização contínua com o Serviço de Migração da Base de Dados Azure":::

## <a name="next-steps"></a>Passos seguintes
- Veja a modernização da aplicação de vídeo [com o Microsoft Azure,](https://medius.studios.ms/Embed/Video/BRK2102?sid=BRK2102)que contém uma demonstração que mostra como migrar aplicações PostgreSQL para Azure Database for PostgreSQL.
- Consulte o tutorial [Migrar PostgreSQL para Azure Database para PostgreSQL on-line usando DMS](../dms/tutorial-postgresql-azure-postgresql-online.md).