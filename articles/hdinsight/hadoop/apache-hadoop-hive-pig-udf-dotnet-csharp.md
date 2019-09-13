---
title: Usar C# com Apache Hive e Apache Pig no Apache Hadoop no HDInsight – Azure
description: Saiba como usar C# o UDF (funções definidas pelo usuário) com o Apache Hive e o Pig de streaming do Apache no Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: hrasheed
ms.openlocfilehash: 4e6825b40700fe3bd24631094a2a6060d94524cc
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70882298"
---
# <a name="use-c-user-defined-functions-with-apache-hive-and-apache-pig-on-apache-hadoop-in-hdinsight"></a>Usar C# funções definidas pelo usuário com Apache Hive e Apache Pig no Apache Hadoop no HDInsight

Saiba como usar C# o UDF (funções definidas pelo usuário) com o Apache Hive e o Apache Pig no HDInsight.

> [!IMPORTANT]
> As etapas neste documento funcionam com clusters HDInsight baseados em Linux e Windows. O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, consulte [controle de versão de componente do HDInsight](../hdinsight-component-versioning.md).

Tanto o hive quanto o Pig podem passar dados para aplicativos externos para processamento. Esse processo é conhecido como _streaming_. Ao usar um aplicativo .NET, os dados são passados para o aplicativo em STDIN e o aplicativo retorna os resultados em STDOUT. Para ler e gravar em stdin e stdout, você pode usar `Console.ReadLine()` e `Console.WriteLine()` de um aplicativo de console.

## <a name="prerequisites"></a>Pré-requisitos

