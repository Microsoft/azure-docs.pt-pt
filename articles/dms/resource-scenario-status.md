---
title: Status do cenário de migração de banco de dados | Microsoft Docs
description: Saiba mais sobre o status dos cenários de migração com suporte pelo serviço de migração de banco de dados do Azure.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 09/05/2019
ms.openlocfilehash: 9ea070ea86a4d0d5e7101e82450235a59c936d2d
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/05/2019
ms.locfileid: "70376394"
---
# <a name="status-of-migration-scenarios-supported-by-azure-database-migration-service"></a>Status dos cenários de migração com suporte pelo serviço de migração de banco de dados do Azure

O serviço de migração de banco de dados do Azure foi projetado para dar suporte a diferentes cenários de migração (pares de origem/destino) para migrações offline (de uma vez) e online (sincronização contínua). A cobertura do cenário fornecida pelo serviço de migração de banco de dados do Azure está sendo estendida ao longo do tempo. Novos cenários estão sendo adicionados regularmente. Este artigo identifica os cenários de migração com suporte no momento pelo serviço de migração de banco de dados do Azure e o status (visualização privada, visualização pública ou disponibilidade geral) para cada cenário.

## <a name="offline-versus-online-migrations"></a>Offline versus migrações online

Com o serviço de migração de banco de dados do Azure, você pode fazer uma migração online ou offline. Com as migrações *offline* , o tempo de inatividade do aplicativo começa ao mesmo tempo em que a migração é iniciada. Para limitar o tempo de inatividade ao horário necessário para passar para o novo ambiente quando a migração for concluída, use uma migração *online* . É recomendável testar uma migração offline para determinar se o tempo de inatividade é aceitável; caso contrário, faça uma migração online.

## <a name="migration-scenario-status"></a>Status do cenário de migração

O status dos cenários de migração com suporte pelo serviço de migração de banco de dados do Azure varia com o tempo. Em geral, os cenários são lançados pela primeira vez em versão **prévia privada**. A participação em visualização privada exige que os clientes enviem uma indicação por meio do [site de visualização do DMS](https://aka.ms/dms-preview). Após a visualização privada, o status do cenário muda para **Visualização pública**. Os usuários do serviço de migração de banco de dados do Azure podem experimentar cenários de migração na visualização pública diretamente da interface do usuário. Nenhuma inscrição é necessária.  No entanto, os cenários de migração na visualização pública podem não estar disponíveis em todas as regiões e podem sofrer alterações adicionais antes da versão final. Após a visualização pública, o status do cenário muda para a **disponibilidade geral**. GA (disponibilidade geral) é o status da versão final e a funcionalidade está completa e acessível a todos os usuários.

## <a name="migration-scenario-support"></a>Suporte ao cenário de migração

As tabelas a seguir mostram quais cenários de migração têm suporte ao usar o serviço de migração de banco de dados do Azure.

> [!NOTE]
> Se um cenário listado como com suporte abaixo não aparecer na interface do usuário, entre em contato com o alias [pedir migrações de banco de dados do Azure](mailto:AskAzureDatabaseMigrations@service.microsoft.com) para obter informações adicionais.

> [!IMPORTANT]
> Para exibir todos os cenários com suporte no momento pelo serviço de migração de banco de dados do Azure em versão prévia privada, consulte o [site de visualização do DMS](https://aka.ms/dms-preview).

### <a name="offline-one-time-migration-support"></a>Suporte à migração offline (de uso único)

A tabela a seguir mostra o suporte ao serviço de migração de banco de dados do Azure para migrações offline.

| Destino  | Source | Suporte | State |
| ------------- | ------------- |:-------------:|:-------------:|
| **Banco de BD SQL do Azure** | SQL Server | ✔ | GA |
|   | RDS SQL |  |  |
|   | Oracle |  |  |
| **Banco de BD SQL do Azure MI** | SQL Server | ✔ | GA |
|   | RDS SQL |  |  |
|   | Oracle |  |   |
| **VM do SQL do Azure** | SQL Server | ✔ | GA |
|   | Oracle |   |   |
| **BD do Cosmos para o Azure** | MongoDB | ✔ | GA |
| **BD do Azure para MySQL** | MySQL |   |   |
|   | RDS MySQL |   |   |
| **BD do Azure para PostgreSQL** | PostgreSQL |  |
|  | RDS PostgreSQL |   |   |

### <a name="online-continuous-sync-migration-support"></a>Suporte à migração online (sincronização contínua)

A tabela a seguir mostra o suporte ao serviço de migração de banco de dados do Azure para migrações online.

| Destino  | Source | Suporte | State |
| ------------- | ------------- |:-------------:|:-------------:|
| **Banco de BD SQL do Azure** | SQL Server | ✔ | GA |
|   | RDS SQL | ✔ | GA |
|   | Oracle |  |  |
| **Banco de BD SQL do Azure MI** | SQL Server | ✔ | GA |
|   | RDS SQL | ✔ | GA |
|   | Oracle | ✔ | Visualização privada |
| **VM do SQL do Azure** | SQL Server |   |   |
|   | Oracle  |  |  |
| **BD do Cosmos para o Azure** | MongoDB | ✔ | GA |
| **BD do Azure para MySQL** | MySQL | ✔ | GA |
|   | RDS MySQL | ✔ | GA |
| **BD do Azure para PostgreSQL** | PostgreSQL | ✔ | GA |
|   | RDS PostgreSQL | ✔ | GA |
|   | Oracle | ✔ | Pré-visualização pública |

## <a name="next-steps"></a>Passos seguintes

Para obter uma visão geral do serviço de migração de banco de dados do Azure e da disponibilidade regional, consulte o artigo o [que é o serviço de migração de banco de dados](dms-overview.md)
