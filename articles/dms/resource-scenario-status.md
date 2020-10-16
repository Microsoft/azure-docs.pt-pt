---
title: Estado do cenário da migração de base de dados
titleSuffix: Azure Database Migration Service
description: Conheça o estado dos cenários de migração suportados pelo Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: troubleshooting
ms.date: 07/08/2020
ms.openlocfilehash: 77c54d6ea463b5a8a4952d243886b80f38312d27
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91291491"
---
# <a name="status-of-migration-scenarios-supported-by-azure-database-migration-service"></a>Estado dos cenários de migração suportados pelo Azure Database Migration Service

O Azure Database Migration Service é projetado para suportar diferentes cenários de migração (pares de origem/alvo) tanto para migrações offline (one-time) como on-line (sincronização contínua). A cobertura de cenário fornecida pelo Azure Database Migration Service está a ser alargada ao longo do tempo. Novos cenários são adicionados regularmente. Este artigo identifica cenários de migração atualmente suportados pelo Azure Database Migration Service e o estado (pré-visualização privada, pré-visualização pública ou disponibilidade geral) para cada cenário.

## <a name="offline-versus-online-migrations"></a>Migrações offline versus on-line

Com o Azure Database Migration Service, pode fazer uma migração offline ou on-line. Com as migrações *offline,* o tempo de inatividade da aplicação começa ao mesmo tempo que a migração começa. Para limitar o tempo de inatividade ao tempo necessário para reduzir para o novo ambiente quando a migração terminar, utilize uma migração *online.* É recomendado testar uma migração offline para determinar se o tempo de inatividade é aceitável; se não, faça uma migração on-line.

## <a name="migration-scenario-status"></a>Estado do cenário de migração

O estado dos cenários de migração suportados pelo Azure Database Migration Service varia com o tempo. Geralmente, os cenários são lançados pela primeira vez em **pré-visualização privada**. Participar na pré-visualização privada requer que os clientes apresentem uma nomeação através do [site de pré-visualização DMS](https://aka.ms/dms-preview). Após a pré-visualização privada, o estado do cenário muda para **visualização pública**. Os utilizadores do Azure Database Migration Service podem experimentar cenários de migração em visualização pública diretamente da interface do utilizador. Não é necessária qualquer inscrição.  No entanto, os cenários de migração em visualização pública podem não estar disponíveis em todas as regiões e podem sofrer alterações adicionais antes do lançamento final. Após a visualização pública, o estado do cenário muda para **a disponibilidade geral**. A disponibilidade geral (GA) é o estado final de lançamento, e a funcionalidade é completa e acessível a todos os utilizadores.

## <a name="migration-scenario-support"></a>Apoio ao cenário de migração

As tabelas que se seguem mostram quais os cenários de migração suportados quando se utilizam o Serviço de Migração da Base de Dados Azure.

> [!NOTE]
> Se um cenário listado como suportado abaixo não aparecer na interface do utilizador, contacte o pseudónimo [Ask Azure Database Migrations](mailto:AskAzureDatabaseMigrations@service.microsoft.com) para obter informações adicionais.

> [!IMPORTANT]
> Para visualizar todos os cenários atualmente suportados pelo Azure Database Migration Service em Pré-visualização Privada, consulte o [site de pré-visualização DMS](https://aka.ms/dms-preview).

### <a name="offline-one-time-migration-support"></a>Suporte à migração offline (uma vez)

A tabela seguinte mostra o suporte do Serviço de Migração da Base de Dados Azure para migrações offline.

| Destino  | Origem | Suporte | Estado |
| ------------- | ------------- |:-------------:|:-------------:|
| **BD SQL do Azure** | SQL Server | ✔ | GA |
|   | RDS SQL | X |  |
|   | Oracle | X |  |
| **Azure SQL DB MI** | SQL Server | ✔ | GA |
|   | RDS SQL | X |  |
|   | Oracle | X |   |
| **VM do Azure SQL** | SQL Server | ✔ | GA |
|   | Oracle | X |   |
| **BD do Cosmos para o Azure** | MongoDB | ✔ | GA |
| **BD do Azure para MySQL** | MySQL | X |   |
|   | RDS MySQL | X |   |
| **Azure DB para PostgreSQL - Servidor único** | PostgreSQL | X |
|  | RDS PostgreSQL | X |   |
| **Azure DB para PostgreSQL - Hiperescala (Citus)** | PostgreSQL | X |
|  | RDS PostgreSQL | X |   |

### <a name="online-continuous-sync-migration-support"></a>Suporte de migração online (sincronização contínua)

A tabela seguinte mostra o suporte do Serviço de Migração da Base de Dados Azure para migrações online.

| Destino  | Origem | Suporte | Estado |
| ------------- | ------------- |:-------------:|:-------------:|
| **BD SQL do Azure** | SQL Server | ✔ | GA |
|   | RDS SQL | ✔ | GA |
|   | Oracle | X |  |
| **Azure SQL DB MI** | SQL Server | ✔ | GA |
|   | RDS SQL | ✔ | GA |
|   | Oracle | X |  |
| **VM do Azure SQL** | SQL Server | X |   |
|   | Oracle  | X |  |
| **BD do Cosmos para o Azure** | MongoDB | ✔ | GA |
| **BD do Azure para MySQL** | MySQL | ✔ | GA |
|   | RDS MySQL | ✔ | GA |
| **Azure DB para PostgreSQL - Servidor único** | PostgreSQL | ✔ | GA |
|   | Azure DB para PostgreSQL - Servidor único* | ✔ | GA |
|   | RDS PostgreSQL | ✔ | GA |
|   | Oracle | ✔ | Pré-visualização pública |
| **Azure DB para PostgreSQL - Hiperescala (Citus)** | PostgreSQL | ✔ | GA |
|   | RDS PostgreSQL | ✔ | GA |

*Suportado para a versão 10 do PostgreSQL e posteriormente.

## <a name="next-steps"></a>Passos seguintes

Para uma visão geral do Serviço de Migração da Base de Dados Azure e disponibilidade regional, consulte o artigo [O que é o Serviço de Migração de Bases de Dados Azure.](dms-overview.md)
