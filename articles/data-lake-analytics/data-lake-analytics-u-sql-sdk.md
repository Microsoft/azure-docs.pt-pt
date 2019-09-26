---
title: Executar trabalhos do U-SQL localmente-Azure Data Lake o SDK U-SQL
description: Saiba como executar e testar trabalhos do U-SQL localmente usando a linha de comando e interfaces de programação em sua estação de trabalho local.
services: data-lake-analytics
ms.service: data-lake-analytics
author: yanacai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.topic: conceptual
ms.date: 03/01/2017
ms.openlocfilehash: 51d9060eaf4b30c696ef2a3b5f798a31e2f2a98a
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71309689"
---
# <a name="run-and-test-u-sql-with-azure-data-lake-u-sql-sdk"></a>Executar e testar U-SQL com Azure Data Lake SDK U-SQL

Ao desenvolver o script U-SQL, é comum executar e testar o script U-SQL localmente antes de enviá-lo para a nuvem. Azure Data Lake fornece um pacote NuGet chamado Azure Data Lake SDK U-SQL para esse cenário, por meio do qual você pode facilmente dimensionar a execução e o teste do U-SQL. Também é possível integrar esse teste do U-SQL com o sistema CI (integração contínua) para automatizar a compilação e o teste.

Se você se preocupa em como executar manualmente e depurar o script U-SQL com as ferramentas de GUI, você pode usar Ferramentas do Azure Data Lake para Visual Studio para isso. Você pode aprender mais [aqui](data-lake-analytics-data-lake-tools-local-run.md).

## <a name="install-azure-data-lake-u-sql-sdk"></a>Instalar Azure Data Lake o SDK do U-SQL

Você pode obter o Azure Data Lake o SDK do U-SQL [aqui](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) em NuGet.org. E antes de usá-lo, você precisa verificar se tem dependências da seguinte maneira.

### <a name="dependencies"></a>Dependências

O SDK do U-SQL Data Lake requer as seguintes dependências:

