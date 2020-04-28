---
title: C#, Apache Hive & Apache Pig em Apache Hadoop - Azure HDInsight
description: Aprenda a utilizar funções definidas pelo utilizador (UDF) com a Apache Hive e o Apache Pig em streaming em Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 9ef9eada9b9aec50642a8bf357edab0677868817
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74949394"
---
# <a name="use-c-user-defined-functions-with-apache-hive-and-apache-pig-on-apache-hadoop-in-hdinsight"></a>Utilize funções definidas pelo utilizador c# com A Hive Apache e O Porco Apache no Apache Hadoop em HDInsight

Aprenda a utilizar funções definidas pelo utilizador (UDF) com [A Hive Apache](https://hive.apache.org) e [Apache Pig](https://pig.apache.org) no HDInsight.

> [!IMPORTANT]
> Os passos neste documento funcionam com clusters HDInsight baseados em Linux. O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para mais informações, consulte [a versão do componente HDInsight](../hdinsight-component-versioning.md).

Tanto a Hive como o Pig podem passar dados para aplicações externas para processamento. Este processo é conhecido como _streaming_. Ao utilizar uma aplicação .NET, os dados são transmitidos para a aplicação no STDIN, e a aplicação devolve os resultados no STDOUT. Para ler e escrever a partir de STDIN `Console.ReadLine()` `Console.WriteLine()` e STDOUT, pode utilizar e a partir de uma aplicação de consola.

## <a name="prerequisites"></a>Pré-requisitos

* Uma familiaridade com a escrita e construção de código C# que visa .NET Framework 4.5.

    Usa o iDE que quiseres. Recomendamos o [Visual Studio](https://www.visualstudio.com/vs) ou o Visual [Studio Code.](https://code.visualstudio.com/) Os passos neste documento utilizam o Visual Studio 2019.

* Uma forma de enviar ficheiros .exe para o cluster e executar trabalhos de Porco e Colmeia. Recomendamos [ferramentas data lake para estúdio visual,](../../data-lake-analytics/data-lake-analytics-data-lake-tools-install.md) [Azure PowerShell](/powershell/azure)e [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest). Os passos neste documento utilizam as Ferramentas data lake para o Estúdio Visual para carregar os ficheiros e executar a consulta de hive exemplo.

    Para obter informações sobre outras formas de executar consultas da Hive, veja [o que é Apache Hive e HiveQL no Azure HDInsight?](hdinsight-use-hive.md)

* Um Hadoop no cluster HDInsight. Para obter mais informações sobre a criação de um cluster, consulte [Create HDInsight clusters](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="net-on-hdinsight"></a>.NET em HDInsight

Os clusters *HDInsight baseados em Linux* usam [mono (https://mono-project.com) ](https://mono-project.com) para executar aplicações .NET. A versão mono 4.2.1 está incluída na versão 3.6 da HDInsight.

Para obter mais informações sobre a compatibilidade de Mono com as versões .NET Framework, consulte a [compatibilidade mono](https://www.mono-project.com/docs/about-mono/compatibility/).

Para obter mais informações sobre a versão do .NET Framework e Mono incluídos nas versões HDInsight, consulte [as versões componentes HDInsight](../hdinsight-component-versioning.md).

## <a name="create-the-c-projects"></a>Criar os\# projetos C

As seguintes secções descrevem como criar um projeto C# no Estúdio Visual para uma UDF da Colmeia Apache e uma UDF de Porco Apache.

### <a name="apache-hive-udf"></a>Apache Hive UDF

Para criar um projeto C# para uma UDF da Colmeia Apache:

1. Inicie o Visual Studio.

2. Selecione **Criar um novo projeto.**

3. Na **janela Criar uma nova** janela de projeto, escolha o modelo de **App consola (.QUADRO NET)** (a versão C#). Em seguida, selecione **Seguinte**.

4. Na **Configuração** da sua nova janela de projeto, insira um nome de **Projeto** de *HiveCSharp,* e navegue para ou crie um **Local** para salvar o novo projeto. Em seguida, selecione **Criar**.

5. No Estúdio Visual IDE, substitua os conteúdos de *Program.cs* pelo seguinte código:

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

6. A partir da barra de menus, selecione **Build Build** > **Solution** para construir o projeto.

7. Feche a solução.

### <a name="apache-pig-udf"></a>Apache Pig UDF

Para criar um projeto C# para uma UDF da Colmeia Apache:

1. Abra o Visual Studio.

2. Na janela **Iniciar,** selecione **Criar um novo projeto**.

3. Na **janela Criar uma nova** janela de projeto, escolha o modelo de **App consola (.QUADRO NET)** (a versão C#). Em seguida, selecione **Seguinte**.

4. Na **Configuração** da sua nova janela de projeto, insira um nome de **Projeto** *pigUDF,* e vá ou crie um **Local** para salvar o novo projeto. Em seguida, selecione **Criar**.

5. No Estúdio Visual IDE, substitua os conteúdos de *Program.cs* pelo seguinte código:

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

    Este código analisa as linhas enviadas do Pig `java.lang.Exception`e reformats linhas que começam com .

6. A partir da barra de menus, escolha **Build Build** > **Solution** para construir o projeto.

7. Deixe a solução aberta.

## <a name="upload-to-storage"></a>Carregar para o armazenamento

Em seguida, faça upload das aplicações da Hive e Pig UDF para armazenamento num cluster HDInsight.

1. No Estúdio Visual, navegue para **Ver** > **Server Explorer**.

1. A partir do **Server Explorer,** clique no **Azure,** selecione **Connect to Microsoft Azure Subscription**, e complete o processo de início de sessão.

1. Expanda o cluster HDInsight para o que pretende implementar esta aplicação. Está listada uma entrada com o texto (Conta de **Armazenamento Predefinido).**

    ![Conta de armazenamento padrão, cluster HDInsight, Server Explorer](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/hdinsight-storage-account.png)

    * Se esta entrada puder ser expandida, está a utilizar uma Conta de **Armazenamento Azure** como armazenamento predefinido para o cluster. Para ver os ficheiros no armazenamento predefinido do cluster, expanda a entrada e, em seguida, clique duas vezes no **(Recipiente Predefinido)**.

    * Se esta entrada não puder ser expandida, está a usar o Armazenamento do **Lago De Dados Azure** como armazenamento padrão para o cluster. Para ver os ficheiros no armazenamento predefinido do cluster, clique duas vezes na entrada (Conta de **Armazenamento Predefinido).**

1. Para fazer o upload dos ficheiros .exe, utilize um dos seguintes métodos:

    * Se estiver a utilizar uma conta de **armazenamento Azure,** selecione o ícone **'Upload Blob'.**

        ![Ícone de upload HDInsight para novo projeto](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/hdinsight-upload-icon.png)

        Na caixa de diálogo **Upload New File,** sob o nome do **ficheiro,** selecione **Browse**. Na caixa de diálogo **Upload Blob,** vá à pasta *bin\debug* para o projeto *HiveCSharp* e, em seguida, escolha o ficheiro *HiveCSharp.exe.* Por fim, selecione **Open** e, em seguida, **OK** para completar o upload.

    * Se estiver a utilizar o Armazenamento do **Lago De Dados Azure,** clique à direita numa área vazia na listagem de ficheiros e, em seguida, selecione **Upload**. Por fim, escolha o ficheiro *HiveCSharp.exe* e selecione **Open**.

    Uma vez terminado o upload *HiveCSharp.exe,* repita o processo de upload para o ficheiro *PigUDF.exe.*

## <a name="run-an-apache-hive-query"></a>Executar uma consulta de Apache Hive

Agora você pode executar uma consulta de Colmeia que usa a sua aplicação Hive UDF.

1. No Estúdio Visual, navegue para **Ver** > **Server Explorer**.

2. Expanda **Azure** e, em seguida, expanda **HDInsight**.

3. Clique à direita no cluster para o qual implementou a aplicação *HiveCSharp* e, em seguida, selecione **Escrever uma Consulta**de Colmeia .

4. Utilize o seguinte texto para a consulta da Colmeia:

    ```hiveql
    -- Uncomment the following if you are using Azure Storage
    -- add file wasbs:///HiveCSharp.exe;
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
    > Descodere a `add file` declaração que corresponde ao tipo de armazenamento predefinido utilizado para o seu cluster.

    Esta consulta seleciona `clientid`a `devicemodel` , `hivesampletable` `devicemake`e os campos de , e depois passa os campos para a aplicação *HiveCSharp.exe.* A consulta espera que a aplicação devolva `clientid` `phoneLabel`três `phoneHash`campos, que são armazenados como , e . A consulta também espera encontrar *HiveCSharp.exe* na raiz do recipiente de armazenamento padrão.

5. Mude o padrão **Interativo** para **O Lote**e, em seguida, selecione **Submeter** para submeter o trabalho ao cluster HDInsight. A janela **sumária** da Colmeia abre- se.

6. Selecione **Refresh** para refrescar o resumo até que o **Estado do Trabalho** mude para **concluído**. Para visualizar a saída de trabalho, selecione **Saída de Trabalho**.

## <a name="run-an-apache-pig-job"></a>Executar um trabalho de Porco Apache

Você também pode executar um trabalho de porco que usa o seu aplicativo Pig UDF.

1. Utilize o SSH para se ligar ao seu cluster HDInsight. (Por exemplo, executar `ssh sshuser@<clustername>-ssh.azurehdinsight.net`o comando .) Para mais informações, consulte [Use SSH comHDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Utilize o seguinte comando para iniciar a linha de comando pig:

    ```shell
    pig
    ```

    Um `grunt>` pedido é exibido.

3. Insira o seguinte trabalho de porco que utilize a aplicação .NET Framework:

    ```pig
    DEFINE streamer `PigUDF.exe` CACHE('/PigUDF.exe');
    LOGS = LOAD '/example/data/sample.log' as (LINE:chararray);
    LOG = FILTER LOGS by LINE is not null;
    DETAILS = STREAM LOG through streamer as (col1, col2, col3, col4, col5);
    DUMP DETAILS;
    ```

    A `DEFINE` declaração cria um `streamer` pseudónimo para a aplicação *PigUDF.exe,* e `CACHE` carrega-a do armazenamento padrão para o cluster. Posteriormente, `streamer` é `STREAM` utilizado com o operador para `LOG` processar as linhas únicas contidas e devolver os dados como uma série de colunas.

    > [!NOTE]
    > O nome da aplicação utilizado para o \` streaming deve ser rodeado pelo carácter (backtick) quando pseudónimo, `SHIP`e pelo carácter ' (citação única) quando utilizado com .

4. Depois de entrar na última linha, o trabalho deve começar. Devolve a saída semelhante ao seguinte texto:

    ```output
    (2019-07-15 16:43:25 SampleClass5 [WARN] problem finding id 1358451042 - java.lang.Exception)
    (2019-07-15 16:43:25 SampleClass5 [DEBUG] detail for id 1976092771)
    (2019-07-15 16:43:25 SampleClass5 [TRACE] verbose detail for id 1317358561)
    (2019-07-15 16:43:25 SampleClass5 [TRACE] verbose detail for id 1737534798)
    (2019-07-15 16:43:25 SampleClass7 [DEBUG] detail for id 1475865947)
    ```

5. Usa `exit` para sair do porco.

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a usar uma aplicação .NET Framework da Hive and Pig no HDInsight. Se quiser aprender a usar Python com Hive e Pig, consulte [Use Python com Apache Hive e Apache Pig em HDInsight](python-udf-hdinsight.md).

Para outras formas de usar a Hive, e aprender sobre a utilização do MapReduce, consulte os seguintes artigos:

* [Use a Colmeia Apache com HDInsight](hdinsight-use-hive.md)
* [Utilizar mapeiaReduzir com HDInsight](hdinsight-use-mapreduce.md)
* [Fundamentos Latinos de Porco](https://pig.apache.org/docs/latest/basic.html)