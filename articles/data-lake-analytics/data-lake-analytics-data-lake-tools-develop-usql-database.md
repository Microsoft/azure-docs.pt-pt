---
title: Desenvolver um projeto de base de dados U-SQL - Azure Data Lake
description: Aprenda a desenvolver uma base de dados U-SQL utilizando ferramentas de lago de dados Azure para estúdio visual.
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/03/2018
ms.openlocfilehash: a9b271b5f7d4e53dbf871d03dd43b62b9299aa53
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "71309934"
---
# <a name="use-a-u-sql-database-project-to-develop-a-u-sql-database-for-azure-data-lake"></a>Use um projeto de base de dados U-SQL para desenvolver uma base de dados U-SQL para O Lago de Dados Azure

A base de dados U-SQL fornece pontos de vista estruturados sobre dados não estruturados e dados estruturados geridos em tabelas. Também fornece um sistema geral de catálogo de metadados para organizar os seus dados estruturados e código personalizado. A base de dados é o conceito que agrupa estes objetos relacionados.

Saiba mais sobre a [base de dados U-SQL e](/u-sql/data-definition-language-ddl-statements)a Linguagem de Definição de Dados (DDL) . 

O projeto de base de dados U-SQL é um tipo de projeto no Estúdio Visual que ajuda os desenvolvedores a desenvolver, gerir e implementar as suas bases de dados U-SQL de forma rápida e fácil.

## <a name="create-a-u-sql-database-project"></a>Criar um projeto de base de dados U-SQL

Azure Data Lake Tools for Visual Studio adicionou um novo modelo de projeto chamado projeto de base de dados U-SQL após a versão 2.3.3000.0. Para criar um projeto U-SQL, selecione **File > New > Project**. O Projeto de Base de Dados U-SQL pode ser encontrado sob o **Lago de Dados Azure > nó U-SQL**.

![Ferramentas de Data Lake para Estúdio Visual -- criar projeto de base de dados U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-usql-database-project-creation.png) 

## <a name="develop-u-sql-database-objects-by-using-a-database-project"></a>Desenvolver objetos de base de dados U-SQL utilizando um projeto de base de dados

Clique no projeto de base de dados U-SQL. O **seleto Adicionar > Novo item**. Pode encontrar todos os novos tipos de objetos suportados no **Add New Item** Wizard. 

Para um objeto não-montagem (por exemplo, uma função de valor de tabela), um novo script U-SQL é criado depois de adicionar um novo item. Pode começar a desenvolver a declaração do DDL para esse objeto no editor.

Para um objeto de montagem, a ferramenta fornece um editor uI amigável que o ajuda a registar a montagem e a implementar ficheiros DLL e outros ficheiros adicionais. Os seguintes passos mostram-lhe como adicionar uma definição de objeto de montagem ao projeto de base de dados U-SQL:

1.  Adicione referências ao projeto C# que incluem o UDO/UDAG/UDF para o projeto de base de dados U-SQL.

    ![Ferramentas de data lake para estúdio visual-- adicionar referência de projeto de base de dados U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference.png) 

    ![Ferramentas de data lake para estúdio visual-- adicionar referência de projeto de base de dados U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference-wizard.png)

2.  Na vista de design de montagem, escolha o conjunto referenciado de Criar montagem a partir do menu de entrega de **referência.**

    ![Ferramentas de Data Lake para Estúdio Visual -- criar montagem a partir de referência](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-assembly-from-reference.png)

3.  Adicione **Dependências Geridas** e **Ficheiros Adicionais** se houver. Quando adiciona ficheiros adicionais, a ferramenta utiliza o caminho relativo para se certificar de que pode encontrar os conjuntos tanto na sua máquina local como na máquina de construção mais tarde. 

@_DeployTempDirectoryé uma variável predefinida que aponta a ferramenta para a pasta de saída de construção. Sob a pasta de saída de construção, cada conjunto tem uma subpasta com o nome de montagem. Todos os DLLs e ficheiros adicionais estão nessa subpasta. 
 
## <a name="build-a-u-sql-database-project"></a>Construa um projeto de base de dados U-SQL

A produção de construção de um projeto de base de dados U-SQL `.usqldbpack`é um pacote de implantação de bases de dados U-SQL, nomeado com o sufixo . O `.usqldbpack` pacote é um ficheiro .zip que inclui todas as declarações de DDL num único script U-SQL na pasta **DDL,** e todos os DLLs e ficheiros adicionais para conjuntos na pasta **Temp.**

Saiba mais sobre como construir um projeto de [base de dados U-SQL com a linha de comando MSBuild e uma tarefa de construção de Serviços Azure DevOps](data-lake-analytics-cicd-overview.md).

## <a name="deploy-a-u-sql-database"></a>Implementar uma base de dados U-SQL

O pacote .usqldbpack pode ser implantado para uma conta local ou uma conta Azure Data Lake Analytics utilizando o Visual Studio ou o SDK de implementação. 

