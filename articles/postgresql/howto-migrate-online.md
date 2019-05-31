---
title: Migração de período de indisponibilidade mínimo à base de dados do Azure para PostgreSQL - servidor único
description: Este artigo descreve como efetuar uma migração de período de indisponibilidade mínimo de uma base de dados do PostgreSQL à base de dados do Azure para PostgreSQL - único servidor com o Azure Database Migration Service.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 93cd390889c023adf1c30a8470e1c2298598439e
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "65067518"
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql---single-server"></a>Migração de período de indisponibilidade mínimo à base de dados do Azure para PostgreSQL - servidor único
Pode efetuar as migrações de PostgreSQL para o banco de dados do Azure para PostgreSQL com o período de indisponibilidade mínimo utilizando recém-lançado **capacidade de sincronização contínua** para o [Azure Database Migration Service](https://aka.ms/get-dms) (DMS) . Esta funcionalidade limita a quantidade de tempo de inatividade que é incorrido pela aplicação.

## <a name="overview"></a>Descrição geral
O Azure DMS executa uma carga inicial de seu local para a base de dados do Azure para PostgreSQL e, em seguida, sincroniza continuamente nenhuma transação nova para o Azure enquanto o aplicativo permanece em execução. Depois de captura os dados de cópia de segurança no lado do Azure de destino, pare a aplicação por um breve momento (tempo de inatividade mínimo), espera até que o último lote de dados (a partir da hora de parar a aplicação até que o aplicativo é efetivamente indisponível para qualquer novo tráfego) para capturar o destino de cópia de segurança e, em seguida, atualize a cadeia de ligação para apontar para o Azure. Quando tiver terminado, a aplicação ficará em direto no Azure!

![Sincronia contínua com o serviço de migração de base de dados do Azure](./media/howto-migrate-online/ContinuousSync.png)

## <a name="next-steps"></a>Passos Seguintes
- Ver o vídeo [modernização de aplicações com o Microsoft Azure](https://medius.studios.ms/Embed/Video/BRK2102?sid=BRK2102), que contém uma demonstração de como migrar aplicações de PostgreSQL para base de dados do Azure para PostgreSQL.
- Veja o tutorial [PostgreSQL migrar a base de dados do Azure para PostgreSQL online com o DMS](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online).