- [Microsoft .NET Framework 4,6 ou mais recente](https://www.microsoft.com/download/details.aspx?id=17851).
- Microsoft Visual C++ 14 e SDK do Windows 10.0.10240.0 ou mais recente (que é chamado de CppSDK neste artigo). Há duas maneiras de obter CppSDK:

  - Instale o [Visual Studio Community Edition](https://developer.microsoft.com/downloads/vs-thankyou). Você terá uma pasta \Windows Kits\10 na pasta arquivos de programas, por exemplo, C:\Program Files (x86) \Windows Kits\10\. Você também encontrará a versão do SDK do Windows 10 em \Windows Kits\10\Lib. Se você não vir essas pastas, reinstale o Visual Studio e certifique-se de selecionar o SDK do Windows 10 durante a instalação. Se você tiver isso instalado com o Visual Studio, o compilador local do U-SQL o encontrará automaticamente.

    ![Ferramentas de Data Lake para o SDK do Windows 10 de execução local do Visual Studio](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-windows-10-sdk.png)

  - Instale o [Data Lake Tools para Visual Studio](https://aka.ms/adltoolsvs). Você pode encontrar os arquivos de SDK do Windows e C++ visuais predefinidos em C:\Program Files (x86) \Microsoft Visual Studio 14.0 \ Common7\IDE\Extensions\Microsoft\ADL Tools\X.X.xxxx.X\CppSDK. Nesse caso, o compilador local do U-SQL não pode localizar as dependências automaticamente. Você precisa especificar o caminho CppSDK para ele. Você pode copiar os arquivos para outro local ou usá-los como estão.

## <a name="understand-basic-concepts"></a>Entender os conceitos básicos

### <a name="data-root"></a>Raiz de dados

A pasta raiz de dados é um "armazenamento local" para a conta de computação local. É equivalente à conta de Azure Data Lake Store de uma conta de Data Lake Analytics. Alternar para uma pasta raiz de dados diferente é exatamente como mudar para uma conta de repositório diferente. Se você quiser acessar dados comumente compartilhados com diferentes pastas raiz de dados, deverá usar caminhos absolutos em seus scripts. Ou crie links simbólicos do sistema de arquivos (por exemplo, **MKLINK** em NTFS) na pasta raiz de dados para apontar para os dados compartilhados.

A pasta raiz de dados é usada para:

- Armazene metadados locais, incluindo bancos de dados, tabelas, TVFs (funções com valor de tabela) e assemblies.
- Pesquisar os caminhos de entrada e saída que são definidos como caminhos relativos no U-SQL. O uso de caminhos relativos facilita a implantação de seus projetos do U-SQL no Azure.

### <a name="file-path-in-u-sql"></a>Caminho do arquivo no U-SQL

Você pode usar um caminho relativo e um caminho absoluto local em scripts U-SQL. O caminho relativo é relativo ao caminho da pasta raiz de dados especificada. Recomendamos que você use "/" como o separador de caminho para tornar os scripts compatíveis com o lado do servidor. Aqui estão alguns exemplos de caminhos relativos e seus caminhos absolutos equivalentes. Nestes exemplos, C:\LocalRunDataRoot é a pasta raiz de dados.

|Caminho relativo|Caminho absoluto|
|-------------|-------------|
|/abc/def/input.csv |C:\LocalRunDataRoot\abc\def\input.csv|
|abc/def/input.csv  |C:\LocalRunDataRoot\abc\def\input.csv|
|D:/abc/def/input.csv |D:\abc\def\input.csv|

### <a name="working-directory"></a>Diretório de trabalho

Ao executar o script U-SQL localmente, um diretório de trabalho é criado durante a compilação no diretório em execução atual. Além das saídas de compilação, os arquivos de tempo de execução necessários para execução local serão copiados em sombra para esse diretório de trabalho. A pasta raiz do diretório de trabalho é chamada de "ScopeWorkDir" e os arquivos no diretório de trabalho são os seguintes:

|Diretório/arquivo|Diretório/arquivo|Diretório/arquivo|Definição|Descrição|
|--------------|--------------|--------------|----------|-----------|
|C6A101DDCB470506| | |Cadeia de caracteres de hash da versão de tempo de execução|Cópia de sombra dos arquivos de tempo de execução necessários para a execução local|
| |Script_66AE4909AA0ED06C| |Nome do script + cadeia de caracteres hash do caminho do script|Saídas de compilação e log de etapa de execução|
| | |\_script\_.abr|Saída do compilador|Arquivo Algebra|
| | |\_ScopeCodeGen\_.*|Saída do compilador|Código gerenciado gerado|
| | |\_ScopeCodeGenEngine\_.*|Saída do compilador|Código nativo gerado|
| | |assemblies referenciados|Referência de assembly|Arquivos de assembly referenciados|
| | |deployed_resources|Implantação de recursos|Arquivos de implantação de recursos|
| | |xxxxxxxx.xxx[1..n]\_\*.*|Log de execução|Log de etapas de execução|


## <a name="use-the-sdk-from-the-command-line"></a>Usar o SDK da linha de comando

### <a name="command-line-interface-of-the-helper-application"></a>Interface de linha de comando do aplicativo auxiliar

Em SDK directory\build\runtime, o LocalRunHelper. exe é o aplicativo auxiliar de linha de comando que fornece interfaces para a maioria das funções de execução local comumente usadas. Observe que o comando e as opções de argumento diferenciam maiúsculas de minúsculas. Para chamá-lo:

    LocalRunHelper.exe <command> <Required-Command-Arguments> [Optional-Command-Arguments]

Execute LocalRunHelper. exe sem argumentos ou com a opção **Help** para mostrar as informações de ajuda:

    > LocalRunHelper.exe help

        Command 'help' :  Show usage information
        Command 'compile' :  Compile the script
        Required Arguments :
            -Script param
                    Script File Path
        Optional Arguments :
            -Shallow [default value 'False']
                    Shallow compile

Nas informações de ajuda:

-  O **comando** fornece o nome do comando.  
-  O **argumento necessário** lista os argumentos que devem ser fornecidos.  
-  O **argumento opcional** lista os argumentos que são opcionais, com valores padrão.  Argumentos boolianos opcionais não têm parâmetros e suas aparências significam negativo para seu valor padrão.

### <a name="return-value-and-logging"></a>Valor de retorno e registro em log

O aplicativo auxiliar retorna **0** para êxito e **-1** para falha. Por padrão, o auxiliar envia todas as mensagens para o console atual. No entanto, a maioria dos comandos dá suporte ao argumento opcional **-MessageOut path_to_log_file,** que redireciona as saídas para um arquivo de log.

### <a name="environment-variable-configuring"></a>Configuração da variável de ambiente

A execução local do U-SQL precisa de uma raiz de dados especificada como conta de armazenamento local, bem como um caminho CppSDK especificado para dependências. Você pode definir o argumento na linha de comando ou definir a variável de ambiente para eles.

- Defina a variável de ambiente **SCOPE_CPP_SDK** .

    Se você receber o Microsoft C++ visual e o SDK do Windows instalando o data Lake Tools para Visual Studio, verifique se você tem a seguinte pasta:

        C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK

    Defina uma nova variável de ambiente chamada **SCOPE_CPP_SDK** para apontar para esse diretório. Ou copie a pasta para o outro local e especifique **SCOPE_CPP_SDK** como isso.

    Além de definir a variável de ambiente, você pode especificar o argumento **-CppSDK** quando estiver usando a linha de comando. Esse argumento substitui a variável de ambiente CppSDK padrão.

- Defina a variável de ambiente **LOCALRUN_DATAROOT** .

    Defina uma nova variável de ambiente chamada **LOCALRUN_DATAROOT** que aponta para a raiz de dados.

    Além de definir a variável de ambiente, você pode especificar o argumento **-DataRoot** com o caminho raiz de dados quando estiver usando uma linha de comando. Esse argumento substitui a variável de ambiente de raiz de dados padrão. Você precisa adicionar esse argumento a cada linha de comando que estiver executando para que você possa substituir a variável de ambiente de raiz de dados padrão para todas as operações.

### <a name="sdk-command-line-usage-samples"></a>Exemplos de uso de linha de comando do SDK

#### <a name="compile-and-run"></a>Compilar e executar

O comando **Run** é usado para compilar o script e, em seguida, executar resultados compilados. Seus argumentos de linha de comando são uma combinação dos de **Compilar** e **executar**.

    LocalRunHelper run -Script path_to_usql_script.usql [optional_arguments]

Estes são os argumentos opcionais para **execução**:


|Argumento|Valor predefinido|Descrição|
|--------|-------------|-----------|
|-CodeBehind|False|O script tem code-behind. cs|
|-CppSDK| |Diretório CppSDK|
|-DataRoot| Variável de ambiente DataRoot|DataRoot para execução local, padrão para a variável de ambiente ' LOCALRUN_DATAROOT '|
|-MessageOut| |Despejar mensagens no console para um arquivo|
|-Paralelo|1|Executar o plano com o paralelismo especificado|
|-Referências| |Lista de caminhos para assemblies de referência extra ou arquivos de dados de code-behind, separados por '; '|
|-UdoRedirect|False|Gerar configuração de redirecionamento de assembly Udo|
|-UseDatabase|forma mestre|Banco de dados a ser usado para o registro de assembly temporário de code-behind|
|-Verbose|False|Mostrar saídas detalhadas do tempo de execução|
|-WorkDir|Diretório atual|Diretório para uso e saídas do compilador|
|-RunScopeCEP|0|Modo de ScopeCEP a ser usado|
|-ScopeCEPTempPath|Temp|Caminho temporário a ser usado para streaming de dados|
|-OptFlags| |Lista separada por vírgulas de sinalizadores do otimizador|


Segue-se um exemplo:

    LocalRunHelper run -Script d:\test\test1.usql -WorkDir d:\test\bin -CodeBehind -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB –Parallel 5 -Verbose

Além de combinar **compile** e **Execute**, você pode compilar e executar os executáveis compilados separadamente.

#### <a name="compile-a-u-sql-script"></a>Compilar um script U-SQL

O comando **compile** é usado para compilar um script U-SQL para executáveis.

    LocalRunHelper compile -Script path_to_usql_script.usql [optional_arguments]

Estes são os argumentos opcionais para **compilação**:


|Argumento|Descrição|
|--------|-----------|
| -Codebehind [valor padrão ' false ']|O script tem code-behind. cs|
| -CppSDK [valor padrão ' ']|Diretório CppSDK|
| -DataRoot [valor padrão ' variável de ambiente DataRoot ']|DataRoot para execução local, padrão para a variável de ambiente ' LOCALRUN_DATAROOT '|
| -MessageOut [valor padrão ' ']|Despejar mensagens no console para um arquivo|
| -References [valor padrão ' ']|Lista de caminhos para assemblies de referência extra ou arquivos de dados de code-behind, separados por '; '|
| -Raso [valor padrão ' false ']|Compilação superficial|
| -UdoRedirect [valor padrão ' false ']|Gerar configuração de redirecionamento de assembly Udo|
| -UseDatabase [valor padrão ' Master ']|Banco de dados a ser usado para o registro de assembly temporário de code-behind|
| -WorkDir [valor padrão ' diretório atual ']|Diretório para uso e saídas do compilador|
| -RunScopeCEP [valor padrão ' 0 ']|Modo de ScopeCEP a ser usado|
| -ScopeCEPTempPath [valor padrão ' temp ']|Caminho temporário a ser usado para streaming de dados|
| -OptFlags [valor padrão ' ']|Lista separada por vírgulas de sinalizadores do otimizador|


Aqui estão alguns exemplos de uso.

Compilar um script U-SQL:

    LocalRunHelper compile -Script d:\test\test1.usql

Compile um script U-SQL e defina a pasta raiz de dados. Observe que isso substituirá a variável de ambiente set.

    LocalRunHelper compile -Script d:\test\test1.usql –DataRoot c:\DataRoot

Compile um script U-SQL e defina um diretório de trabalho, um assembly de referência e um banco de dados:

    LocalRunHelper compile -Script d:\test\test1.usql -WorkDir d:\test\bin -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB

#### <a name="execute-compiled-results"></a>Executar resultados compilados

O comando **Execute** é usado para executar resultados compilados.   

    LocalRunHelper execute -Algebra path_to_compiled_algebra_file [optional_arguments]

Estes são os argumentos opcionais para **Execute**:

|Argumento|Valor predefinido|Descrição|
|--------|-------------|-----------|
|-DataRoot | '' |Raiz de dados para execução de metadados. O padrão é a variável de ambiente **LOCALRUN_DATAROOT** .|
|-MessageOut | '' |Despeje mensagens no console em um arquivo.|
|-Paralelo | '1' |Indicador para executar as etapas de execução local geradas com o nível de paralelismo especificado.|
|-Verbose | For |Indicador para mostrar saídas detalhadas do tempo de execução.|

Veja um exemplo de uso:

    LocalRunHelper execute -Algebra d:\test\workdir\C6A101DDCB470506\Script_66AE4909AA0ED06C\__script__.abr –DataRoot c:\DataRoot –Parallel 5


## <a name="use-the-sdk-with-programming-interfaces"></a>Usar o SDK com interfaces de programação

As interfaces de programação estão todas localizadas no LocalRunHelper. exe. Você pode usá-los para integrar a funcionalidade do SDK do U-SQL e C# da estrutura de teste para dimensionar seu teste local do script u-SQL. Neste artigo, usarei o projeto de teste C# de unidade padrão para mostrar como usar essas interfaces para testar seu script U-SQL.

### <a name="step-1-create-c-unit-test-project-and-configuration"></a>Passo 1: Criar C# projeto e configuração de teste de unidade

- Crie um C# projeto de teste de unidade por meio do arquivo > novo C# projeto de > > Visual > teste > projeto de teste de unidade.
- Adicione LocalRunHelper. exe como uma referência para o projeto. O LocalRunHelper. exe está localizado em \build\runtime\LocalRunHelper.exe no pacote NuGet.

    ![Azure Data Lake Adicionar referência do SDK U-SQL](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-add-reference.png)

- O SDK do U-SQL **só** dá suporte ao ambiente x64, certifique-se de definir o destino da plataforma de compilação como x64. Você pode definir isso por meio da Propriedade do projeto > compilar > destino da plataforma.

    ![Azure Data Lake SDK do U-SQL configurar projeto x64](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-configure-x64.png)

- Certifique-se de definir seu ambiente de teste como x64. No Visual Studio, você pode defini-lo por meio do teste > configurações de teste > arquitetura de processador padrão > x64.

    ![Azure Data Lake SDK do U-SQL configurar o ambiente de teste x64](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-configure-test-x64.png)

- Certifique-se de copiar todos os arquivos de dependência em NugetPackage\build\runtime\ para o diretório de trabalho do projeto, que geralmente está abaixo de ProjectFolder\bin\x64\Debug.

### <a name="step-2-create-u-sql-script-test-case"></a>Passo 2: Criar caso de teste de script U-SQL

Veja abaixo o código de exemplo para o teste de script U-SQL. Para teste, você precisa preparar scripts, arquivos de entrada e arquivos de saída esperados.

    using System;
    using Microsoft.VisualStudio.TestTools.UnitTesting;
    using System.IO;
    using System.Text;
    using System.Security.Cryptography;
    using Microsoft.Analytics.LocalRun;

    namespace UnitTestProject1
    {
        [TestClass]
        public class USQLUnitTest
        {
            [TestMethod]
            public void TestUSQLScript()
            {
                //Specify the local run message output path
                StreamWriter MessageOutput = new StreamWriter("../../../log.txt");

                LocalRunHelper localrun = new LocalRunHelper(MessageOutput);

                //Configure the DateRoot path, Script Path and CPPSDK path
                localrun.DataRoot = "../../../";
                localrun.ScriptPath = "../../../Script/Script.usql";
                localrun.CppSdkDir = "../../../CppSDK";

                //Run U-SQL script
                localrun.DoRun();

                //Script output 
                string Result = Path.Combine(localrun.DataRoot, "Output/result.csv");

                //Expected script output
                string ExpectedResult = "../../../ExpectedOutput/result.csv";

                Test.Helpers.FileAssert.AreEqual(Result, ExpectedResult);

                //Don't forget to close MessageOutput to get logs into file
                MessageOutput.Close();
            }
        }
    }

    namespace Test.Helpers
    {
        public static class FileAssert
        {
            static string GetFileHash(string filename)
            {
                Assert.IsTrue(File.Exists(filename));

                using (var hash = new SHA1Managed())
                {
                    var clearBytes = File.ReadAllBytes(filename);
                    var hashedBytes = hash.ComputeHash(clearBytes);
                    return ConvertBytesToHex(hashedBytes);
                }
            }

            static string ConvertBytesToHex(byte[] bytes)
            {
                var sb = new StringBuilder();

                for (var i = 0; i < bytes.Length; i++)
                {
                    sb.Append(bytes[i].ToString("x"));
                }
                return sb.ToString();
            }

            public static void AreEqual(string filename1, string filename2)
            {
                string hash1 = GetFileHash(filename1);
                string hash2 = GetFileHash(filename2);

                Assert.AreEqual(hash1, hash2);
            }
        }
    }


### <a name="programming-interfaces-in-localrunhelperexe"></a>Interfaces de programação em LocalRunHelper. exe

O LocalRunHelper. exe fornece as interfaces de programação para compilação local do U-SQL, execução, etc. As interfaces são listadas da seguinte maneira.

**Qu**

public LocalRunHelper([System.IO.TextWriter messageOutput = null])

|Parâmetro|Type|Descrição|
|---------|----|-----------|
|messageOutput|System.IO.TextWriter|para mensagens de saída, defina como nulo para usar o console|

**Properties**

|Propriedade|Tipo|Descrição|
|--------|----|-----------|
|AlgebraPath|Cadeia de caracteres|O caminho para o arquivo Algebra (o arquivo Algebra é um dos resultados da compilação)|
|CodeBehindReferences|Cadeia de caracteres|Se o script tiver referências de code-behind adicionais, especifique os caminhos separados com '; '|
|CppSdkDir|Cadeia de caracteres|Diretório CppSDK|
|CurrentDir|Cadeia de caracteres|Diretório atual|
|DataRoot|Cadeia de caracteres|Caminho raiz de dados|
|DebuggerMailPath|Cadeia de caracteres|O caminho para o processador de processadores do depurador|
|GenerateUdoRedirect|bool|Se quisermos gerar a configuração de substituição de redirecionamento de carregamento de assembly|
|HasCodeBehind|bool|Se o script tiver code-behind|
|InputDir|Cadeia de caracteres|Diretório para dados de entrada|
|MessagePath|Cadeia de caracteres|Caminho do arquivo de despejo de mensagem|
|OutputDir|Cadeia de caracteres|Diretório para dados de saída|
|Paralelismo|int|Paralelismo para executar a Algebra|
|ParentPid|int|PID do pai no qual o serviço monitora a saída, definido como 0 ou negativo para ignorar|
|ResultPath|Cadeia de caracteres|Caminho do arquivo de despejo de resultado|
|RuntimeDir|Cadeia de caracteres|Diretório de tempo de execução|
|ScriptPath|Cadeia de caracteres|Onde encontrar o script|
|Raso|bool|Compilação superficial ou não|
|TempDir|Cadeia de caracteres|Diretório temporário|
|UseDataBase|Cadeia de caracteres|Especifique o banco de dados a ser usado para o registro de assembly temporário por trás do código, Master por padrão|
|WorkDir|Cadeia de caracteres|Diretório de trabalho preferencial|


**Método**

|Método|Descrição|Voltar|Parâmetro|
|------|-----------|------|---------|
|Public bool docompile ()|Compilar o script U-SQL|Verdadeiro em caso de sucesso| |
|Public bool doexec ()|Executar o resultado compilado|Verdadeiro em caso de sucesso| |
|Public bool DoRun ()|Executar o script U-SQL (compilar + executar)|Verdadeiro em caso de sucesso| |
|Public bool IsValidRuntimeDir (caminho da cadeia de caracteres)|Verifique se o caminho fornecido é um caminho de tempo de execução válido|Verdadeiro para válido|O caminho do diretório de tempo de execução|


## <a name="faq-about-common-issue"></a>Perguntas frequentes sobre o problema comum

### <a name="error-1"></a>Erro 1:
E_CSC_SYSTEM_INTERNAL: Erro interno! Não foi possível carregar o arquivo ou o assembly ' ScopeEngineManaged. dll ' ou uma de suas dependências. Não foi possível encontrar o módulo especificado.

Verifique o seguinte:

- Verifique se você tem um ambiente x64. A plataforma de destino de compilação e o ambiente de teste devem ser x64 **, consulte a etapa 1: Crie C# projeto de teste de unidade** e configuração acima.
- Verifique se você copiou todos os arquivos de dependência em NugetPackage\build\runtime\ para o diretório de trabalho do projeto.


## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre U-SQL, consulte [Introdução à linguagem U-SQL da Análise do Azure Data Lake](data-lake-analytics-u-sql-get-started.md).
* Para registrar em log informações de diagnóstico, consulte [acessando logs de diagnóstico para Azure data Lake Analytics](data-lake-analytics-diagnostic-logs.md).
* Para ver uma consulta mais complexa, consulte [analisar logs do site usando Azure data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Para exibir detalhes do trabalho, consulte [usar o navegador de trabalho e a exibição de trabalho para trabalhos de Azure data Lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md).
* Para usar o modo de exibição de execução de vértice, consulte [usar o modo de exibição de execução de vértice em ferramentas de data Lake para Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
