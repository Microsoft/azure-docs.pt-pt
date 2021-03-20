---
title: Desenvolver um projeto de base de dados U-SQL - Azure Data Lake
description: Saiba como desenvolver uma base de dados U-SQL utilizando ferramentas do Lago de Dados Azure para o Estúdio Visual.
author: liudan66
ms.author: liud
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 07/03/2018
ms.openlocfilehash: 706457a602e20dd37e64e5f389948b351ac8ebcf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96015252"
---
# <a name="use-a-u-sql-database-project-to-develop-a-u-sql-database-for-azure-data-lake"></a>Utilize um projeto de base de dados U-SQL para desenvolver uma base de dados U-SQL para O Lago de Dados Azure

A base de dados U-SQL fornece vistas estruturadas sobre dados não estruturados e dados estruturados geridos em tabelas. Também fornece um sistema geral de catálogo de metadados para organizar os seus dados estruturados e código personalizado. A base de dados é o conceito que agrupam estes objetos relacionados.

Saiba mais sobre [a base de dados U-SQL e a Linguagem de Definição de Dados (DDL)](/u-sql/data-definition-language-ddl-statements).

O projeto de base de dados U-SQL é um tipo de projeto no Visual Studio que ajuda os desenvolvedores a desenvolver, gerir e implementar as suas bases de dados U-SQL de forma rápida e fácil.

## <a name="create-a-u-sql-database-project"></a>Criar um projeto de base de dados U-SQL

Azure Data Lake Tools for Visual Studio adicionou um novo modelo de projeto chamado U-SQL projeto de base de dados após a versão 2.3.3000.0. Para criar um projeto U-SQL, selecione **File > New > Project**. O U-SQL Database Project pode ser encontrado sob **o nó Azure Data Lake > U-SQL**.

![Ferramentas do Lago de Dados para Estúdio Visual--criar projeto de base de dados U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-usql-database-project-creation.png)

## <a name="develop-u-sql-database-objects-by-using-a-database-project"></a>Desenvolver objetos de base de dados U-SQL utilizando um projeto de base de dados

Clique com o botão direito no projeto de base de dados U-SQL. O **selecionador Adicionar > Novo item**. Pode encontrar todos os novos tipos de objetos suportados no **'Add New Item Wizard'.**

Para um objeto não-montagem (por exemplo, uma função valorizada por tabela), um novo script U-SQL é criado depois de adicionar um novo item. Pode começar a desenvolver a declaração de DDL para esse objeto no editor.

Para um objeto de montagem, a ferramenta fornece um editor de UI fácil de usar que o ajuda a registar a montagem e a implementar ficheiros DLL e outros ficheiros adicionais. Os seguintes passos mostram como adicionar uma definição de objeto de montagem ao projeto de base de dados U-SQL:

1. Adicione referências ao projeto C# que inclua o UDO/UDAG/UDF para o projeto de base de dados U-SQL.

   ![Ferramentas do Lago de Dados para Estúdio Visual - Adicionar referência](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference.png)

   ![Ferramentas do Lago de Dados para Estúdio Visual - Adicionar referência do projeto de base de dados U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference-wizard.png)

2. Na vista de design de montagem, escolha o conjunto referenciado do conjunto Criar a partir do menu suspenso de **referência.**

   ![Ferramentas do Lago de Dados para Estúdio Visual -- criar montagem a partir de referência](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-assembly-from-reference.png)

3. Adicione **dependências geridas** e **ficheiros adicionais** se houver algum. Quando adiciona ficheiros adicionais, a ferramenta utiliza o caminho relativo para se certificar de que pode encontrar os conjuntos tanto na máquina local como na máquina de construção mais tarde.

@_DeployTempDirectory é uma variável predefinida que aponta a ferramenta para a pasta de saída de construção. Sob a pasta de saída de construção, cada conjunto tem uma sub-dobragem nomeada com o nome de montagem. Todos os DLLs e ficheiros adicionais estão na subclasse.

## <a name="build-a-u-sql-database-project"></a>Construir um projeto de base de dados U-SQL

A saída de construção para um projeto de base de dados U-SQL é um pacote de implementação de base de dados U-SQL, nomeado com o sufixo `.usqldbpack` . O `.usqldbpack` pacote é um ficheiro .zip que inclui todas as declarações de DDL num único script U-SQL na pasta **DDL,** bem como todos os DLLs e ficheiros adicionais para conjuntos na pasta **Temp.**

Saiba mais sobre [como construir um projeto de base de dados U-SQL com a linha de comando MSBuild e uma tarefa de construção de Serviços Azure DevOps](data-lake-analytics-cicd-overview.md).

## <a name="deploy-a-u-sql-database"></a>Implementar uma base de dados U-SQL

O pacote .usqldbpack pode ser implantado numa conta local ou numa conta Azure Data Lake Analytics utilizando o Visual Studio ou o SDK de implantação.

