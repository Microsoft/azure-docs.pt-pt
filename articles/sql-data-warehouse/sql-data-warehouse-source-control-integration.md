---
title: Integração de controle do código-fonte | Microsoft Docs
description: Experiência de DevOps de banco de dados de classe empresarial para SQL Data Warehouse com integração de controle do código-fonte nativo usando Azure Repos (git e GitHub).
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: overview
ms.subservice: integration
ms.date: 08/23/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: bb43127deea26f427dac58b1717368b0c2d8dbd5
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035504"
---
# <a name="source-control-integration-for-azure-sql-data-warehouse"></a>Integração de controle do código-fonte para o Azure SQL Data Warehouse

Este tutorial descreve como integrar seu projeto de banco de dados SSDT (SQL Server Data Tools) com controle do código-fonte.  A integração de controle do código-fonte é a primeira etapa na criação de seu pipeline de implantação e integração contínua com o SQL Data Warehouse. 

## <a name="before-you-begin"></a>Antes de começar

- Inscrever-se em uma [organização DevOps do Azure](https://azure.microsoft.com/services/devops/)
- Consulte o tutorial [criar e conectar](https://docs.microsoft.com/azure/sql-data-warehouse/create-data-warehouse-portal)
-  [Instalar o Visual Studio 2019](https://visualstudio.microsoft.com/vs/older-downloads/) 

## <a name="set-up-and-connect-to-azure-devops"></a>Configurar e conectar-se ao Azure DevOps

1. Em sua organização do Azure DevOps, crie um projeto que hospedará seu projeto de banco de dados do SSDT por meio de um repositório do Azure Repository

   ![Criar projeto](media/sql-data-warehouse-source-control-integration/1-create-project-azure-devops.png "Criar projeto")

2. Abra o Visual Studio e conecte-se à sua organização DevOps do Azure e projeto da etapa 1 selecionando "gerenciar conexões"

   ![Gerenciar conexões](media/sql-data-warehouse-source-control-integration/2-manage-connections.png "Gerenciar conexões")

   ![Conectar](media/sql-data-warehouse-source-control-integration/3-connect.png "Conectar")

3. Clone seu repositório do Azure repositório do seu projeto para seu computador local

   ![Clonar repositório](media/sql-data-warehouse-source-control-integration/4-clone-repo.png "Clonar repositório")

## <a name="create-and-connect-your-project"></a>Criar e conectar seu projeto

1. No Visual Studio, crie um novo projeto de banco de dados SQL Server com um diretório e repositório git local em seu **repositório clonado local**

   ![Criar novo projeto](media/sql-data-warehouse-source-control-integration/5-create-new-project.png "Criar novo projeto")  

2. Clique com o botão direito do mouse em seu sqlproject vazio e importe seu data warehouse para o projeto de banco de dados

   ![Importar projeto](media/sql-data-warehouse-source-control-integration/6-import-new-project.png "Importar projeto")  

3. No Team Explorer no Visual Studio, confirme suas alterações em seu repositório git local 

   ![Confirmar](media/sql-data-warehouse-source-control-integration/6.5-commit-push-changes.png "Confirmar")  

4. Agora que você tem as alterações confirmadas localmente no repositório clonado, sincronize e envie por push suas alterações para o repositório do Azure repositório no seu projeto DevOps do Azure.

   ![Sincronizar e enviar por push-preparo](media/sql-data-warehouse-source-control-integration/7-commit-push-changes.png "Sincronizar e enviar por push-preparo")

   ![Sincronizar e enviar por push](media/sql-data-warehouse-source-control-integration/7.5-commit-push-changes.png "Sincronizar e enviar por push")  

## <a name="validation"></a>Validação

1. Verifique se as alterações foram enviadas por push para o repositório do Azure atualizando uma coluna de tabela no projeto de banco de dados do Visual Studio SQL Server Data Tools (SSDT)

   ![Validar coluna de atualização](media/sql-data-warehouse-source-control-integration/8-validation-update-column.png "Validar coluna de atualização")

2. Confirme e envie por push a alteração do repositório local para o repositório do Azure

   ![Enviar alterações por push](media/sql-data-warehouse-source-control-integration/9-push-column-change.png "Enviar alterações por push")

3. Verifique se a alteração foi enviada por push em seu repositório do Azure Repository

   ![Verificar](media/sql-data-warehouse-source-control-integration/10-verify-column-change-pushed.png "Verificar alterações")

4. (**Opcional**) Use a comparação de esquemas e atualize as alterações para seu destino data warehouse usando o SSDT para garantir que as definições de objeto no repositório do Azure Repository e no repositório local reflitam suas data warehouse

## <a name="next-steps"></a>Passos seguintes

- [Desenvolvendo para o Azure SQL Data Warehouse](/azure/sql-data-warehouse/sql-data-warehouse-overview-develop.md)

<!--Image references-->

<!--Article references-->


<!--MSDN references-->

<!--Other Web references-->

