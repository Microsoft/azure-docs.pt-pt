---
title: Integração do Controlo de Origem
description: Base de Dados de classe empresarial DevOps experiência para SQL Data Warehouse com integração de controlo de fonte nativa usando Azure Repos (Git e GitHub).
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: overview
ms.subservice: integration
ms.date: 08/23/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: fc1fdddc7a157828fcc4770c4e8237af9a45fdc3
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720032"
---
# <a name="source-control-integration-for-azure-sql-data-warehouse"></a>Integração de Controlo de Fontes para Armazém de Dados Azure SQL

Este tutorial descreve como integrar o seu projeto de base de dados de dados sQL Server Data (SSDT) com controlo de fonte.  A integração do controlo de fontes é o primeiro passo para a construção do seu oleoduto de integração e implantação contínua com o SQL Data Warehouse. 

## <a name="before-you-begin"></a>Antes de começar

- Inscreva-se numa [organização Azure DevOps](https://azure.microsoft.com/services/devops/)
- Passe pelo tutorial [Criar e Ligar](https://docs.microsoft.com/azure/sql-data-warehouse/create-data-warehouse-portal)
-  [Instalar o Estúdio Visual 2019](https://visualstudio.microsoft.com/vs/older-downloads/) 

## <a name="set-up-and-connect-to-azure-devops"></a>Configurar e ligar a Azure DevOps

1. Na sua Organização Azure DevOps, crie um projeto que irá acolher o seu projeto de base de dados SSDT através de um repositório Azure Repo

   ![Criar Projeto](media/sql-data-warehouse-source-control-integration/1-create-project-azure-devops.png "Criar Projeto")

2. Open Visual Studio e ligue-se à sua organização Azure DevOps e projeto a partir do passo 1, selecionando "Gerir ligações"

   ![Gerir Ligações](media/sql-data-warehouse-source-control-integration/2-manage-connections.png "Gerir Ligações")

   ![Ligar](media/sql-data-warehouse-source-control-integration/3-connect.png "Ligar")

3. Clone o seu repositório Azure Repo do seu projeto para a sua máquina local

   ![Repo clone](media/sql-data-warehouse-source-control-integration/4-clone-repo.png "Repo clone")

## <a name="create-and-connect-your-project"></a>Crie e conecte o seu projeto

1. No Estúdio Visual, crie um novo Projeto de Base de Dados de Servidores SQL com um repertório de diretório e repositório local git no seu **repositório clonado local**

   ![Criar novo projeto](media/sql-data-warehouse-source-control-integration/5-create-new-project.png "Criar novo projeto")  

2. Clique à direita no seu projeto de sqlproject vazio e importe o seu armazém de dados para o projeto de base de dados

   ![Projeto de Importação](media/sql-data-warehouse-source-control-integration/6-import-new-project.png "Projeto de Importação")  

3. No explorador de equipas no Estúdio Visual, comprometa todas as suas alterações ao seu repositório git local 

   ![Comprometer](media/sql-data-warehouse-source-control-integration/6.5-commit-push-changes.png "Consolidação")  

4. Agora que tem as mudanças comprometidas localmente no repositório clonado, sincroniza e empurra as suas alterações para o seu repositório Azure Repo no seu projeto Azure DevOps.

   ![Sync and Push - encenação](media/sql-data-warehouse-source-control-integration/7-commit-push-changes.png "Sincronizar e empurrar - encenação")

   ![Sincronizar e empurrar](media/sql-data-warehouse-source-control-integration/7.5-commit-push-changes.png "Sincronizar e empurrar")  

## <a name="validation"></a>Validação

1. Verifique se as alterações foram empurradas para o seu Azure Repo atualizando uma coluna de tabelas no seu projeto de base de dados a partir de Ferramentas de Dados do Servidor Visual Studio SQL (SSDT)

   ![Validar coluna de atualização](media/sql-data-warehouse-source-control-integration/8-validation-update-column.png "Validar coluna de atualização")

2. Comprometa-se e empurre a mudança do seu repositório local para o seu Azure Repo

   ![Alterações de impulso](media/sql-data-warehouse-source-control-integration/9-push-column-change.png "Emitir alterações")

3. Verifique se a mudança foi empurrada no seu repositório Azure Repo

   ![Verificar](media/sql-data-warehouse-source-control-integration/10-verify-column-change-pushed.png "Verificar alterações")

4. **(Opcional)** Utilize o Schema Compare e atualize as alterações no seu armazém de dados-alvo utilizando o SSDT para garantir que as definições de objeto no seu repositório Azure Repo e repositório local refletem o seu armazém de dados

## <a name="next-steps"></a>Passos seguintes

- [Desenvolvimento para Armazém de Dados Azure SQL](sql-data-warehouse-overview-develop.md)
