---
title: Migração mínima - Base de Dados Azure para MySQL
description: Este artigo descreve como realizar uma migração mínima de tempo de paragem de uma base de dados MySQL para a Base de Dados Azure para MySQL utilizando o Serviço de Migração de Bases de Dados Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: df818fa4106aec341607d8142b2a672699b8e9d3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80063349"
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>Migração mínima para a Base de Dados Azure para o MySQL
Pode realizar migrações MySQL para a Base de Dados Azure para mySQL com o mínimo de tempo de inatividade utilizando a capacidade de **sincronização contínua** recentemente introduzida para o Serviço de Migração de Bases de [Dados Azure](https://aka.ms/get-dms) (DMS). Esta funcionalidade limita a quantidade de tempo de inatividade que é incorrido pela aplicação.

## <a name="overview"></a>Descrição geral
O Azure DMS executa uma carga inicial das suas instalações para a Base de Dados Azure para o MySQL e, em seguida, sincroniza continuamente quaisquer novas transações para o Azure enquanto a aplicação permanece em execução. Depois de os dados se atualizarem no lado do azure alvo, interrompe a aplicação por um breve momento (tempo mínimo de inatividade), aguarde pelo último lote de dados (desde o momento em que para a aplicação até que a aplicação esteja efetivamente indisponível para apanhar qualquer novo tráfego) para recuperar o alvo e, em seguida, atualizar a sua linha de ligação para apontar para o Azure. Quando terminar, a sua candidatura estará em direto no Azure!

![Sincronização contínua com o Serviço de Migração de Bases de Dados Azure](./media/howto-migrate-online/ContinuousSync.png)

## <a name="next-steps"></a>Passos seguintes
- Veja o vídeo [Facilmente migrar aplicações MySQL/PostgreSQL para o serviço gerido](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201)pelo Azure , que contém uma demonstração que mostra como migrar as aplicações MySQL para a Base de Dados Azure para o MySQL.
- Consulte o tutorial [Migrate MySQL para Azure Database para MySQL on-line usando DMS](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online).
