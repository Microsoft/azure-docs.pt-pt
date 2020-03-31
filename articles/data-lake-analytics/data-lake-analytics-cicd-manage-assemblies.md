---
title: Gerir conjuntos U-SQL num oleoduto CI/CD - Lago de Dados Azure
description: Aprenda as melhores práticas para gerir os conjuntos U-SQL C# num pipeline CI/CD com Azure DevOps.
author: yanancai
ms.author: yanacai
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: e6de10ed712688e4ee9dccc22176e81ad5e574ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71315856"
---
# <a name="best-practices-for-managing-u-sql-assemblies-in-a-cicd-pipeline"></a>Boas práticas para gerir conjuntos U-SQL num oleoduto CI/CD

Neste artigo, aprende-se a gerir o código fonte de origem de montagem U-SQL com o projeto de base de dados U-SQL recentemente introduzido. Também aprende a configurar um oleoduto de integração e implantação contínua (CI/CD) para o registo de montagem utilizando o Azure DevOps.

## <a name="use-the-u-sql-database-project-to-manage-assembly-source-code"></a>Use o projeto de base de dados U-SQL para gerir o código fonte de montagem

O projeto de [base de dados U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md) é um tipo de projeto no Estúdio Visual que ajuda os desenvolvedores a desenvolver, gerir e implementar as suas bases de dados U-SQL de forma rápida e fácil. Pode gerir todos os objetos de base de dados U-SQL (exceto credenciais) com o projeto de base de dados U-SQL. 

Para gerir o código fonte de montagem C# e o registo de montagem DDL U-SQL scripts, utilize o:

* Projeto de base de dados U-SQL para gerir scripts U-SQL de registo de montagem.
* Biblioteca de classes (Para aplicação U-SQL) para gerir o código fonte C# e dependências para operadores, funções e agregadores definidos pelo utilizador (UDOs, UDFs e UDAGs).
* Projeto de base de dados U-SQL para referenciar o projeto da Biblioteca de Classes. 

Um projeto de base de dados U-SQL pode fazer referência a um projeto de Biblioteca de Classes (Para Aplicação U-SQL). Pode criar conjuntos registados na base de dados U-SQL utilizando código fonte C# referenciado deste projeto de Biblioteca de Classes (Para Aplicação U-SQL).

