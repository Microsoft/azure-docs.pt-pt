---
title: Como testar seu código do Azure Data Lake Analytics
description: Saiba como adicionar casos de teste para U-SQL e o código c# expandido para o Azure Data Lake Analytics.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/03/2018
ms.openlocfilehash: 4532e0c6e8095c9d64897410e0492e2135d8a478
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60630112"
---
# <a name="test-your-azure-data-lake-analytics-code"></a>Testar o seu código do Azure Data Lake Analytics

O Azure Data Lake fornece a linguagem U-SQL, que combina SQL declarativa com imperativo c# para processar dados em qualquer escala. Neste documento, saiba como criar casos de teste para U-SQL e o código do c# UDO (operador definido pelo utilizador) expandido.

## <a name="test-u-sql-scripts"></a>Testar scripts U-SQL

O script de U-SQL é compilado e otimizado para o código executável ser executado em máquinas na cloud ou no seu computador local. O processo de compilação e a otimização de processa todo o script de U-SQL como um todo. Não é possível fazer um tradicional "teste de unidade" para cada instrução. No entanto, ao utilizar o U-SQL testar SDK e a execução local SDK, pode fazer testes de nível de script.

### <a name="create-test-cases-for-u-sql-script"></a>Criar casos de teste de script de U-SQL

O Azure Data Lake Tools para Visual Studio permite-lhe criar casos de teste do script de U-SQL.

1.  Faça duplo clique de um script de U-SQL no Solution Explorer e, em seguida, selecione **criar teste de unidade**.
2.  Criar um novo projeto de teste ou inserir o caso de teste num projeto de teste existente.

    ![Ferramentas do Data Lake para Visual Studio, crie um projeto de teste de U-SQL](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project.png) 

    ![Ferramentas do Data Lake para Visual Studio-- criar uma configuração de projeto de teste de U-SQL](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project-configure.png) 

### <a name="manage-the-test-data-source"></a>Gerir a origem de dados de teste

Quando testar scripts U-SQL, precisa testar ficheiros de entrada. Pode gerir os dados de teste, configurando **origem de dados de teste** em U-SQL de propriedades do projeto. 

Quando chama o `Initialize()` interface no teste de U-SQL SDK, uma pasta de raiz de dados local temporário é criado sob o diretório de trabalho do projeto de teste, e todos os ficheiros e subpastas (e ficheiros em subpastas) na pasta de origem de dados de teste são copiados para o dados temporários de locais de pasta raiz antes de executar os casos de teste do script de U-SQL. Pode adicionar mais pastas de origem de dados de teste, dividindo o caminho de pasta de dados de teste com um ponto e vírgula.

![Data Lake Tools para Visual Studio – configurar origem de dados de teste do projeto](./media/data-lake-analytics-cicd-test/data-lake-tools-configure-project-test-data-source.png)

### <a name="manage-the-database-environment-for-testing"></a>Gerir o ambiente de base de dados para fins de teste

Se seus scripts U-SQL utilizam ou consultar com o banco de dados U-SQL objetos (por exemplo, ao chamar procedimentos armazenados), em seguida, precisa inicializar o ambiente de base de dados antes de executar casos de teste de U-SQL. O `Initialize()` interface no SDK do teste de U-SQL ajuda-o a implementar todas as bases de dados que são referenciados pelo projeto U-SQL para a pasta de raiz de dados local temporário no diretório de trabalho do projeto de teste. 

Saiba mais sobre [como gerir as referências de projeto de banco de dados U-SQL para um projeto U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project).

### <a name="verify-test-results"></a>Verifique se os resultados do teste

O `Run()` interface devolve um resultado de execução de tarefa. 0 significa êxito e falha 1 significa que. Também pode utilizar funções de assert do c# para verificar as saídas. 

### <a name="run-test-cases-in-visual-studio"></a>Executa casos de teste no Visual Studio

Um projeto de teste do script de U-SQL é criado sobre uma estrutura de teste de unidade em C#. Depois de compilar o projeto, pode percorrer todos os casos de teste **Test Explorer > lista de reprodução**. Em alternativa, clique com o botão direito do arquivo. cs e, em seguida, selecione **executar testes**.

