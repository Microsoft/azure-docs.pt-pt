---
title: Migrar no local cargas de trabalho do SSIS para o SSIS na Fábrica de Dados Azure
description: Migrar no local as cargas de trabalho do SSIS para o SSIS em ADF.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 9/3/2019
ms.openlocfilehash: eecf7ba1471e35e2d9ab26394c7295f324c4ca20
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84116395"
---
# <a name="migrate-on-premises-ssis-workloads-to-ssis-in-adf"></a>Migrar no local cargas de trabalho do SSIS para o SSIS em ADF

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Descrição geral

Quando migra as suas cargas de dados do SQL Server nas instalações para os serviços de base de dados Azure, nomeadamente a Base de Dados Azure SQL ou a Instância Gerida Azure SQL, as suas cargas de trabalho ETL nos Serviços de Integração de Servidores SQL (SSIS) como um dos serviços de valor acrescentado primário também terão de ser migrados.

O tempo de funcionamento do Funcionamento do Tempo de Funcionamento (IR) da Azure-SSIS em Azure Data Factory (ADF) suporta a execução de pacotes SSIS. Uma vez aprovisionado o IR Azure-SSIS, pode utilizar ferramentas familiares, tais como Ferramentas de Dados do Servidor SQL (SSDT)/SQL Server Management Studio (SSMS), e utilitários de linha de comando, tais como dtinstall/dtutil/dtexec, para implementar e executar os seus pacotes em Azure. Para mais informações, consulte a visão geral do [lifting e do turno do Azure SSIS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview).

Este artigo destaca o processo de migração das suas cargas de trabalho ETL de SSIS no local para SSIS em ADF. O processo de migração consiste em duas fases: **Avaliação** e **Migração.**

## <a name="assessment"></a>Avaliação

Para estabelecer um plano de migração completo, uma avaliação minuciosa ajudará a identificar problemas com os pacotes SSIS de origem que impediriam uma migração bem sucedida.

O Data Migration Assistant (DMA) é uma ferramenta livremente transferível para este fim que pode ser instalada e executada localmente. O projeto de avaliação do DMA dos serviços de **integração** do tipo pode ser criado para avaliar os pacotes SSIS em lotes e identificar questões de compatibilidade que são apresentadas nas seguintes categorias:

- Bloqueadores de migração: Estas são questões de compatibilidade que bloqueiam os pacotes de fontes de migração para funcionar no IR Azure-SSIS. A DMA fornece orientações para ajudá-lo a resolver estas questões.

- Questões informativas: Estas são características parcialmente suportadas ou depreciadas que são usadas em pacotes de origem. A DMA fornece um conjunto abrangente de recomendações, abordagens alternativas disponíveis no Azure e medidas mitigadoras para resolver.

### <a name="four-storage-types-for-ssis-packages"></a>Quatro tipos de armazenamento para pacotes SSIS

- Catálogo SSIS (SSISDB). Isto foi introduzido com o SQL Server 2012 e contém um conjunto de procedimentos, vistas e funções de valor de tabela usados para trabalhar com projetos/pacotes SSIS.
- Sistema de Ficheiros.
- Base de dados do sistema SQL Server (MSDB).
- Loja de pacotes SSIS. Esta é uma camada de gestão de pacotes em cima de dois subtipos:
  - MSDB, que é uma base de dados do sistema no SQL Server usado para armazenar pacotes SSIS.
  - Sistema de ficheiros gerido, que é uma pasta específica no caminho de instalação do SQL Server utilizado para armazenar pacotes SSIS.

A DMA suporta atualmente a avaliação de lotes de pacotes armazenados no Sistema de **Ficheiros,** Loja de **Pacotes**e **catálogo SSIS** desde a **versão DMA v5.0**.

