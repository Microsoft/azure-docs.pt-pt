---
title: Executar e testar as tarefas de U-SQL localmente, utilizando o SDK do Azure Data Lake U-SQL
description: Saiba como executar e testar as tarefas de U-SQL localmente, utilizando a linha de comando e interfaces na estação de trabalho local de programação.
services: data-lake-analytics
ms.service: data-lake-analytics
author: yanacai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.topic: conceptual
ms.date: 03/01/2017
ms.openlocfilehash: 14908225e78b79cb748e712ae23643ddde4a4242
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58089969"
---
# <a name="run-and-test-u-sql-with-azure-data-lake-u-sql-sdk"></a>Executar e testar o U-SQL com o SDK do Azure Data Lake U-SQL

Ao desenvolver scripts U-SQL, é comum para executar e script de teste U-SQL localmente antes do envio-lo para a cloud. O Azure Data Lake fornece um pacote de Nuget chamado SDK do Azure Data Lake U-SQL para este cenário, através do qual pode facilmente aumentar U-SQL execute e teste. Também é possível integrar este teste de U-SQL com o sistema de CI (integração contínua) para automatizar a compilação e teste.

Se se preocupa como manualmente local para executar e depurar o script de U-SQL com as ferramentas de GUI, pode utilizar o Azure Data Lake Tools para Visual Studio para que. Pode saber mais no [aqui](data-lake-analytics-data-lake-tools-local-run.md).

## <a name="install-azure-data-lake-u-sql-sdk"></a>SDK de instalação do Azure Data Lake U-SQL

Pode obter o SDK do Azure Data Lake U-SQL [aqui](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) em Nuget.org. E antes de o utilizar, terá de certificar-se de que têm dependências da seguinte forma.

### <a name="dependencies"></a>Dependências

O SDK do Data Lake U-SQL requer as seguintes dependências:

- [Microsoft .NET Framework 4.6 ou mais recente](https://www.microsoft.com/download/details.aspx?id=17851).
- Microsoft Visual C++ 14 e o Windows SDK 10.0.10240.0 ou mais recente (que é chamado CppSDK neste artigo). Existem duas formas de obter CppSDK:

  - Instale [Visual Studio Community Edition](https://developer.microsoft.com/downloads/vs-thankyou). Terá uma pasta de \Windows Kits\10 sob a pasta de ficheiros de programa – por exemplo, C:\Program Files (x86) \Windows Kits\10\. Também encontrará a versão do SDK do Windows 10 em \Windows Kits\10\Lib. Se não vir estas pastas, reinstale o Visual Studio e certifique-se de que seleciona o SDK do Windows 10 durante a instalação. Se o ter isso instalado com o Visual Studio, o compilador local do U-SQL irá encontrá-lo automaticamente.

    ![Ferramentas do Data Lake para Visual Studio SDK do Windows 10 de execução local](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-windows-10-sdk.png)

  - Instale [Data Lake Tools para Visual Studio](https://aka.ms/adltoolsvs). Pode encontrar o Visual C++ e o Windows SDK previamente incluídas em pacotes de ficheiros em C:\Program Files (x86) \Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\ADL Tools\X.X.XXXX.X\CppSDK. Neste caso, o compilador de local do U-SQL não é possível localizar as dependências automaticamente. Tem de especificar o caminho de CppSDK para ele. Pode copiar os ficheiros para outra localização ou utilizá-la como está.

## <a name="understand-basic-concepts"></a>Compreender os conceitos básicos

### <a name="data-root"></a>Raiz de dados

A pasta de raiz de dados é um "armazenamento local" para a conta de computação local. É equivalente à conta do Azure Data Lake Store de uma conta do Data Lake Analytics. Mudar para uma pasta raiz de dados de diferentes é igual a mudar para uma conta de armazenamento diferentes. Se quiser aceder a dados normalmente partilhados com pastas de raiz de dados diferentes, tem de utilizar caminhos absolutos nos seus scripts. Em alternativa, crie links simbólicos de sistema de ficheiros (por exemplo, **mklink** no NTFS) na pasta raiz de dados para que apontem para os dados compartilhados.

A pasta raiz de dados é utilizada para:

- Store metadados locais, incluindo bases de dados, tabelas, funções com valor de tabela (TVFs) e assemblies.
- Procure os caminhos de entrada e saídos que são definidos como caminhos relativos em U-SQL. Utilizar caminhos relativos torna mais fácil de implementar os seus projetos de U-SQL para o Azure.

### <a name="file-path-in-u-sql"></a>Caminho do ficheiro no U-SQL

Pode utilizar um caminho relativo e um caminho absoluto local em scripts U-SQL. O caminho relativo é relativo ao caminho da pasta de raiz de dados especificado. Recomendamos que utilize "/" como separador de caminho para tornar seus scripts compatível com o lado do servidor. Aqui estão alguns exemplos de caminhos relativos e seus equivalentes caminhos absolutos. Nestes exemplos, C:\LocalRunDataRoot é a pasta de raiz de dados.

|Caminho relativo|Caminho absoluto|
|-------------|-------------|
|/abc/def/input.csv |C:\LocalRunDataRoot\abc\def\input.csv|
|abc/def/input.csv  |C:\LocalRunDataRoot\abc\def\input.csv|
|D:/abc/def/input.csv |D:\abc\def\input.csv|

### <a name="working-directory"></a>Diretório de trabalho

Ao executar o script de U-SQL localmente, um diretório de trabalho é criado durante a compilação sob o diretório de execução atual. Além das saídas de compilação, os arquivos de tempo de execução necessários para execução local será copiada para este diretório de trabalho de cópias de sombra. A pasta de raiz do diretório de trabalho é chamada "ScopeWorkDir" e os ficheiros no diretório de trabalho são os seguintes:

|Diretório/ficheiro|Diretório/ficheiro|Diretório/ficheiro|Definição|Descrição|
|--------------|--------------|--------------|----------|-----------|
|C6A101DDCB470506| | |Cadeia de hash da versão de runtime|Cópia de sombra de arquivos de tempo de execução necessários para execução local|
| |Script_66AE4909AA0ED06C| |Nome do script + cadeia do caminho do script de hash|Saídas de compilação e execução passo de registo|
| | |\_script\_.abr|Saída de compilador|Ficheiro de álgebra|
| | |\_ScopeCodeGen\_.*|Saída de compilador|Gerado o código gerenciado|
| | |\_ScopeCodeGenEngine\_.*|Saída de compilador|Código nativo gerado|
| | |odkazovaná sestavení|Referência ao Assembly|Ficheiros de assemblagem referenciada|
| | |deployed_resources|Implementação de recursos|Arquivos de implantação de recursos|
| | |xxxxxxxx.xxx[1..n]\_\*.*|Registo de execução|Registo de passos de execução|


## <a name="use-the-sdk-from-the-command-line"></a>Utilizar o SDK da linha de comando

### <a name="command-line-interface-of-the-helper-application"></a>Interface de linha de comandos do aplicativo auxiliar

No SDK directory\build\runtime, LocalRunHelper.exe é a aplicação de linha de comandos do programa auxiliar que fornece interfaces para a maioria das funções de execução local usadas. Tenha em atenção que o comando e os comutadores de argumento diferenciam maiúsculas de minúsculas. Invocá-lo:

    LocalRunHelper.exe <command> <Required-Command-Arguments> [Optional-Command-Arguments]

Executar LocalRunHelper.exe sem argumentos ou com o **ajudar** comutador para mostrar as informações de ajuda:

    > LocalRunHelper.exe help

        Command 'help' :  Show usage information
        Command 'compile' :  Compile the script
        Required Arguments :
            -Script param
                    Script File Path
        Optional Arguments :
            -Shallow [default value 'False']
                    Shallow compile

As informações de ajuda:

-  **Comando** dá o nome do comando.  
-  **Necessário argumento** apresenta uma lista de argumentos que tem de ser fornecidos.  
-  **Argumento opcional** apresenta uma lista de argumentos que são opcionais, com valores predefinidos.  Argumentos booleanos opcionais não tem parâmetros e seus aparências dizer negativo no respetivo valor predefinido.

### <a name="return-value-and-logging"></a>Valor de retorno e Registro em log

Devolve o aplicativo de programa auxiliar **0** para o sucesso e **-1** falha. Por predefinição, o auxiliar envia todas as mensagens para a consola atual. No entanto, a maioria dos comandos suporta o **path_to_log_file - MessageOut** argumento opcional que redireciona as saídas para um ficheiro de registo.

### <a name="environment-variable-configuring"></a>Configurar a variável de ambiente

Local do U-SQL execute às suas necessidades de uma raiz de dados especificada como conta de armazenamento local, bem como um caminho de CppSDK especificado para as dependências. Pode ambos definir o argumento na variável de ambiente de linha de comandos ou definida para eles.

- Definir o **SCOPE_CPP_SDK** variável de ambiente.

    Se obtiver o Microsoft Visual C++ e o SDK do Windows ao instalar as ferramentas do Data Lake para Visual Studio, certifique-se de que tem a seguinte pasta:

        C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK

    Definir uma nova variável de ambiente chamada **SCOPE_CPP_SDK** para apontar para este diretório. Ou copie a pasta para o outro local e especifique **SCOPE_CPP_SDK** que.

    Além de definir a variável de ambiente, pode especificar a **- CppSDK** argumento quando estiver a utilizar a linha de comandos. Este argumento substitui a variável de ambiente de CppSDK padrão.

- Definir o **LOCALRUN_DATAROOT** variável de ambiente.

    Definir uma nova variável de ambiente chamada **LOCALRUN_DATAROOT** que aponta para a raiz de dados.

    Além de definir a variável de ambiente, pode especificar a **- DataRoot** argumento com o caminho de raiz de dados quando estiver a utilizar uma linha de comando. Este argumento substitui a variável de ambiente de raiz de dados padrão. Tem de adicionar esse argumento para cada linha de comandos que está a executar para que pode substituir a variável de ambiente de raiz de dados predefinida para todas as operações.

### <a name="sdk-command-line-usage-samples"></a>Exemplos de utilização de linha de comando do SDK

#### <a name="compile-and-run"></a>Compilar e executar

O **executar** comando é utilizado para compilar o script e, em seguida, execute os resultados compilados. Os argumentos da linha de comandos são uma combinação de os do **compilar** e **executar**.

    LocalRunHelper run -Script path_to_usql_script.usql [optional_arguments]

Seguem-se para os argumentos opcionais **executar**:


|Argumento|Valor predefinido|Descrição|
|--------|-------------|-----------|
|-CodeBehind|Falso|O script tem. cs code-behind|
|-CppSDK| |Diretório de CppSDK|
|-DataRoot| Variável de ambiente de DataRoot|DataRoot para execução, o padrão a variável de ambiente 'LOCALRUN_DATAROOT' local|
|-MessageOut| |Mensagens na consola para um ficheiro de cópia de segurança|
|-Paralelo|1|Executar o plano com o paralelismo especificado|
|-References| |Lista de caminhos para os assemblies de referência adicionais ou ficheiros de dados de code-behind, separados por ";"|
|-UdoRedirect|Falso|Gerar a configuração de redirecionamento do assembly Udo|
|-UseDatabase|master|Base de dados a utilizar para o code-behind do registo de assembly temporário|
|-Verbose|Falso|Mostrar saídas detalhadas de tempo de execução|
|-WorkDir|Diretório atual|Diretório para a utilização de compilador e saídas|
|-RunScopeCEP|0|ScopeCEP de modo a utilizar|
|-ScopeCEPTempPath|temp|Temp caminho a utilizar para dados de transmissão em fluxo|
|-OptFlags| |Lista separada por vírgulas de sinalizadores de otimizador|


Segue-se um exemplo:

    LocalRunHelper run -Script d:\test\test1.usql -WorkDir d:\test\bin -CodeBehind -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB –Parallel 5 -Verbose

Além de combinar **compilar** e **executar**, pode compilar e executar os executáveis compilados separadamente.

#### <a name="compile-a-u-sql-script"></a>Compilar um script de U-SQL

O **compilar** comando é utilizado para compilar um script de U-SQL para executáveis.

    LocalRunHelper compile -Script path_to_usql_script.usql [optional_arguments]

Seguem-se para os argumentos opcionais **compilar**:


|Argumento|Descrição|
|--------|-----------|
| -Code-behind [valor predefinido 'False']|O script tem. cs code-behind|
| -CppSDK [valor predefinido ']|Diretório de CppSDK|
| -DataRoot [valor predefinido 'Variável de ambiente de DataRoot']|DataRoot para execução, o padrão a variável de ambiente 'LOCALRUN_DATAROOT' local|
| -MessageOut [valor predefinido ']|Mensagens na consola para um ficheiro de cópia de segurança|
| -Faz referência [valor predefinido ']|Lista de caminhos para os assemblies de referência adicionais ou ficheiros de dados de code-behind, separados por ";"|
| -Raso [valor predefinido 'False']|Compilação rasa|
| -UdoRedirect [valor predefinido 'False']|Gerar a configuração de redirecionamento do assembly Udo|
| -UseDatabase [valor predefinido 'Mestra']|Base de dados a utilizar para o code-behind do registo de assembly temporário|
| -WorkDir [valor predefinido 'Diretório atual']|Diretório para a utilização de compilador e saídas|
| -RunScopeCEP [valor predefinido '0']|ScopeCEP de modo a utilizar|
| -ScopeCEPTempPath [valor predefinido 'temp']|Temp caminho a utilizar para dados de transmissão em fluxo|
| -OptFlags [valor predefinido ']|Lista separada por vírgulas de sinalizadores de otimizador|


Aqui estão alguns exemplos de utilização.

Compile um script de U-SQL:

    LocalRunHelper compile -Script d:\test\test1.usql

Compilar um script de U-SQL e defina a pasta raiz de dados. Tenha em atenção que isto irá substituir a variável de ambiente de conjunto.

    LocalRunHelper compile -Script d:\test\test1.usql –DataRoot c:\DataRoot

Compilar um script de U-SQL e definir um diretório de trabalho, o assembly de referência e a base de dados:

    LocalRunHelper compile -Script d:\test\test1.usql -WorkDir d:\test\bin -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB

#### <a name="execute-compiled-results"></a>Executar os resultados compilados

O **executar** comando é utilizado para executar os resultados compilados.   

    LocalRunHelper execute -Algebra path_to_compiled_algebra_file [optional_arguments]

Seguem-se para os argumentos opcionais **executar**:

|Argumento|Valor predefinido|Descrição|
|--------|-------------|-----------|
|-DataRoot | '' |Raiz de dados para a execução de metadados. Assume como predefinição o **LOCALRUN_DATAROOT** variável de ambiente.|
|-MessageOut | '' |Mensagens na consola para um ficheiro de cópia de segurança.|
|-Paralelo | '1' |Indicador de executar os passos de execução local gerados com o nível de paralelismo especificado.|
|-Verbose | 'False' |Indicador para Mostrar saídas detalhadas de tempo de execução.|

Eis um exemplo de utilização:

    LocalRunHelper execute -Algebra d:\test\workdir\C6A101DDCB470506\Script_66AE4909AA0ED06C\__script__.abr –DataRoot c:\DataRoot –Parallel 5


## <a name="use-the-sdk-with-programming-interfaces"></a>Utilize o SDK com interfaces de programação

As interfaces de programação estão localizadas no LocalRunHelper.exe. Pode usá-los para integrar a funcionalidade do SDK do U-SQL e a estrutura de teste do c# para dimensionar o seu teste de local de script de U-SQL. Neste artigo, vou usar o padrão C# projeto teste de unidade para mostrar como utilizar essas interfaces para testar o script de U-SQL.

### <a name="step-1-create-c-unit-test-project-and-configuration"></a>Passo 1: Criar C# projeto e a configuração de teste de unidade

- Criar um projeto de teste de unidade em C# através do ficheiro > novo > projeto > em Visual C# > teste > projeto de teste de unidade.
- Adicione LocalRunHelper.exe como uma referência para o projeto. O LocalRunHelper.exe está localizado em \build\runtime\LocalRunHelper.exe no pacote Nuget.

    ![SDK do Azure Data Lake U-SQL Adicionar referência](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-add-reference.png)

- SDK de U-SQL **apenas** ambiente suporte x64, certifique-se de definir o destino da plataforma de compilação como x64. Pode definir que por meio da propriedade projeto > criar > destino de plataforma.

    ![Configurar o SDK do Azure Data Lake U-SQL x64 projeto](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-configure-x64.png)

- Certifique-se definir o seu ambiente de teste como x64. No Visual Studio, pode configurá-lo por meio do teste > configurações de teste > predefinido a arquitetura do processador > x64.

    ![Configurar o SDK do Azure Data Lake U-SQL x64 ambiente de teste](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-configure-test-x64.png)

- Certifique-se copiar todos os ficheiros de dependência em NugetPackage\build\runtime\ ao diretório que é normalmente em ProjectFolder\bin\x64\Debug de trabalho do projeto.

### <a name="step-2-create-u-sql-script-test-case"></a>Passo 2: Criar o caso de teste do script de U-SQL

Segue-se o código de exemplo para teste de script de U-SQL. Para fins de teste, terá de preparar scripts, ficheiros de entrada e de ficheiros de saída esperado.

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


### <a name="programming-interfaces-in-localrunhelperexe"></a>Interfaces de programação em LocalRunHelper.exe

LocalRunHelper.exe fornece as interfaces de programação de compilação local do U-SQL, executar, etc. As interfaces são listadas da seguinte forma.

**Construtor**

public LocalRunHelper([System.IO.TextWriter messageOutput = null])

|Parâmetro|Type|Descrição|
|---------|----|-----------|
|messageOutput|System.IO.TextWriter|para mensagens de saída, definido como nulo para utilizar a consola|

**Propriedades**

|Propriedade|Tipo|Descrição|
|--------|----|-----------|
|AlgebraPath|string|O caminho para o ficheiro de álgebra (arquivo de álgebra é um dos resultados da compilação)|
|CodeBehindReferences|string|Se o script tiver adicional code-behind de referências, especifique os caminhos separados por ";"|
|CppSdkDir|string|Diretório de CppSDK|
|CurrentDir|string|Diretório atual|
|DataRoot|string|Caminho de raiz de dados|
|DebuggerMailPath|string|O caminho para o depurador mailslot|
|GenerateUdoRedirect|booleano|Se quisermos gerar a configuração de substituição de redirecionamento de carregamento de assemblagem|
|HasCodeBehind|booleano|Se o script contém code-behind|
|InputDir|string|Diretório de dados de entrada|
|MessagePath|string|Caminho do arquivo de despejo de mensagem|
|OutputDir|string|Diretório de dados de saída|
|Paralelismo|int|Paralelismo para executar a álgebra|
|ParentPid|int|PID do pai no qual o serviço monitoriza para sair, definido como 0 ou negativos para ignorar|
|ResultPath|string|Caminho do ficheiro de informação de estado de resultado|
|RuntimeDir|string|Diretório de tempo de execução|
|ScriptPath|string|Onde encontrar o script|
|Raso|booleano|Superficialmente a compilação ou não|
|TempDir|string|Diretório temporário|
|UseDataBase|string|Especifique a base de dados a utilizar para o code-behind do registo de assembly temporário, mestre por padrão|
|WorkDir|string|Diretório de trabalho preferencial|


**Método**

|Método|Descrição|Voltar|Parâmetro|
|------|-----------|------|---------|
|public bool DoCompile()|Compilar o script de U-SQL|VERDADEIRO em caso de êxito| |
|bool pública DoExec()|O resultado compilado de execução|VERDADEIRO em caso de êxito| |
|bool pública DoRun()|Execute o script de U-SQL (compilação + execução)|VERDADEIRO em caso de êxito| |
|bool pública IsValidRuntimeDir (caminho de cadeia de caracteres)|Verifique se o caminho especificado é o caminho de tempo de execução válido|Verdadeiro para válido|O caminho do diretório de tempo de execução|


## <a name="faq-about-common-issue"></a>FAQ sobre o problema comum

### <a name="error-1"></a>Erro 1:
E_CSC_SYSTEM_INTERNAL: Erro interno! Não foi possível carregar o ficheiro ou a assemblagem 'ScopeEngineManaged.dll' ou uma de suas dependências. Não foi possível encontrar o módulo especificado.

Verifique o seguinte:

- Certifique-se de que tem o x64 ambiente. A plataforma de destino de compilação e o ambiente de teste deve ser o x64, consulte o **passo 1: Criar C# projeto e a configuração de teste de unidade** acima.
- Certifique-se de que copiou todos os ficheiros de dependência em NugetPackage\build\runtime\ ao diretório de trabalho do projeto.


## <a name="next-steps"></a>Passos Seguintes

* Para saber mais sobre U-SQL, consulte [Introdução à linguagem U-SQL da Análise do Azure Data Lake](data-lake-analytics-u-sql-get-started.md).
* Para registar informações de diagnóstico, veja [aceder a registos de diagnóstico para o Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md).
* Para ver uma consulta mais complexa, consulte [analisar registos de sites com o Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Para ver os detalhes da tarefa, consulte [utilizar o Browser de trabalho e a vista de tarefas para tarefas de Azure Data Lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md).
* Para utilizar a vista de execução de vértice, consulte [utilizar a vista de execução de vértice no Data Lake Tools para Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