### <a name="deploy-a-u-sql-database-in-visual-studio"></a>Implementar uma base de dados U-SQL no Estúdio Visual

Pode implementar uma base de dados U-SQL através de um projeto de base de dados U-SQL ou de um pacote .usqldbpack no Visual Studio.

#### <a name="deploy-through-a-u-sql-database-project"></a>Implementar através de um projeto de base de dados U-SQL

1.  Clique no projeto de base de dados U-SQL e, em seguida, selecione **Deploy**.
2.  No Assistente de **Base de Dados U-SQL de implantação,** selecione a **conta ADLA** para a qual pretende implementar a base de dados. Tanto as contas locais como as contas da ADLA são suportadas.
3.  **A Fonte** de Base de Dados é preenchida automaticamente e aponta para o pacote .usqldbpack na pasta de saída de construção do projeto.
4.  Introduza um nome no **Nome da Base** de Dados para criar uma base de dados. Se uma base de dados com esse mesmo nome já existir na conta-alvo Do Lago de Dados Do Lago De dados, todos os objetos definidos no projeto de base de dados são criados sem recriar a base de dados.
5.  Para implementar a base de dados U-SQL, selecione **Enviar**. Todos os recursos (conjuntos e ficheiros adicionais) são enviados, e um trabalho U-SQL que inclui todas as declarações de DDL é submetido.

    ![Ferramentas de data lake para estúdio visual--implementar projeto de base de dados U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project.png)

    ![Ferramentas de data lake para estúdio visual--implementar assistente de projeto de base de dados U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project-wizard.png)

#### <a name="deploy-through-a-u-sql-database-deployment-package"></a>Implementar através de um pacote de implementação de base de dados U-SQL

1.  Open **Server Explorer**. Em seguida, expanda a **conta Azure Data Lake Analytics** para a qual pretende implementar a base de dados.
2.  Clique em direito nas bases de **dados U-SQL**e, em seguida, escolha a Base de **Dados de Implementação**.
3.  Deteto **a Fonte de Base** de Dados para o pacote de implementação da base de dados U-SQL (ficheiro.usqldbpack).
4.  Introduza o **Nome da Base** de Dados para criar uma base de dados. Se existe uma base de dados com o mesmo nome que já existe na conta-alvo Do Lago de Dados Do Lago De dados, todos os objetos que estão definidos no projeto da base de dados são criados sem recriar a base de dados.

    ![Ferramentas de data lake para estúdio visual--implementar pacote de base de dados U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package.png)

    ![Ferramentas de data lake para estúdio visual--implementar u-SQL pacote de pacote de dados](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package-wizard.png)
  
### <a name="deploy-u-sql-database-by-using-the-sdk"></a>Implementar base de dados U-SQL utilizando o SDK

`PackageDeploymentTool.exe`fornece as interfaces de linha de programação e comando que ajudam a implementar bases de dados U-SQL. O SDK está incluído no [pacote U-SQL SDK Nuget,](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/)localizado em `build/runtime/PackageDeploymentTool.exe`.

[Saiba mais sobre o SDK e como configurar o pipeline CI/CD para a implantação da base de dados U-SQL](data-lake-analytics-cicd-overview.md).

## <a name="reference-a-u-sql-database-project"></a>Referência a um projeto de base de dados U-SQL

Um projeto U-SQL pode fazer referência a um projeto de base de dados U-SQL. A referência afeta duas cargas de trabalho:

- *Construção do projeto*: Instale os ambientes de base de dados referenciados antes de construir os scripts U-SQL. 
- *Local run against (um projeto local) account*: Os ambientes de base de dados referenciados são implantados para (um projeto local) conta antes da execução do script U-SQL. [Saiba mais sobre as corridas locais e a diferença entre (a máquina local) e (um projeto local) conta aqui](data-lake-analytics-data-lake-tools-local-run.md).

### <a name="how-to-add-a-u-sql-database-reference"></a>Como adicionar uma referência de base de dados U-SQL

1. Clique à direita no projeto U-SQL no **Solution Explorer,** e depois escolha adicionar referência de **base de dados U-SQL...**.

    ![Data Lake Tools for Visual Studio -- adicione referência de projeto de base de dados](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference.png)

2. Configure uma referência de base de dados de um projeto de base de dados U-SQL na solução atual ou num ficheiro de pacote de base de dados U-SQL.
3. Forneça o nome para a base de dados.

    ![Ferramentas data Lake para Estúdio Visual adicionam assistente de referência de projeto de base de dados](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference-wizard.png)

## <a name="next-steps"></a>Passos seguintes

- [Como criar um oleoduto CI/CD para o Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
- [Como testar o seu código Azure Data Lake Analytics](data-lake-analytics-cicd-test.md)
- [Executar script U-SQL na sua máquina local](data-lake-analytics-data-lake-tools-local-run.md)
