---
title: Migração SSIS com Base de Dados Azure SQL gerida como o destino da carga de trabalho da base de dados
description: A migração do SSIS com a Base de Dados Azure SQL geriu a instância como o destino da carga de trabalho da base de dados.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74929787"
---
# <a name="ssis-migration-with-azure-sql-database-managed-instance-as-the-database-workload-destination"></a>Migração SSIS com Base de Dados Azure SQL gerida como o destino da carga de trabalho da base de dados

Ao migrar cargas de trabalho de base de dados do SQL Server nas instalações para a base de dados Azure SQL gerida, deve estar familiarizado com o Serviço de Migração de [Dados Azure](https://docs.microsoft.com/azure/dms/dms-overview)(DMS), e as topoologias de rede para a Base de [Dados Azure SQL geridas por migrações de instâncias utilizando DMS](https://docs.microsoft.com/azure/dms/resource-network-topologies).

Este artigo centra-se na migração de pacotes do SQL Server Integration Service (SSIS) armazenados no catálogo SSIS (SSISDB) e no SQL Server Agent que programam execuções de pacotes SSIS.

## <a name="migrate-ssis-catalog-ssisdb"></a>Catálogo Migrate SSIS (SSISDB)

A migração Do SSISDB pode ser feita utilizando DMS, conforme descrito no artigo: [Migrar pacotes SSIS para uma instância gerida](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)pela Base de Dados Azure SQL .

## <a name="ssis-jobs-to-azure-sql-database-managed-instance-agent"></a>SSIS empregos para Azure SQL Database gerido agente de instância

A base de dados Azure SQL gerida tem um programador nativo de primeira classe, tal como o Agente de Servidores SQL nas instalações.  Uma vez que uma ferramenta de migração para empregos SSIS ainda não está disponível, estes têm de ser migrados do SQL Server Agent nas instalações para o Agente de instância gerido pela Azure SQL Database através de scripts/cópia manual.

## <a name="additional-resources"></a>Recursos adicionais

- [Fábrica de Dados Azure](https://docs.microsoft.com/azure/data-factory/introduction)
- [Tempo de execução de integração Azure-SSIS](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)
- [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)
- [Topologs de rede para Base de Dados Azure SQL gerida seletiva seletivas usando DMS](https://docs.microsoft.com/azure/dms/resource-network-topologies)
- [Migrar pacotes do SSIS para uma instância gerida da Base de Dados SQL do Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)

## <a name="next-steps"></a>Passos seguintes

- [Ligue-se ao SSISDB em Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Executar pacotes SSIS implantados em Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
