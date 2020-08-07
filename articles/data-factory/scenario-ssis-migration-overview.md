---
title: Migrar cargas de trabalho SSIS para sSIS na Fábrica de Dados Azure
description: Migrar no local cargas de trabalho SSIS para SSIS em ADF.
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
ms.openlocfilehash: 034d1c4dbbb91ad7317ffb56b1fe38e010694c44
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/07/2020
ms.locfileid: "87927101"
---
# <a name="migrate-on-premises-ssis-workloads-to-ssis-in-adf"></a>Migrar cargas de trabalho SSIS no local para ssis em ADF

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Descrição geral

Quando migra as suas cargas de dados do SQL Server nas instalações para os serviços de base de dados Azure, nomeadamente Azure SQL Database ou Azure SQL Managed Instance, as suas cargas de trabalho ETL nos Serviços de Integração de Servidores SQL (SSIS) como um dos serviços de valor acrescentado primários também terão de ser migradas.

O tempo de execução da integração Azure-SSIS (IR) na Azure Data Factory (ADF) suporta a execução de pacotes SSIS. Uma vez alojado Azure-SSIS IR, pode então utilizar ferramentas familiares, tais como SQL Server Data Tools (SSDT)/SQL Server Management Studio (SSMS) e utilitários de linha de comando, tais como dtinstall/dtutil/dtexec, para implementar e executar os seus pacotes em Azure. Para mais informações, consulte [a visão geral do elevador e da mudança do Azure SSIS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview).

Este artigo destaca o processo de migração das suas cargas de trabalho ETL, desde as instalações SSIS a SSIS em ADF. O processo de migração consiste em duas fases: **Avaliação** e **Migração.**

## <a name="assessment"></a>Avaliação

Para estabelecer um plano de migração completo, uma avaliação aprofundada ajudará a identificar problemas com os pacotes SSIS de origem que impediriam uma migração bem sucedida.

Data Migration Assistant (DMA) é uma ferramenta livremente descarregada para este fim que pode ser instalada e executada localmente. O projeto de avaliação do DMA dos Serviços de **Integração** do tipo pode ser criado para avaliar os pacotes SSIS em lotes e identificar questões de compatibilidade que são apresentadas nas seguintes categorias:

- Bloqueadores de migração: Trata-se de questões de compatibilidade que bloqueiam os pacotes de fontes de migração para funcionar em Azure-SSIS IR. A DMA fornece orientações para o ajudar a resolver estas questões.

- Questões informativas: Estas são funcionalidades parcialmente suportadas ou preprecadas que são utilizadas em pacotes de origem. A DMA fornece um conjunto abrangente de recomendações, abordagens alternativas disponíveis no Azure e medidas mitigadoras para resolver.

### <a name="four-storage-types-for-ssis-packages"></a>Quatro tipos de armazenamento para pacotes SSIS

- Catálogo SSIS (SSISDB). Isto foi introduzido com o SQL Server 2012 e contém um conjunto de procedimentos, vistas e funções de valor de mesa usadas para trabalhar com projetos/pacotes SSIS.
- Sistema de Ficheiros.
- Base de dados do sistema SQL Server (MSDB).
- Loja de pacotes SSIS. Esta é uma camada de gestão de pacotes em cima de dois subtipos:
  - MSDB, que é uma base de dados de sistema no SQL Server usado para armazenar pacotes SSIS.
  - Sistema de ficheiros gerido, que é uma pasta específica no caminho de instalação do SQL Server usado para armazenar pacotes SSIS.

A DMA suporta atualmente a avaliação de lotes de pacotes armazenados no Sistema de **Ficheiros,** **Loja de Pacotes**e **catálogo SSIS** desde **a versão DMA v5.0**.

