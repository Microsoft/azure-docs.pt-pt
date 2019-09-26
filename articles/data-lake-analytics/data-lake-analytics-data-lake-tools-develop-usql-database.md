---
title: Desenvolver um projeto de banco de dados U-SQL-Azure Data Lake
description: Saiba como desenvolver um banco de dados U-SQL usando Ferramentas do Azure Data Lake para Visual Studio.
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/03/2018
ms.openlocfilehash: a9b271b5f7d4e53dbf871d03dd43b62b9299aa53
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71309934"
---
# <a name="use-a-u-sql-database-project-to-develop-a-u-sql-database-for-azure-data-lake"></a>Usar um projeto de banco de dados U-SQL para desenvolver um banco de dados U-SQL para Azure Data Lake

O banco de dados do U-SQL fornece exibições estruturadas em relação à data não estruturada e a dados estruturados gerenciados em tabelas. Ele também fornece um sistema de catálogo de metadados geral para organizar seus dados estruturados e código personalizado. O banco de dados é o conceito que agrupa esses objetos relacionados.

Saiba mais sobre o [banco de dados U-SQL e a linguagem DDL](/u-sql/data-definition-language-ddl-statements). 

O projeto de banco de dados U-SQL é um tipo de projeto no Visual Studio que ajuda os desenvolvedores a desenvolver, gerenciar e implantar seus bancos de dados U-SQL de maneira rápida e fácil.

## <a name="create-a-u-sql-database-project"></a>Criar um projeto de banco de dados U-SQL

Ferramentas do Azure Data Lake para Visual Studio adicionou um novo modelo de projeto chamado projeto de banco de dados U-SQL após a versão 2.3.3000.0. Para criar um projeto do U-SQL, selecione **arquivo > novo projeto de >** . O projeto de banco de dados U-SQL pode ser encontrado em **Azure Data Lake > nó U-SQL**.

![Ferramentas de Data Lake para Visual Studio-criar projeto de banco de dados U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-usql-database-project-creation.png) 

## <a name="develop-u-sql-database-objects-by-using-a-database-project"></a>Desenvolver objetos de banco de dados U-SQL usando um projeto de banco de dados

Clique com o botão direito do mouse no projeto do banco de dados U-SQL. Selecione **adicionar > novo item**. Você pode encontrar todos os novos tipos de objeto com suporte no Assistente para **Adicionar novo item** . 

Para um objeto não assembly (por exemplo, uma função com valor de tabela), um novo script U-SQL é criado depois que você adiciona um novo item. Você pode começar a desenvolver a instrução DDL para esse objeto no editor.

Para um objeto assembly, a ferramenta fornece um editor de interface do usuário amigável que ajuda a registrar o assembly e implantar arquivos DLL e outros arquivos adicionais. As etapas a seguir mostram como adicionar uma definição de objeto de assembly ao projeto de banco de dados U-SQL:

1.  Adicione referências ao C# projeto que incluem o Udo/UDAG/UDF para o projeto de banco de dados U-SQL.

    ![Ferramentas de Data Lake para Visual Studio--Adicionar referência de projeto de banco de dados U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference.png) 

    ![Ferramentas de Data Lake para Visual Studio--Adicionar referência de projeto de banco de dados U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference-wizard.png)

2.  No modo de exibição de design do assembly, escolha o assembly referenciado no menu suspenso **criar assembly do referência** .

    ![Ferramentas de Data Lake para Visual Studio – criar assembly de referência](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-assembly-from-reference.png)

3.  Adicione **dependências gerenciadas** e **arquivos adicionais** , se houver. Quando você adiciona arquivos adicionais, a ferramenta usa o caminho relativo para garantir que ele possa encontrar os assemblies no computador local e no computador de compilação mais tarde. 

@_DeployTempDirectoryé uma variável predefinida que aponta a ferramenta para a pasta de saída da compilação. Na pasta de saída da compilação, cada assembly tem uma subpasta chamada com o nome do assembly. Todas as DLLs e arquivos adicionais estão nessa subpasta. 
 
## <a name="build-a-u-sql-database-project"></a>Criar um projeto de banco de dados U-SQL

A saída de Build para um projeto de banco de dados U-SQL é um pacote de implantação de banco de dados `.usqldbpack`u-SQL, chamado com o sufixo. O `.usqldbpack` pacote é um arquivo. zip que inclui todas as instruções DDL em um único script U-SQL na pasta **DDL** e todas as DLLs e arquivos adicionais para assemblies na pasta **Temp** .

Saiba mais sobre [como criar um projeto de banco de dados U-SQL com a linha de comando do MSBuild e uma Azure DevOps Services tarefa de compilação](data-lake-analytics-cicd-overview.md).

## <a name="deploy-a-u-sql-database"></a>Implantar um banco de dados U-SQL

O pacote. usqldbpack pode ser implantado em uma conta local ou em uma conta de Azure Data Lake Analytics usando o Visual Studio ou o SDK de implantação. 

