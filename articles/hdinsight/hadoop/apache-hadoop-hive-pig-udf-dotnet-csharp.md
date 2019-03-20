---
title: Utilize C# com o Apache Hive e Apache Pig no Apache Hadoop no HDInsight - Azure
description: Saiba como utilizar C# definido pelo utilizador Udfs (funções) com o Apache Hive e Apache Pig, transmissão em fluxo no Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: hrasheed
ms.openlocfilehash: df8196389ecb92229ff9495002c4bce394d7c048
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58202712"
---
# <a name="use-c-user-defined-functions-with-apache-hive-and-apache-pig-streaming-on-apache-hadoop-in-hdinsight"></a>Utilize C# funções definidas pelo utilizador com o Apache Hive e Apache Pig, transmissão em fluxo no Apache Hadoop no HDInsight

Saiba como utilizar C# definido pelo utilizador Udfs (funções) com o Apache Hive e Apache Pig no HDInsight.

> [!IMPORTANT]
> Os passos neste documento funcionam com os clusters do HDInsight baseados em Windows e Linux. O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, consulte [controlo de versões de componente de HDInsight](../hdinsight-component-versioning.md).

Tanto o Hive e Pig pode passar dados em aplicativos externos para processamento. Este processo é conhecido como _transmissão em fluxo_. Quando utilizar uma aplicação .NET, os dados são passados para o aplicativo no STDIN e a aplicação devolve os resultados no STDOUT. Para ler e gravar em STDIN e STDOUT, pode usar `Console.ReadLine()` e `Console.WriteLine()` partir de uma aplicação de consola.

## <a name="prerequisites"></a>Pré-requisitos

