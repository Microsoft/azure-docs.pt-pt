---
title: Executar empregos U-SQL localmente - Azure Data Lake U-SQL SDK
description: Aprenda a executar e testar trabalhos U-SQL localmente usando a linha de comando e interfaces de programação na sua estação de trabalho local.
ms.service: data-lake-analytics
author: yanacai
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 03/01/2017
ms.openlocfilehash: 8fb60e62a63bfc4562f19d483dc84c99c37676b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92215540"
---
# <a name="run-and-test-u-sql-with-azure-data-lake-u-sql-sdk"></a>Executar e testar U-SQL com Azure Data Lake U-SQL SDK

Ao desenvolver o script U-SQL, é comum executar e testar o script U-SQL localmente antes de submetê-lo à nuvem. O Azure Data Lake fornece um pacote Nuget chamado Azure Data Lake U-SQL SDK para este cenário, através do qual você pode facilmente escalar u-SQL run e testar. Também é possível integrar este teste U-SQL com o sistema CI (Integração Contínua) para automatizar a compilação e o teste.

Se se preocupa em como executar manualmente localmente e depurar o script U-SQL com ferramenta GUI, então pode usar ferramentas do Lago de Dados Azure para o Estúdio Visual para isso. Pode aprender mais [daqui.](data-lake-analytics-data-lake-tools-local-run.md)

## <a name="install-azure-data-lake-u-sql-sdk"></a>Instalar Azure Data Lake U-SQL SDK

Você pode obter o Azure Data Lake U-SQL SDK [aqui](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) em Nuget.org. E antes de usá-lo, você precisa ter certeza de que tem dependências como se segue.

### <a name="dependencies"></a>Dependências

O Data Lake U-SQL SDK requer as seguintes dependências:

