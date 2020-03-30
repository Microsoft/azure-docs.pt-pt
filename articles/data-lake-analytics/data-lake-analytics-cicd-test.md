---
title: Como testar o seu código Azure Data Lake Analytics
description: Aprenda a adicionar casos de teste para U-SQL e código C# estendido para Azure Data Lake Analytics.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 08/30/2019
ms.openlocfilehash: d568a267952a22d2e7a6b7acb6d54cf41f803367
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "70913951"
---
# <a name="test-your-azure-data-lake-analytics-code"></a>Teste o seu código Azure Data Lake Analytics

O Lago de Dados Azure fornece a língua [U-SQL.](data-lake-analytics-u-sql-get-started.md) U-SQL combina SQL declarativo com C# imperativo para processar dados em qualquer escala. Neste documento, aprende-se a criar casos de teste para o código de operador definido pelo utilizador (UDO) u-SQL e estendido.

## <a name="test-u-sql-scripts"></a>Testar scripts U-SQL

O script U-SQL é compilado e otimizado para código executável a ser executado em Azure ou no seu computador local. O processo de compilação e otimização trata todo o script U-SQL como um todo. Não pode fazer um teste de unidade tradicional para cada declaração. No entanto, ao utilizar o sDK de teste U-SQL e o SDK de execução local, pode fazer testes de nível de script.

### <a name="create-test-cases-for-u-sql-script"></a>Criar casos de teste para script U-SQL

As ferramentas do Lago de Dados Azure para estúdio visual permitem criar casos de teste de script U-SQL.

1. Clique à direita num script U-SQL no Solution Explorer e, em seguida, selecione **Create Unit Test**.

1. Crie um novo projeto de teste ou insira a caixa de teste num projeto de teste existente.

   ![Data Lake Tools for Visual Studio -- crie uma configuração de projeto de teste U-SQL](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project-configure.png)

### <a name="manage-the-test-data-source"></a>Gerir a fonte de dados do teste

Quando testa scripts U-SQL, precisa de ficheiros de entrada de teste. Para gerir os dados de teste, no **Solution Explorer,** clique à direita no projeto U-SQL e selecione **Properties**. Pode introduzir uma fonte na Fonte de Dados de **Teste**.

![Ferramentas data lake para estúdio visual - configurar fonte de dados de teste de projeto](./media/data-lake-analytics-cicd-test/data-lake-tools-configure-project-test-data-source.png)

Quando você `Initialize()` chama a interface no teste U-SQL SDK, uma pasta de raiz de dados local temporária é criada sob o diretório de trabalho do projeto de teste. Todos os ficheiros e pastas da pasta de origem de dados de teste são copiados para a pasta de raiz de dados local temporária antes de executar os casos de teste de script U-SQL. Pode adicionar mais pastas de origem de dados de teste dividindo o caminho da pasta de dados de teste com um ponto e vírgula.

### <a name="manage-the-database-environment-for-testing"></a>Gerir o ambiente de base de dados para testes

Se os seus scripts U-SQL utilizarem ou consultarem objetos de base de dados U-SQL, é necessário inicializar o ambiente de base de dados antes de executar casos de teste U-SQL. Esta abordagem pode ser necessária quando se ligam para os procedimentos armazenados. A `Initialize()` interface no teste U-SQL SDK ajuda-o a implementar todas as bases de dados referenciadas pelo projeto U-SQL à pasta de raiz de dados local temporário no diretório de trabalho do projeto de teste.

Para obter mais informações sobre como gerir referências de projetos de base de dados U-SQL para um projeto U-SQL, consulte [Reference a U-SQL database project](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project).

### <a name="verify-test-results"></a>Verificar os resultados dos testes

A `Run()` interface devolve um resultado de execução de emprego. *0* significa sucesso, e *1* significa fracasso. Também pode utilizar funções de afirmação C# para verificar as saídas.

### <a name="run-test-cases-in-visual-studio"></a>Executar casos de teste no Estúdio Visual

Um projeto de teste de script U-SQL é construído em cima de uma estrutura de teste de unidade C#. Depois de construir o projeto, selecione **Test** > **Windows** > **Test Explorer**. Pode executar casos de teste do **Test Explorer.** Em alternativa, clique no ficheiro .cs no teste de unidade e selecione Testes de **Execução**.

## <a name="test-c-udos"></a>Teste C# UDOs

### <a name="create-test-cases-for-c-udos"></a>Criar casos de teste para C# UDOs

Pode utilizar uma estrutura de teste de unidade C# para testar os seus operadores c# definidos pelo utilizador (UDOs). Ao testar Os UDOs, é necessário preparar os objetos **IRowset** correspondentes como inputs.

Há duas maneiras de criar um objeto **IRowset:**

