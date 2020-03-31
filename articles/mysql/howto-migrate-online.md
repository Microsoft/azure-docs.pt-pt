---
title: Migração mínima - Base de Dados Azure para MySQL
description: Este artigo descreve como realizar uma migração mínima de tempo de paragem de uma base de dados MySQL para a Base de Dados Azure para MySQL utilizando o Serviço de Migração de Bases de Dados Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: df818fa4106aec341607d8142b2a672699b8e9d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063349"
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>Migração mínima para a Base de Dados Azure para o MySQL
Pode realizar migrações MySQL para a Base de Dados Azure para mySQL com o mínimo de tempo de inatividade utilizando a capacidade de **sincronização contínua** recentemente introduzida para o Serviço de Migração de Bases de [Dados Azure](https://aka.ms/get-dms) (DMS). Esta funcionalidade limita a quantidade de tempo de inatividade que é incorrido pela aplicação.

## <a name="overview"></a>Descrição geral
O Azure DMS executa uma carga inicial das suas instalações para a Base de Dados Azure para o MySQL e, em seguida, sincroniza continuamente quaisquer novas transações para o Azure enquanto a aplicação permanece em execução. Depois de os dados se aproximarem do lado do azure alvo, você para a aplicação por um breve momento (tempo mínimo de inatividade), aguarde o último lote de dados (a partir do momento em que pare a aplicação até que a aplicação esteja efetivamente indisponível para tomar qualquer novo tráfego) para capturar até no alvo e, em seguida, atualizar a sua cadeia de ligação para apontar para Azure. Quando terminar, a sua candidatura estará em direto no Azure!

![Sincronização contínua com o Serviço de Migração de Bases de Dados Azure](./media/howto-migrate-online/ContinuousSync.png)

## <a name="next-steps"></a>Passos seguintes
- Veja o vídeo [Facilmente migrar aplicações MySQL/PostgreSQL para o serviço gerido](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201)pelo Azure , que contém uma demonstração que mostra como migrar as aplicações MySQL para a Base de Dados Azure para o MySQL.
- Consulte o tutorial [Migrate MySQL para Azure Database para MySQL on-line usando DMS](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online).
