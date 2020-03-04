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
ms.topic: article
ms.date: 09/05/2019
ms.openlocfilehash: 9652b78674d6a6b905eb049564d1b17cdc7c17b7
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78254918"
---
# <a name="status-of-migration-scenarios-supported-by-azure-database-migration-service"></a>Estado dos cenários de migração apoiados pelo Serviço de Migração da Base de Dados Azure

O Azure Database Migration Service foi concebido para apoiar diferentes cenários de migração (par de fonte/alvo) tanto para migrações offline (uma vez) como online (sincronização contínua). A cobertura de cenário fornecida pelo Azure Database Migration Service está a ser alargada ao longo do tempo. Novos cenários são adicionados regularmente. Este artigo identifica cenários de migração atualmente suportados pelo Azure Database Migration Service e o estado (pré-visualização privada, pré-visualização pública ou disponibilidade geral) para cada cenário.

## <a name="offline-versus-online-migrations"></a>Offline versus migrações online

Com o Serviço de Migração de Bases de Dados Azure, pode fazer uma migração offline ou online. Com as migrações *offline,* o tempo de inatividade da aplicação começa ao mesmo tempo que a migração começa. Para limitar o tempo de inatividade ao tempo necessário para reduzir para o novo ambiente quando a migração terminar, utilize uma migração *online.* É recomendado testar uma migração offline para determinar se o tempo de inatividade é aceitável; se não, faça uma migração online.

## <a name="migration-scenario-status"></a>Estado do cenário de migração

O estado dos cenários de migração suportados pelo Serviço de Migração da Base de Dados Azure varia com o tempo. Geralmente, os cenários são lançados pela primeira vez em **pré-visualização privada.** Participar na pré-visualização privada requer que os clientes apresentem uma nomeação através do [site de pré-visualização dMS](https://aka.ms/dms-preview). Após a pré-visualização privada, o estado do cenário muda para **pré-visualização pública**. Os utilizadores do Serviço de Migração de Bases de Dados Azure podem experimentar cenários de migração em visualização pública diretamente a partir da interface do utilizador. Não é necessária inscrição.  No entanto, os cenários de migração em visualização pública podem não estar disponíveis em todas as regiões e podem sofrer alterações adicionais antes do lançamento final. Após a pré-visualização do público, o estado do cenário muda para **geralmente disponibilidade**. A disponibilidade geral (GA) é o estado de lançamento final, e a funcionalidade é completa e acessível a todos os utilizadores.

## <a name="migration-scenario-support"></a>Apoio ao cenário de migração

As tabelas que se seguem mostram quais os cenários de migração que são suportados na utilização do Serviço de Migração de Bases de Dados Azure.

> [!NOTE]
> Se um cenário listado como suportado abaixo não aparecer dentro da interface do utilizador, contacte o pseudónimo [Ask Azure Database Migrations](mailto:AskAzureDatabaseMigrations@service.microsoft.com) para obter informações adicionais.

> [!IMPORTANT]
> Para ver todos os cenários atualmente suportados pelo Serviço de Migração de Bases de Dados Azure em Pré-visualização Privada, consulte o [site de pré-visualização do DMS](https://aka.ms/dms-preview).

### <a name="offline-one-time-migration-support"></a>Apoio à migração offline (uma vez)

A tabela que se segue mostra o apoio do Serviço de Migração de Bases de Dados Azure para migrações offline.

| Destino  | Origem | Suporte | Estado |
| ------------- | ------------- |:-------------:|:-------------:|
| **Azure SQL DB** | SQL Server | ✔ | GA |
|   | RDS SQL |  |  |
|   | Oracle |  |  |
| **Azure SQL DB MI** | SQL Server | ✔ | GA |
|   | RDS SQL |  |  |
|   | Oracle |  |   |
| **Azure SQL VM** | SQL Server | ✔ | GA |
|   | Oracle |   |   |
| **Azure Cosmos DB** | MongoDB | ✔ | GA |
| **Azure DB para MySQL** | MySQL |   |   |
|   | RDS MySQL |   |   |
| **Azure DB para PostgreSQL** | PostgreSQL |  |
|  | RDS PostgreSQL |   |   |

### <a name="online-continuous-sync-migration-support"></a>Suporte à migração online (sincronizado contínuo)

A tabela que se segue mostra o apoio do Serviço de Migração de Bases de Dados Azure para migrações online.

| Destino  | Origem | Suporte | Estado |
| ------------- | ------------- |:-------------:|:-------------:|
| **Azure SQL DB** | SQL Server | ✔ | GA |
|   | RDS SQL | ✔ | GA |
|   | Oracle |  |  |
| **Azure SQL DB MI** | SQL Server | ✔ | GA |
|   | RDS SQL | ✔ | GA |
|   | Oracle | ✔ | Pré-visualização privada |
| **Azure SQL VM** | SQL Server |   |   |
|   | Oracle  |  |  |
| **Azure Cosmos DB** | MongoDB | ✔ | GA |
| **Azure DB para MySQL** | MySQL | ✔ | GA |
|   | RDS MySQL | ✔ | GA |
| **Azure DB para PostgreSQL** | PostgreSQL | ✔ | GA |
|   | RDS PostgreSQL | ✔ | GA |
|   | Oracle | ✔ | Pré-visualização pública |

## <a name="next-steps"></a>Passos seguintes

Para uma visão geral do Serviço de Migração da Base de Dados Azure e disponibilidade regional, consulte o artigo O que é o Serviço de Migração de Bases de [Dados Azure.](dms-overview.md)
