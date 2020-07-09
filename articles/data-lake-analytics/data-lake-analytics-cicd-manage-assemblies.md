---
title: Gerir conjuntos U-SQL num oleoduto CI/CD - Azure Data Lake
description: Aprenda as melhores práticas para gerir conjuntos U-SQL C# num pipeline CI/CD com Azure DevOps.
author: yanancai
ms.author: yanacai
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 10/30/2018
ms.openlocfilehash: 9daf2cc6ed176e46399f2cdb2f424a622fdeb812
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86115396"
---
# <a name="best-practices-for-managing-u-sql-assemblies-in-a-cicd-pipeline"></a>Melhores práticas para a gestão de conjuntos U-SQL num gasoduto CI/CD

Neste artigo, aprende-se a gerir o código fonte de montagem U-SQL com o recém-introduzido projeto de base de dados U-SQL. Também aprende a configurar um pipeline de integração e implantação contínua (CI/CD) para o registo de montagem utilizando Azure DevOps.

## <a name="use-the-u-sql-database-project-to-manage-assembly-source-code"></a>Utilize o projeto de base de dados U-SQL para gerir o código fonte de montagem

[O projeto de base de dados U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md) é um tipo de projeto no Visual Studio que ajuda os desenvolvedores a desenvolver, gerir e implementar as suas bases de dados U-SQL de forma rápida e fácil. Pode gerir todos os objetos de base de dados U-SQL (exceto credenciais) com o projeto de base de dados U-SQL. 

Para gerir o código fonte de montagem C# e o registo de montagem DDL U-SQL scripts, utilize o seguinte:

* Projeto de base de dados U-SQL para gerir scripts U-SQL de registo de montagem.
* Biblioteca de Classes (Para aplicação U-SQL) para gerir o código-fonte C# e dependências para operadores, funções e agregadores definidos pelo utilizador (UDOs, UDFs e UDAGs).
* Projeto de base de dados U-SQL para fazer referência ao projeto da Biblioteca de Classes. 

Um projeto de base de dados U-SQL pode fazer referência a um projeto de Biblioteca de Classes (Aplicação U-SQL). Pode criar conjuntos registados na base de dados U-SQL utilizando código fonte C# referenciado a partir deste projeto de Biblioteca de Classe (Aplicação U-SQL).