### <a name="deploy-a-u-sql-database-in-visual-studio"></a>Implantar um banco de dados U-SQL no Visual Studio

Você pode implantar um banco de dados U-SQL por meio de um projeto de banco de dados U-SQL ou um pacote. usqldbpack no Visual Studio.

#### <a name="deploy-through-a-u-sql-database-project"></a>Implantar por meio de um projeto de banco de dados U-SQL

1.  Clique com o botão direito do mouse no projeto do banco de dados U-SQL e selecione **implantar**.
2.  No **Assistente para implantar banco de dados U-SQL**, selecione a **conta adla** para a qual você deseja implantar o banco de dados. As contas locais e as contas ADLA têm suporte.
3.  A **origem do banco de dados** é preenchida automaticamente e aponta para o pacote. usqldbpack na pasta de saída da compilação do projeto.
4.  Insira um nome no **nome do banco de dados** para criar um banco de dados. Se já existir um banco de dados com esse mesmo nome na conta de Azure Data Lake Analytics de destino, todos os objetos definidos no projeto de banco de dados serão criados sem recriar o banco de dados.
5.  Para implantar o banco de dados U-SQL, selecione **Enviar**. Todos os recursos (assemblies e arquivos adicionais) são carregados e um trabalho U-SQL que inclui todas as instruções DDL é enviado.

    ![Ferramentas de Data Lake para Visual Studio – implantar projeto de banco de dados U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project.png)

    ![Ferramentas de Data Lake para Visual Studio – implantar o assistente de projeto de banco de dados U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project-wizard.png)

#### <a name="deploy-through-a-u-sql-database-deployment-package"></a>Implantar por meio de um pacote de implantação do banco de dados U-SQL

1.  Abra **Gerenciador de servidores**. Em seguida, expanda a **conta de Azure data Lake Analytics** para a qual você deseja implantar o banco de dados.
2.  Clique com o botão direito do mouse em **bancos de dados U-SQL**e escolha **implantar banco de dados**.
3.  Defina a **origem do banco de dados** para o caminho do pacote de implantação do banco de dados U-SQL (arquivo. usqldbpack).
4.  Insira o **nome do banco de dados** para criar um banco de dados. Se houver um banco de dados com o mesmo nome que já existe na conta de Azure Data Lake Analytics de destino, todos os objetos definidos no projeto de banco de dados serão criados sem recriar o banco de dados.

    ![Ferramentas de Data Lake para Visual Studio – implantar pacote de banco de dados U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package.png)

    ![Ferramentas do Data Lake para Visual Studio – implantar o assistente de pacote do banco de dados U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package-wizard.png)
  
### <a name="deploy-u-sql-database-by-using-the-sdk"></a>Implantar o banco de dados U-SQL usando o SDK

`PackageDeploymentTool.exe`fornece a programação e as interfaces de linha de comando que ajudam a implantar bancos de dados U-SQL. O SDK está incluído no [pacote NuGet do SDK do U-SQL](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), localizado `build/runtime/PackageDeploymentTool.exe`em.

[Saiba mais sobre o SDK e como configurar o pipeline de CI/CD para a implantação do banco de dados U-SQL](data-lake-analytics-cicd-overview.md).

## <a name="reference-a-u-sql-database-project"></a>Fazer referência a um projeto de banco de dados U-SQL

Um projeto U-SQL pode fazer referência a um projeto de banco de dados U-SQL. A referência afeta duas cargas de trabalho:

- *Compilação do projeto*: Configure os ambientes de banco de dados referenciados antes de compilar os scripts U-SQL. 
- *Execução local em relação à conta (um projeto local)* : Os ambientes de banco de dados referenciados são implantados em uma conta do (um projeto local) antes da execução do script U-SQL. [Saiba mais sobre as execuções locais e a diferença entre (a máquina local) e a conta (um projeto local) aqui](data-lake-analytics-data-lake-tools-local-run.md).

### <a name="how-to-add-a-u-sql-database-reference"></a>Como adicionar uma referência de banco de dados U-SQL

1. Clique com o botão direito do mouse no projeto U-SQL em **Gerenciador de soluções**e escolha **Adicionar referência de banco de dados u-SQL...** .

    ![Ferramentas de Data Lake para Visual Studio – Adicionar referência de projeto de banco de dados](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference.png)

2. Configure uma referência de banco de dados de um projeto de banco de dados U-SQL na solução atual ou em um arquivo de pacote de banco de dados U-SQL.
3. Forneça o nome do banco de dados.

    ![Assistente de referência de projeto de ferramentas de banco de dados do Data Lake para Visual Studio](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference-wizard.png)

## <a name="next-steps"></a>Passos seguintes

- [Como configurar um pipeline de CI/CD para Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
- [Como testar seu código de Azure Data Lake Analytics](data-lake-analytics-cicd-test.md)
- [Executar o script U-SQL em seu computador local](data-lake-analytics-data-lake-tools-local-run.md)
