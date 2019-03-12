---
title: Melhores práticas para o gerenciamento de assemblies de U-SQL num pipeline de CI/CD para o Azure Data Lake
description: Aprenda as melhores práticas para o gerenciamento de U-SQL C# assemblies num pipeline CI/CD com o Azure DevOps.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: ''
ms.assetid: ''
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 10/30/2018
ms.openlocfilehash: 27a873fac8bf2b53ee06780b8a348eaaa5c94e97
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57768030"
---
# <a name="best-practices-for-managing-u-sql-assemblies-in-a-cicd-pipeline"></a>Melhores práticas para o gerenciamento de assemblies de U-SQL num pipeline CI/CD

Neste artigo, irá aprender a gerenciar código-fonte assembly U-SQL com o projeto de banco de dados U-SQL recentemente introduzido. Também irá aprender a configurar um pipeline de implementação (CI/CD) para o registo de assemblagem e a integração contínua através do Azure DevOps.

## <a name="use-the-u-sql-database-project-to-manage-assembly-source-code"></a>Utilizar o projeto de banco de dados U-SQL para gerir o código de origem da assemblagem

[O projeto de banco de dados U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md) é um tipo de projeto no Visual Studio que ajuda os desenvolvedores a desenvolver, gerir e implementar seus bancos de dados U-SQL de forma rápida e fácil. Pode gerir todos os objetos de base de dados de U-SQL (exceto para credenciais) com o projeto de banco de dados U-SQL. 

Para gerir o C# utilizar o código-fonte assembly e scripts de DDL U-SQL de registo de assemblagem, o:

* Projeto de banco de dados de U-SQL para gerir scripts do U-SQL de registo de assemblagem.
* (Para do U-SQL aplicativo) para gerir de biblioteca de classes do C# origem de código e as dependências para operadores definidos pelo utilizador, funções e agregadores (UDOs, UDFs e UDAGs).
* Projeto de banco de dados de U-SQL para o projeto de biblioteca de classes de referência. 

Um projeto de banco de dados U-SQL, pode referenciar um projeto de biblioteca de classe (para aplicação U-SQL). Pode criar assemblies registados na base de dados U-SQL através da utilização referenciada C# origem de código deste projeto de biblioteca de classes (para aplicação U-SQL).

