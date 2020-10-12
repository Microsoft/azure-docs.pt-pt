---
title: Migração SSIS com Azure SQL Caso Gerido como destino de carga de trabalho de base de dados
description: Migração SSIS com Azure SQL Managed Instance como destino de carga de trabalho de base de dados.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: 6de08faee78deeb86117084b420eb5043153f62d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88186051"
---
# <a name="ssis-migration-with-azure-sql-managed-instance-as-the-database-workload-destination"></a>Migração SSIS com Azure SQL Caso Gerido como destino de carga de trabalho de base de dados

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ao migrar cargas de trabalho de base de dados de uma instância sql server para Azure SQL Managed Instance, deve estar familiarizado com o [Azure Data Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)(DMS) e as [topologias de rede para migrações de instâncias geridas SQL usando DMS](https://docs.microsoft.com/azure/dms/resource-network-topologies).

Este artigo centra-se na migração de pacotes do SQL Server Integration Service (SSIS) armazenados no catálogo SSIS (SSISDB) e no SQL Server Agent que programam execuções de pacotes SSIS.

## <a name="migrate-ssis-catalog-ssisdb"></a>Catálogo SSIS migrar (SSISDB)

A migração SSISDB pode ser feita utilizando dMS, conforme descrito no artigo: [Migrar pacotes SSIS para SQL Managed Instance](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance).

## <a name="ssis-jobs-to-sql-managed-instance-agent"></a>SSIS empregos para agente de instância gerida da SQL

SQL Managed Instance tem um programador nativo de primeira classe, tal como o SQL Server Agent no local.  Pode [executar pacotes SSIS através do Agente de Instância Gerida Azure SQL](how-to-invoke-ssis-package-managed-instance-agent.md).

Uma vez que uma ferramenta de migração para empregos SSIS ainda não está disponível, eles têm que ser migrados do SqL Server Agent nas instalações para o agente sql Managed Instance através de scripts/cópia manual.

## <a name="additional-resources"></a>Recursos adicionais

- [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction)
- [Azure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)
- [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)
- [Topologias de rede para migrações de instância gerida sql usando DMS](https://docs.microsoft.com/azure/dms/resource-network-topologies)
- [Migrar pacotes SSIS para uma sql gestudizada instância](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)

## <a name="next-steps"></a>Passos seguintes

- [Ligue ao SSISDB em Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Executar pacotes SSIS implantados em Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
