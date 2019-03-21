---
title: Usar um projeto de banco de dados U-SQL para desenvolver uma base de dados U-SQL para o Azure Data Lake
description: Saiba como desenvolver uma base de dados U-SQL com o Azure Data Lake Tools para Visual Studio.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/03/2018
ms.openlocfilehash: 1982f974acd1e9d5e8cae91201f6503eb2ae678a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57898810"
---
# <a name="use-a-u-sql-database-project-to-develop-a-u-sql-database-for-azure-data-lake"></a>Usar um projeto de banco de dados U-SQL para desenvolver uma base de dados U-SQL para o Azure Data Lake

Base de dados U-SQL fornece vistas estruturadas sobre dados não estruturados e dados estruturados geridos nas tabelas. Ele também fornece um sistema de catálogo de metadados geral para organizar os seus dados estruturados e o código personalizado. A base de dados é o conceito que agrupa esses objetos relacionados.

Saiba mais sobre [base de dados U-SQL e de linguagem DDL (Data Definition)](https://msdn.microsoft.com/azure/data-lake-analytics/u-sql/data-definition-language-ddl-statements-u-sql). 

O projeto de banco de dados U-SQL é um tipo de projeto no Visual Studio que ajuda os desenvolvedores a desenvolver, gerir e implementar seus bancos de dados U-SQL de forma rápida e fácil.

## <a name="create-a-u-sql-database-project"></a>Criar um projeto de banco de dados U-SQL

O Azure Data Lake Tools para Visual Studio adicionado um novo modelo de projeto chamado projeto de banco de dados U-SQL após a versão 2.3.3000.0. Para criar um projeto U-SQL, selecione **ficheiro > novo > projeto**. O projeto de banco de dados U-SQL pode ser encontrado na **do Azure Data Lake > nó de U-SQL**.

![Ferramentas do Data Lake para Visual Studio – criar projeto de banco de dados U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-usql-database-project-creation.png) 

## <a name="develop-u-sql-database-objects-by-using-a-database-project"></a>Desenvolver objetos de banco de dados U-SQL com um projeto de banco de dados

Com o botão direito do projeto de banco de dados U-SQL. Select **adicionar > novo item**. Pode encontrar todos os novos tipos de objeto suportados no **Add New Item** assistente. 

Para um objeto não assembly (por exemplo, uma função com valor de tabela), um novo script de U-SQL é criado depois de adicionar um novo item. Pode começar a desenvolver a instrução DDL para esse objeto no editor.

Para um objeto de assembly, a ferramenta fornece um editor de interface do Usuário amigável que o ajuda a registrar o assembly e implementar ficheiros. DLL e outros ficheiros adicionais. Os passos seguintes mostram como adicionar uma definição de objeto de assemblagem para o projeto de banco de dados U-SQL:

1.  Adicione referências ao projeto c#, que incluem a UDO/UDAG/UDF para o projeto de banco de dados U-SQL.

    ![Referência do projeto de banco de dados do Data Lake Tools para Visual Studio – adicionar U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference.png) 

    ![Referência do projeto de banco de dados do Data Lake Tools para Visual Studio – adicionar U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference-wizard.png)

2.  Na vista de estrutura de assembly, escolha a assemblagem referenciada a partir **criar o assembly de referência** menu pendente.

    ![Ferramentas do Data Lake para Visual Studio-- criar o assembly de referência](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-assembly-from-reference.png)

3.  Adicione **dependências geridos** e **ficheiros adicionais** se existir alguma. Ao adicionar ficheiros adicionais, a ferramenta usa o caminho relativo para se certificar de que ele possa encontrar os assemblies no seu computador local e na máquina de compilação mais tarde. 

@_DeployTempDirectory é uma variável predefinida que aponta a ferramenta para a pasta de saída da compilação. Sob a pasta de saída da compilação, a cada assembly possui uma subpasta com o nome com o nome do assembly. Todas as DLLs e ficheiros adicionais são nessa subpasta. 
 
## <a name="build-a-u-sql-database-project"></a>Criar um projeto de banco de dados U-SQL

A compilação de saída para um projeto de banco de dados U-SQL é um pacote de implementação da base de dados U-SQL, com o nome com o sufixo `.usqldbpack`. O `.usqldbpack` pacote é um arquivo. zip que inclui todas as instruções DDL num único script de U-SQL no **DDL** pasta e todas as DLLs e outros ficheiros para os assemblies no **Temp** pasta.

Saiba mais sobre [como criar um projeto de banco de dados U-SQL com o MSBuild linha de comandos e um serviços do Azure DevOps tarefa de compilação](data-lake-analytics-cicd-overview.md).

## <a name="deploy-a-u-sql-database"></a>Implementar uma base de dados U-SQL

O pacote de .usqldbpack pode ser implementado para uma conta local ou uma conta do Azure Data Lake Analytics com o Visual Studio ou a implementação do SDK. 

### <a name="deploy-a-u-sql-database-in-visual-studio"></a>Implementar uma base de dados U-SQL no Visual Studio

Pode implementar uma base de dados U-SQL através de um projeto de banco de dados U-SQL ou um pacote de .usqldbpack no Visual Studio.

#### <a name="deploy-through-a-u-sql-database-project"></a>Implementar através de um projeto de banco de dados U-SQL

1.  O projeto de banco de dados U-SQL com o botão direito e, em seguida, selecione **Deploy**.
2.  Na **Assistente de base de dados de implementação de U-SQL**, selecione a **conta ADLA** para a qual pretende implementar a base de dados. Contas locais e contas ADLA são suportadas.
3.  **Base de dados de origem** é preenchida automaticamente, e a pasta de saída de compilação de pontos para o pacote de .usqldbpack do projeto.
4.  Introduza um nome na **nome da base de dados** para criar uma base de dados. Se já existir uma base de dados com o mesmo nome no destino conta do Azure Data Lake Analytics, todos os objetos que estão definidos no projeto de banco de dados são criados sem recriar a base de dados.
5.  Para implementar a base de dados U-SQL, selecione **submeter**. Todos os recursos (assemblies e arquivos adicionais) são carregados e é submetida uma tarefa de U-SQL que inclui todas as instruções DDL.

    ![Projeto de banco de dados do Data Lake Tools para Visual Studio – implemente U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project.png)

    ![Assistente de projeto de banco de dados do Data Lake Tools para Visual Studio – implemente U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project-wizard.png)

#### <a name="deploy-through-a-u-sql-database-deployment-package"></a>Implementar através de um pacote de implementação do U-SQL da base de dados

1.  Open **Explorador de servidores**. Em seguida, expanda o **conta do Azure Data Lake Analytics** para a qual pretende implementar a base de dados.
2.  Clique com botão direito **bases de dados U-SQL**e, em seguida, escolha **implementar base de dados**.
3.  Definir **da base de dados de origem** para o caminho de pacote (ficheiro .usqldbpack) de implementação do U-SQL da base de dados.
4.  Introduza o **nome da base de dados** para criar uma base de dados. Se existir uma base de dados com o mesmo nome já existe no destino conta do Azure Data Lake Analytics, todos os objetos que estão definidos no projeto de banco de dados são criados sem recriar a base de dados.

    ![Pacote de base de dados do Data Lake Tools para Visual Studio – implemente U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package.png)

    ![Assistente de pacote de base de dados do Data Lake Tools para Visual Studio – implemente U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package-wizard.png)
  
### <a name="deploy-u-sql-database-by-using-the-sdk"></a>Implementar a base de dados U-SQL com o SDK

`PackageDeploymentTool.exe` Fornece as interfaces de linha de comandos que ajudam a implementar bases de dados U-SQL e programação. O SDK está incluído nos [pacote Nuget do SDK U-SQL](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), localizado em `build/runtime/PackageDeploymentTool.exe`.

[Saiba mais sobre o SDK e como configurar o pipeline de CI/CD para a implementação de base de dados U-SQL](data-lake-analytics-cicd-overview.md).

## <a name="reference-a-u-sql-database-project"></a>Referenciar um projeto de banco de dados U-SQL

Um projeto U-SQL, pode referenciar um projeto de banco de dados U-SQL. A referência afeta duas cargas de trabalho:

- *Compilação do projeto*: Configure os ambientes de banco de dados referenciados antes de compilar os scripts U-SQL. 
- *Conta de execução contra (um local-projeto) local*: Os ambientes de banco de dados referenciada são implementados (um local-projeto) conta antes da execução de script de U-SQL. [Saiba mais sobre execuções de locais e a diferença entre (a local-máquina) e (um local-projeto) conta aqui](data-lake-analytics-data-lake-tools-local-run.md).

### <a name="how-to-add-a-u-sql-database-reference"></a>Como adicionar uma referência de base de dados U-SQL

1. Com o botão direito no projeto U-SQL no **Explorador de soluções**e, em seguida, escolha **Adicionar referência do U-SQL da base de dados...** .

    ![Ferramentas do Data Lake para Visual Studio – Adicionar referência de projeto de banco de dados](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference.png)

2. Configure uma referência de base de dados de um projeto de banco de dados U-SQL na solução atual ou num arquivo de pacote do U-SQL da base de dados.
3. Forneça o nome da base de dados.

    ![Ferramentas do Data Lake para Visual Studio Adicionar Assistente de referência do projeto de banco de dados](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference-wizard.png)

## <a name="next-steps"></a>Passos Seguintes

- [Como configurar um pipeline CI/CD para o Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
- [Como testar seu código do Azure Data Lake Analytics](data-lake-analytics-cicd-test.md)
- [Execute o script de U-SQL no seu computador local](data-lake-analytics-data-lake-tools-local-run.md)