Obtenha [DMA](https://docs.microsoft.com/sql/dma/dma-overview)e [realize a sua avaliação do pacote com ele](https://docs.microsoft.com/sql/dma/dma-assess-ssis).

## <a name="migration"></a>Migração

Dependendo dos tipos de [armazenamento](#four-storage-types-for-ssis-packages) de pacotes SSIS de origem e do destino migratório das cargas de trabalho da base de dados, as etapas para migrar **pacotes SSIS** e **trabalhos de agente de servidor sql** que programam execuções de pacotes SSIS podem variar. Há dois cenários:

- [**Azure SQL Caso Gerido** como destino de carga de trabalho de base de dados](#azure-sql-managed-instance-as-database-workload-destination)
- [**Azure SQL Database** como destino de carga de trabalho de base de dados](#azure-sql-database-as-database-workload-destination)

### <a name="azure-sql-managed-instance-as-database-workload-destination"></a>**Azure SQL Caso Gerido** como destino de carga de trabalho de base de dados

| **Tipo de armazenamento de pacote** |Como migrar pacotes SSIS em lote|Como migrar os empregos SSIS em lotes|
|-|-|-|
|SSISDB|[Migrar **SSISDB**](scenario-ssis-migration-ssisdb-mi.md)|[Migrar empregos SSIS para agente de instância gerida Azure SQL](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-sql-managed-instance-agent)|
|Sistema de Ficheiros|Reimplantá-los para arquivar ações/Ficheiros Azure através de dtinstall/dtutil/cópia manual, ou para manter em sistemas de ficheiros para aceder via VNet/AUTO-Hosted IR. Para mais informações, consulte [o utilitário dtutil](https://docs.microsoft.com/sql/integration-services/dtutil-utility).|<li> Migrar com [o Assistente de Migração de Emprego SSIS em SSMS](how-to-migrate-ssis-job-ssms.md) <li>Converta-os em gasodutos/atividades/gatilhos ADF através de scripts/portal SSMS/ADF. Para mais informações, consulte [a funcionalidade de agendamento SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Sql Server (MSDB)|Exporte-os para sistemas de ficheiros/partilhas de ficheiros/Ficheiros Azure via SSMS/dtutil. Para mais informações, consulte [os pacotes SSIS de exportação.](https://docs.microsoft.com/sql/integration-services/service/package-management-ssis-service#import-and-export-packages)|Converta-os em gasodutos/atividades/gatilhos ADF através de scripts/portal SSMS/ADF. Para mais informações, consulte [a funcionalidade de agendamento SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Loja de Pacotes|Exporte-os para arquivar sistemas/partilhas de ficheiros/Ficheiros Azure via SSMS/dtutil ou reimplantá-los para arquivar ações/Ficheiros Azure através de dtinstall/dtutil/cópia manual ou mantê-los em sistemas de ficheiros para aceder via VNet/AUTO-Hosted IR. Para mais informações, consulte a utilidade dtutil. Para mais informações, consulte [o utilitário dtutil](https://docs.microsoft.com/sql/integration-services/dtutil-utility).|Converta-os em gasodutos/atividades/gatilhos ADF através de scripts/portal SSMS/ADF. Para mais informações, consulte [a funcionalidade de agendamento SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|

### <a name="azure-sql-database-as-database-workload-destination"></a>**Azure SQL Database** como destino de carga de trabalho de base de dados

| **Tipo de armazenamento de pacote** |Como migrar pacotes SSIS em lote|Como fazer trabalhos migratórios|
|-|-|-|
|SSISDB|Recolocação para Azure-SSISDB via SSDT/SSMS. Para obter mais informações, consulte [a implementação de pacotes SSIS em Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial).|Converta-os em gasodutos/atividades/gatilhos ADF através de scripts/portal SSMS/ADF. Para mais informações, consulte [a funcionalidade de agendamento SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Sistema de Ficheiros|Reimplantá-los para arquivar ações/Ficheiros Azure através de dtinstall/dtutil/cópia manual, ou para manter em sistemas de ficheiros para aceder via VNet/AUTO-Hosted IR. Para mais informações, consulte [o utilitário dtutil](https://docs.microsoft.com/sql/integration-services/dtutil-utility).|<li> Migrar com [o Assistente de Migração de Emprego SSIS em SSMS](how-to-migrate-ssis-job-ssms.md) <li> Converta-os em gasodutos/atividades/gatilhos ADF através de scripts/portal SSMS/ADF. Para mais informações, consulte [a funcionalidade de agendamento SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Sql Server (MSDB)|Exporte-os para sistemas de ficheiros/partilhas de ficheiros/Ficheiros Azure via SSMS/dtutil. Para mais informações, consulte [os pacotes SSIS de exportação.](https://docs.microsoft.com/sql/integration-services/service/package-management-ssis-service#import-and-export-packages)|Converta-os em gasodutos/atividades/gatilhos ADF através de scripts/portal SSMS/ADF. Para mais informações, consulte [a funcionalidade de agendamento SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Loja de Pacotes|Exporte-os para arquivar sistemas/partilhas de ficheiros/Ficheiros Azure via SSMS/dtutil ou reimplantá-los para arquivar ações/Ficheiros Azure através de dtinstall/dtutil/cópia manual ou mantê-los em sistemas de ficheiros para aceder via VNet/AUTO-Hosted IR. Para mais informações, consulte a utilidade dtutil. Para mais informações, consulte [o utilitário dtutil](https://docs.microsoft.com/sql/integration-services/dtutil-utility).|Converta-os em gasodutos/atividades/gatilhos ADF através de scripts/portal SSMS/ADF. Para mais informações, consulte [a funcionalidade de agendamento SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|

## <a name="additional-resources"></a>Recursos adicionais

- [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction)
- [Assistente de Migração de Dados](https://docs.microsoft.com/sql/dma/dma-overview)
- [Levante e desloque cargas de trabalho SSIS para a nuvem](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview?view=sql-server-2017)
- [Migrar pacotes do SSIS para uma Instância Gerida do SQL no Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)
- [Reafectar pacotes para a Base de Dados Azure SQL](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)

## <a name="next-steps"></a>Passos seguintes

- [Validar pacotes SSIS implantados no Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-validate-packages)
- [Executar pacotes SSIS implantados em Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
- [Monitor Azure-SSIS Integração Tempo de execução](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime)
- [Agendar execuções de pacotes SSIS em Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
