---
title: C#, Apache Hive & Apache Pig em Apache Hadoop - Azure HDInsight
description: 'Saiba como utilizar funções definidas pelo utilizador C #(UDF) com o streaming Apache Hive e Apache Pig em Azure HDInsight.'
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 12/06/2019
ms.openlocfilehash: b5682ff58ad827f5a165342f11f03fb49bbe6d2d
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104867887"
---
# <a name="use-c-user-defined-functions-with-apache-hive-and-apache-pig-on-apache-hadoop-in-hdinsight"></a>Use funções definidas pelo utilizador C# com Apache Hive e Apache Pig em Apache Hadoop em HDInsight

Saiba como utilizar funções definidas pelo utilizador C #(UDF) com [a Hive Apache](https://hive.apache.org) e [o Porco Apache](https://pig.apache.org) em HDInsight.

> [!IMPORTANT]
> Os passos neste documento funcionam com clusters HDInsight baseados em Linux. O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, consulte [a versão do componente HDInsight](../hdinsight-component-versioning.md).

Tanto a Hive como a Pig podem passar dados para aplicações externas para processamento. Este processo é conhecido como _streaming._ Ao utilizar uma aplicação .NET, os dados são transmitidos para a aplicação no STDIN, e a aplicação devolve os resultados em STDOUT. Para ler e escrever a partir de STDIN e STDOUT, pode utilizar `Console.ReadLine()` e a partir de uma `Console.WriteLine()` aplicação de consola.

## <a name="prerequisites"></a>Pré-requisitos

