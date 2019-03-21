---
title: Estado de cenário de migração de base de dados | Documentos da Microsoft
description: Saiba mais sobre o estado dos cenários de migração suportados pelo serviço de migração de base de dados do Azure.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 03/12/2019
ms.openlocfilehash: b07f36055f9c0690450e8d4ab5abacb8c84d9bf2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58182545"
---
# <a name="status-of-migration-scenarios-supported-by-the-azure-database-migration-service"></a>Estado de cenários de migração suportados pelo serviço de migração de base de dados do Azure
O Azure Database Migration Service foi concebido para suportar uma variedade de cenários de migração (pares origem/destino) para ambos offline (única) e migrações de online (sincronização contínua). A cobertura de cenário fornecida pelo serviço de migração de base de dados do Azure está a ser expandida ao longo do tempo. Novos cenários estão sendo adicionados regularmente. Este artigo identifica os cenários de migração atualmente suportados pelo serviço de migração de base de dados do Azure e o estado (privada [ou limitado] pré-visualização, pré-visualização pública ou em disponibilidade geral) ou a cada cenário.

## <a name="offline-versus-online-migrations"></a>Offline versus migrações online
Quando migrar bases de dados para o Azure ao utilizar o serviço de migração de base de dados do Azure, pode efetuar uma migração online ou uma offline. Com o *offline* migrações, tempo de inatividade do aplicativo começa ao mesmo tempo que começa a migração. Para *online* migrações, o período de indisponibilidade é limitado ao tempo necessário para transitar para o novo ambiente quando a migração estiver concluída. É recomendado para testar uma migração offline para determinar se o tempo de inatividade é aceitável; caso contrário, efetuar uma migração online.

## <a name="migration-scenario-status"></a>Estado de cenário de migração
O estado de cada cenário de migração suportado pelo serviço de migração de base de dados do Azure varia de acordo com o tempo. Em geral, os cenários são pela primeira vez no **pré-visualização privada**, e tirar partido da funcionalidade requer um cliente submeter uma nomeação por meio do [site de pré-visualização do DMS](https://aka.ms/dms-preview). Quando a pré-visualização privada é concluída, o estado de cenário é alterado para **pré-visualização pública**. Todos os utilizadores do Azure Database Migration Service podem tirar partido de cenários de migração, disponíveis em pré-visualização pública. No entanto, o cenário de migração pode não estar disponível em todas as regiões e a funcionalidade pode sofrer alterações adicionais antes do lançamento final. Quando se tornar um cenário de migração **em disponibilidade geral**, o último Estado de lançamento, a funcionalidade está concluída e acessível a todos os utilizadores do Azure Database Migration Service. 

## <a name="migration-scenario-support"></a>Suporte de cenário de migração

As tabelas seguintes mostram quais cenários de migração são suportados quando utilizar o serviço de migração de base de dados do Azure.

> [!NOTE]
> Se um cenário listado como suportado abaixo não aparecer na interface de utilizador, contacte o [equipa de migração de dados](mailto:datamigrationteam@microsoft.com) para obter informações adicionais.

### <a name="offline-one-time-migration-support"></a>Suporte de migração (única) offline
A tabela seguinte mostra o suporte de serviço de migração de base de dados do Azure para migrações offline.

| Destino  | Origem | Suporte |
| ------------- | ------------- | :-------------: |
| **BD SQL do Azure**  | SQL Server | ✔ |
|   | RDS SQL  |  ✔ |
|   | Oracle  |   |
| **MI de BD SQL do Azure**  | SQL Server  | ✔ |
|   | RDS SQL  | ✔ |
|   | Oracle  | ✔  |
| **VM do SQL do Azure**  | SQL Server | ✔ |
|   | Oracle  |   |
| **BD do Cosmos para o Azure**  | MongoDB | ✔ |
| **BD do Azure para MySQL**  | MySQL |  |
|   | RDS MySQL  |  |
| **BD do Azure para PostgreSQL**  | PostgreSQL |  |
|  | RDS PostgreSQL  |  |

### <a name="online-continuous-sync-migration-support"></a>Suporte de migração online (sincronização contínua)
A tabela seguinte mostra o suporte de serviço de migração de base de dados do Azure para migrações online.

| Destino  | Origem | Suporte |
| ------------- | ------------- | :-------------: |
| **BD SQL do Azure**  | SQL Server | ✔ |
|   | RDS SQL  |   |
|   | Oracle  |  ✔ |
| **MI de BD SQL do Azure**  | SQL Server  | ✔ |
|   | RDS SQL  |  |
|   | Oracle  | ✔  |
| **VM do SQL do Azure**  | SQL Server  |   |
|   | Oracle  | ✔  |
| **BD do Cosmos para o Azure**  | MongoDB  | ✔ |
| **BD do Azure para MySQL**  | MySQL | ✔ |
|   | RDS MySQL  | ✔ |
| **BD do Azure para PostgreSQL**  | PostgreSQL | ✔ |
|  | RDS PostgreSQL  | ✔ |

## <a name="next-steps"></a>Passos Seguintes
Para uma descrição geral do serviço de migração de base de dados do Azure e a disponibilidade regional, consulte o artigo [o que é o Azure Database Migration Service](dms-overview.md). 
