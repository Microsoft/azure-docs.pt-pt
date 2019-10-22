---
title: Integração e implantação contínuas | Microsoft Docs
description: Experiência de DevOps de banco de dados de classe empresarial para SQL Data Warehouse com suporte interno para integração e implantação contínua usando o Azure Pipelines.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: overview
ms.subservice: integration
ms.date: 08/28/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 7afb616fffaf01b1efa4f5d5a4af9dddd9683781
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "70143150"
---
# <a name="continuous-integration-and-deployment-for-azure-sql-data-warehouse"></a>Integração e implantação contínuas para o Azure SQL Data Warehouse

Este tutorial simples descreve como integrar seu projeto de banco de dados SSDT (SQL Server Data Tools) com o Azure DevOps e aproveitar Azure Pipelines para configurar a integração e a implantação contínuas. Este tutorial é a segunda etapa para criar seu pipeline de implantação e integração contínua com o SQL Data Warehouse. 

## <a name="before-you-begin"></a>Antes de começar

- Percorrer o [tutorial de integração de controle do código-fonte](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-source-control-integration)

- Criar um [agente auto-hospedado](https://docs.microsoft.com/azure/devops/pipelines/agents/agents?view=azure-devops#install) que tenha o bits de visualização do SSDT (16,3 Preview 2 e superior) instalado para SQL data warehouse (versão prévia)

- Configurar e conectar-se ao Azure DevOps

  > [!NOTE]
  > No momento, o SSDT está em visualização, no qual você precisará aproveitar um agente auto-hospedado. Os agentes hospedados pela Microsoft serão atualizados nos próximos meses.

## <a name="continuous-integration-with-visual-studio-build"></a>Integração contínua com o Visual Studio Build

1. Navegue até Azure Pipelines e crie um novo pipeline de compilação

      ![Novo pipeline](media/sql-data-warehouse-continuous-integration-and-deployment/1-new-build-pipeline.png "Novo Pipeline")

2. Selecione o repositório de código-fonte (Azure Repos git) e selecione o modelo de aplicativo da área de trabalho .NET

      ![Configuração do pipeline](media/sql-data-warehouse-continuous-integration-and-deployment/2-pipeline-setup.png "Configuração do pipeline") 

3. Edite o arquivo YAML para usar o pool apropriado do seu agente. O arquivo YAML deve ser semelhante a este:

      ![YAML](media/sql-data-warehouse-continuous-integration-and-deployment/3-yaml-file.png "YAML")

Neste ponto, você tem um ambiente simples em que qualquer check-in em sua Branch mestre do repositório de controle do código-fonte deve disparar automaticamente uma compilação bem-sucedida do Visual Studio do seu projeto de banco de dados. Valide se a automação está funcionando de ponta a ponta fazendo uma alteração no projeto de banco de dados local e fazendo o check-in dessa alteração para sua ramificação mestre.


## <a name="continuous-deployment-with-the-azure-sql-database-deployment-task"></a>Implantação contínua com a tarefa de implantação do banco de dados SQL do Azure

1. Adicione uma nova tarefa usando a [tarefa de implantação do banco de dados SQL do Azure](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/sql-azure-dacpac-deployment?view=azure-devops) e preencha os campos obrigatórios para se conectar ao seu data warehouse de destino. Quando essa tarefa é executada, o DACPAC gerado a partir do processo de compilação anterior é implantado no data warehouse de destino.

      ![Tarefa de implantação](media/sql-data-warehouse-continuous-integration-and-deployment/4-deployment-task.png "Tarefa de implantação")

2. Ao usar um agente auto-hospedado, certifique-se de definir sua variável de ambiente para usar o SqlPackage. exe correto para SQL Data Warehouse. O caminho deve ser semelhante a este:

      ![Variável de ambiente](media/sql-data-warehouse-continuous-integration-and-deployment/5-environment-variable-preview.png "Variável de ambiente")

   C:\Arquivos de programas (x86) \Microsoft Visual Studio\2019\Preview\Common7\IDE\Extensions\Microsoft\SQLDB\DAC\150  

   Execute e valide seu pipeline. Você pode fazer alterações localmente e fazer alterações no controle do código-fonte que devem gerar uma compilação e implantação automática.

## <a name="next-steps"></a>Passos seguintes

- Explorar a [arquitetura de SQL data warehouse do Azure](/azure/sql-data-warehouse/massively-parallel-processing-mpp-architecture)
- [Criar rapidamente um SQL data warehouse][create a SQL Data Warehouse]
- [Carregar dados de exemplo][load sample data].
- Explorar [vídeos](/azure/sql-data-warehouse/sql-data-warehouse-videos)



<!--Image references-->

[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Create a support ticket]: ./sql-data-warehouse-get-started-create-support-ticket.md
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[Integrated tools overview]: ./sql-data-warehouse-overview-integrate.md
[Backup and restore overview]: ./sql-data-warehouse-restore-database-overview.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[Blogs]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Customer Advisory Team blogs]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Feature requests]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN forum]: https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureSQLDataWarehouse
[Stack Overflow forum]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for SQL Data Warehouse]: https://azure.microsoft.com/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://azure.microsoft.com/support/legal/sla/