Siga estes passos para criar projetos e adicione referências.
1. Criar um projeto de biblioteca de classe (para aplicação U-SQL), selecionando **arquivo** > **New** > **projeto**. O projeto está sob o **do Azure Data Lake > U-SQL** nó.

   ![Data Lake Tools para Visual Studio-- criar C# projeto de biblioteca de classe](./media/data-lake-analytics-cicd-manage-assemblies/create-c-sharp-class-library-project.png)
1. Adicionar a sua definidas pelo utilizador C# código no projeto da biblioteca de classe (para aplicação U-SQL).

1. Criar um projeto U-SQL, selecionando **arquivo** > **New** > **projeto**. O projeto está sob o **do Azure Data Lake** > **U-SQL** nó.

   ![Projeto de banco de dados do Data Lake Tools para Visual Studio--criar U-SQL](media/data-lake-analytics-cicd-manage-assemblies/create-u-sql-database-project.png)
1. Adicionar uma referência para o C# projeto de biblioteca de classes para o projeto de banco de dados U-SQL.

    ![Referência do projeto de banco de dados do Data Lake Tools para Visual Studio – adicionar U-SQL](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference.png) 

    ![Referência do projeto de banco de dados do Data Lake Tools para Visual Studio – adicionar U-SQL](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference-wizard.png)

5. Criar um script de assembly no projeto de banco de dados U-SQL, clicando com o botão direito no projeto e selecionando **Add New Item**.

   ![Data Lake Tools para Visual Studio, adicione o script de assemblagem](media/data-lake-analytics-cicd-manage-assemblies/add-assembly-script.png)

1. Abra o script do assembly na vista de estrutura de assembly. Selecione o assembly referenciado a partir da **criar o assembly de referência** menu pendente.

    ![Ferramentas do Data Lake para Visual Studio-- criar o assembly de referência](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-create-assembly-from-reference.png)

7. Adicione **dependências geridos** e **ficheiros adicionais**, se existir alguma. Ao adicionar ficheiros adicionais, a ferramenta usa o caminho relativo para se certificar de que ele possa encontrar os assemblies no seu computador local e na máquina de compilação mais tarde.

**\@_DeployTempDirectory** no editor de janela na parte inferior é uma variável predefinida que aponta a ferramenta para a pasta de saída da compilação. Sob a pasta de saída da compilação, a cada assembly possui uma subpasta com o nome com o nome do assembly. Todas as DLLs e ficheiros adicionais são nessa subpasta.

## <a name="build-a-u-sql-database-project"></a>Criar um projeto de banco de dados U-SQL

O resultado de compilação para um projeto de banco de dados U-SQL é um pacote de implementação do U-SQL da base de dados. Ele é denominado com o sufixo `.usqldbpack`. O `.usqldbpack` pacote é um arquivo. zip que inclui todas as instruções DDL num único script de U-SQL na pasta de DDL. Todos os arquivos. dll criados e outros ficheiros para os assemblies estão na pasta Temp.

## <a name="deploy-a-u-sql-database"></a>Implementar uma base de dados U-SQL

O `.usqldbpack` pacote pode ser implementado para uma conta local ou uma conta do Azure Data Lake Analytics. Utilize o Visual Studio ou a implementação do SDK. 

### <a name="deploy-a-u-sql-database-in-visual-studio"></a>Implementar uma base de dados U-SQL no Visual Studio

Pode implementar uma base de dados U-SQL com um projeto de banco de dados U-SQL ou um `.usqldbpack` pacote no Visual Studio.

#### <a name="deploy-by-using-a-u-sql-database-project"></a>Implementar com um projeto de banco de dados U-SQL

1.  O projeto de banco de dados U-SQL com o botão direito e, em seguida, selecione **Deploy**.
2.  Na **implementar a base de dados de U-SQL** assistente, selecione a **ADLA conta** para a qual pretende implementar a base de dados. Contas locais e contas ADLA são suportadas.
3.  **Base de dados de origem** é preenchida automaticamente. Ele aponta para o pacote de .usqldbpack na pasta de saída de compilação do projeto.
4.  Introduza um nome na **nome da base de dados** para criar uma base de dados. Se já existir uma base de dados com o mesmo nome no destino conta do Azure Data Lake Analytics, todos os objetos que estão definidos no projeto de banco de dados são criados sem recriar a base de dados.
5.  Para implementar a base de dados U-SQL, selecione **submeter**. Todos os recursos, tais como assemblies e arquivos adicionais, são carregados. É submetida uma tarefa de U-SQL que inclui todas as instruções DDL.

    ![Projeto de banco de dados do Data Lake Tools para Visual Studio – implemente U-SQL](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project.png)

    ![Assistente de projeto de banco de dados do Data Lake Tools para Visual Studio – implemente U-SQL](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project-wizard.png)

### <a name="deploy-a-u-sql-database-in-azure-devops"></a>Implementar uma base de dados U-SQL do Azure DevOps

`PackageDeploymentTool.exe` Fornece as interfaces de linha de comandos que ajudam a implementar bases de dados U-SQL e programação. O SDK está incluído nos [pacote Nuget do SDK U-SQL](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), localizado em `build/runtime/PackageDeploymentTool.exe`.

Em DevOps do Azure, pode utilizar uma tarefa da linha de comandos e este SDK para configurar um pipeline de automatização para a atualização da base de dados U-SQL. [Saiba mais sobre o SDK e como configurar um pipeline CI/CD para a implementação de base de dados U-SQL](data-lake-analytics-cicd-overview.md#deploy-u-sql-database-through-azure-pipelines).

## <a name="next-steps"></a>Passos Seguintes

* [Configurar um pipeline CI/CD para o Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
* [Testar o seu código do Azure Data Lake Analytics](data-lake-analytics-cicd-test.md)
* [Execute o script de U-SQL no seu computador local](data-lake-analytics-data-lake-tools-local-run.md)