### <a name="deploy-a-u-sql-database-in-visual-studio"></a>Implementar uma base de dados U-SQL no Visual Studio

Pode implementar uma base de dados U-SQL através de um projeto de base de dados U-SQL ou de um pacote .usqldbpack no Visual Studio.

#### <a name="deploy-through-a-u-sql-database-project"></a>Implementar através de um projeto de base de dados U-SQL

1. Clique com o botão direito no projeto de base de dados U-SQL e, em seguida, selecione **Implementar**.

1. No **Assistente de Base de Dados U-SQL de implementação,** selecione a **conta ADLA** para a qual pretende implantar a base de dados. As contas locais e as contas ADLA são suportadas.

1. **A Base de Dados Fonte** é preenchida automaticamente e aponta para o pacote .usqldbpack na pasta de saída de construção do projeto.

1. Introduza um nome no **Nome da Base de Dados** para criar uma base de dados. Se já existe uma base de dados com o mesmo nome na conta target Azure Data Lake Analytics, todos os objetos que são definidos no projeto de base de dados são criados sem recriar a base de dados.

1. Para implementar a base de dados U-SQL, **selecione Enviar por isso.** Todos os recursos (conjuntos e ficheiros adicionais) são carregados, e um trabalho U-SQL que inclui todas as declarações de DDL é submetido.

   ![Ferramentas do Lago de Dados para Estúdio Visual--Implementar projeto de base de dados U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project.png)

   ![Ferramentas do Lago de Dados para Estúdio Visual--Implementar assistente de projeto de base de dados U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project-wizard.png)

#### <a name="deploy-through-a-u-sql-database-deployment-package"></a>Implementar através de um pacote de implementação de base de dados U-SQL

1. Abrir **o Explorador do Servidor**. Em seguida, expanda a **conta Azure Data Lake Analytics** para a qual pretende implementar a base de dados.

1. Clique em direito nas **bases de dados do U-SQL** e, em seguida, escolha **'Implementar base de dados'.**

1. Definir **Base de Dados Fonte** para o pacote de implementação de base de dados U-SQL (.usqldbpack file) caminho.

1. Introduza o **Nome da Base de Dados** para criar uma base de dados. Se houver uma base de dados com o mesmo nome que já existe na conta target Azure Data Lake Analytics, todos os objetos que são definidos no projeto de base de dados são criados sem recriar a base de dados.

   ![Ferramentas do Lago de Dados para Estúdio Visual--Implementar pacote de base de dados U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package.png)

   ![Ferramentas do Lago de Dados para Estúdio Visual--Implementar assistente de pacote de base de dados U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package-wizard.png)
  
### <a name="deploy-u-sql-database-by-using-the-sdk"></a>Implementar base de dados U-SQL utilizando o SDK

`PackageDeploymentTool.exe` fornece a programação e interfaces de linha de comando que ajudam a implementar bases de dados U-SQL. O SDK está incluído no [pacote U-SQL SDK Nuget,](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/)localizado em `build/runtime/PackageDeploymentTool.exe` .

[Saiba mais sobre o SDK e como configurar o gasoduto CI/CD para a implementação da base de dados U-SQL](data-lake-analytics-cicd-overview.md).

## <a name="reference-a-u-sql-database-project"></a>Referência a um projeto de base de dados U-SQL

Um projeto U-SQL pode fazer referência a um projeto de base de dados U-SQL. A referência afeta duas cargas de trabalho:

- *Construção de* projetos : Crie os ambientes de base de dados referenciados antes de construir os scripts U-SQL.
- *Execução local contra (um projeto local)* conta : Os ambientes de base de dados referenciados são implantados para (um projeto local) antes da execução do script U-SQL. [Saiba mais sobre as corridas locais e a diferença entre a conta local-máquina e (um projeto local) aqui.](data-lake-analytics-data-lake-tools-local-run.md)

### <a name="how-to-add-a-u-sql-database-reference"></a>Como adicionar uma referência de base de dados U-SQL

1. Clique com o botão direito no projeto U-SQL no **Solution Explorer** e, em seguida, escolha Adicionar Referência de Base **de Dados U-SQL...**.

    ![Ferramentas do Lago de Dados para Estúdio Visual -- adicionar referência de projeto de base de dados](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference.png)

2. Configure uma referência de base de dados a partir de um projeto de base de dados U-SQL na solução atual ou num ficheiro de pacote de pacote de base de dados U-SQL.

3. Forneça o nome da base de dados.

    ![Ferramentas do Lago de Dados para Estúdio Visual adicionar assistente de referência de projeto de base de dados](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference-wizard.png)

## <a name="next-steps"></a>Passos seguintes

- [Como configurar um pipeline CI/CD para a Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
- [Como testar o seu código Azure Data Lake Analytics](data-lake-analytics-cicd-test.md)
- [Executar script U-SQL na sua máquina local](data-lake-analytics-data-lake-tools-local-run.md)
