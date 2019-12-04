---
title: Migração de tempo de inatividade mínimo-banco de dados do Azure para MySQL
description: Este artigo descreve como executar uma migração de tempo de inatividade mínimo de um banco de dados MySQL para o banco de dados do Azure para MySQL usando o serviço de migração de banco de dados do Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 9f166323cc72cd22cc4dd28babdfd056100a32e0
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74774148"
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>Migração de tempo de inatividade mínimo para o banco de dados do Azure para MySQL
Você pode executar migrações do MySQL para o banco de dados do Azure para MySQL com tempo de inatividade mínimo usando o **recurso de sincronização contínua** introduzido recentemente para o [serviço de migração de banco de dados do Azure](https://aka.ms/get-dms) (DMS). Essa funcionalidade limita a quantidade de tempo de inatividade incorrido do aplicativo.

## <a name="overview"></a>Visão geral
O Azure DMS executa uma carga inicial do seu local para o banco de dados do Azure para MySQL e sincroniza continuamente todas as transações novas para o Azure enquanto o aplicativo permanece em execução. Depois que os dados são atualizados no lado do Azure de destino, você interrompe o aplicativo por um breve momento (tempo de inatividade mínimo), aguardando o último lote de dados (desde o momento em que você interromper o aplicativo até que o aplicativo fique efetivamente indisponível para realizar qualquer tráfego novo). para cima no destino e atualize sua cadeia de conexão para apontar para o Azure. Quando você terminar, seu aplicativo ficará ativo no Azure!

![Sincronização contínua com o serviço de migração de banco de dados do Azure](./media/howto-migrate-online/ContinuousSync.png)

## <a name="next-steps"></a>Passos seguintes
- Exibir o vídeo [facilmente migrar aplicativos MySQL/PostgreSQL para o serviço gerenciado do Azure](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201), que contém uma demonstração mostrando como migrar aplicativos MySQL para o banco de dados do Azure para MySQL.
- Consulte o tutorial [migrar MySQL para o banco de dados do Azure para MySQL online usando DMS](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online).
