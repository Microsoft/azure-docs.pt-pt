---
title: Migração e ferramentas de serviço de matriz de dados - Azure | Documentos da Microsoft
description: Saiba mais sobre os serviços e ferramentas disponíveis para migrar bases de dados e para suportar várias fases do processo de migração.
services: database-migration
author: HJToland3
ms.author: rajpo
manager: craigg
ms.reviewer: douglasl
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 03/12/2019
ms.openlocfilehash: f62c3105674f741702c35e50ce4afe436f67918d
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57792094"
---
# <a name="services-and-tools-available-for-data-migration-scenarios"></a>Serviços e ferramentas disponíveis para cenários de migração de dados

Este artigo fornece uma matriz dos Microsoft e os serviços de terceiros e ferramentas disponíveis para ajudá-lo com vários cenários de migração de dados e da base de dados e tarefas de especialidade.

A tabela a seguir identifica o serviço e as ferramentas que pode utilizar para planear com êxito para a migração de dados e para concluir seu várias fases.

> [!NOTE]
> Nas tabelas a seguir, os itens marcados com um asterisco (*) representam as ferramentas de terceiros.

## <a name="business-justification-phase"></a>Fase de justificação de negócio

| **Origem** | **Target** | **Detetar /**<br/>**Inventário** | **SKU e de destino**<br/>**recommendation** | **TCO/ROI e**<br/>**Caso comercial** |
| --- | --- | --- | --- | --- |
| SQL Server | BD SQL do Azure | [Kit de ferramentas de mapa](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloud Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [Calculadora de TCO](https://azure.microsoft.com/pricing/tco/calculator/) |
| SQL Server | MI de BD SQL do Azure | [Kit de ferramentas de mapa](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [Cloud Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [Calculadora de TCO](https://azure.microsoft.com/pricing/tco/calculator/) |
| SQL Server | VM do Azure SQL | [Kit de ferramentas de mapa](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [Cloud Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [Calculadora de TCO](https://azure.microsoft.com/pricing/tco/calculator/) |
| SQL Server | O SQL DW |  |  | [Calculadora de TCO](https://azure.microsoft.com/pricing/tco/calculator/) |
| RDS SQL | VM do Azure SQL DB, MI, |  | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | [Calculadora de TCO](https://azure.microsoft.com/pricing/tco/calculator/) |
| Oracle | VM do Azure SQL DB, MI, | [Kit de ferramentas de mapa](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [MigVisor*](https://www.migvisor.com/) |  |
| Oracle | O SQL DW | [Kit de ferramentas de mapa](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) |  |  |
| Oracle | BD do Azure para PostgreSQL |  |  |  |
| MongoDB | BD do Cosmos | [Cloudamize*](https://www.cloudamize.com/) | [Cloudamize*](https://www.cloudamize.com/) |  |
| Cassandra | BD do Cosmos |  |  |  |
| MySQL | VM do Azure SQL DB, MI, | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [Cloud Atlas *](https://www.unifycloud.com/cloud-migration-tool/) | [Calculadora de TCO](https://azure.microsoft.com/pricing/tco/calculator/) |
| MySQL | BD do Azure para MySQL | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) |  | [Calculadora de TCO](https://azure.microsoft.com/pricing/tco/calculator/) |
| RDS MySQL | BD do Azure para MySQL |  |  | [Calculadora de TCO](https://azure.microsoft.com/pricing/tco/calculator/) |
| PostgreSQL | BD do Azure para PostgreSQL | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) |  | [Calculadora de TCO](https://azure.microsoft.com/pricing/tco/calculator/) |
| RDS PostgreSQL | BD do Azure para PostgreSQL |  |  | [Calculadora de TCO](https://azure.microsoft.com/pricing/tco/calculator/) |
| DB2 | VM do Azure SQL DB, MI, |  |  |  |
| Access | VM do Azure SQL DB, MI, |  |  |  |
| Sybase | VM do Azure SQL DB, MI, |  |  |  |
| | | | | |

## <a name="pre-migration-phase"></a>Fase de pré-migração

| **Origem** | **Target** | **Acesso a dados de aplicação**<br/>**Avaliação de camada** | **Base de Dados**<br/>**Assessment** | **Performance** (Desempenho)<br/>**Assessment** |
| --- | --- | --- | --- | --- |
| SQL Server | BD SQL do Azure |  | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloud Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DEA](https://www.microsoft.com/download/details.aspx?id=54090)<br/>[Cloudamize*](https://www.cloudamize.com/) |
| SQL Server | MI de BD SQL do Azure |  | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloud Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DEA](https://www.microsoft.com/download/details.aspx?id=54090)<br/>[Cloudamize*](https://www.cloudamize.com/) |
| SQL Server | VM do Azure SQL |  | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloud Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DEA](https://www.microsoft.com/download/details.aspx?id=54090)<br/>[Cloudamize*](https://www.cloudamize.com/) |
| SQL Server | O SQL DW |  |  |  |
| RDS SQL | VM do Azure SQL DB, MI, |  | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | [DEA](https://www.microsoft.com/download/details.aspx?id=54090) |
| Oracle | VM do Azure SQL DB, MI, |  | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [Simora*](http://www.simora.co.uk/) |
| Oracle | O SQL DW |  | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [Simora*](http://www.simora.co.uk/) |
| Oracle | BD do Azure para PostgreSQL |  |  |  |
| MongoDB | BD do Cosmos |  | [Cloudamize*](https://www.cloudamize.com/) | [Cloudamize*](https://www.cloudamize.com/) |
| Cassandra | BD do Cosmos |  |  |  |
| MySQL | VM do Azure SQL DB, MI, |  | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Cloud Atlas *](https://www.unifycloud.com/cloud-migration-tool/) |  |
| MySQL | BD do Azure para MySQL |  |  |  |
| RDS MySQL | BD do Azure para MySQL |  |  |  |
| PostgreSQL | BD do Azure para PostgreSQL |  |  |  |
| RDS PostgreSQL | BD do Azure para PostgreSQL |  |  |  |
| DB2 | VM do Azure SQL DB, MI, |  | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Access | VM do Azure SQL DB, MI, |  | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Sybase | VM do Azure SQL DB, MI, |  | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| | | | | |

## <a name="migration-phase"></a>Fase de migração

| **Origem** | **Target** | **Schema** | **Dados**<br/>**(Offline)** | **Dados**<br/>**(Online)** |
| --- | --- | --- | --- | --- |
| SQL Server | BD SQL do Azure | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/)<br/>[Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| SQL Server | MI de BD SQL do Azure | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/)<br/>[Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| SQL Server | VM do Azure SQL | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/)<br/>[Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| SQL Server | O SQL DW |  |  |  |
| RDS SQL | VM do Azure SQL DB, MI, | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | [DMS](https://azure.microsoft.com/services/database-migration/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Oracle | VM do Azure SQL DB, MI, | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[SharePlex*](https://www.quest.com/products/shareplex/) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[SharePlex*](https://www.quest.com/products/shareplex/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[SharePlex*](https://www.quest.com/products/shareplex/)<br/>[Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Oracle | O SQL DW | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |
| Oracle | BD do Azure para PostgreSQL |  |  |  |
| MongoDB | BD do Cosmos | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/)<br/>[Imanis dados *](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/)<br/>[Imanis dados *](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) | [Cloudamize*](https://www.cloudamize.com/)<br/>[Imanis dados *](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Cassandra | BD do Cosmos | [Imanis dados *](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) | [Imanis dados *](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) | [Imanis dados *](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) |
| MySQL | VM do Azure SQL DB, MI, | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| MySQL | BD do Azure para MySQL | [MySQL despejo *](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) | [DMS](https://azure.microsoft.com/services/database-migration/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| RDS MySQL | BD do Azure para MySQL | [MySQL despejo *](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) | [DMS](https://azure.microsoft.com/services/database-migration/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| PostgreSQL | BD do Azure para PostgreSQL | [PG despejo *](https://www.postgresql.org/docs/11/static/app-pgdump.html) | [DMS](https://azure.microsoft.com/services/database-migration/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| RDS PostgreSQL | BD do Azure para PostgreSQL | [PG despejo *](https://www.postgresql.org/docs/11/static/app-pgdump.html) | [DMS](https://azure.microsoft.com/services/database-migration/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| DB2 | VM do Azure SQL DB, MI, | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Access | VM do Azure SQL DB, MI, | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |
| Sybase | VM do Azure SQL DB, MI, | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity *](https://www.attunity.com/products/replicate/)<br/>[Striim *](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| | | | | |

## <a name="post-migration-phase"></a>Fase de pós-migração

| **Origem** | **Target** | **Optimize** |
| --- | --- | --- |
| SQL Server | BD SQL do Azure | [Cloud Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) |
| SQL Server | MI de BD SQL do Azure | [Cloud Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) |
| SQL Server | VM do Azure SQL | [Cloud Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) |
| SQL Server | O SQL DW |  |
| RDS SQL | VM do Azure SQL DB, MI, |  |
| Oracle | VM do Azure SQL DB, MI, |  |
| Oracle | O SQL DW |  |
| Oracle | BD do Azure para PostgreSQL |  |
| MongoDB | BD do Cosmos | [Cloudamize*](https://www.cloudamize.com/) |
| Cassandra | BD do Cosmos |  |
| MySQL | VM do Azure SQL DB, MI, |  |
| MySQL | BD do Azure para MySQL |  |
| RDS MySQL | BD do Azure para MySQL |  |
| PostgreSQL | BD do Azure para PostgreSQL |  |
| RDS PostgreSQL | BD do Azure para PostgreSQL |  |
| DB2 | VM do Azure SQL DB, MI, |  |
| Access | VM do Azure SQL DB, MI, |  |
| Sybase | VM do Azure SQL DB, MI, |  |
| | | |

## <a name="next-steps"></a>Passos Seguintes

Para uma descrição geral do serviço de migração de base de dados do Azure, consulte o artigo [o que é o Azure Database Migration Service](dms-overview.md).
