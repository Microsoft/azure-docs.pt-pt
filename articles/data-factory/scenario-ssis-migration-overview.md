---
title: Migrar no local as cargas de trabalho dos Serviços de Integração de Servidores SQL (SSIS) para o SSIS na Azure Data Factory (ADF)
description: Migrar no local cargas de trabalho SSIS para SSIS em ADF.
author: chugugrace
ms.author: chugu
ms.service: data-factory
ms.topic: conceptual
ms.date: 9/3/2019
ms.openlocfilehash: 78c488302a874319f79a143e4657d161fe849855
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100373611"
---
# <a name="migrate-on-premises-ssis-workloads-to-ssis-in-adf"></a>Migrar cargas de trabalho SSIS no local para ssis em ADF

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Descrição Geral

Quando migra as suas cargas de dados do SQL Server nas instalações para os serviços de base de dados Azure, nomeadamente Azure SQL Database ou Azure SQL Managed Instance, as suas cargas de trabalho ETL nos Serviços de Integração de Servidores SQL (SSIS) como um dos serviços de valor acrescentado primários também terão de ser migradas.

O tempo de execução da integração Azure-SSIS (IR) na Azure Data Factory (ADF) suporta a execução de pacotes SSIS. Uma vez alojado Azure-SSIS IR, pode então utilizar ferramentas familiares, tais como SQL Server Data Tools (SSDT)/SQL Server Management Studio (SSMS) e utilitários de linha de comando, tais como dtinstall/dtutil/dtexec, para implementar e executar os seus pacotes em Azure. Para mais informações, consulte [a visão geral do elevador e da mudança do Azure SSIS](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview).

Este artigo destaca o processo de migração das suas cargas de trabalho ETL, desde as instalações SSIS a SSIS em ADF. O processo de migração consiste em duas fases: **Avaliação** e **Migração.**

## <a name="assessment"></a>Avaliação

Para estabelecer um plano de migração completo, uma avaliação aprofundada ajudará a identificar problemas com os pacotes SSIS de origem que impediriam uma migração bem sucedida.

Data Migration Assistant (DMA) é uma ferramenta livremente descarregada para este fim que pode ser instalada e executada localmente. O projeto de avaliação do DMA dos Serviços de **Integração** do tipo pode ser criado para avaliar os pacotes SSIS em lotes e identificar questões de compatibilidade que são apresentadas nas seguintes categorias:

- Bloqueadores de migração: problemas de compatibilidade que bloqueiam os pacotes de fontes de migração para funcionar em Azure-SSIS IR. A DMA fornece orientações para o ajudar a resolver estas questões.

- Questões informativas: funcionalidades parcialmente suportadas ou preprecadas que são utilizadas em pacotes de origem. A DMA fornece um conjunto abrangente de recomendações, abordagens alternativas disponíveis no Azure e medidas mitigadoras para resolver.

### <a name="four-storage-types-for-ssis-packages"></a>Quatro tipos de armazenamento para pacotes SSIS

- Catálogo SSIS (SSISDB). Introduzido com o SQL Server 2012 e contém um conjunto de procedimentos, vistas e funções de valor de mesa armazenadas para trabalhar com projetos/pacotes SSIS.
- Sistema de Ficheiros.
- Base de dados do sistema SQL Server (MSDB).
- Loja de pacotes SSIS. Uma camada de gestão de pacotes em cima de dois subtipos:
  - MSDB, que é uma base de dados de sistema no SQL Server usado para armazenar pacotes SSIS.
  - Sistema de ficheiros gerido, que é uma pasta específica no caminho de instalação do SQL Server usado para armazenar pacotes SSIS.

A DMA suporta atualmente a avaliação de lotes de pacotes armazenados no Sistema de **Ficheiros,** **Loja de Pacotes** e **catálogo SSIS** desde **a versão DMA v5.0**.

Obtenha [DMA](/sql/dma/dma-overview)e [realize a sua avaliação do pacote com ele](/sql/dma/dma-assess-ssis).

## <a name="migration"></a>Migração