Siga estes passos para criar projetos e adicionar referências.
1. Criar um projeto de Biblioteca de Classes (Para Aplicação U-SQL) selecionando **o Arquivo**  >  **Novo**  >  **Project**. O projeto está sob o **nó Azure Data Lake > U-SQL.**

   ![Ferramentas do Lago de Dados para Estúdio Visual--Criar projeto de biblioteca de classes C#](./media/data-lake-analytics-cicd-manage-assemblies/create-c-sharp-class-library-project.png)
1. Adicione o seu código C# definido pelo utilizador no projeto Class Library (Para aplicação U-SQL).

1. Crie um projeto U-SQL selecionando **File**  >  **New**  >  **Project**. O projeto está no nó **Azure Data Lake**  >  **U-SQL.**

   ![Ferramentas do Lago de Dados para Estúdio Visual--Criar projeto de base de dados U-SQL](media/data-lake-analytics-cicd-manage-assemblies/create-u-sql-database-project.png)
1. Adicione uma referência ao projeto da biblioteca da classe C# para o projeto de base de dados U-SQL.

    ![Ferramentas do Lago de Dados para Estúdio Visual--Adicionar referência do projeto de base de dados U-SQL](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference.png) 

    ![Ferramentas do Lago de Dados para Estúdio Visual--Adicionar referência do projeto de base de dados U-SQL](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference-wizard.png)

5. Crie um script de montagem no projeto de base de dados U-SQL clicando no projeto e selecionando **Add New Item**.

   ![Ferramentas do Lago de Dados para Estúdio Visual--Adicionar script de montagem](media/data-lake-analytics-cicd-manage-assemblies/add-assembly-script.png)

1. Abra o roteiro de montagem na vista de design de montagem. Selecione o conjunto referenciado a partir do conjunto Criar do menu suspenso de **referência.**

    ![Ferramentas do Lago de Dados para Estúdio Visual -- criar montagem a partir de referência](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-create-assembly-from-reference.png)

7. Adicione **Dependências Geridas** e **Ficheiros Adicionais,** se houver. Quando adiciona ficheiros adicionais, a ferramenta utiliza o caminho relativo para se certificar de que pode encontrar os conjuntos na sua máquina local e na máquina de construção mais tarde.

** \@ _DeployTempDirectory** na janela do editor na parte inferior é uma variável predefinida que aponta a ferramenta para a pasta de saída de construção. Sob a pasta de saída de construção, cada conjunto tem uma sub-dobragem nomeada com o nome de montagem. Todos os DLLs e ficheiros adicionais estão na subclasse.

## <a name="build-a-u-sql-database-project"></a>Construir um projeto de base de dados U-SQL

A saída de construção para um projeto de base de dados U-SQL é um pacote de implementação de base de dados U-SQL. Tem o nome do `.usqldbpack` sufixo. O `.usqldbpack` pacote é um ficheiro .zip que inclui todas as declarações de DDL num único script U-SQL na pasta DDL. Todos os ficheiros .dll construídos e ficheiros adicionais para conjuntos estão na pasta Temp.

## <a name="deploy-a-u-sql-database"></a>Implementar uma base de dados U-SQL

O `.usqldbpack` pacote pode ser implantado numa conta local ou numa conta Azure Data Lake Analytics. Utilize o Visual Studio ou o SDK de implementação. 

### <a name="deploy-a-u-sql-database-in-visual-studio"></a>Implementar uma base de dados U-SQL no Visual Studio

Pode implementar uma base de dados U-SQL utilizando um projeto de base de dados U-SQL ou um `.usqldbpack` pacote no Visual Studio.

#### <a name="deploy-by-using-a-u-sql-database-project"></a>Implementar utilizando um projeto de base de dados U-SQL

1.  Clique com o botão direito no projeto de base de dados U-SQL e, em seguida, selecione **Implementar**.
2.  No assistente **de base de dados U-SQL de implementação,** selecione a **Conta ADLA** para a qual pretende implantar a base de dados. As contas locais e as contas ADLA são suportadas.
3.  **A Base de Dados Fonte** é preenchida automaticamente. Aponta para o pacote .usqldbpack na pasta de saída de construção do projeto.
4.  Introduza um nome no **Nome da Base de Dados** para criar uma base de dados. Se já existe uma base de dados com o mesmo nome na conta target Azure Data Lake Analytics, todos os objetos definidos no projeto de base de dados são criados sem recriar a base de dados.
5.  Para implementar a base de dados U-SQL, **selecione Enviar por isso.** Todos os recursos, tais como conjuntos e ficheiros adicionais, são carregados. É submetido um trabalho U-SQL que inclui todas as declarações do DDL.

    ![Ferramentas do Lago de Dados para Estúdio Visual--Implementar projeto de base de dados U-SQL](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project.png)

    ![Ferramentas do Lago de Dados para Estúdio Visual--Implementar assistente de projeto de base de dados U-SQL](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project-wizard.png)

### <a name="deploy-a-u-sql-database-in-azure-devops"></a>Implementar uma base de dados U-SQL em Azure DevOps

`PackageDeploymentTool.exe`fornece a programação e interfaces de linha de comando que ajudam a implementar bases de dados U-SQL. O SDK está incluído no [pacote U-SQL SDK Nuget,](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/)localizado em `build/runtime/PackageDeploymentTool.exe` .

Em Azure DevOps, pode utilizar uma tarefa de linha de comando e este SDK para configurar um pipeline de automação para a atualização da base de dados U-SQL. [Saiba mais sobre o SDK e como configurar um pipeline CI/CD para a implementação da base de dados U-SQL](data-lake-analytics-cicd-overview.md#deploy-u-sql-database-through-azure-pipelines).

## <a name="next-steps"></a>Próximos passos

* [Configurar um pipeline CI/CD para a Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
* [Teste o seu código Azure Data Lake Analytics](data-lake-analytics-cicd-test.md)
* [Executar script U-SQL na sua máquina local](data-lake-analytics-data-lake-tools-local-run.md)