- [Microsoft .NET Framework 4.6 ou mais recente](https://www.microsoft.com/download/details.aspx?id=17851).
- Microsoft Visual C++ 14 e Windows SDK 10.0.10240.0 ou mais recente (que se chama CppSDK neste artigo). Há duas maneiras de obter CppSDK:

  - Instalar [a Edição Comunitária do Estúdio Visual.](https://developer.microsoft.com/downloads/vs-thankyou) Terá uma pasta \Windows Kits\10 na pasta Ficheiros de Programas -- por exemplo, C:\Program Files (x86)\Windows Kits\10\. Também encontrará a versão SDK do Windows 10 em \Windows Kits\10\Lib. Se não vir estas pastas, reinstale o Visual Studio e certifique-se de selecionar o Windows 10 SDK durante a instalação. Se tiver isto instalado com o Visual Studio, o compilador local U-SQL irá encontrá-lo automaticamente.

    ![Ferramentas do Lago de Dados para Estúdio Visual Gerido localMente Windows 10 SDK](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-windows-10-sdk.png)

  - Instale [ferramentas do Lago de Dados para o Estúdio Visual.](https://aka.ms/adltoolsvs) Pode encontrar os ficheiros Visual C++ e Windows SDK pré-embalados em `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\ADL Tools\X.X.XXXX.X\CppSDK.`

    Neste caso, o compilador local U-SQL não consegue encontrar as dependências automaticamente. Tens de especificar o caminho do CppSDK para isso. Pode copiar os ficheiros para outro local ou usá-lo como está.

## <a name="understand-basic-concepts"></a>Compreender conceitos básicos

### <a name="data-root"></a>Raiz de dados

A pasta de raiz de dados é uma "loja local" para a conta de computação local. É equivalente à conta da Azure Data Lake Store de uma conta data lake analytics. Mudar para uma pasta de raiz de dados diferente é como mudar para uma conta de loja diferente. Se pretender aceder a dados partilhados com diferentes pastas de raiz de dados, deve utilizar caminhos absolutos nos seus scripts. Ou, criar links simbólicos do sistema de ficheiros (por exemplo, **mklink** no NTFS) sob a pasta de raiz de dados para apontar para os dados partilhados.

A pasta de raiz de dados é utilizada para:

- Armazenar metadados locais, incluindo bases de dados, tabelas, funções de valor de tabela (TVFs) e conjuntos.
- Procure os caminhos de entrada e saída que são definidos como caminhos relativos em U-SQL. A utilização de caminhos relativos facilita a implementação dos seus projetos U-SQL para o Azure.

### <a name="file-path-in-u-sql"></a>Caminho de arquivo em U-SQL

Você pode usar um caminho relativo e um caminho absoluto local em scripts U-SQL. O caminho relativo é relativo ao caminho da pasta de raiz de dados especificado. Recomendamos que utilize "/" como separador de caminhos para tornar os seus scripts compatíveis com o lado do servidor. Aqui estão alguns exemplos de caminhos relativos e seus caminhos absolutos equivalentes. Nestes exemplos, C:\LocalRunDataRoot é a pasta raiz de dados.

|Caminho relativo|Caminho absoluto|
|-------------|-------------|
|/abc/def/input.csv |C:\LocalRunDataRoot\abc\def\input.csv|
|abc/def/input.csv  |C:\LocalRunDataRoot\abc\def\input.csv|
|D:/abc/def/input.csv |D:\abc\def\input.csv|

### <a name="working-directory"></a>Diretório de trabalho

Ao executar o script U-SQL localmente, um diretório de trabalho é criado durante a compilação sob o diretório atual. Além das saídas de compilação, os ficheiros de tempo de execução necessários para a execução local serão copiados para este diretório de trabalho. A pasta raiz do diretório de trabalho chama-se "ScopeWorkDir" e os ficheiros no diretório de trabalho são os seguintes:

|Diretório/arquivo|Diretório/arquivo|Diretório/arquivo|Definição|Description|
|--------------|--------------|--------------|----------|-----------|
|C6A101DDCB470506| | |Cadeia de haxixe da versão runtime|Cópia-sombra dos ficheiros de tempo de execução necessários para a execução local|
| |Script_66AE4909AA0ED06C| |Nome do script + cadeia de haxixe do caminho do script|Saídas de compilação e registo de etapas de execução|
| | |\_script \_ .abr|Saída do compilador|Arquivo de álgebra|
| | |\_ScopeCodeGen \_ .*|Saída do compilador|Código gerido gerado|
| | |\_ScopeCodeGenEngine \_ .*|Saída do compilador|Código nativo gerado|
| | |conjuntos referenciados|Referência de montagem|Ficheiros de montagem referenciados|
| | |deployed_resources|Implantação de recursos|Ficheiros de implementação de recursos|
| | |xxxxxxxx.xxx[1.n] \_ \* .*|Registo de execução|Registo das etapas de execução|

## <a name="use-the-sdk-from-the-command-line"></a>Utilize o SDK a partir da linha de comando

### <a name="command-line-interface-of-the-helper-application"></a>Interface de linha de comando da aplicação helper

No diretório SDK\build\runtime, LocalRunHelper.exe é a aplicação de ajuda de linha de comando que fornece interfaces para a maioria das funções de gestão local comumente utilizadas. Note que tanto o comando como os interruptores de argumento são sensíveis a maiísso. Para invocá-lo:

```console
LocalRunHelper.exe <command> <Required-Command-Arguments> [Optional-Command-Arguments]
```

Executar LocalRunHelper.exe sem argumentos ou com o interruptor **de ajuda** para mostrar a informação de ajuda:

```console
> LocalRunHelper.exe help
    Command 'help' :  Show usage information
    Command 'compile' :  Compile the script
    Required Arguments :
        -Script param
                Script File Path
    Optional Arguments :
        -Shallow [default value 'False']
                Shallow compile
```

Na informação de ajuda:

- **O comando** dá o nome do comando.  
- **Argumento obrigatório** lista os argumentos que devem ser fornecidos.  
- **Argumento opcional** lista argumentos que são opcionais, com valores predefinidos.  Os argumentos opcionais booleanos não têm parâmetros, e as suas aparências significam negativa para o seu valor padrão.

### <a name="return-value-and-logging"></a>Valor de retorno e registo

A aplicação helper devolve **0** para o sucesso e **-1** por falha. Por predefinição, o ajudante envia todas as mensagens para a consola atual. No entanto, a maioria dos comandos suportam o argumento opcional **-MessageOut path_to_log_file** que redireciona as saídas para um ficheiro de registo.

### <a name="environment-variable-configuring"></a>Configuração variável do ambiente

A execução local U-SQL necessita de uma raiz de dados especificada como conta de armazenamento local, bem como um caminho específico de CppSDK para dependências. Ambos podem definir o argumento na linha de comando ou definir a variável ambiente para eles.

- Desaprote a variável ambiente **SCOPE_CPP_SDK.**

  Se obter o Microsoft Visual C++ e o Windows SDK instalando ferramentas data lake para estúdio visual, verifique se tem a seguinte pasta:

    `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK`

  Defina uma nova variável ambiental chamada **SCOPE_CPP_SDK** para apontar para este diretório. Ou copie a pasta para o outro local e especifique **SCOPE_CPP_SDK** como tal.

  Além de definir a variável ambiente, pode especificar o argumento **-CppSDK** quando estiver a utilizar a linha de comando. Este argumento substitui a variável ambiental CppSDK padrão.

- Desaprote a variável ambiente **LOCALRUN_DATAROOT.**

  Defina uma nova variável ambiental chamada **LOCALRUN_DATAROOT** que aponta para a raiz de dados.

  Além de definir a variável ambiente, pode especificar o argumento **-DataRoot** com o caminho raiz de dados quando estiver a utilizar uma linha de comando. Este argumento substitui a variável do ambiente de raiz de dados padrão. Tem de adicionar este argumento a todas as linhas de comando que está a executar para que possa substituir a variável de ambiente de raiz de dados padrão para todas as operações.

### <a name="sdk-command-line-usage-samples"></a>Amostras de utilização da linha de comando SDK

#### <a name="compile-and-run"></a>Compilar e correr

O comando **de execução** é utilizado para compilar o script e, em seguida, executar resultados compilados. Os seus argumentos de linha de comando são uma combinação daqueles que são de **compilar** e **executar.**

```console
LocalRunHelper run -Script path_to_usql_script.usql [optional_arguments]
```

Seguem-se argumentos opcionais a **executar:**

|Argumento|Valor predefinido|Description|
|--------|-------------|-----------|
|-CodeBehind|Falso|O guião tem .cs código por trás|
|-CppSDK| |Diretório CppSDK|
|- DataRoot| Variável do ambiente DataRoot|DataRoot para execução local, padrão para variável ambiente 'LOCALRUN_DATAROOT'|
|-MensagemOut| |Despeje mensagens na consola para um ficheiro|
|-Paralelo|1|Executar o plano com o paralelismo especificado|
|-Referências| |Lista de caminhos para conjuntos de referência extra ou ficheiros de dados de código por trás, separados por ';'|
|-UdoRedirect|Falso|Gerar reorientar o conjunto Udo config|
|-UseDatabase|master|Base de dados para utilizar para código por trás do registo de montagem temporária|
|-Verbose|Falso|Mostrar saídas detalhadas do tempo de execução|
|-WorkDir|Diretório atual|Diretório para utilização e saídas do compilador|
|-RunScopeCEP|0|Modo ScopeCEP para usar|
|-ScopeCEPTempPath|temp|Caminho temporário a utilizar para dados de streaming|
|-OptFlags| |Lista separada por vírgula de bandeiras optimizadoras|

Eis um exemplo:

`LocalRunHelper run -Script d:\test\test1.usql -WorkDir d:\test\bin -CodeBehind -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB –Parallel 5 -Verbose`

Além de combinar **compilar** e **executar,** pode compilar e executar os executáveis compilados separadamente.

#### <a name="compile-a-u-sql-script"></a>Compilar um script U-SQL

O comando **de compilação** é usado para compilar um script U-SQL para executáveis.

```console
LocalRunHelper compile -Script path_to_usql_script.usql [optional_arguments]
```

Seguem-se argumentos opcionais para **a compilação:**

|Argumento|Description|
|--------|-----------|
| -CodeBehind [valor predefinido 'Falso']|O guião tem .cs código por trás|
| -CppSDK [valor predefinido '']|Diretório CppSDK|
| -DataRoot [valor predefinido 'DataRoot ambiente variável']|DataRoot para execução local, padrão para variável ambiente 'LOCALRUN_DATAROOT'|
| -MessageOut [valor predefinido '']|Despeje mensagens na consola para um ficheiro|
| -Referências [valor predefinido '']|Lista de caminhos para conjuntos de referência extra ou ficheiros de dados de código por trás, separados por ';'|
| -Superficial [valor predefinido 'Falso']|Compilação rasa|
| -UdoRedirect [valor predefinido 'Falso']|Gerar reorientar o conjunto Udo config|
| -UseDatabase [valor predefinido 'master']|Base de dados para utilizar para código por trás do registo de montagem temporária|
| -WorkDir [valor predefinido 'Current Directory']|Diretório para utilização e saídas do compilador|
| -RunScopeCEP [valor predefinido '0']|Modo ScopeCEP para usar|
| -ScopeCEPTempPath [valor predefinido 'temporário']|Caminho temporário a utilizar para dados de streaming|
| -OptFlags [valor predefinido '']|Lista separada por vírgula de bandeiras optimizadoras|

Aqui estão alguns exemplos de uso.

Compilar um script U-SQL:

```console
LocalRunHelper compile -Script d:\test\test1.usql
```

Compilar um script U-SQL e definir a pasta raiz de dados. Note que isto substituirá a variável ambiente definida.

```console
LocalRunHelper compile -Script d:\test\test1.usql –DataRoot c:\DataRoot
```

Compilar um script U-SQL e definir um diretório de trabalho, conjunto de referência e base de dados:

```console
LocalRunHelper compile -Script d:\test\test1.usql -WorkDir d:\test\bin -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB
```

#### <a name="execute-compiled-results"></a>Executar resultados compilados

O comando **de execução** é utilizado para executar resultados compilados.

```console
LocalRunHelper execute -Algebra path_to_compiled_algebra_file [optional_arguments]
```

Seguem-se argumentos opcionais para **execução:**

|Argumento|Valor predefinido|Description|
|--------|-------------|-----------|
|- DataRoot | '' |Raiz de dados para a execução de metadados. Está em incumprimento da variável **ambiente LOCALRUN_DATAROOT.**|
|-MensagemOut | '' |Despeje mensagens na consola para um ficheiro.|
|-Paralelo | '1' |Indicador para executar os passos gerados localmente com o nível de paralelismo especificado.|
|-Verbose | 'Falso' |Indicador para mostrar saídas detalhadas do tempo de funcionação.|

Aqui está um exemplo de utilização:

```console
LocalRunHelper execute -Algebra d:\test\workdir\C6A101DDCB470506\Script_66AE4909AA0ED06C\__script__.abr –DataRoot c:\DataRoot –Parallel 5
```

## <a name="use-the-sdk-with-programming-interfaces"></a>Utilize o SDK com interfaces de programação

As interfaces de programação estão todas localizadas no LocalRunHelper.exe. Pode utilizá-los para integrar a funcionalidade do U-SQL SDK e da estrutura de teste C# para escalar o teste local do script U-SQL. Neste artigo, utilizarei o projeto de teste padrão da unidade C# para mostrar como usar estas interfaces para testar o seu script U-SQL.

### <a name="step-1-create-c-unit-test-project-and-configuration"></a>Passo 1: Criar projeto e configuração de teste de unidade C#

- Crie um projeto de teste de unidade C# através de file > Projeto de Nova > > Visual C# > Teste de > Projeto de Teste unitário.
- Adicione LocalRunHelper.exe como referência para o projeto. O LocalRunHelper.exe está localizado em \build\runtime\LocalRunHelper.exe no pacote Nuget.

   ![Azure Data Lake U-SQL SDK Adicionar Referência](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-add-reference.png)

- U-SQL SDK **suporta apenas** o ambiente x64, certifique-se de definir o alvo da plataforma como x64. Você pode definir isso através do projeto propriedade > construir > plataforma alvo.

   ![Projeto Azure Data Lake U-SQL SDK Configure x64](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-configure-x64.png)

- Certifique-se de que define o seu ambiente de teste como x64. No Estúdio Visual, pode defini-lo através de Definições de Teste > de Teste > Arquitetura de Processador Padrão > x64.

   ![Azure Data Lake U-SQL SDK Configure x64 Test Environment](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-configure-test-x64.png)

- Certifique-se de copiar todos os ficheiros de dependência em NugetPackage\build\runtime\ para projetar o diretório de trabalho que normalmente está no ProjectFolder\bin\x64\Debug.

### <a name="step-2-create-u-sql-script-test-case"></a>Passo 2: Criar caso de teste de script U-SQL

Abaixo está o código de amostra para o teste de script U-SQL. Para testes, precisa de preparar scripts, ficheiros de entrada e ficheiros de saída esperados.

```usql
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
```

### <a name="programming-interfaces-in-localrunhelperexe"></a>Interfaces de programação em LocalRunHelper.exe

LocalRunHelper.exe fornece as interfaces de programação para compilação local U-SQL, corrida, etc. As interfaces estão listadas da seguinte forma.

### <a name="constructor"></a>Construtor

público LocalRunHelper ([System.IO.TextWriter messageOutput = nulo])

|Parâmetro|Tipo|Description|
|---------|----|-----------|
|saída de mensagens|System.io.TextWriter|para mensagens de saída, definido para nula para usar consola|

### <a name="properties"></a>Propriedades

|Propriedade|Tipo|Description|
|--------|----|-----------|
|AlgebraPath|string|O caminho para o ficheiro de álgebra (o ficheiro de álgebra é um dos resultados da compilação)|
|CodeBehindReferences|string|Se o guião tiver código adicional por trás de referências, especifique os caminhos separados com ';'|
|CppSdkDir|string|Diretório CppSDK|
|CurrentDir|string|Diretório atual|
|DataRoot|string|Caminho da raiz de dados|
|DebuggerMailPath|string|O caminho para debugger mailslot|
|GenerateUdoRedirect|bool|Se quisermos gerar reorientação de carregamento de montagem sobrepõe-se config|
|HasCodeBehind|bool|Se o script tiver código por trás|
|InputDir|string|Diretório para dados de entrada|
|MensagemPata|string|Caminho do arquivo de despejo de mensagem|
|Resultados|string|Diretório para dados de saída|
|Paralelismo|int|Paralelismo para executar a álgebra|
|ParentPid|int|PID do progenitor em que o serviço monitoriza a saída, definido para 0 ou negativo para ignorar|
|Caminho do Resultado|string|Percurso de arquivo de despejo de resultados|
|RuntimeDir|string|Diretório de tempo de execução|
|ScriptPath|string|Onde encontrar o roteiro|
|Raso|bool|Compilação superficial ou não|
|TempDir|string|Diretório temporário|
|UseDataBase|string|Especifique a base de dados para usar para código por trás do registo de montagem temporária, mestre por padrão|
|WorkDir|string|Diretório de trabalho preferido|

### <a name="method"></a>Método

|Método|Descrição|Retorno|Parâmetro|
|------|-----------|------|---------|
|bool público DoCompile()|Compilar o script U-SQL|Verdade no sucesso| |
|bool público DoExec()|Execute o resultado compilado|Verdade no sucesso| |
|bool público DoRun()|Execute o script U-SQL (Compile + Execute)|Verdade no sucesso| |
|bool público IsValidRuntimeDir (caminho das cordas)|Verifique se o caminho dado é um caminho de execução válido|Verdadeiro para válido|O caminho do diretório de tempo de execução|

## <a name="faq-about-common-issue"></a>FAQ sobre questões comuns

### <a name="error-1"></a>Erro 1

E_CSC_SYSTEM_INTERNAL: Erro interno! Não foi possível carregar ficheiros ou montagem 'ScopeEngineManaged.dll' ou uma das suas dependências. O módulo especificado não foi encontrado.

Verifique o seguinte:

- Certifique-se de que tem ambiente x64. A plataforma-alvo de construção e o ambiente de teste devem ser x64, consulte o **Passo 1: Criar projeto de teste de unidade C# e configuração** acima.
- Certifique-se de que copiou todos os ficheiros de dependência no âmbito do NugetPackage\build\runtime\ para projetar o diretório de trabalho.

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre U-SQL, consulte [Introdução à linguagem U-SQL da Análise do Azure Data Lake](data-lake-analytics-u-sql-get-started.md).
- Para registar informações de diagnóstico, consulte [aceder aos registos de diagnósticos do Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md).
- Para ver uma consulta mais complexa, consulte [os registos do site da Análise utilizando o Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
- Para ver detalhes do trabalho, consulte [use job browser e job view para trabalhos Azure Data Lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md).
- Para utilizar a vista de execução do vértice, consulte [a vista de execução do vertex em ferramentas do lago de dados para estúdio visual](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