Obtenha [DMA](https://docs.microsoft.com/sql/dma/dma-overview)e [faça a sua avaliação do pacote com ele](https://docs.microsoft.com/sql/dma/dma-assess-ssis).

## <a name="migration"></a>Migração

Dependendo dos tipos de [armazenamento](#four-storage-types-for-ssis-packages) de pacotes SSIS de origem e do destino de migração das cargas de trabalho da base de dados, as etapas para migrar **pacotes SSIS** e empregos do Agente de **ServidorEs SQL** que programam execuções de pacotes SSIS podem variar. Há dois cenários:

- [**Instância gerida azure SQL** como destino de carga de trabalho de base de dados](#azure-sql-managed-instance-as-database-workload-destination)
- [**Base de Dados Azure SQL** como destino de carga de trabalho de base de dados](#azure-sql-database-as-database-workload-destination)

### <a name="azure-sql-managed-instance-as-database-workload-destination"></a>**Instância gerida azure SQL** como destino de carga de trabalho de base de dados

| **Tipo de armazenamento de pacote** |Como migrar pacotes SSIS em lote|Como migrar os empregos sSIS em lote|
|-|-|-|
|SSISDB|[**SSISDB** migração](scenario-ssis-migration-ssisdb-mi.md)|[Migrar empregos SSIS para agente de instância gerida Azure SQL](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-sql-managed-instance-agent)|
|Sistema de Ficheiros|Reimplementá-las para arquivar ações/Ficheiros Azure através de dtinstall/dtutil/cópia manual, ou para manter nos sistemas de ficheiros para aceder via VNet/Self-Hosted IR. Para mais informações, consulte [a utilidade dtutil](https://docs.microsoft.com/sql/integration-services/dtutil-utility).|<li> Migrar com assistente de migração de [emprego SSIS em SSMS](how-to-migrate-ssis-job-ssms.md) <li>Converta-os em oleodutos/atividades/gatilhos ADF através de scripts/portal SSMS/ADF. Para mais informações, consulte a funcionalidade de [agendamento SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Servidor SQL (MSDB)|Exporte-os para sistemas de ficheiros/file shares/Ficheiros Azure via SSMS/dtutil. Para mais informações, consulte [pacotes SSIS exportadores](https://docs.microsoft.com/sql/integration-services/import-and-export-packages-ssis-service).|Converta-os em oleodutos/atividades/gatilhos ADF através de scripts/portal SSMS/ADF. Para mais informações, consulte a funcionalidade de [agendamento SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Loja de Pacotes|Exporte-os para sistemas de ficheiros/file shares/Ficheiros Azure via SSMS/dtutil ou reimplantá-los para arquivar ações/Ficheiros Azure através de dtinstall/dtutil/manual ou mantê-los em sistemas de ficheiros para aceder via VNet/Self-Hosted IR. Para mais informações, consulte a utilidade dtutil. Para mais informações, consulte [a utilidade dtutil](https://docs.microsoft.com/sql/integration-services/dtutil-utility).|Converta-os em oleodutos/atividades/gatilhos ADF através de scripts/portal SSMS/ADF. Para mais informações, consulte a funcionalidade de [agendamento SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|

### <a name="azure-sql-database-as-database-workload-destination"></a>**Base de Dados Azure SQL** como destino de carga de trabalho de base de dados

| **Tipo de armazenamento de pacote** |Como migrar pacotes SSIS em lote|Como migrar em lotes|
|-|-|-|
|SSISDB|Reimplantação para Azure-SSISDB via SSDT/SSMS. Para mais informações, consulte [a implementação de pacotes SSIS em Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial).|Converta-os em oleodutos/atividades/gatilhos ADF através de scripts/portal SSMS/ADF. Para mais informações, consulte a funcionalidade de [agendamento SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Sistema de Ficheiros|Reimplementá-las para arquivar ações/Ficheiros Azure através de dtinstall/dtutil/cópia manual, ou para manter nos sistemas de ficheiros para aceder via VNet/Self-Hosted IR. Para mais informações, consulte [a utilidade dtutil](https://docs.microsoft.com/sql/integration-services/dtutil-utility).|<li> Migrar com assistente de migração de [emprego SSIS em SSMS](how-to-migrate-ssis-job-ssms.md) <li> Converta-os em oleodutos/atividades/gatilhos ADF através de scripts/portal SSMS/ADF. Para mais informações, consulte a funcionalidade de [agendamento SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Servidor SQL (MSDB)|Exporte-os para sistemas de ficheiros/file shares/Ficheiros Azure via SSMS/dtutil. Para mais informações, consulte [pacotes SSIS exportadores](https://docs.microsoft.com/sql/integration-services/import-and-export-packages-ssis-service).|Converta-os em oleodutos/atividades/gatilhos ADF através de scripts/portal SSMS/ADF. Para mais informações, consulte a funcionalidade de [agendamento SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Loja de Pacotes|Exporte-os para sistemas de ficheiros/file shares/Ficheiros Azure via SSMS/dtutil ou reimplantá-los para arquivar ações/Ficheiros Azure através de dtinstall/dtutil/manual ou mantê-los em sistemas de ficheiros para aceder via VNet/Self-Hosted IR. Para mais informações, consulte a utilidade dtutil. Para mais informações, consulte [a utilidade dtutil](https://docs.microsoft.com/sql/integration-services/dtutil-utility).|Converta-os em oleodutos/atividades/gatilhos ADF através de scripts/portal SSMS/ADF. Para mais informações, consulte a funcionalidade de [agendamento SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|

## <a name="additional-resources"></a>Recursos adicionais

- [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction)
- [Assistente de Migração de Bases de Dados](https://docs.microsoft.com/sql/dma/dma-overview)
- [Levantar e deslocar cargas de trabalho do SSIS para a nuvem](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview?view=sql-server-2017)
- [Migrar pacotes SSIS para a Instância Gerida Azure SQL](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)
- [Reutilizar pacotes para a Base de Dados SQL do Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)

## <a name="next-steps"></a>Próximos passos

- [Validar pacotes SSIS implantados no Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-validate-packages)
- [Executar pacotes SSIS implantados em Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
- [Monitor Azure-SSIS Integração Tempo de Execução](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime)
- [Agendar execuções de pacotes SSIS em Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