## <a name="test-c-udos"></a>Teste c# UDOs

### <a name="create-test-cases-for-c-udos"></a>Criar casos de teste para c# UDOs

Pode utilizar uma estrutura de teste de unidade em C# para testar sua UDOs do c# (operadores definidos pelo utilizador). Ao testar UDOs, tem de preparar correspondente **IRowset** objetos como entradas.

Existem duas formas de criar um objeto de IRowset:

- Carregar dados de um arquivo para criar IRowset:

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

- Utilize dados de uma coleção de dados para criar IRowset:

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

### <a name="verify-test-results"></a>Verifique se os resultados do teste

Depois de chamar funções UDO, pode verificar os resultados de esquema e a verificação de valor do conjunto de linhas utilizando funções de assert do c#. Pode usar o código de exemplo num projeto de exemplo do teste de unidade U-SQL c# UDO através de **ficheiro > novo > projeto** no Visual Studio.

### <a name="run-test-cases-in-visual-studio"></a>Executa casos de teste no Visual Studio

Depois de compilar o projeto de teste, pode executar todos os casos de teste entanto **Test Explorer > lista de reprodução**, ou com o botão direito do arquivo. cs e escolha **executar testes**.

## <a name="run-test-cases-in-azure-devops"></a>Executa casos de teste no Azure DevOps

Ambos **projetos de teste do script de U-SQL** e **projetos de teste do c# UDO** herdar de projetos de teste c# unidade. O [tarefas de teste do Visual Studio](https://docs.microsoft.com/azure/devops/pipelines/test/getting-started-with-continuous-testing?view=vsts) em DevOps do Azure, pode executar os casos de teste. 

### <a name="run-u-sql-test-cases-in-azure-devops"></a>Executa casos de teste de U-SQL no Azure DevOps

Para um teste de U-SQL, certifique-se de que carregue `CPPSDK` na sua máquina de compilação e, em seguida, passe o `CPPSDK` caminho para USqlScriptTestRunner (cppSdkFolderFullPath: \@"").

**O que é CPPSDK?**

CPPSDK é um pacote que inclui o Microsoft Visual C++ 14 e o Windows SDK 10.0.10240.0. Este é o ambiente necessário pelo tempo de execução de U-SQL. Pode obter esse pacote sob o Azure Data Lake Tools para a pasta de instalação do Visual Studio:

- Para o Visual Studio 2015, está sob `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK`
- Para Visual Studio 2017, é em `C:\Program Files (x86)\Microsoft Visual Studio\2017\Enterprise\SDK\ScopeCppSDK`

**Preparar CPPSDK no agente de compilação do Azure DevOps**

A forma mais comum para preparar a dependência CPPSDK em DevOps do Azure é o seguinte:

1.  Zip na pasta que inclui as bibliotecas CPPSDK.
2.  Devolver o arquivo. zip para o sistema de controle de origem. (O ficheiro. zip garante que fizer check-in todas as bibliotecas sob a pasta CPPSDK, de forma que alguns ficheiros não são ignorados pelos ". gitignore").   
3.  Deszipe o ficheiro. zip no pipeline de compilação.
4.  Ponto `USqlScriptTestRunner` para esta pasta descompactada na máquina de compilação.

### <a name="run-c-udo-test-cases-in-azure-devops"></a>Executa casos de teste de c# UDO no Azure DevOps

Para um teste de c# UDO, certifique-se fazer referência os assemblies a seguir, que são necessários para UDOs. Se fizer referência através de [o pacote de Nuget Microsoft.Azure.DataLake.USQL.Interfaces](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.Interfaces/), certifique-se de adicionar uma tarefa de restauro do NuGet no seu pipeline de compilação.

* Microsoft.Analytics.Interfaces
* Microsoft.Analytics.Types
* Microsoft.Analytics.UnitTest

## <a name="next-steps"></a>Passos Seguintes

- [Como configurar o pipeline CI/CD para o Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
- [Execute o script de U-SQL no seu computador local](data-lake-analytics-data-lake-tools-local-run.md)
- [Utilizar o projeto de banco de dados U-SQL para desenvolver a base de dados U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md)