Siga estes passos para criar projetos e adicionar referências.
1. Crie um projeto de Biblioteca de Classes (Para Aplicação U-SQL) selecionando O**Novo** > **Projeto**de **Arquivo** > . O projeto está sob o Lago de Dados Azure > nó **U-SQL.**

   ![Ferramentas data lake para estúdio visual -- Criar projeto de biblioteca de classe C#](./media/data-lake-analytics-cicd-manage-assemblies/create-c-sharp-class-library-project.png)
1. Adicione o seu código C# definido pelo utilizador no projeto Biblioteca de Classes (Para Aplicação U-SQL).

1. Crie um projeto U-SQL selecionando **File** > **New** > **Project**. O projeto está sob o nó > **U-SQL** do Lago de **Dados Azure.**

   ![Ferramentas de data lake para estúdio visual -- Criar projeto de base de dados U-SQL](media/data-lake-analytics-cicd-manage-assemblies/create-u-sql-database-project.png)
1. Adicione uma referência ao projeto da biblioteca da classe C# para o projeto de base de dados U-SQL.

    ![Ferramentas de data lake para estúdio visual-- adicionar referência de projeto de base de dados U-SQL](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference.png) 

    ![Ferramentas de data lake para estúdio visual-- adicionar referência de projeto de base de dados U-SQL](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference-wizard.png)

5. Crie um script de montagem no projeto de base de dados U-SQL clicando no projeto e selecionando **Adicionar Novo Item**.

   ![Ferramentas de Data Lake para Estúdio Visual--Adicionar script de montagem](media/data-lake-analytics-cicd-manage-assemblies/add-assembly-script.png)

1. Abra o script de montagem na vista de design de montagem. Selecione o conjunto referenciado do conjunto Criar a partir do menu de entrega de **referência.**

    ![Ferramentas de Data Lake para Estúdio Visual -- criar montagem a partir de referência](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-create-assembly-from-reference.png)

7. Adicione **Dependências Geridas** e **Ficheiros Adicionais,** se houver algum. Quando adiciona ficheiros adicionais, a ferramenta utiliza o caminho relativo para se certificar de que pode encontrar os conjuntos na sua máquina local e na máquina de construção mais tarde.

_DeployTempDirectory na janela do editor na parte inferior é uma variável predefinida que aponta a ferramenta para a pasta de saída de construção. ** \@** Sob a pasta de saída de construção, cada conjunto tem uma subpasta com o nome de montagem. Todos os DLLs e ficheiros adicionais estão nessa subpasta.

## <a name="build-a-u-sql-database-project"></a>Construa um projeto de base de dados U-SQL

A produção de construção de um projeto de base de dados U-SQL é um pacote de implementação de bases de dados U-SQL. É nomeado com o sufixo. `.usqldbpack` O `.usqldbpack` pacote é um ficheiro .zip que inclui todas as declarações de DDL num único script U-SQL na pasta DDL. Todos os ficheiros incorporados .dll e ficheiros adicionais para conjuntos estão na pasta Temp.

## <a name="deploy-a-u-sql-database"></a>Implementar uma base de dados U-SQL

O `.usqldbpack` pacote pode ser implantado para uma conta local ou uma conta Azure Data Lake Analytics. Utilize o Estúdio Visual ou o SDK de implantação. 

### <a name="deploy-a-u-sql-database-in-visual-studio"></a>Implementar uma base de dados U-SQL no Estúdio Visual

Pode implementar uma base de dados U-SQL utilizando um `.usqldbpack` projeto de base de dados U-SQL ou um pacote no Estúdio Visual.

#### <a name="deploy-by-using-a-u-sql-database-project"></a>Implementar utilizando um projeto de base de dados U-SQL

1.  Clique no projeto de base de dados U-SQL e, em seguida, selecione **Deploy**.
2.  No assistente de base de **dados U-SQL de implantação,** selecione a **Conta ADLA** para a qual pretende implementar a base de dados. Tanto as contas locais como as contas da ADLA são suportadas.
3.  **A Fonte de Base de Dados** é preenchida automaticamente. Aponta para o pacote .usqldbpack na pasta de saída de construção do projeto.
4.  Introduza um nome no **Nome da Base** de Dados para criar uma base de dados. Se uma base de dados com esse mesmo nome já existir na conta-alvo Do Lago de Dados Do Lago De dados, todos os objetos definidos no projeto de base de dados são criados sem recriar a base de dados.
5.  Para implementar a base de dados U-SQL, selecione **Enviar**. Todos os recursos, tais como conjuntos e ficheiros adicionais, são enviados. Um trabalho U-SQL que inclui todas as declarações de DDL é submetido.

    ![Ferramentas de data lake para estúdio visual--implementar projeto de base de dados U-SQL](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project.png)

    ![Ferramentas de data lake para estúdio visual--implementar assistente de projeto de base de dados U-SQL](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project-wizard.png)

### <a name="deploy-a-u-sql-database-in-azure-devops"></a>Implementar uma base de dados U-SQL em Azure DevOps

`PackageDeploymentTool.exe`fornece as interfaces de linha de programação e comando que ajudam a implementar bases de dados U-SQL. O SDK está incluído no [pacote U-SQL SDK Nuget,](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/)localizado em `build/runtime/PackageDeploymentTool.exe`.

Em Azure DevOps, você pode usar uma tarefa de linha de comando e este SDK para configurar um pipeline de automação para a atualização da base de dados U-SQL. [Saiba mais sobre o SDK e como configurar um pipeline CI/CD para a implantação da base de dados U-SQL](data-lake-analytics-cicd-overview.md#deploy-u-sql-database-through-azure-pipelines).

## <a name="next-steps"></a>Passos seguintes

* [Criar um oleoduto CI/CD para azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
* [Teste o seu código Azure Data Lake Analytics](data-lake-analytics-cicd-test.md)
* [Executar script U-SQL na sua máquina local](data-lake-analytics-data-lake-tools-local-run.md)
