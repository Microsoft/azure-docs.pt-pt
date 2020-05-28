---
title: Migração SSIS com Instância Gerida Azure SQL como o destino da carga de trabalho da base de dados
description: Migração SSIS com O Caso Gerido Azure SQL como o destino de carga de trabalho da base de dados.
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
ms.openlocfilehash: c91731d70d5db59ecf5daa726fd42ee42c58f51e
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84116317"
---
# <a name="ssis-migration-with-azure-sql-managed-instance-as-the-database-workload-destination"></a>Migração SSIS com Instância Gerida Azure SQL como o destino da carga de trabalho da base de dados

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ao migrar cargas de trabalho de base de dados de uma instância do Servidor SQL para a Instância Gerida azure SQL, deve estar familiarizado com o Serviço de Migração de [Dados Azure](https://docs.microsoft.com/azure/dms/dms-overview)(DMS), e as [topoologias de rede para migrações de instância gerida sQL utilizando DMS](https://docs.microsoft.com/azure/dms/resource-network-topologies).

Este artigo centra-se na migração de pacotes do SQL Server Integration Service (SSIS) armazenados no catálogo SSIS (SSISDB) e no SQL Server Agent que programam execuções de pacotes SSIS.

## <a name="migrate-ssis-catalog-ssisdb"></a>Catálogo Migrate SSIS (SSISDB)

A migração SSISDB pode ser feita usando DMS, como descrito no artigo: [Migrar pacotes SSIS para SQL Managed Instance](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance).

## <a name="ssis-jobs-to-sql-managed-instance-agent"></a>Empregos ssis para agente de instância gerida SQL

A SQL Managed Instance tem um programador nativo de primeira classe, tal como o Agente de Servidores SQL nas instalações.  Uma vez que uma ferramenta de migração para trabalhos SSIS ainda não está disponível, eles têm que ser migrados do SQL Server Agent nas instalações para o agente SQL Managed Instance através de scripts/cópia manual.

## <a name="additional-resources"></a>Recursos adicionais

- [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction)
- [Azure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)
- [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)
- [Topologs de rede para migrações de instância gerida sQL usando DMS](https://docs.microsoft.com/azure/dms/resource-network-topologies)
- [Migrar pacotes SSIS para uma instância gerida pela SQL](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)

## <a name="next-steps"></a>Próximos passos

- [Ligue-se ao SSISDB em Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Executar pacotes SSIS implantados em Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
