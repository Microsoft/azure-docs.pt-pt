---
title: Migração mínima para base de dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve como realizar uma migração mínima de tempo de paragem de uma base de dados PostgreSQL para a Base de Dados Azure para PostgreSQL - Servidor Único utilizando o Serviço de Migração de Bases de Dados Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 93cd390889c023adf1c30a8470e1c2298598439e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "65067518"
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql---single-server"></a>Migração mínima para base de dados Azure para PostgreSQL - Servidor Único
Pode realizar migrações PostgreSQL para a Base de Dados Azure para PostgreSQL com o mínimo de tempo de inatividade utilizando a capacidade de **sincronização contínua** recentemente introduzida para o Serviço de Migração de Bases de [Dados Azure](https://aka.ms/get-dms) (DMS). Esta funcionalidade limita a quantidade de tempo de inatividade que é incorrido pela aplicação.

## <a name="overview"></a>Descrição geral
O Azure DMS executa uma carga inicial das suas instalações para a Base de Dados Azure para postgreSQL, e depois sincroniza continuamente quaisquer novas transações para o Azure enquanto a aplicação permanece em execução. Depois de os dados se aproximarem do lado do azure alvo, você para a aplicação por um breve momento (tempo mínimo de inatividade), aguarde o último lote de dados (a partir do momento em que pare a aplicação até que a aplicação esteja efetivamente indisponível para tomar qualquer novo tráfego) para capturar até no alvo e, em seguida, atualizar a sua cadeia de ligação para apontar para Azure. Quando terminar, a sua candidatura estará em direto no Azure!

![Sincronização contínua com o Serviço de Migração de Bases de Dados Azure](./media/howto-migrate-online/ContinuousSync.png)

## <a name="next-steps"></a>Passos seguintes
- Veja a modernização da aplicação de vídeo com o [Microsoft Azure](https://medius.studios.ms/Embed/Video/BRK2102?sid=BRK2102), que contém uma demonstração que mostra como migrar as aplicações PostgreSQL para a Base de Dados Azure para postgreSQL.
- Consulte o tutorial [Migrate PostgreSQL para azure Database para PostgreSQL on-line usando DMS](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online).
