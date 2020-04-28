---
title: Executar empregos U-SQL localmente - Azure Data Lake U-SQL SDK
description: Aprenda a executar e testar trabalhos U-SQL localmente usando a linha de comando e interfaces de programação na sua estação de trabalho local.
services: data-lake-analytics
ms.service: data-lake-analytics
author: yanacai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.topic: conceptual
ms.date: 03/01/2017
ms.openlocfilehash: 51d9060eaf4b30c696ef2a3b5f798a31e2f2a98a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "71309689"
---
# <a name="run-and-test-u-sql-with-azure-data-lake-u-sql-sdk"></a>Executar e testar U-SQL com Azure Data Lake U-SQL SDK

Ao desenvolver o script U-SQL, é comum executar e testar o script U-SQL localmente antes de submetê-lo à nuvem. O Azure Data Lake fornece um pacote Nuget chamado Azure Data Lake U-SQL SDK para este cenário, através do qual você pode facilmente escalar o run e o teste U-SQL. Também é possível integrar este teste U-SQL com o sistema CI (Integração Contínua) para automatizar a compilação e teste.

Se se preocupa com a forma de executar manualmente localmente e desbugifar o script U-SQL com ferramentas GUI, então pode utilizar ferramentas de Lago de Dados Azure para estúdio visual para isso. Pode aprender mais a partir [daqui.](data-lake-analytics-data-lake-tools-local-run.md)

## <a name="install-azure-data-lake-u-sql-sdk"></a>Instale O Lago de Dados Azure U-SQL SDK

Você pode obter o Azure Data Lake U-SQL SDK [aqui](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) em Nuget.org. E antes de usá-lo, tem de ter a certeza de que tem dependências da seguinte forma.

### <a name="dependencies"></a>Dependências

O Data Lake U-SQL SDK requer as seguintes dependências:

