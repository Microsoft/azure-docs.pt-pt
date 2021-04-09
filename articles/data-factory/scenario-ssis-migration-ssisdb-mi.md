---
title: Migração SSIS com Azure SQL Caso Gerido como destino de carga de trabalho de base de dados
description: Migração SSIS com Azure SQL Managed Instance como destino de carga de trabalho de base de dados.
author: chugugrace
ms.author: chugu
ms.service: data-factory
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: 3d2bc60f8ba7120f8d962500c06be50e905c11a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100373594"
---
# <a name="ssis-migration-with-azure-sql-managed-instance-as-the-database-workload-destination"></a>Migração SSIS com Azure SQL Caso Gerido como destino de carga de trabalho de base de dados

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ao migrar cargas de trabalho de base de dados de uma instância sql server para Azure SQL Managed Instance, deve estar familiarizado com o [Azure Data Migration Service](../dms/dms-overview.md)(DMS) e as [topologias de rede para migrações de instâncias geridas SQL usando DMS](../dms/resource-network-topologies.md).

Este artigo centra-se na migração de pacotes do SQL Server Integration Service (SSIS) armazenados no catálogo SSIS (SSISDB) e no SQL Server Agent que programam execuções de pacotes SSIS.

## <a name="migrate-ssis-catalog-ssisdb"></a>Catálogo SSIS migrar (SSISDB)

A migração SSISDB pode ser feita utilizando dMS, conforme descrito no artigo: [Migrar pacotes SSIS para SQL Managed Instance](../dms/how-to-migrate-ssis-packages-managed-instance.md).

## <a name="ssis-jobs-to-sql-managed-instance-agent"></a>SSIS empregos para agente de instância gerida da SQL

SQL Managed Instance tem um programador nativo de primeira classe, tal como o SQL Server Agent no local.  Pode [executar pacotes SSIS através do Agente de Instância Gerida Azure SQL](how-to-invoke-ssis-package-managed-instance-agent.md).

Uma vez que uma ferramenta de migração para empregos SSIS ainda não está disponível, eles têm que ser migrados do SqL Server Agent nas instalações para o agente sql Managed Instance através de scripts/cópia manual.

## <a name="additional-resources"></a>Recursos adicionais

- [Azure Data Factory](./introduction.md)
- [Azure-SSIS Integration Runtime](./create-azure-ssis-integration-runtime.md)
- [Azure Database Migration Service](../dms/dms-overview.md)
- [Topologias de rede para migrações de instância gerida sql usando DMS](../dms/resource-network-topologies.md)
- [Migrar pacotes SSIS para uma sql gestudizada instância](../dms/how-to-migrate-ssis-packages-managed-instance.md)

## <a name="next-steps"></a>Passos seguintes

- [Ligue ao SSISDB em Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Executar pacotes SSIS implantados em Azure](/sql/integration-services/lift-shift/ssis-azure-run-packages)