* Uma familiaridade com a escrita e construção do código C# que visa .NET Framework 4.5.

    Use o IDE que quiser. Recomendamos [o Visual Studio](https://www.visualstudio.com/vs) ou Visual Studio [Code](https://code.visualstudio.com/). Os passos deste documento utilizam o Visual Studio 2019.

* Uma forma de enviar ficheiros .exe para o cluster e executar trabalhos de Pig and Hive. Recomendamos [ferramentas do Lago de Dados para Estúdio Visual,](../../data-lake-analytics/data-lake-analytics-data-lake-tools-install.md) [Azure PowerShell](/powershell/azure)e [Azure CLI](/cli/azure/install-azure-cli). Os passos deste documento utilizam as Ferramentas do Lago de Dados para o Estúdio Visual para carregar os ficheiros e executar o exemplo consulta Hive.

    Para obter informações sobre outras formas de executar consultas de Hive, veja [o que é Apache Hive e HiveQL em Azure HDInsight?](hdinsight-use-hive.md). . .

* Um Hadoop no aglomerado HDInsight. Para obter mais informações sobre a criação de um cluster, consulte [criar clusters HdInsight](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="net-on-hdinsight"></a>.NET em HDInsight

Os clusters *HDInsight baseados em Linux* utilizam [Mono (para https://mono-project.com)](https://mono-project.com) executar aplicações .NET. A versão mono 4.2.1 está incluída na versão HDInsight 3.6.

Para obter mais informações sobre a compatibilidade do Mono com as versões .NET Framework, consulte [Mono compatibilidade](https://www.mono-project.com/docs/about-mono/compatibility/).

Para obter mais informações sobre a versão do Quadro .NET e Mono incluídos com as versões HDInsight, consulte as [versões componentes HDInsight](../hdinsight-component-versioning.md).

## <a name="create-the-c-projects"></a>Criar os \# projetos C

As secções seguintes descrevem como criar um projeto C# em Visual Studio para um Apache Hive UDF e um Apache Pig UDF.

### <a name="apache-hive-udf"></a>Apache Hive UDF

Para criar um projeto C# para um Apache Hive UDF:

1. Lançamento Visual Studio.

2. **Selecione Criar um novo projeto.**

3. Na nova janela do **projeto,** escolha o modelo **de App consola (.NET Framework)** (a versão C#). Em seguida, selecione **Seguinte**.

4. Na janela do **Configure o seu novo projeto,** insira um nome de **Projeto** da *HiveCSharp,* e navegue para ou crie uma **Localização** para salvar o novo projeto em. Em seguida, selecione **Criar**.

5. No Visual Studio IDE, substitua o conteúdo do *Programa.cs* pelo seguinte código:

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

6. A partir da barra de menu, selecione **Build**  >  **Build Solution** para construir o projeto.

7. Feche a solução.

### <a name="apache-pig-udf"></a>Apache Pig UDF

Para criar um projeto C# para um Apache Hive UDF:

1. Abra o Visual Studio.

2. Na janela **Iniciar,** **selecione Criar um novo projeto.**

3. Na nova janela do **projeto,** escolha o modelo **de App consola (.NET Framework)** (a versão C#). Em seguida, selecione **Seguinte**.

4. Na **configuração** da sua nova janela de projeto, insira um **nome** de Projeto da *PigUDF,* e vá ou crie uma **Localização** para salvar o novo projeto em. Em seguida, selecione **Criar**.

5. No Visual Studio IDE, substitua o conteúdo do *Programa.cs* pelo seguinte código:

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

    Este código analisa as linhas enviadas a partir de linhas de Pig e reformats que começam com `java.lang.Exception` .

6. A partir da barra de menu, escolha **Build**  >  **Build Solution** para construir o projeto.

7. Deixe a solução aberta.

## <a name="upload-to-storage"></a>Carregar para o armazenamento

Em seguida, faça o upload das aplicações Hive e Pig UDF para armazenamento num cluster HDInsight.

1. No Estúdio Visual, navegue para **ver o** Explorador  >  **do Servidor**.

1. A partir do **Server Explorer,** clique à direita **Azure**, selecione **Connect to Microsoft Azure Subscription** e complete o processo de iniciar súpido.

1. Expanda o cluster HDInsight para o que pretende implementar esta aplicação. Uma entrada com o texto **(Conta de Armazenamento Padrão)** é listada.

    :::image type="content" source="./media/apache-hadoop-hive-pig-udf-dotnet-csharp/hdinsight-storage-account.png" alt-text="Conta de armazenamento predefinido, cluster HDInsight, Explorador de Servidor" border="true":::

    * Se esta entrada puder ser expandida, está a utilizar uma **Conta de Armazenamento Azure** como armazenamento predefinido para o cluster. Para visualizar os ficheiros no armazenamento predefinido para o cluster, expanda a entrada e, em seguida, clique duas vezes no **(Recipiente Padrão)**.

    * Se esta entrada não puder ser expandida, está a utilizar **o Azure Data Lake Storage** como o armazenamento padrão para o cluster. Para visualizar os ficheiros no armazenamento predefinido para o cluster, clique duas vezes na entrada **(Conta de Armazenamento Predefinido).**

1. Para fazer o upload dos ficheiros .exe, utilize um dos seguintes métodos:

    * Se estiver a utilizar uma **conta de armazenamento Azure,** selecione o ícone **Upload Blob.**

        :::image type="content" source="./media/apache-hadoop-hive-pig-udf-dotnet-csharp/hdinsight-upload-icon.png" alt-text="HdInsight ícone de upload para novo projeto" border="true":::

        Na caixa de diálogo **de novo ficheiro de upload,** em nome de **ficheiro,** selecione **Procurar**. Na caixa de diálogo **Upload Blob,** vá à pasta *bin\debug* para o projeto *HiveCSharp* e, em seguida, escolha o ficheiro *HiveCSharp.exe.* Por fim, selecione **Open** e, em seguida, **OK** para completar o upload.

    * Se estiver a utilizar **o Azure Data Lake Storage,** clique com o botão direito numa área vazia na listagem de ficheiros e, em seguida, selecione **Upload**. Por fim, escolha o ficheiro *HiveCSharp.exe* e selecione **Open**.

    Uma vez terminada a *HiveCSharp.exe* upload, repita o processo de upload para o ficheiro *PigUDF.exe.*

## <a name="run-an-apache-hive-query"></a>Executar uma consulta de Colmeia Apache

Agora você pode executar uma consulta hive que usa a sua aplicação Hive UDF.

1. No Estúdio Visual, navegue para **ver o** Explorador  >  **do Servidor**.

2. Expanda **Azure** e, em seguida, expanda **HDInsight**.

3. Clique com o botão direito no cluster a que implementou a aplicação *HiveCSharp* e, em seguida, **selecione Escreva uma Consulta de Colmeia**.

4. Utilize o seguinte texto para a consulta hive:

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
    > Descomprimir a `add file` declaração que corresponde ao tipo de armazenamento predefinido utilizado para o seu cluster.

    Esta consulta seleciona o `clientid` , e campos de , e depois passa os campos para a `devicemake` `devicemodel` `hivesampletable` *aplicaçãoHiveCSharp.exe.* A consulta espera que a aplicação devolva três campos, que são armazenados como `clientid` `phoneLabel` , e `phoneHash` . A consulta também espera encontrar *HiveCSharp.exe* na raiz do recipiente de armazenamento padrão.

5. Mude o **Interactive** padrão para **Lote** e, em seguida, Selecione **Submeter** para submeter o trabalho ao cluster HDInsight. A janela **do Resumo do Trabalho da Colmeia** abre-se.

6. Selecione **Refresh** para atualizar o resumo até que **o Estado do Trabalho** mude para **Concluído**. Para visualizar a saída de trabalho, selecione **Job Output**.

## <a name="run-an-apache-pig-job"></a>Executar um trabalho de Porco Apache

Você também pode executar um trabalho de porco que usa a sua aplicação Pig UDF.

1. Utilize o SSH para ligar ao seu cluster HDInsight. (Por exemplo, executar o comando `ssh sshuser@<clustername>-ssh.azurehdinsight.net` .) Para obter mais informações, consulte [Use SSH com SHDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Utilize o seguinte comando para iniciar a linha de comando Pig:

    ```shell
    pig
    ```

    É `grunt>` apresentado um aviso.

3. Introduza o seguinte para executar um trabalho de suíno que utilize a aplicação .NET Framework:

    ```pig
    DEFINE streamer `PigUDF.exe` CACHE('/PigUDF.exe');
    LOGS = LOAD '/example/data/sample.log' as (LINE:chararray);
    LOG = FILTER LOGS by LINE is not null;
    DETAILS = STREAM LOG through streamer as (col1, col2, col3, col4, col5);
    DUMP DETAILS;
    ```

    A `DEFINE` declaração cria um pseudónimo para a `streamer` aplicação *PigUDF.exe,* e `CACHE` carrega-a do armazenamento padrão para o cluster. Posteriormente, `streamer` é utilizado com o operador para processar as `STREAM` linhas únicas contidas `LOG` e devolver os dados como uma série de colunas.

    > [!NOTE]
    > O nome da aplicação utilizado para o streaming deve ser rodeado pelo \` carácter (retrosetro) quando aliased, e pelo carácter ' (citação única) quando usado com `SHIP` .

4. Depois de entrar na última linha, o trabalho deve começar. Devolve a saída semelhante ao seguinte texto:

    ```output
    (2019-07-15 16:43:25 SampleClass5 [WARN] problem finding id 1358451042 - java.lang.Exception)
    (2019-07-15 16:43:25 SampleClass5 [DEBUG] detail for id 1976092771)
    (2019-07-15 16:43:25 SampleClass5 [TRACE] verbose detail for id 1317358561)
    (2019-07-15 16:43:25 SampleClass5 [TRACE] verbose detail for id 1737534798)
    (2019-07-15 16:43:25 SampleClass7 [DEBUG] detail for id 1475865947)
    ```

5. Use `exit` para sair de porco.

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a usar uma aplicação .NET Framework da Hive and Pig on HDInsight. Se você gostaria de aprender a usar Python com Colmeia e Porco, consulte [Use Python com Apache Hive e Apache Pig em HDInsight](python-udf-hdinsight.md).

Para outras formas de utilizar a Colmeia e de aprender a usar o MapReduce, consulte os seguintes artigos:

* [Use a Colmeia Apache com HDInsight](hdinsight-use-hive.md)
* [Use MapReduce com HDInsight](hdinsight-use-mapreduce.md)
* [Básicos latinos do porco](https://pig.apache.org/docs/latest/basic.html)
