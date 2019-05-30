---
title: Migração de período de indisponibilidade mínimo para a base de dados do Azure para MySQL
description: Este artigo descreve como efetuar uma migração de período de indisponibilidade mínimo de uma base de dados do MySQL para a base de dados do Azure para MySQL com o Azure Database Migration Service.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/07/2018
ms.openlocfilehash: 49e2662f215d845d416e46246b03e4408fae118b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "61424180"
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>Migração de período de indisponibilidade mínimo para a base de dados do Azure para MySQL
Pode efetuar as migrações de MySQL para a base de dados do Azure para MySQL com o período de indisponibilidade mínimo utilizando recém-lançado **capacidade de sincronização contínua** para o [Azure Database Migration Service](https://aka.ms/get-dms) (DMS). Esta funcionalidade limita a quantidade de tempo de inatividade que é incorrido pela aplicação.

## <a name="overview"></a>Descrição geral
O Azure DMS executa uma carga inicial de seu local para a base de dados do Azure para MySQL e, em seguida, sincroniza continuamente nenhuma transação nova para o Azure enquanto o aplicativo permanece em execução. Depois de captura os dados de cópia de segurança no lado do Azure de destino, pare a aplicação por um breve momento (tempo de inatividade mínimo), espera até que o último lote de dados (a partir da hora de parar a aplicação até que o aplicativo é efetivamente indisponível para qualquer novo tráfego) para capturar o destino de cópia de segurança e, em seguida, atualize a cadeia de ligação para apontar para o Azure. Quando tiver terminado, a aplicação ficará em direto no Azure!

![Sincronia contínua com o serviço de migração de base de dados do Azure](./media/howto-migrate-online/ContinuousSync.png)

## <a name="next-steps"></a>Passos Seguintes
- Ver o vídeo [MySQL/PostgreSQL de migrar facilmente aplicações para o Azure managed service](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201), que contém uma demonstração de como migrar aplicações do MySQL para base de dados do Azure para MySQL.
- Veja o tutorial [MySQL migrar a base de dados do Azure para MySQL online com o DMS](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online).