- Carregue os dados de um ficheiro para criar **o IRowset:**

    ```csharp
    //Schema: "a:int, b:int"
    USqlColumn<int> col1 = new USqlColumn<int>("a");
    USqlColumn<int> col2 = new USqlColumn<int>("b");
    List<IColumn> columns = new List<IColumn> { col1, col2 };
    USqlSchema schema = new USqlSchema(columns);

    //Generate one row with default values
    IUpdatableRow output = new USqlRow(schema, null).AsUpdatable();

    //Get data from file
    IRowset rowset = UnitTestHelper.GetRowsetFromFile(@"processor.txt", schema, output.AsReadOnly(), discardAdditionalColumns: true, rowDelimiter: null, columnSeparator: '\t');
    ```

- Utilize dados de uma recolha de dados para criar **iRowset:**

    ```csharp
    //Schema: "a:int, b:int"
    USqlSchema schema = new USqlSchema(
        new USqlColumn<int>("a"),
        new USqlColumn<int>("b")
    );

    IUpdatableRow output = new USqlRow(schema, null).AsUpdatable();

    //Generate Rowset with specified values
    List<object[]> values = new List<object[]>{
        new object[2] { 2, 3 },
        new object[2] { 10, 20 }
    };

    IEnumerable<IRow> rows = UnitTestHelper.CreateRowsFromValues(schema, values);
    IRowset rowset = UnitTestHelper.GetRowsetFromCollection(rows, output.AsReadOnly());
    ```

### <a name="verify-test-results"></a>Verificar os resultados dos testes

Depois de ligar para as funções UDO, pode verificar os resultados através da verificação de valor de esquema e Rowset utilizando funções de afirmação C#. Pode adicionar à sua solução um **U-SQL C# UDO Unit Test Project.** Para isso, selecione **File > New > Project** in Visual Studio.

### <a name="run-test-cases-in-visual-studio"></a>Executar casos de teste no Estúdio Visual

Depois de construir o projeto, selecione **Test** > **Windows** > **Test Explorer**. Pode executar casos de teste do **Test Explorer.** Em alternativa, clique no ficheiro .cs no teste de unidade e selecione Testes de **Execução**.

## <a name="run-test-cases-in-azure-pipelines"></a>Executar casos de teste em Pipelines Azure<a name="run-test-cases-in-azure-devops"></a>

Ambos os projetos de teste de **script U-SQL** e **os projetos de teste C# UDO** herdam projetos de teste de unidade C#. A tarefa de [teste do Estúdio Visual](https://docs.microsoft.com/azure/devops/pipelines/test/getting-started-with-continuous-testing?view=vsts) em Pipelines Azure pode executar estes casos de teste.

### <a name="run-u-sql-test-cases-in-azure-pipelines"></a>Executar casos de teste U-SQL em Pipelines Azure

Para um teste U-SQL, `CPPSDK` certifique-se de que carrega `CPPSDK` no `USqlScriptTestRunner(cppSdkFolderFullPath: @"")`computador de construção e, em seguida, passe o caminho para .

#### <a name="what-is-cppsdk"></a>O que é CPPSDK?

CpPSDK é um pacote que inclui Microsoft Visual C++ 14 e Windows SDK 10.0.10240.0. Este pacote inclui o ambiente que é necessário pelo tempo de execução u-SQL. Pode obter este pacote sob a pasta de instalação Azure Data Lake Tools for Visual Studio:

- Para o Visual Studio 2015, está em baixo`C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK`
- Para o Visual Studio 2017, está em baixo`C:\Program Files (x86)\Microsoft Visual Studio\2017\<Visual Studio Edition>\SDK\ScopeCppSDK`
- Para o Visual Studio 2019, está em baixo`C:\Program Files (x86)\Microsoft Visual Studio\2019\<Visual Studio Edition>\SDK\ScopeCppSDK`

#### <a name="prepare-cppsdk-in-the-azure-pipelines-build-agent"></a>Prepare cpPSDK no agente de construção de Oleodutos Azure

A forma mais comum de preparar a dependência do CPPSDK em Pipelines Azure é a seguinte:

1. Feche a pasta que inclui as bibliotecas CPPSDK.

1. Verifique o ficheiro .zip no seu sistema de controlo de origem. O ficheiro .zip garante que verifica todas as bibliotecas sob a pasta CPPSDK `.gitignore` para que os ficheiros não sejam ignorados por causa de um ficheiro.

1. Desaperte o ficheiro .zip no oleoduto de construção.

1. Aponte `USqlScriptTestRunner` para esta pasta desapertada no computador de construção.

### <a name="run-c-udo-test-cases-in-azure-pipelines"></a>Executar c# udo casos de teste em Pipelines Azure

Para um teste C# UDO, certifique-se de fazer referência aos seguintes conjuntos, que são necessários para os UDOs.

- Microsoft.Analytics.Interfaces
- Microsoft.Analytics.Types
- Microsoft.Analytics.UnitTest

Se os referir através [do pacote Nuget Microsoft.Azure.DataLake.USQL.Interfaces,](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.Interfaces/)certifique-se de adicionar uma tarefa NuGet Restore no seu pipeline de construção.

## <a name="next-steps"></a>Passos seguintes

- [Como configurar o oleoduto CI/CD para o Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
- [Executar script U-SQL na sua máquina local](data-lake-analytics-data-lake-tools-local-run.md)
- [Use o projeto de base de dados U-SQL para desenvolver base de dados U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md)