* Familiaridade com a escrita e criação de código c# que tenha como destino o .NET Framework 4.5.

    * Utilize qualquer IDE que quiser. Recomendamos [Visual Studio](https://www.visualstudio.com/vs) 2015, 2017, ou [Visual Studio Code](https://code.visualstudio.com/). Os passos neste documento utilizam o Visual Studio 2017.

* Uma forma de carregar ficheiros de .exe para o cluster e executar trabalhos de Pig e Hive. Recomendamos que o Data Lake Tools para Visual Studio, o Azure PowerShell e CLI clássica do Azure. Os passos neste documento utiliza o Data Lake Tools para Visual Studio para carregar os ficheiros e executar o exemplo de consulta do Hive.

    Para obter informações sobre outras formas de executar consultas e tarefas do Pig o Hive, veja os documentos seguintes:

    * [Utilizar o Apache Hive com o HDInsight](hdinsight-use-hive.md)

    * [Utilizar o Apache Pig com o HDInsight](hdinsight-use-pig.md)

* Um Hadoop num cluster do HDInsight. Para obter mais informações sobre como criar um cluster, consulte [criar um cluster do HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="net-on-hdinsight"></a>.NET no HDInsight

* __HDInsight baseado em Linux__ clusters com [Mono (https://mono-project.com) ](https://mono-project.com) para executar aplicações de .NET. Versão mono 4.2.1 está incluída com o HDInsight versão 3.6.

    Para obter mais informações sobre a Mono compatibilidade com versões do .NET Framework, consulte [Mono compatibility](https://www.mono-project.com/docs/about-mono/compatibility/).

    Para utilizar uma versão específica do Mono, consulte a [instalar ou atualizar o Mono](../hdinsight-hadoop-install-mono.md) documento.

* __HDInsight baseado em Windows__ clusters utilizam o Microsoft .NET CLR para executar aplicações de .NET.

Para obter mais informações sobre a versão do .NET framework e Mono incluído com as versões do HDInsight, consulte [versões de componente de HDInsight](../hdinsight-component-versioning.md).

## <a name="create-the-c-projects"></a>Criar o C\# projetos

### <a name="apache-hive-udf"></a>Apache Hive UDF

1. Abra o Visual Studio e crie uma solução. Para o tipo de projeto, selecione **aplicação de consola (.NET Framework)** e dê o nome do novo projeto **HiveCSharp**.

    > [!IMPORTANT]
    > Selecione __.NET Framework 4.5__ se estiver a utilizar um cluster do HDInsight baseado em Linux. Para obter mais informações sobre a Mono compatibilidade com versões do .NET Framework, consulte [Mono compatibility](https://www.mono-project.com/docs/about-mono/compatibility/).

2. Substitua o conteúdo do **Program.cs** com o código a seguir:

    ```csharp
    using System;
    using System.Security.Cryptography;
    using System.Text;
    using System.Threading.Tasks;

    namespace HiveCSharp
    {
        class Program
        {
            static void Main(string[] args)
            {
                string line;
                // Read stdin in a loop
                while ((line = Console.ReadLine()) != null)
                {
                    // Parse the string, trimming line feeds
                    // and splitting fields at tabs
                    line = line.TrimEnd('\n');
                    string[] field = line.Split('\t');
                    string phoneLabel = field[1] + ' ' + field[2];
                    // Emit new data to stdout, delimited by tabs
                    Console.WriteLine("{0}\t{1}\t{2}", field[0], phoneLabel, GetMD5Hash(phoneLabel));
                }
            }
            /// <summary>
            /// Returns an MD5 hash for the given string
            /// </summary>
            /// <param name="input">string value</param>
            /// <returns>an MD5 hash</returns>
            static string GetMD5Hash(string input)
            {
                // Step 1, calculate MD5 hash from input
                MD5 md5 = System.Security.Cryptography.MD5.Create();
                byte[] inputBytes = System.Text.Encoding.ASCII.GetBytes(input);
                byte[] hash = md5.ComputeHash(inputBytes);

                // Step 2, convert byte array to hex string
                StringBuilder sb = new StringBuilder();
                for (int i = 0; i < hash.Length; i++)
                {
                    sb.Append(hash[i].ToString("x2"));
                }
                return sb.ToString();
            }
        }
    }
    ```

3. Crie o projeto.

### <a name="apache-pig-udf"></a>Apache Pig UDF

1. Abra o Visual Studio e crie uma solução. Para o tipo de projeto, selecione **aplicação de consola**e dê o nome do novo projeto **PigUDF**.

2. Substitua os conteúdos do **Program.cs** ficheiro com o código a seguir:

    ```csharp
    using System;

    namespace PigUDF
    {
        class Program
        {
            static void Main(string[] args)
            {
                string line;
                // Read stdin in a loop
                while ((line = Console.ReadLine()) != null)
                {
                    // Fix formatting on lines that begin with an exception
                    if(line.StartsWith("java.lang.Exception"))
                    {
                        // Trim the error info off the beginning and add a note to the end of the line
                        line = line.Remove(0, 21) + " - java.lang.Exception";
                    }
                    // Split the fields apart at tab characters
                    string[] field = line.Split('\t');
                    // Put fields back together for writing
                    Console.WriteLine(String.Join("\t",field));
                }
            }
        }
    }
    ```

    Esse código analisa as linhas enviadas a partir do Pig e reformata linhas que começam com `java.lang.Exception`.

3. Guarde **Program.cs**e, em seguida, crie o projeto.

## <a name="upload-to-storage"></a>Carregar para o armazenamento

1. No Visual Studio, abra **Explorador de servidores**.

2. Expanda **Azure** e, em seguida, expanda **HDInsight**.

3. Se lhe for pedido, introduza as credenciais de subscrição do Azure e, em seguida, clique em **sessão**.

4. Expanda o cluster do HDInsight que pretende implementar esta aplicação. Uma entrada com o texto __(conta de armazenamento predefinida)__ está listado.

    ![Explorador de servidores que mostra a conta de armazenamento para o cluster](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/storage.png)

    * Se esta entrada pode ser expandida, estiver a utilizar um __conta de armazenamento do Azure__ como armazenamento predefinido para o cluster. Para ver os ficheiros no armazenamento padrão para o cluster, expanda a entrada e, em seguida, faça duplo clique nas __(contentor predefinido)__.

    * Se esta entrada não pode ser expandida, estiver a utilizar __armazenamento do Azure Data Lake__ como armazenamento predefinido para o cluster. Para ver os ficheiros no armazenamento padrão para o cluster, clique duas vezes o __(conta de armazenamento predefinida)__ entrada.

6. Para carregar os ficheiros de .exe, utilize um dos seguintes métodos:

   * Se utilizar um __conta de armazenamento do Azure__, clique no ícone de carregamento e, em seguida, navegue para o **bin\debug** pasta para o **HiveCSharp** projeto. Por fim, selecione o **HiveCSharp.exe** do ficheiro e clique em **Ok**.

       ![carregar ícone](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/upload.png)
    
   * Se utilizar __armazenamento do Azure Data Lake__, uma área vazia na listagem de ficheiros com o botão direito e, em seguida, selecione __carregar__. Por fim, selecione o **HiveCSharp.exe** do ficheiro e clique em **aberto**.

     Uma vez a __HiveCSharp.exe__ carregamento for concluído, repita o processo de carregamento para o __PigUDF.exe__ ficheiro.

## <a name="run-an-apache-hive-query"></a>Executar uma consulta Apache Hive

1. No Visual Studio, abra **Explorador de servidores**.

2. Expanda **Azure** e, em seguida, expanda **HDInsight**.

3. Com o botão direito do cluster que implementou a **HiveCSharp** aplicação para e, em seguida, selecione **escrever uma consulta do Hive**.

4. Utilize o seguinte texto para a consulta do Hive:

    ```hiveql
    -- Uncomment the following if you are using Azure Storage
    -- add file wasb:///HiveCSharp.exe;
    -- Uncomment the following if you are using Azure Data Lake Storage Gen1
    -- add file adl:///HiveCSharp.exe;
    -- Uncomment the following if you are using Azure Data Lake Storage Gen2
    -- add file abfs:///HiveCSharp.exe;

    SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'HiveCSharp.exe' AS
    (clientid string, phoneLabel string, phoneHash string)
    FROM hivesampletable
    ORDER BY clientid LIMIT 50;
    ```

    > [!IMPORTANT]
    > Anule os comentários a `add file` instrução que corresponde ao tipo de armazenamento de predefinido utilizado para o seu cluster.

    Esta consulta seleciona os `clientid`, `devicemake`, e `devicemodel` campos do `hivesampletable`e transmite os campos para o aplicativo HiveCSharp.exe. A consulta espera que o aplicativo para retornar três campos, o que são armazenados como `clientid`, `phoneLabel`, e `phoneHash`. A consulta também espera encontrar HiveCSharp.exe na raiz do contentor de armazenamento predefinido.

5. Clique em **submeter** para submeter a tarefa para o cluster do HDInsight. O **resumo da tarefa do Hive** é aberta a janela.

6. Clique em **Atualize** para atualizar o resumo até **estado da tarefa** é alterado para **concluído**. Para ver o resultado da tarefa, clique em **saída da tarefa**.

## <a name="run-an-apache-pig-job"></a>Executar uma tarefa de Apache Pig

1. Utilize o SSH para ligar ao cluster do HDInsight. Por exemplo, `ssh sshuser@mycluster-ssh.azurehdinsight.net`. Para obter mais informações, consulte [withHDInsight utilizar o SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)

2. Utilize um o seguinte comando para iniciar a linha de comandos do Pig:

        pig

    > [!IMPORTANT]
    > Se estiver a utilizar um cluster baseado em Windows, utilize em vez disso, os seguintes comandos:
    > ```
    > cd %PIG_HOME%
    > bin\pig
    > ```

    A `grunt>` prompt é exibido.

3. Introduza o seguinte para executar uma tarefa Pig que utiliza a aplicação do .NET Framework:

        DEFINE streamer `PigUDF.exe` CACHE('/PigUDF.exe');
        LOGS = LOAD '/example/data/sample.log' as (LINE:chararray);
        LOG = FILTER LOGS by LINE is not null;
        DETAILS = STREAM LOG through streamer as (col1, col2, col3, col4, col5);
        DUMP DETAILS;

    O `DEFINE` instrução cria um alias da `streamer` para as aplicações de pigudf.exe, e `CACHE` carrega do armazenamento de predefinido para o cluster. Mais tarde, `streamer` é utilizado com o `STREAM` operador para processar as linhas únicas contidas no registo e retornar os dados como uma série de colunas.

    > [!NOTE]
    > O nome da aplicação que é utilizado para transmissão em fluxo tem de ser rodeado pela \` (backtick) de caracteres quando um alias, e "(aspas) quando utilizado com `SHIP`.

4. Depois de introduzir a última linha, a tarefa deve começar. Ele devolve resultados semelhantes ao seguinte texto:

        (2012-02-03 20:11:56 SampleClass5 [WARN] problem finding id 1358451042 - java.lang.Exception)
        (2012-02-03 20:11:56 SampleClass5 [DEBUG] detail for id 1976092771)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1317358561)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1737534798)
        (2012-02-03 20:11:56 SampleClass7 [DEBUG] detail for id 1475865947)

## <a name="next-steps"></a>Passos Seguintes

Neste documento, aprendeu como utilizar uma aplicação .NET Framework a partir do Hive e Pig no HDInsight. Se gostaria de saber como utilizar Python com Hive e Pig, veja [utilizar Python com o Apache Hive e Apache Pig no HDInsight](python-udf-hdinsight.md).

Para outras formas de utilizar o Pig e Hive e para saber como utilizar o MapReduce, consulte os seguintes documentos:

* [Utilizar o Apache Hive com o HDInsight](hdinsight-use-hive.md)
* [Utilizar o Apache Pig com o HDInsight](hdinsight-use-pig.md)
* [Utilizar o MapReduce com o HDInsight](hdinsight-use-mapreduce.md)
