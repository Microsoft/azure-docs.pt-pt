---
title: Migração do SSIS com a instância gerenciada do banco de dados SQL do Azure como destino da carga de trabalho
description: Migração do SSIS com a instância gerenciada do banco de dados SQL do Azure como o destino de carga de trabalho
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
ms.openlocfilehash: 38010e3aaa2d0544dfbfe19135d25250d2b021a2
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74929787"
---
# <a name="ssis-migration-with-azure-sql-database-managed-instance-as-the-database-workload-destination"></a>Migração do SSIS com a instância gerenciada do banco de dados SQL do Azure como destino da carga de trabalho

Ao migrar cargas de trabalho de banco de dados do SQL Server local para a instância gerenciada do banco de dados SQL do Azure, você deve estar familiarizado com o [serviço de migração de data do Azure](https://docs.microsoft.com/azure/dms/dms-overview)(DMS) e as [topologias de rede para migrações de instância gerenciada do banco de dado SQL](https://docs.microsoft.com/azure/dms/resource-network-topologies)

Este artigo se concentra na migração de pacotes do SSIS (serviço de integração do SQL Server) armazenados no catálogo do SSIS (SSISDB) e em trabalhos de SQL Server Agent que agendam execuções de pacotes SSIS.

## <a name="migrate-ssis-catalog-ssisdb"></a>Migrar catálogo do SSIS (SSISDB)

A migração do SSISDB pode ser feita usando DMS, conforme descrito no artigo: [migrar pacotes do SSIS para uma instância gerenciada do banco de dados SQL do Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance).

## <a name="ssis-jobs-to-azure-sql-database-managed-instance-agent"></a>Trabalhos do SSIS para o agente de instância gerenciada do banco de dados SQL

A instância gerenciada do banco de dados SQL do Azure tem um Agendador nativo de primeira classe, assim como SQL Server Agent local.  Como uma ferramenta de migração para trabalhos do SSIS ainda não está disponível, eles precisam ser migrados do SQL Server Agent local para o agente de instância gerenciada do banco de dados SQL do Azure por meio de scripts/cópia manual.

## <a name="additional-resources"></a>Recursos adicionais

- [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction)
- [Azure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)
- [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)
- [Topologias de rede para migrações de instância gerenciada do banco de dados SQL do Azure usando DMS](https://docs.microsoft.com/azure/dms/resource-network-topologies)
- [Migrar pacotes do SSIS para uma instância gerenciada do banco de dados SQL do Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)

## <a name="next-steps"></a>Passos seguintes

- [Conectar-se ao SSISDB no Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Executar pacotes do SSIS implantados no Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
