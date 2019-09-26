---
title: Gerenciar assemblies U-SQL em um pipeline de CI/CD-Azure Data Lake
description: Conheça as práticas recomendadas para gerenciar assemblies U C# -SQL em um pipeline de CI/CD com o Azure DevOps.
author: yanancai
ms.author: yanacai
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: e6de10ed712688e4ee9dccc22176e81ad5e574ca
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71315856"
---
# <a name="best-practices-for-managing-u-sql-assemblies-in-a-cicd-pipeline"></a>Práticas recomendadas para gerenciar assemblies U-SQL em um pipeline de CI/CD

Neste artigo, você aprenderá a gerenciar o código-fonte do assembly U-SQL com o projeto de banco de dados U-SQL recentemente introduzido. Você também aprende como configurar um pipeline de CI/CD (integração e implantação contínuas) para o registro de assembly usando o Azure DevOps.

## <a name="use-the-u-sql-database-project-to-manage-assembly-source-code"></a>Usar o projeto de banco de dados U-SQL para gerenciar o código-fonte do assembly

[O projeto de banco de dados U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md) é um tipo de projeto no Visual Studio que ajuda os desenvolvedores a desenvolver, gerenciar e implantar seus bancos de dados U-SQL de maneira rápida e fácil. Você pode gerenciar todos os objetos de banco de dados U-SQL (exceto as credenciais) com o projeto de banco de dados U-SQL. 

Para gerenciar o C# código-fonte do assembly e os scripts U-SQL DDL, use:

* Projeto de banco de dados u-SQL para gerenciar scripts U-SQL de registro de assembly.
* Biblioteca de classes (para aplicativo U-SQL) para gerenciar C# o código-fonte e as dependências de operadores, funções e agregadores definidos pelo usuário (UDOs, UDFs e UDAGs).
* Projeto de banco de dados U-SQL para referenciar o projeto de biblioteca de classes. 

Um projeto de banco de dados U-SQL pode fazer referência a um projeto de biblioteca de classes (para aplicativo U-SQL). Você pode criar assemblies registrados no banco de dados U-SQL usando o C# código-fonte referenciado deste projeto da biblioteca de classes (para aplicativo U-SQL).

