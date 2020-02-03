---
title: Integração e implementação contínua
description: Base de Dados de classe empresarial DevOps experiência para SQL Data Warehouse com suporte incorporado para integração contínua e implantação usando Pipelines Azure.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: overview
ms.subservice: integration
ms.date: 08/28/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: a8178e5ff9ff4816ddd422d3c45cfc0e1e0b3d41
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712996"
---
# <a name="continuous-integration-and-deployment-for-azure-sql-data-warehouse"></a>Integração e implantação contínua sql data Warehouse

Este simples tutorial descreve como integrar o seu projeto de base de dados SQL Server Data (SSDT) com o Azure DevOps e alavancar os Pipelines Azure para configurar a integração e implementação contínuas. Este tutorial é o segundo passo na construção do seu oleoduto de integração e implantação contínua com o SQL Data Warehouse. 

## <a name="before-you-begin"></a>Antes de começar

- Passe pelo tutorial de integração de controlo de [fontes](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-source-control-integration)

- Configurar e ligar a Azure DevOps


## <a name="continuous-integration-with-visual-studio-build"></a>Integração contínua com a construção de Estúdio Visual

1. Navegue para os Oleodutos Azure e crie um novo oleoduto de construção.

      ![Novo Oleoduto](media/sql-data-warehouse-continuous-integration-and-deployment/1-new-build-pipeline.png "Novo Pipeline")

2. Selecione o seu repositório de código fonte (Azure Repos Git) e selecione o modelo de aplicação .NET Desktop.

      ![Configuração do gasoduto](media/sql-data-warehouse-continuous-integration-and-deployment/2-pipeline-setup.png "Configuração do gasoduto") 

3. Edite o seu ficheiro YAML para utilizar a piscina adequada do seu agente. O seu ficheiro YAML deve ser parecido com isto:

      ![YAML](media/sql-data-warehouse-continuous-integration-and-deployment/3-yaml-file.png "YAML")

Neste ponto, você tem um ambiente simples onde qualquer check-in no seu ramo principal de controlo de fonte deve desencadear automaticamente uma construção bem sucedida do Visual Studio do seu projeto de base de dados. Validar que a automatização está a trabalhar de ponta a ponta, fazendo uma alteração no seu projeto de base de dados local e verificando essa alteração no seu ramo principal.


## <a name="continuous-deployment-with-the-azure-sql-data-warehouse-or-database-deployment-task"></a>Implantação contínua com a tarefa de implantação do Armazém de Dados Azure SQL (ou Base de Dados)

1. Adicione uma nova tarefa utilizando a tarefa de implementação da Base de [Dados Azure SQL](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/sql-azure-dacpac-deployment?view=azure-devops) e preencha os campos necessários para se ligar ao seu armazém de dados-alvo. Quando esta tarefa funciona, o DACPAC gerado a partir do processo de construção anterior é implantado para o armazém de dados alvo. Também pode utilizar a tarefa de implementação do Armazém de [Dados Azure SQL](https://marketplace.visualstudio.com/items?itemName=ms-sql-dw.SQLDWDeployment). 

      ![Tarefa de implantação](media/sql-data-warehouse-continuous-integration-and-deployment/4-deployment-task.png "Tarefa de implantação")

2. Se estiver a utilizar um agente auto-hospedado, certifique-se de que define a sua variável ambiental para utilizar o SqlPackage.exe correto para o SQL Data Warehouse. O caminho deve ser parecido com isto:

      ![Variável ambiental](media/sql-data-warehouse-continuous-integration-and-deployment/5-environment-variable-preview.png "Variável de ambiente")

   C:\Program Files (x86)\Microsoft Visual Studio\2019\Preview\Common7\IDE\Extensions\Microsoft\SQLDB\DAC\150  

   Corra e valide o seu oleoduto. Pode fazer alterações localmente e verificar alterações no seu controlo de origem que devem gerar uma construção e implementação automáticas.

## <a name="next-steps"></a>Passos Seguintes

- Explore a [arquitetura azure SQL Data Warehouse](massively-parallel-processing-mpp-architecture.md)
- Crie rapidamente um Armazém de [Dados SQL](create-data-warehouse-portal.md)
- [Dados da amostra de carga](sql-data-warehouse-load-sample-databases.md)
- Explore [vídeos](/azure/sql-data-warehouse/sql-data-warehouse-videos)
