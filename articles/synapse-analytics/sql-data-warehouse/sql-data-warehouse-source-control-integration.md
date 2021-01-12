---
title: Integração do Controlo de Origem
description: Experiência de DevOps de classe empresarial para piscina de SQL dedicada com integração nativa de controlo de fontes usando Azure Repos (Git e GitHub).
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql-dw
ms.date: 08/23/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 56b417c65eae0df9b073cf72fe87ce0002fc2ba0
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98115842"
---
# <a name="source-control-integration-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Integração de Controlo de Fontes para piscina SQL dedicada em Azure Synapse Analytics

Este tutorial descreve como integrar o seu projeto de base de dados SQL Server Data Tools (SSDT) com controlo de origem.  A integração do controlo de fontes é o primeiro passo para construir o seu oleoduto de integração e implantação contínua com o recurso de piscina SQL dedicado no Azure Synapse Analytics.

## <a name="before-you-begin"></a>Before you begin

- Inscreva-se numa [organização da Azure DevOps](https://azure.microsoft.com/services/devops/)
- Passe pelo tutorial [De Criar e Ligar](create-data-warehouse-portal.md)
- [Instalar Visual Studio 2019](https://visualstudio.microsoft.com/vs/older-downloads/)

## <a name="set-up-and-connect-to-azure-devops"></a>Configurar e ligar ao Azure DevOps

1. Na sua Azure DevOps Organization, crie um projeto que irá acolher o seu projeto de base de dados SSDT através de um repositório Azure Repo.

   ![Criar Projeto](./media/sql-data-warehouse-source-control-integration/1-create-project-azure-devops.png "Criar Projeto")

2. Abra o Estúdio Visual e ligue-se à sua organização E projeto Azure DevOps a partir do primeiro passo, selecionando **Gerir a Ligação.**

   ![Gerir Ligações](./media/sql-data-warehouse-source-control-integration/2-manage-connections.png "Gerir Ligações")

3. Conecte-se ao seu projeto selecionando **Gerir Ligações** e, em seguida, **Ligar a um projeto.**
 
    ![Ligação1](./media/sql-data-warehouse-source-control-integration/3-connect-project.png "Ligar")


4. Encontre o projeto que criou no primeiro passo, selecione **Connect**.
 
    ![Ligação2](./media/sql-data-warehouse-source-control-integration/3.5-connect.png "Ligar")


3. Clone o seu repositório Azure DevOps do seu projeto para a sua máquina local.

   ![Repo clone](./media/sql-data-warehouse-source-control-integration/4-clone-repo.png "Repo clone")

Para obter mais informações sobre a ligação de projetos utilizando o Visual Studio, consulte o [Connect para projetos no Team Explorer.](/visualstudio/ide/connect-team-project?view=vs-2019) Para obter orientações sobre a clonagem de um repo usando o Visual Studio, reveja o Clone um artigo [de repo de saída do Git.](/azure/devops/repos/git/clone?tabs=visual-studio&view=azure-devops) 

## <a name="create-and-connect-your-project"></a>Crie e ligue o seu projeto

1. No Visual Studio, crie um novo SQL Server Database Project com um diretório e um repositório local de Git no seu **repositório clonado local.**

   ![Criar novo projeto](./media/sql-data-warehouse-source-control-integration/5-create-new-project.png "Criar novo projeto")  

2. Clique com o direito no seu projeto sql vazio e importe o seu armazém de dados para o projeto de base de dados.

   ![Projeto de Importação](./media/sql-data-warehouse-source-control-integration/6-import-new-project.png "Projeto de Importação")  

3. No Team Explorer em Visual Studio, comprometa as suas alterações ao seu repositório local de Git.

   ![Consolidação](./media/sql-data-warehouse-source-control-integration/6.5-commit-push-changes.png "Consolidação")  

4. Agora que tem as alterações cometidas localmente no repositório clonado, sincronize e empurre as suas alterações para o seu repositório Azure Repo no seu projeto Azure DevOps.

   ![Sync and Push - encenação](./media/sql-data-warehouse-source-control-integration/7-commit-push-changes.png "Sincronização e impulso - encenação")

   ![Sincronização e Push](./media/sql-data-warehouse-source-control-integration/7.5-commit-push-changes.png "Sincronizar e empurrar")  

## <a name="validation"></a>Validação

1. Verifique se as alterações foram empurradas para o seu Azure Repo atualizando uma coluna de tabela no seu projeto de base de dados a partir de Ferramentas de Dados do Servidor SQL do Estúdio Visual (SSDT).

   ![Validar coluna de atualização](./media/sql-data-warehouse-source-control-integration/8-validation-update-column.png "Validar coluna de atualização")

2. Comprometa-se e empurre a mudança do seu repositório local para o seu Azure Repo.

   ![Emitir alterações](./media/sql-data-warehouse-source-control-integration/9-push-column-change.png "Emitir alterações")

3. Verifique se a mudança foi empurrada no seu repositório Azure Repositório.

   ![Verificar](./media/sql-data-warehouse-source-control-integration/10-verify-column-change-pushed.png "Verificação das alterações")

4. (**Opcional)** Utilize o Schema Compare e atualize as alterações à sua piscina SQL dedicada ao seu alvo utilizando O SSDT para garantir que as definições de objeto no seu repositório de Azure Repositório e repositório local reflitam a sua piscina DE SQL dedicada.

## <a name="next-steps"></a>Passos seguintes

- [Desenvolvimento para piscina SQL dedicada](sql-data-warehouse-overview-develop.md)