* Familiaridade com a escrita e a C# criação de código direcionados .NET Framework 4,5.

    * Use qualquer IDE que desejar. Recomendamos o [Visual Studio](https://www.visualstudio.com/vs) 2015, 2017 ou [Visual Studio Code](https://code.visualstudio.com/). As etapas neste documento usam o Visual Studio 2017.

* Uma maneira de carregar arquivos. exe no cluster e executar trabalhos do Pig e do hive. Recomendamos as ferramentas de Data Lake para Visual Studio, Azure PowerShell e CLI clássica do Azure. As etapas neste documento usam as ferramentas de Data Lake para o Visual Studio para carregar os arquivos e executar a consulta de hive de exemplo.

    Para obter informações sobre outras maneiras de executar consultas do hive e trabalhos do Pig, consulte os seguintes documentos:

    * [Usar o Apache Hive com o HDInsight](hdinsight-use-hive.md)

    * [Usar o Apache Pig com o HDInsight](hdinsight-use-pig.md)

* Um Hadoop no cluster HDInsight. Para obter mais informações sobre como criar um cluster, consulte [criar um cluster HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="net-on-hdinsight"></a>.NET no HDInsight

* Clusters __HDInsight baseados em Linux__ usando [mono (https://mono-project.com) ](https://mono-project.com) para executar aplicativos .net. A versão do mono 4.2.1 está incluída no HDInsight versão 3,6.

    Para obter mais informações sobre a compatibilidade mono com versões .NET Framework, consulte [compatibilidade mono](https://www.mono-project.com/docs/about-mono/compatibility/).

* Os clusters __HDInsight baseados no Windows__ usam o Microsoft .NET CLR para executar aplicativos .net.

Para obter mais informações sobre a versão do .NET Framework e do mono incluído com versões do HDInsight, consulte [versões de componente do hdinsight](../hdinsight-component-versioning.md).

## <a name="create-the-c-projects"></a>Criar projetos C\#

### <a name="apache-hive-udf"></a>UDF Apache Hive

1. Abra o Visual Studio e crie uma solução. Para o tipo de projeto, selecione **aplicativo de console (.NET Framework)** e nomeie o novo projeto **HiveCSharp**.

    > [!IMPORTANT]
    > Selecione __.NET Framework 4,5__ se você estiver usando um cluster HDInsight baseado em Linux. Para obter mais informações sobre a compatibilidade mono com versões .NET Framework, consulte [compatibilidade mono](https://www.mono-project.com/docs/about-mono/compatibility/).

2. Substitua o conteúdo de **Program.cs** pelo código a seguir:

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

### <a name="apache-pig-udf"></a>UDF do Apache Pig

1. Abra o Visual Studio e crie uma solução. Para o tipo de projeto, selecione **aplicativo de console**e nomeie o novo projeto **PigUDF**.

2. Substitua o conteúdo do arquivo **Program.cs** pelo código a seguir:

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

    Esse código analisa as linhas enviadas de Pig e reformata as linhas que começam com `java.lang.Exception`.

3. Salve **Program.cs**e, em seguida, compile o projeto.

## <a name="upload-to-storage"></a>Carregar para o armazenamento

1. No Visual Studio, abra **Gerenciador de servidores**.

2. Expanda **Azure** e, em seguida, expanda **HDInsight**.

3. Se solicitado, insira suas credenciais de assinatura do Azure e, em seguida, clique em **entrar**.

4. Expanda o cluster HDInsight no qual você deseja implantar esse aplicativo. Uma entrada com o texto __(conta de armazenamento padrão)__ é listada.

    ![Gerenciador de Servidores mostrando a conta de armazenamento para o cluster](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/hdinsight-storage-account.png)

    * Se essa entrada puder ser expandida, você estará usando uma __conta de armazenamento do Azure__ como armazenamento padrão para o cluster. Para exibir os arquivos no armazenamento padrão do cluster, expanda a entrada e clique duas vezes no __(contêiner padrão)__ .

    * Se essa entrada não puder ser expandida, você estará usando __Azure data Lake Storage__ como o armazenamento padrão para o cluster. Para exibir os arquivos no armazenamento padrão do cluster, clique duas vezes na entrada __(conta de armazenamento padrão)__ .

6. Para carregar os arquivos. exe, use um dos seguintes métodos:

   * Se estiver usando uma __conta de armazenamento do Azure__, clique no ícone carregar e navegue até a pasta **bin\Debug** para o projeto **HiveCSharp** . Por fim, selecione o arquivo **HiveCSharp. exe** e clique em **OK**.

       ![ícone carregar](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/hdinsight-upload-icon.png)
    
   * Se estiver usando __Azure data Lake Storage__, clique com o botão direito do mouse em uma área vazia na listagem de arquivos e, em seguida, selecione __carregar__. Por fim, selecione o arquivo **HiveCSharp. exe** e clique em **abrir**.

     Depois que o carregamento do __HiveCSharp. exe__ for concluído, repita o processo de carregamento para o arquivo __PigUDF. exe__ .

## <a name="run-an-apache-hive-query"></a>Executar uma consulta Apache Hive

1. No Visual Studio, abra **Gerenciador de servidores**.

2. Expanda **Azure** e, em seguida, expanda **HDInsight**.

3. Clique com o botão direito do mouse no cluster no qual você implantou o aplicativo **HiveCSharp** e, em seguida, selecione **gravar uma consulta do hive**.

4. Use o seguinte texto para a consulta de Hive:

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
    > Remova a marca `add file` de comentário da instrução que corresponde ao tipo de armazenamento padrão usado para o cluster.

    Essa consulta seleciona os `clientid`campos `devicemake`, e `devicemodel` de `hivesampletable`e passa os campos para o aplicativo HiveCSharp. exe. A consulta espera que o aplicativo retorne três campos, que são armazenados `clientid`como `phoneLabel`, e `phoneHash`. A consulta também espera localizar HiveCSharp. exe na raiz do contêiner de armazenamento padrão.

5. Clique em **Enviar** para enviar o trabalho para o cluster HDInsight. A janela **Resumo do trabalho do hive** é aberta.

6. Clique em **Atualizar** para atualizar o resumo até que o **status do trabalho** seja alterado para **concluído**. Para exibir a saída do trabalho, clique em **saída do trabalho**.

## <a name="run-an-apache-pig-job"></a>Executar um trabalho do Apache Pig

1. Use o SSH para se conectar ao cluster HDInsight. Por exemplo, `ssh sshuser@mycluster-ssh.azurehdinsight.net`. Para obter mais informações, consulte [usar SSH withHDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md)

2. Use um dos seguintes comandos para iniciar a linha de comando Pig:

        pig

    > [!IMPORTANT]
    > Se você estiver usando um cluster baseado no Windows, use os seguintes comandos em vez disso:
    > ```
    > cd %PIG_HOME%
    > bin\pig
    > ```

    Um `grunt>` prompt é exibido.

3. Insira o seguinte para executar um trabalho do Pig que usa o aplicativo .NET Framework:

        DEFINE streamer `PigUDF.exe` CACHE('/PigUDF.exe');
        LOGS = LOAD '/example/data/sample.log' as (LINE:chararray);
        LOG = FILTER LOGS by LINE is not null;
        DETAILS = STREAM LOG through streamer as (col1, col2, col3, col4, col5);
        DUMP DETAILS;

    A `DEFINE` instrução cria um alias de `streamer` para os aplicativos pigudf. exe e `CACHE` o carrega do armazenamento padrão para o cluster. Posteriormente, `streamer` é usado com o `STREAM` operador para processar as linhas individuais contidas no log e retornar os dados como uma série de colunas.

    > [!NOTE]
    > O nome do aplicativo que é usado para streaming deve ser circundado \` pelo caractere (acento grave) quando com alias e ' (aspa simples) quando usado com `SHIP`.

4. Depois de inserir a última linha, o trabalho deve ser iniciado. Ele retorna uma saída semelhante ao seguinte texto:

        (2012-02-03 20:11:56 SampleClass5 [WARN] problem finding id 1358451042 - java.lang.Exception)
        (2012-02-03 20:11:56 SampleClass5 [DEBUG] detail for id 1976092771)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1317358561)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1737534798)
        (2012-02-03 20:11:56 SampleClass7 [DEBUG] detail for id 1475865947)

## <a name="next-steps"></a>Passos seguintes

Neste documento, você aprendeu a usar um aplicativo .NET Framework do hive e do Pig no HDInsight. Se você quiser aprender a usar o Python com o hive e o Pig, consulte [usar o Python com o Apache Hive e o Apache Pig no HDInsight](python-udf-hdinsight.md).

Para outras maneiras de usar o Pig e o Hive, e para saber mais sobre como usar o MapReduce, consulte os seguintes documentos:

* [Usar o Apache Hive com o HDInsight](hdinsight-use-hive.md)
* [Usar o Apache Pig com o HDInsight](hdinsight-use-pig.md)
* [Usar o MapReduce com o HDInsight](hdinsight-use-mapreduce.md)