Siga estas etapas para criar projetos e adicionar referências.
1. Crie um projeto de biblioteca de classes (para aplicativo U-SQL) selecionando **arquivo** > **novo** > **projeto**. O projeto está sob o **Azure Data Lake > nó U-SQL** .

   ![Ferramentas de Data Lake para Visual Studio – criar C# projeto de biblioteca de classes](./media/data-lake-analytics-cicd-manage-assemblies/create-c-sharp-class-library-project.png)
1. Adicione seu código definido pelo C# usuário no projeto biblioteca de classes (para aplicativo U-SQL).

1. Crie um projeto do U-SQL selecionando **arquivo** > **novo** > **projeto**. O projeto está no nó **Azure data Lake** > **U-SQL** .

   ![Ferramentas de Data Lake para Visual Studio-criar projeto de banco de dados U-SQL](media/data-lake-analytics-cicd-manage-assemblies/create-u-sql-database-project.png)
1. Adicione uma referência ao projeto C# de biblioteca de classes para o projeto de banco de dados U-SQL.

    ![Ferramentas de Data Lake para Visual Studio--Adicionar referência de projeto de banco de dados U-SQL](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference.png) 

    ![Ferramentas de Data Lake para Visual Studio--Adicionar referência de projeto de banco de dados U-SQL](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference-wizard.png)

5. Crie um script de assembly no projeto de banco de dados U-SQL clicando com o botão direito do mouse no projeto e selecionando **Adicionar novo item**.

   ![Ferramentas de Data Lake para Visual Studio--Adicionar script de assembly](media/data-lake-analytics-cicd-manage-assemblies/add-assembly-script.png)

1. Abra o script de assembly no modo de exibição de design do assembly. Selecione o assembly referenciado no menu suspenso **criar assembly de referência** .

    ![Ferramentas de Data Lake para Visual Studio – criar assembly de referência](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-create-assembly-from-reference.png)

7. Adicione **dependências gerenciadas** e **arquivos adicionais**, se houver. Quando você adiciona arquivos adicionais, a ferramenta usa o caminho relativo para garantir que ele possa encontrar os assemblies no computador local e no computador de compilação mais tarde.

_DeployTempDirectory na janela do editor na parte inferior é uma variável predefinida que aponta a ferramenta para a pasta de saída da compilação.  **\@** Na pasta de saída da compilação, cada assembly tem uma subpasta chamada com o nome do assembly. Todas as DLLs e arquivos adicionais estão nessa subpasta.

## <a name="build-a-u-sql-database-project"></a>Criar um projeto de banco de dados U-SQL

A saída de Build para um projeto de banco de dados U-SQL é um pacote de implantação de banco de dados U-SQL. Ele é nomeado com o sufixo `.usqldbpack`. O `.usqldbpack` pacote é um arquivo. zip que inclui todas as instruções DDL em um único script U-SQL na pasta DDL. Todos os arquivos. dll e arquivos adicionais criados para assemblies estão na pasta Temp.

## <a name="deploy-a-u-sql-database"></a>Implantar um banco de dados U-SQL

O `.usqldbpack` pacote pode ser implantado em uma conta local ou em uma conta de Azure data Lake Analytics. Use o Visual Studio ou o SDK de implantação. 

### <a name="deploy-a-u-sql-database-in-visual-studio"></a>Implantar um banco de dados U-SQL no Visual Studio

Você pode implantar um banco de dados u-SQL usando um projeto de banco de dados u `.usqldbpack` -SQL ou um pacote no Visual Studio.

#### <a name="deploy-by-using-a-u-sql-database-project"></a>Implantar usando um projeto de banco de dados U-SQL

1.  Clique com o botão direito do mouse no projeto do banco de dados U-SQL e selecione **implantar**.
2.  No Assistente para **implantar banco de dados U-SQL** , selecione a **conta adla** para a qual você deseja implantar o banco de dados. As contas locais e as contas ADLA têm suporte.
3.  A **origem do banco de dados** é preenchida automaticamente. Ele aponta para o pacote. usqldbpack na pasta de saída da compilação do projeto.
4.  Insira um nome no **nome do banco de dados** para criar um banco de dados. Se já existir um banco de dados com esse mesmo nome na conta de Azure Data Lake Analytics de destino, todos os objetos definidos no projeto de banco de dados serão criados sem recriar o banco de dados.
5.  Para implantar o banco de dados U-SQL, selecione **Enviar**. Todos os recursos, como assemblies e arquivos adicionais, são carregados. Um trabalho do U-SQL que inclui todas as instruções DDL é enviado.

    ![Ferramentas de Data Lake para Visual Studio – implantar projeto de banco de dados U-SQL](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project.png)

    ![Ferramentas de Data Lake para Visual Studio – implantar o assistente de projeto de banco de dados U-SQL](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project-wizard.png)

### <a name="deploy-a-u-sql-database-in-azure-devops"></a>Implantar um banco de dados U-SQL no Azure DevOps

`PackageDeploymentTool.exe`fornece a programação e as interfaces de linha de comando que ajudam a implantar bancos de dados U-SQL. O SDK está incluído no [pacote NuGet do SDK do U-SQL](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), localizado `build/runtime/PackageDeploymentTool.exe`em.

No Azure DevOps, você pode usar uma tarefa de linha de comando e esse SDK para configurar um pipeline de automação para a atualização do banco de dados U-SQL. [Saiba mais sobre o SDK e como configurar um pipeline de CI/CD para a implantação do banco de dados U-SQL](data-lake-analytics-cicd-overview.md#deploy-u-sql-database-through-azure-pipelines).

## <a name="next-steps"></a>Passos seguintes

* [Configurar um pipeline de CI/CD para Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
* [Testar seu código de Azure Data Lake Analytics](data-lake-analytics-cicd-test.md)
* [Executar o script U-SQL em seu computador local](data-lake-analytics-data-lake-tools-local-run.md)