- [Microsoft .NET Framework 4.6 ou mais recente](https://www.microsoft.com/download/details.aspx?id=17851).
- Microsoft Visual C++ 14 e Windows SDK 10.0.10240.0 ou mais recente (que se chama CppSDK neste artigo). Há duas maneiras de obter CppSDK:

  - Instale [a Edição Comunitária do Estúdio Visual.](https://developer.microsoft.com/downloads/vs-thankyou) Terá uma pasta \Windows Kits\10 no âmbito da pasta Ficheiros do Programa --, por exemplo, C:\Program Files (x86)\Windows Kits\10\. Também encontrará a versão SDK do Windows 10 em \Windows Kits\10\Lib. Se não vir estas pastas, reinstale o Visual Studio e certifique-se de selecionar o Windows 10 SDK durante a instalação. Se tiver isto instalado no Visual Studio, o compilador local U-SQL irá encontrá-lo automaticamente.

    ![Ferramentas do Lago de Dados para Visual Studio gerido localmente Windows 10 SDK](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-windows-10-sdk.png)

  - Instale [ferramentas do Lago de Dados para estúdio visual.](https://aka.ms/adltoolsvs) Pode encontrar os ficheiros Visual C++ e Windows SDK pré-embalados em C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\ADL Tools\X.X.XXXX.X\CppSDK. Neste caso, o compilador local U-SQL não consegue encontrar as dependências automaticamente. Tens de especificar o caminho do CppSDK para o fazer. Pode copiar os ficheiros para outro local ou usá-lo como está.

## <a name="understand-basic-concepts"></a>Compreender conceitos básicos

### <a name="data-root"></a>Raiz de dados

A pasta de raiz de dados é uma "loja local" para a conta de cálculo local. É equivalente à conta da Loja do Lago De dados Azure de uma conta data lake analytics. Mudar para uma pasta de raiz de dados diferente é como mudar para uma conta de loja diferente. Se pretender aceder a dados partilhados com diferentes pastas de raiz de dados, deve utilizar caminhos absolutos nos seus scripts. Ou criar ligações simbólicas do sistema de ficheiros (por exemplo, **mklink** no NTFS) sob a pasta da raiz de dados para apontar para os dados partilhados.

A pasta da raiz de dados é utilizada para:

- Armazenar metadados locais, incluindo bases de dados, tabelas, funções de valor de mesa (TVFs) e conjuntos.
- Procure os caminhos de entrada e saída que são definidos como caminhos relativos em U-SQL. A utilização de caminhos relativos facilita a implementação dos seus projetos U-SQL para o Azure.

### <a name="file-path-in-u-sql"></a>Caminho de arquivo em U-SQL

Você pode usar um caminho relativo e um caminho absoluto local em scripts U-SQL. O caminho relativo é relativo ao caminho especificado da pasta da raiz de dados. Recomendamos que utilize "/" como separador de caminho para tornar os seus scripts compatíveis com o lado do servidor. Aqui estão alguns exemplos de caminhos relativos e seus caminhos absolutos equivalentes. Nestes exemplos, C:\LocalRunDataRoot é a pasta raiz de dados.

|Caminho relativo|Caminho absoluto|
|-------------|-------------|
|/abc/def/input.csv |C:\LocalRunDataRoot\abc\def\input.csv|
|abc/def/input.csv  |C:\LocalRunDataRoot\abc\def\input.csv|
|D:/abc/def/input.csv |D:\abc\def\input.csv|

### <a name="working-directory"></a>Diretório de trabalho

Ao executar o script U-SQL localmente, um diretório de trabalho é criado durante a compilação sob o diretório atual de execução. Além das saídas de compilação, os ficheiros de tempo de execução necessários para a execução local serão a sombra copiada para este diretório de trabalho. A pasta raiz do diretório de trabalho chama-se "ScopeWorkDir" e os ficheiros sob o diretório de trabalho são os seguintes:

|Diretório/arquivo|Diretório/arquivo|Diretório/arquivo|Definição|Descrição|
|--------------|--------------|--------------|----------|-----------|
|C6A101DCB470506| | |Cadeia hash da versão runtime|Cópia sombra dos ficheiros runtime necessários para a execução local|
| |Script_66AE4909AA0ED06C| |Nome script + cadeia hash do caminho script|Saídas de compilação e registo de etapas de execução|
| | |\_script\_.abr|Saída de compilador|Arquivo de álgebra|
| | |\_Scopecodegen\_.*|Saída de compilador|Código gerido gerado|
| | |\_ScopeCodeGenEngine\_.*|Saída de compilador|Código nativo gerado|
| | |conjuntos referenciados|Referência da montagem|Arquivos de montagem referenciados|
| | |deployed_resources|Implantação de recursos|Ficheiros de implementação de recursos|
| | |xxxxxxxx.xxx[1.n]\_\*.*|Registo de execução|Registo de etapas de execução|


## <a name="use-the-sdk-from-the-command-line"></a>Use o SDK da linha de comando

### <a name="command-line-interface-of-the-helper-application"></a>Interface de linha de comando da aplicação auxiliar

Sob o diretório SDK\build\runtime, LocalRunHelper.exe é a aplicação auxiliar de linha de comando que fornece interfaces para a maioria das funções de execução local comumente usadas. Note que tanto o comando como os interruptores de argumento são sensíveis a casos. Para invocá-lo:

    LocalRunHelper.exe <command> <Required-Command-Arguments> [Optional-Command-Arguments]

Executar LocalRunHelper.exe sem argumentos ou com o interruptor de **ajuda** para mostrar a informação de ajuda:

    > LocalRunHelper.exe help

        Command 'help' :  Show usage information
        Command 'compile' :  Compile the script
        Required Arguments :
            -Script param
                    Script File Path
        Optional Arguments :
            -Shallow [default value 'False']
                    Shallow compile

Na informação de ajuda:

-  **O comando** dá o nome do comando.  
-  **O argumento exigido** enumera os argumentos que devem ser fornecidos.  
-  **Argumento opcional** lista argumentos que são opcionais, com valores predefinidos.  Argumentos opcionais booleanos não têm parâmetros, e as suas aparências significam negativo para o seu valor padrão.

### <a name="return-value-and-logging"></a>Valor de retorno e exploração madeireira

A aplicação de ajudante devolve **0** para o sucesso e **-1** para o fracasso. Por predefinição, o ajudante envia todas as mensagens para a consola atual. No entanto, a maioria dos comandos suportam o **-MessageOut path_to_log_file** argumento opcional que redireciona as saídas para um ficheiro de registo.

### <a name="environment-variable-configuring"></a>Configuração variável ambiental

A execução local u-SQL precisa de uma raiz de dados especificada como conta de armazenamento local, bem como um caminho de CppSDK especificado para dependências. Ambos podem definir o argumento na linha de comando ou definir a variável ambiente para eles.

- Desloque a variável **ambiente SCOPE_CPP_SDK.**

    Se obtém o Microsoft Visual C++ e o Windows SDK instalando ferramentas do Data Lake para estúdio visual, verifique se tem a seguinte pasta:

        C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK

    Defina uma nova variável ambiental chamada **SCOPE_CPP_SDK** para apontar para este diretório. Ou copie a pasta para o outro local e especifique **SCOPE_CPP_SDK** assim.

    Além de definir a variável ambiental, pode especificar o argumento **-CppSDK** quando estiver a usar a linha de comando. Este argumento substitui a variável ambiente padrão do CppSDK.

- Desloque a variável **ambiente LOCALRUN_DATAROOT.**

    Defina uma nova variável ambiental chamada **LOCALRUN_DATAROOT** que aponta para a raiz de dados.

    Além de definir a variável ambiental, pode especificar o argumento **-DataRoot** com o caminho da raiz de dados quando estiver a utilizar uma linha de comando. Este argumento substitui a variável de ambiente de raiz de dados padrão. Você precisa adicionar este argumento a todas as linhas de comando que você está executando para que você possa sobrepor a variável de ambiente de raiz de dados padrão para todas as operações.

### <a name="sdk-command-line-usage-samples"></a>Amostras de utilização da linha de comando SDK

#### <a name="compile-and-run"></a>Compilar e correr

O comando de **execução** é usado para compilar o script e, em seguida, executar resultados compilados. Os seus argumentos de linha de comando são uma combinação dos que são de **compilação** e **execução.**

    LocalRunHelper run -Script path_to_usql_script.usql [optional_arguments]

Seguem-se os argumentos opcionais para **a execução:**


|Argumento|Valor predefinido|Descrição|
|--------|-------------|-----------|
|-CodeBehind|Falso|O script tem código .cs por trás|
|-CppSDK| |Diretório CppSDK|
|-DataRoot| Variável ambiente DataRoot|DataRoot para execução local, padrão para variável ambiente 'LOCALRUN_DATAROOT'|
|-MessageOut| |Despejar mensagens na consola para um ficheiro|
|-Paralelo|1|Executar o plano com o paralelismo especificado|
|-Referências| |Lista de caminhos para conjuntos de referência extra ou ficheiros de dados de código por trás, separados por ';';|
|-UdoRedirect|Falso|Gerar config redirecionamento de montagem Udo|
|-UseDatabase|master|Base de dados para usar para código por trás do registo temporário de montagem|
|-Verbose|Falso|Mostrar saídas detalhadas a partir do tempo de execução|
|-WorkDir|Diretório atual|Diretório para utilização e saídas de compiladores|
|-RunScopeCEP|0|Modo ScopeCEP a utilizar|
|-ScopeCEPTempPath|temperatura|Caminho temporário para usar para transmitir dados|
|-OptFlags| |Lista separada da vírposta de bandeiras otimizadoras|


Segue-se um exemplo:

    LocalRunHelper run -Script d:\test\test1.usql -WorkDir d:\test\bin -CodeBehind -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB –Parallel 5 -Verbose

Além de combinar **compilação** e **executar,** pode compilar e executar os executáveis compilados separadamente.

#### <a name="compile-a-u-sql-script"></a>Compilar um script U-SQL

O comando **de compilação** é usado para compilar um script U-SQL para executáveis.

    LocalRunHelper compile -Script path_to_usql_script.usql [optional_arguments]

Seguem-se os argumentos opcionais para **a compilação:**


|Argumento|Descrição|
|--------|-----------|
| -CodeBehind [valor predefinido 'Falso']|O script tem código .cs por trás|
| -CppSDK [valor predefinido '']|Diretório CppSDK|
| -DataRoot [valor predefinido 'DataRoot environment variable']|DataRoot para execução local, padrão para variável ambiente 'LOCALRUN_DATAROOT'|
| -MessageOut [valor predefinido']|Despejar mensagens na consola para um ficheiro|
| -Referências [valor predefinido '']|Lista de caminhos para conjuntos de referência extra ou ficheiros de dados de código por trás, separados por ';';|
| -Superficial [valor predefinido 'Falso']|Compilação rasa|
| -UdoRedirect [valor predefinido 'Falso']|Gerar config redirecionamento de montagem Udo|
| -UseDatabase [valor predefinido 'master']|Base de dados para usar para código por trás do registo temporário de montagem|
| -WorkDir [valor padrão 'Atual Directório']|Diretório para utilização e saídas de compiladores|
| -RunScopeCEP [valor predefinido '0']|Modo ScopeCEP a utilizar|
| -ScopeCEPTempPath [valor predefinido 'temporário']|Caminho temporário para usar para transmitir dados|
| -OptFlags [valor predefinido']|Lista separada da vírposta de bandeiras otimizadoras|


Aqui estão alguns exemplos de uso.

Compile um script U-SQL:

    LocalRunHelper compile -Script d:\test\test1.usql

Compile um script U-SQL e detete a pasta da raiz de dados. Note que isto irá sobrepor a variável ambiente definida.

    LocalRunHelper compile -Script d:\test\test1.usql –DataRoot c:\DataRoot

Compile um script U-SQL e detete tede um diretório de trabalho, conjunto de referência e base de dados:

    LocalRunHelper compile -Script d:\test\test1.usql -WorkDir d:\test\bin -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB

#### <a name="execute-compiled-results"></a>Executar resultados compilados

O comando **de execução** é utilizado para executar resultados compilados.   

    LocalRunHelper execute -Algebra path_to_compiled_algebra_file [optional_arguments]

Seguem-se os argumentos opcionais para **a execução:**

|Argumento|Valor predefinido|Descrição|
|--------|-------------|-----------|
|-DataRoot | '' |Raiz de dados para execução de metadados. Não se aplica à variável **ambiente LOCALRUN_DATAROOT.**|
|-MessageOut | '' |Despeje as mensagens na consola para um ficheiro.|
|-Paralelo | '1' |Indicador para executar os passos gerados de execução local com o nível de paralelismo especificado.|
|-Verbose | 'Falso' |Indicador para mostrar saídas detalhadas a partir do tempo de execução.|

Aqui está um exemplo de uso:

    LocalRunHelper execute -Algebra d:\test\workdir\C6A101DDCB470506\Script_66AE4909AA0ED06C\__script__.abr –DataRoot c:\DataRoot –Parallel 5


## <a name="use-the-sdk-with-programming-interfaces"></a>Utilize o SDK com interfaces de programação

As interfaces de programação estão todas localizadas no LocalRunHelper.exe. Pode usá-los para integrar a funcionalidade do U-SQL SDK e da estrutura de teste C# para escalar o seu teste local de script U-SQL. Neste artigo, vou usar o projeto de teste da unidade C# padrão para mostrar como usar estas interfaces para testar o seu script U-SQL.

### <a name="step-1-create-c-unit-test-project-and-configuration"></a>Passo 1: Criar o projeto de teste e configuração da unidade C#

- Crie um projeto de teste de unidade C# através do File > New > Project > Visual C# > Test > Test Test Project.
- Adicione LocalRunHelper.exe como referência para o projeto. O LocalRunHelper.exe está localizado em \build\runtime\LocalRunHelper.exe no pacote Nuget.

    ![Referência de adição de SDK do Lago de Dados Azure U-SQL](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-add-reference.png)

- U-SQL SDK **apenas** suporta o ambiente x64, certifique-se de definir o alvo da plataforma de construção como x64. Pode definir isso através do project Property > Construir > plataforma.

    ![Projeto Azure Data Lake U-SQL SDK Configure x64](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-configure-x64.png)

- Certifique-se de que o seu ambiente de teste é x64. No Estúdio Visual, pode defini-lo através de definições de teste de > teste > Arquitetura padrão do processador > x64.

    ![Azure Data Lake U-SQL SDK Configure x64 Test Environment](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-configure-test-x64.png)

- Certifique-se de copiar todos os ficheiros de dependência sob nugetPackage\build\runtime\ para projectworking diretório que normalmente está no ProjectFolder\bin\x64\Debug.

### <a name="step-2-create-u-sql-script-test-case"></a>Passo 2: Criar caso de teste de script U-SQL

Abaixo está o código de amostra para o teste de script U-SQL. Para testes, é necessário preparar scripts, ficheiros de entrada e ficheiros de saída esperados.

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

LocalRunHelper.exe fornece as interfaces de programação para compilação local U-SQL, executar, etc. As interfaces estão listadas da seguinte forma.

**Construtor**

localRunHelper público ([System.IO.TextWriter messageOutput = nulo])

|Parâmetro|Tipo|Descrição|
|---------|----|-----------|
|mensagemSaída de saída|System.IO.TextWriter|para mensagens de saída, definidas como nulas para usar consola|

**Propriedades**

|Propriedade|Tipo|Descrição|
|--------|----|-----------|
|AlgebraPath|string|O caminho para o ficheiro de álgebra (ficheiro álgebra é um dos resultados da compilação)|
|CodeBehindReferences|string|Se o script tiver um código adicional por trás de referências, especifique os caminhos separados com ';'|
|CppSdkDir|string|Diretório CppSDK|
|Atualdir|string|Diretório atual|
|DataRoot|string|Caminho raiz de dados|
|DebuggerMailPath|string|O caminho para debugger maillot|
|GeraudoRedirect|bool|Se queremos gerar redirecionamento de carga de montagem config|
|HasCodeBehind|bool|Se o script tem código por trás|
|EntradaDir|string|Diretório para dados de entrada|
|MensagemPath|string|Caminho de ficheiro de despejo de mensagem|
|OutputDir|string|Diretório para dados de saída|
|Paralelismo|int|Paralelismo para correr a álgebra|
|ParentPid|int|PID do progenitor em que o serviço monitoriza para sair, definido para 0 ou negativo para ignorar|
|Caminho dos Resultados|string|Resultado do caminho do ficheiro de despejo|
|RuntimeDir|string|Diretório runtime|
|Roteiro|string|Onde encontrar o guião|
|Raso|bool|Compilação rasa ou não|
|TempDir|string|Diretório temporário|
|UseDataBase|string|Especificar a base de dados a utilizar para código por detrás do registo temporário de montagem, mestre por padrão|
|WorkDir|string|Diretório de trabalho preferido|


**Método**

|Método|Descrição|Devolução|Parâmetro|
|------|-----------|------|---------|
|público bool DoCompile()|Compilar o script U-SQL|Verdade no sucesso| |
|público bool DoExec()|Executar o resultado compilado|Verdade no sucesso| |
|público bool DoRun()|Execute o script U-SQL (Compile + Executar)|Verdade no sucesso| |
|bool público IsValidRuntimeDir (caminho de corda)|Verifique se o caminho dado é válido|Verdadeiro para válido|O caminho do diretório runtime|


## <a name="faq-about-common-issue"></a>FAQ sobre questão comum

### <a name="error-1"></a>Erro 1:
E_CSC_SYSTEM_INTERNAL: Erro interno! Não foi possível carregar ficheiros ou montagem 'ScopeEngineManaged.dll' ou uma das suas dependências. O módulo especificado não foi encontrado.

Por favor, verifique o seguinte:

- Certifique-se de ter um ambiente x64. A plataforma alvo de construção e o ambiente de teste devem ser x64, consulte o **Passo 1: Criar o projeto de teste da unidade C# e a configuração** acima.
- Certifique-se de ter copiado todos os ficheiros de dependência no âmbito do NugetPackage\build\runtime\ para o diretório de trabalho do projeto.


## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre U-SQL, consulte [Introdução à linguagem U-SQL da Análise do Azure Data Lake](data-lake-analytics-u-sql-get-started.md).
* Para registar informações de diagnóstico, consulte aceder aos registos de [diagnóstico para o Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md).
* Para ver uma consulta mais complexa, consulte os registos do site Da Análise do [Lago de Dados Do Azure.](data-lake-analytics-analyze-weblogs.md)
* Para ver detalhes de trabalho, consulte [Use Job Browser e Job View para trabalhos de Azure Data Lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md).
* Para utilizar a vista de execução do vértice, consulte Use a vista de [execução vertex em ferramentas](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)de data lake para estúdio visual .