Dependendo dos tipos de [armazenamento](#four-storage-types-for-ssis-packages) de pacotes SSIS de origem e do destino migratório das cargas de trabalho da base de dados, as etapas para migrar  **pacotes SSIS** e **trabalhos de agente de servidor sql** que programam execuções de pacotes SSIS podem variar. Há dois cenários:

- [**Azure SQL Caso Gerido** como destino de carga de trabalho de base de dados](#azure-sql-managed-instance-as-database-workload-destination)
- [**Azure SQL Database** como destino de carga de trabalho de base de dados](#azure-sql-database-as-database-workload-destination)

É também uma forma prática de usar [as Ferramentas SSIS DevOps,](/sql/integration-services/devops/ssis-devops-overview)para fazer a reafectação do pacote de lote para o destino de migração.  

### <a name="azure-sql-managed-instance-as-database-workload-destination"></a>**Azure SQL Caso Gerido** como destino de carga de trabalho de base de dados

| **Tipo de armazenamento de pacote** |Como migrar pacotes SSIS em lote|Como migrar os empregos SSIS em lotes|
|-|-|-|
|SSISDB|[Migrar **SSISDB**](scenario-ssis-migration-ssisdb-mi.md)|<li>[Migrar empregos SSIS para agente de instância gerida Azure SQL](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-sql-managed-instance-agent) <li>Converta-os em gasodutos/atividades/gatilhos ADF através de scripts/portal SSMS/ADF. Para mais informações, consulte [a funcionalidade de agendamento SSMS](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Sistema de Ficheiros|Reimplantá-los para arquivar ações/Ficheiros Azure através de dtinstall/dtutil/cópia manual, ou para manter em sistemas de ficheiros para aceder via VNet/AUTO-Hosted IR. Para mais informações, consulte [o utilitário dtutil](/sql/integration-services/dtutil-utility).|<li>[Migrar empregos SSIS para agente de instância gerida Azure SQL](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-sql-managed-instance-agent) <li> Migrar com [o Assistente de Migração de Emprego SSIS em SSMS](how-to-migrate-ssis-job-ssms.md) <li>Converta-os em gasodutos/atividades/gatilhos ADF através de scripts/portal SSMS/ADF. Para mais informações, consulte [a funcionalidade de agendamento SSMS](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Sql Server (MSDB)|Exporte-os para sistemas de ficheiros/partilhas de ficheiros/Ficheiros Azure via SSMS/dtutil. Para mais informações, consulte [os pacotes SSIS de exportação.](/sql/integration-services/service/package-management-ssis-service#import-and-export-packages)|Converta-os em gasodutos/atividades/gatilhos ADF através de scripts/portal SSMS/ADF. Para mais informações, consulte [a funcionalidade de agendamento SSMS](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Loja de Pacotes|Exporte-os para a loja de pacotes através de SSMS/dtutil ou reimplante-os para a loja de embalagem através de dtinstall/dtutil/cópia manual. Para obter mais informações, consulte Gerir pacotes com a loja de [pacotes de runtime de integração Azure-SSIS.](azure-ssis-integration-runtime-package-store.md)|<li>[Migrar empregos SSIS para agente de instância gerida Azure SQL](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-sql-managed-instance-agent) <li> Converta-os em gasodutos/atividades/gatilhos ADF através de scripts/portal SSMS/ADF. Para mais informações, consulte [a funcionalidade de agendamento SSMS](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|

### <a name="azure-sql-database-as-database-workload-destination"></a>**Azure SQL Database** como destino de carga de trabalho de base de dados

| **Tipo de armazenamento de pacote** |Como migrar pacotes SSIS em lote|Como fazer trabalhos migratórios|
|-|-|-|
|SSISDB|Recolocação para Azure-SSISDB via SSDT/SSMS. Para obter mais informações, consulte [a implementação de pacotes SSIS em Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial).|Converta-os em gasodutos/atividades/gatilhos ADF através de scripts/portal SSMS/ADF. Para mais informações, consulte [a funcionalidade de agendamento SSMS](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Sistema de Ficheiros|Reimplantá-los para arquivar ações/Ficheiros Azure através de dtinstall/dtutil/cópia manual, ou para manter em sistemas de ficheiros para aceder via VNet/AUTO-Hosted IR. Para mais informações, consulte [o utilitário dtutil](/sql/integration-services/dtutil-utility).|<li> Migrar com [o Assistente de Migração de Emprego SSIS em SSMS](how-to-migrate-ssis-job-ssms.md) <li> Converta-os em gasodutos/atividades/gatilhos ADF através de scripts/portal SSMS/ADF. Para mais informações, consulte [a funcionalidade de agendamento SSMS](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Sql Server (MSDB)|Exporte-os para sistemas de ficheiros/partilhas de ficheiros/Ficheiros Azure via SSMS/dtutil. Para mais informações, consulte [os pacotes SSIS de exportação.](/sql/integration-services/service/package-management-ssis-service#import-and-export-packages)|Converta-os em gasodutos/atividades/gatilhos ADF através de scripts/portal SSMS/ADF. Para mais informações, consulte [a funcionalidade de agendamento SSMS](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Loja de Pacotes|Exporte-os para arquivar sistemas/partilhas de ficheiros/Ficheiros Azure via SSMS/dtutil ou reimplantá-los para arquivar ações/Ficheiros Azure através de dtinstall/dtutil/cópia manual ou mantê-los em sistemas de ficheiros para aceder via VNet/AUTO-Hosted IR. Para mais informações, consulte a utilidade dtutil. Para mais informações, consulte [o utilitário dtutil](/sql/integration-services/dtutil-utility).|Converta-os em gasodutos/atividades/gatilhos ADF através de scripts/portal SSMS/ADF. Para mais informações, consulte [a funcionalidade de agendamento SSMS](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|

## <a name="additional-resources"></a>Recursos adicionais

- [Azure Data Factory](./introduction.md)
- [Assistente de Migração de Dados](/sql/dma/dma-overview)
- [Levante e desloque cargas de trabalho SSIS para a nuvem](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview)
- [Ferramentas de DevOps do SSIS](/sql/integration-services/devops/ssis-devops-overview)
- [Migrar pacotes do SSIS para uma Instância Gerida do SQL no Azure](../dms/how-to-migrate-ssis-packages-managed-instance.md)
- [Reafectar pacotes para a Base de Dados Azure SQL](../dms/how-to-migrate-ssis-packages.md)

- [Acesso de dados no local a partir do tempo de execução da integração Azure-SSIS](https://techcommunity.microsoft.com/t5/sql-server-integration-services/vnet-or-no-vnet-secure-data-access-from-ssis-in-azure-data/ba-p/1062056)
- [Personalize a configuração para um tempo de execução de integração Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md)
- [Access data stores and file shares with Windows authentication from SSIS packages in Azure](ssis-azure-connect-with-windows-auth.md) (Aceder a arquivos de dados e a partilhas de ficheiros com a autenticação do Windows a partir de pacotes do SSIS no Azure)
- [Utilizar autenticação de identidade gerida](/sql/integration-services/connection-manager/azure-storage-connection-manager#managed-identities-for-azure-resources-authentication)
- [Utilizar o Azure Key Vault](store-credentials-in-key-vault.md)
- [Configure o tempo de execução da integração Azure-SSIS para um alto desempenho](configure-azure-ssis-integration-runtime-performance.md)
- [Como iniciar e parar o Azure-SSIS Integration Runtime numa agenda](how-to-schedule-azure-ssis-integration-runtime.md)

## <a name="next-steps"></a>Passos seguintes

- [Validar pacotes SSIS implantados no Azure](/sql/integration-services/lift-shift/ssis-azure-validate-packages)
- [Executar pacotes SSIS implantados em Azure](/sql/integration-services/lift-shift/ssis-azure-run-packages)
- [Monitor Azure-SSIS Integração Tempo de execução](./monitor-integration-runtime.md#azure-ssis-integration-runtime)
- [Agendar execuções de pacotes SSIS em Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)