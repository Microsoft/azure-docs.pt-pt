---
title: Estado de cenário de migração de base de dados | Documentos da Microsoft
description: Saiba mais sobre o estado dos cenários de migração suportados pelo Azure Database Migration Service.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 04/04/2019
ms.openlocfilehash: 4159b2e7af83030f46d5aca150ef99a1380e711f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61473013"
---
# <a name="status-of-migration-scenarios-supported-by-azure-database-migration-service"></a>Estado de cenários de migração suportados pelo Azure Database Migration Service
Azure Database Migration Service foi concebido para suportar cenários de migração diferentes (pares origem/destino) para ambos offline (única) e migrações de online (sincronização contínua). A cobertura de cenário fornecida pelo serviço de migração de base de dados do Azure está a ser expandida ao longo do tempo. Novos cenários estão sendo adicionados regularmente. Este artigo identifica os cenários de migração atualmente suportados pelo serviço de migração de base de dados do Azure e o estado (pré-visualização privada, pré-visualização pública ou da disponibilidade geral) para cada cenário.

## <a name="offline-versus-online-migrations"></a>Offline versus migrações online
Com o serviço de migração de base de dados do Azure, pode fazer um offline ou de uma migração online. Com o *offline* migrações, tempo de inatividade do aplicativo começa ao mesmo tempo que começa a migração. Para limitar o tempo de inatividade para o tempo necessário para transitar para o novo ambiente quando a migração estiver concluída, utilize um *online* migração. É recomendado para testar uma migração offline para determinar se o tempo de inatividade é aceitável; Se não o tiver feito, fazer uma migração online.

## <a name="migration-scenario-status"></a>Estado de cenário de migração
O estado dos cenários de migração suportados pelo Azure Database Migration Service varia de acordo com o tempo. Em geral, os cenários são pela primeira vez no **pré-visualização privada**. Participar na pré-visualização privada requer que os clientes submeter uma nomeação através da [site de pré-visualização do DMS](https://aka.ms/dms-preview). Depois de pré-visualização privada, o estado de cenário é alterado para **pré-visualização pública**. Os utilizadores do serviço de migração de base de dados do Azure podem experimentar a cenários de migração em pré-visualização pública diretamente a partir da interface do usuário. Sem precisar de inscrição é obrigatório.  No entanto, os cenários de migração em pré-visualização pública podem não estar disponíveis em todas as regiões e podem passar por alterações adicionais antes do lançamento final. Após a pré-visualização pública, o estado de cenário é alterado para **geralmente disponibilidade**. Disponibilidade geral (GA) é o estado da versão final e a funcionalidade está concluída e acessível a todos os utilizadores.

## <a name="migration-scenario-support"></a>Suporte de cenário de migração
As tabelas seguintes mostram quais cenários de migração são suportados quando o serviço de migração de base de dados do Azure.

> [!NOTE]
> Se um cenário listado como suportado abaixo não aparecer na interface de utilizador, contacte o [equipa de migração de dados](mailto:datamigrationteam@microsoft.com) para obter informações adicionais.

> [!IMPORTANT]
> Para ver todos os cenários suportados atualmente pelo serviço de migração de base de dados do Azure em pré-visualização privada, consulte a [site de pré-visualização do DMS](https://aka.ms/dms-preview).

### <a name="offline-one-time-migration-support"></a>Suporte de migração (única) offline
A tabela seguinte mostra o suporte de serviço de migração de base de dados do Azure para migrações offline.

| Destino  | Origem | Suporte | Estado |
| ------------- | ------------- |:-------------:|:-------------:|
| **BD SQL do Azure** | SQL Server | ✔ | GA |
|   | RDS SQL |  |  |
|   | Oracle |  |  |
| **MI de BD SQL do Azure** | SQL Server | ✔ | GA |
|   | RDS SQL |  |  |
|   | Oracle |  |   |
| **VM do SQL do Azure** | SQL Server | ✔ | GA |
|   | Oracle |   |   |
| **BD do Cosmos para o Azure** | MongoDB | ✔ | Pré-visualização pública |
| **BD do Azure para MySQL** | MySQL |   |   |
|   | RDS MySQL |   |   |
| **BD do Azure para PostgreSQL** | PostgreSQL |  |
|  | RDS PostgreSQL |   |   |

### <a name="online-continuous-sync-migration-support"></a>Suporte de migração online (sincronização contínua)
A tabela seguinte mostra o suporte de serviço de migração de base de dados do Azure para migrações online.

| Destino  | Origem | Suporte | Estado |
| ------------- | ------------- |:-------------:|:-------------:|
| **BD SQL do Azure** | SQL Server | ✔ | GA |
|   | RDS SQL | ✔ | GA |
|   | Oracle |  |  |
| **MI de BD SQL do Azure** | SQL Server | ✔ | GA |
|   | RDS SQL | ✔ | GA |
|   | Oracle | ✔ | Pré-visualização privada |
| **VM do SQL do Azure** | SQL Server |   |   |
|   | Oracle  |  |  |
| **BD do Cosmos para o Azure** | MongoDB | ✔ | Pré-visualização pública |
| **BD do Azure para MySQL** | MySQL | ✔ | GA |
|   | RDS MySQL | ✔ | GA |
| **BD do Azure para PostgreSQL** | PostgreSQL | ✔ | GA |
|   | RDS PostgreSQL | ✔ | GA |
|   | Oracle | ✔ | Pré-visualização privada |

## <a name="next-steps"></a>Passos Seguintes
Para uma descrição geral do serviço de migração de base de dados do Azure e a disponibilidade regional, consulte o artigo [o que é o Azure Database Migration Service](dms-overview.md).
