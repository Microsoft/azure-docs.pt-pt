---
title: Integração contínua e implantação para piscina SQL dedicada
description: Experiência de DevOps de classe empresarial para piscina DE SQL dedicada em Azure Synapse Analytics com suporte incorporado para integração contínua e implementação usando Pipelines Azure.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 95bf3a8c614b8b7c0269257cb62b3c3a0f60be13
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568289"
---
# <a name="continuous-integration-and-deployment-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Integração contínua e implantação para piscina SQL dedicada em Azure Synapse Analytics

Este simples tutorial descreve como integrar o seu projeto de base de dados sql server (SSDT) com Azure DevOps e alavancar a Azure Pipelines para configurar a integração e implementação contínuas. Este tutorial é o segundo passo na construção do seu oleoduto de integração e implantação contínua para armazenamento de dados.

## <a name="before-you-begin"></a>Antes de começar

- Passe pelo tutorial de [integração](sql-data-warehouse-source-control-integration.md) de controlo de fonte

- Configurar e ligar ao Azure DevOps

## <a name="continuous-integration-with-visual-studio-build"></a>Integração contínua com a construção do Estúdio Visual

1. Navegue até a Azure Pipelines e crie um novo oleoduto de construção.

      ![Novo Pipeline](./media/sql-data-warehouse-continuous-integration-and-deployment/1-new-build-pipeline.png "Novo Pipeline")

2. Selecione o seu repositório de código fonte (Azure Repos Git) e selecione o modelo de aplicação .NET Desktop.

      ![Configuração do Pipeline](./media/sql-data-warehouse-continuous-integration-and-deployment/2-pipeline-setup.png "Configuração do Pipeline")

3. Edite o seu ficheiro YAML para utilizar a piscina adequada do seu agente. O seu ficheiro YAML deve ser parecido com isto:

      ![YAML](./media/sql-data-warehouse-continuous-integration-and-deployment/3-yaml-file.png "YAML")

Neste momento, você tem um ambiente simples onde qualquer check-in no seu ramo principal de controlo de origem deve automaticamente desencadear uma construção bem sucedida do Visual Studio do seu projeto de base de dados. Validar a automatização está a trabalhar de ponta a ponta, fazendo uma alteração no seu projeto de base de dados local e verificando essa alteração para o seu ramo principal.

## <a name="continuous-deployment-with-the-azure-synapse-analytics-or-database-deployment-task"></a>Implementação contínua com a tarefa de implantação Azure Synapse Analytics (ou Base de Dados)

1. Adicione uma nova tarefa utilizando a tarefa de implantação da [Base de Dados Azure SQL](/azure/devops/pipelines/targets/azure-sqldb) e preencha os campos necessários para ligar ao seu armazém de dados-alvo. Quando esta tarefa é executado, o DACPAC gerado a partir do processo de construção anterior é implantado para o armazém de dados-alvo. Também pode utilizar a [tarefa de implementação Azure Synapse Analytics](https://marketplace.visualstudio.com/items?itemName=ms-sql-dw.SQLDWDeployment).

      ![Tarefa de implantação](./media/sql-data-warehouse-continuous-integration-and-deployment/4-deployment-task.png "Tarefa de implantação")

2. Se estiver a utilizar um agente auto-hospedado, certifique-se de que define a variável do seu ambiente para utilizar o SqlPackage.exe correto para o Azure Synapse Analytics. O caminho deve ser parecido com isto:

      ![Variável ambiente](./media/sql-data-warehouse-continuous-integration-and-deployment/5-environment-variable-preview.png "Variável ambiente")

   C:\Ficheiros de programas (x86)\Microsoft Visual Studio\2019\Preview\Common7\IDE\Extensions\Microsoft\SQLDB\DAC\150  

   Corra e valide o seu oleoduto. Pode escoar alterações localmente e verificar alterações no seu controlo de origem que devem gerar uma construção e implantação automáticas.

## <a name="next-steps"></a>Passos seguintes

- Explore [a arquitetura dedicada da piscina SQL (anteriormente SQL DW)](massively-parallel-processing-mpp-architecture.md)
- Crie [rapidamente uma piscina SQL dedicada (anteriormente SQL DW)](create-data-warehouse-portal.md)
- [Carregar dados de exemplo](./load-data-from-azure-blob-storage-using-copy.md)
- Explore [vídeos](sql-data-warehouse-videos.md)