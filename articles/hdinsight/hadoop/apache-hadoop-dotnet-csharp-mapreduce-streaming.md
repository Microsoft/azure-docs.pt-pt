---
title: Usar C# com o MapReduce no Hadoop no HDInsight – Azure
description: Saiba como usar C# o para criar soluções MapReduce com Apache Hadoop no Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/17/2019
ms.author: hrasheed
ms.openlocfilehash: 1cdf029d296bd6ff11b6531cd47dc6a7fd3163c3
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73930259"
---
# <a name="use-c-with-mapreduce-streaming-on-apache-hadoop-in-hdinsight"></a>Usar C# com streaming MapReduce no Apache Hadoop no HDInsight

Saiba como usar C# o para criar uma solução MapReduce no HDInsight.

> [!IMPORTANT]
> O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, consulte [Apache Hadoop Components in HDInsight](../hdinsight-component-versioning.md).

Apache Hadoop streaming é um utilitário que permite executar trabalhos MapReduce usando um script ou executável. Neste exemplo, o .NET é usado para implementar o mapeador e o redutor para uma solução de contagem de palavras.

## <a name="net-on-hdinsight"></a>.NET no HDInsight

Os clusters *HDInsight baseados em Linux* usam [mono (https://mono-project.com)](https://mono-project.com) para executar aplicativos .net. A versão do mono 4.2.1 está incluída no HDInsight versão 3,6. Para obter mais informações sobre a versão do mono incluída com o HDInsight, consulte [Apache Hadoop componentes disponíveis com diferentes versões do HDInsight](../hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions). 

Para obter mais informações sobre a compatibilidade mono com versões .NET Framework, consulte [compatibilidade mono](https://www.mono-project.com/docs/about-mono/compatibility/).

## <a name="how-hadoop-streaming-works"></a>Como funciona o streaming do Hadoop

O processo básico usado para streaming neste documento é o seguinte:

1. O Hadoop passa dados para o mapeador (*Mapper. exe* neste exemplo) em stdin.
2. O mapeador processa os dados e emite pares de chave/valor delimitados por tabulação para STDOUT.
3. A saída é lida pelo Hadoop e, em seguida, passada para o redutor (*redutor. exe* neste exemplo) em stdin.
4. O redutor lê os pares de chave/valor delimitados por tabulação, processa os dados e, em seguida, emite o resultado como pares de chave/valor delimitados por tabulação em STDOUT.
5. A saída é lida pelo Hadoop e gravada no diretório de saída.

Para saber mais sobre streaming, confira [streaming do Hadoop](https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html).

## <a name="prerequisites"></a>Pré-requisitos

* Visual Studio.

* Familiaridade com a escrita e a C# criação de código direcionados .NET Framework 4,5.

* Uma maneira de carregar arquivos. exe no cluster. As etapas neste documento usam as ferramentas de Data Lake para o Visual Studio para carregar os arquivos no armazenamento primário do cluster.

* Azure PowerShell ou um cliente Secure Shell (SSH).

* Um Hadoop no cluster HDInsight. Para obter mais informações sobre como criar um cluster, consulte [criar um cluster HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="create-the-mapper"></a>Criar o mapeador

No Visual Studio, crie um novo aplicativo de console .NET Framework chamado *mapeador*. Use o seguinte código para o aplicativo:

```csharp
using System;
using System.Text.RegularExpressions;

namespace mapper
{
    class Program
    {
        static void Main(string[] args)
        {
            string line;
            //Hadoop passes data to the mapper on STDIN
            while((line = Console.ReadLine()) != null)
            {
                // We only want words, so strip out punctuation, numbers, etc.
                var onlyText = Regex.Replace(line, @"\.|;|:|,|[0-9]|'", "");
                // Split at whitespace.
                var words = Regex.Matches(onlyText, @"[\w]+");
                // Loop over the words
                foreach(var word in words)
                {
                    //Emit tab-delimited key/value pairs.
                    //In this case, a word and a count of 1.
                    Console.WriteLine("{0}\t1",word);
                }
            }
        }
    }
}
```

Depois de criar o aplicativo, compile-o para produzir o arquivo */bin/Debug/Mapper.exe* no diretório do projeto.

## <a name="create-the-reducer"></a>Criar o redutor

No Visual Studio, crie um novo aplicativo de console .NET Framework chamado *redutor*. Use o seguinte código para o aplicativo:

```csharp
using System;
using System.Collections.Generic;

namespace reducer
{
    class Program
    {
        static void Main(string[] args)
        {
            //Dictionary for holding a count of words
            Dictionary<string, int> words = new Dictionary<string, int>();

            string line;
            //Read from STDIN
            while ((line = Console.ReadLine()) != null)
            {
                // Data from Hadoop is tab-delimited key/value pairs
                var sArr = line.Split('\t');
                // Get the word
                string word = sArr[0];
                // Get the count
                int count = Convert.ToInt32(sArr[1]);

                //Do we already have a count for the word?
                if(words.ContainsKey(word))
                {
                    //If so, increment the count
                    words[word] += count;
                } else
                {
                    //Add the key to the collection
                    words.Add(word, count);
                }
            }
            //Finally, emit each word and count
            foreach (var word in words)
            {
                //Emit tab-delimited key/value pairs.
                //In this case, a word and a count of 1.
                Console.WriteLine("{0}\t{1}", word.Key, word.Value);
            }
        }
    }
}
```

Depois de criar o aplicativo, compile-o para produzir o arquivo */bin/Debug/Reducer.exe* no diretório do projeto.

## <a name="upload-to-storage"></a>Carregar para o armazenamento

Em seguida, você precisa carregar o *mapeador* e os aplicativos *redutor* no armazenamento do HDInsight.

1. No Visual Studio, escolha **exibir** > **Gerenciador de servidores**.

2. Expanda **Azure** e, em seguida, expanda **HDInsight**.

3. Se solicitado, insira suas credenciais de assinatura do Azure e, em seguida, selecione **entrar**.

4. Expanda o cluster HDInsight no qual você deseja implantar esse aplicativo. Uma entrada com o texto **(conta de armazenamento padrão)** é listada.

   ![Conta de armazenamento, cluster HDInsight, Gerenciador de Servidores, Visual Studio](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-storage-account.png)

   * Se a entrada **(conta de armazenamento padrão)** puder ser expandida, você estará usando uma **conta de armazenamento do Azure** como armazenamento padrão para o cluster. Para exibir os arquivos no armazenamento padrão do cluster, expanda a entrada e clique duas vezes em **(contêiner padrão)** .

   * Se a entrada **(conta de armazenamento padrão)** não puder ser expandida, você estará usando **Azure data Lake Storage** como o armazenamento padrão para o cluster. Para exibir os arquivos no armazenamento padrão do cluster, clique duas vezes na entrada **(conta de armazenamento padrão)** .

5. Para carregar os arquivos. exe, use um dos seguintes métodos:

    * Se você estiver usando uma **conta de armazenamento do Azure**, selecione o ícone **carregar blob** . 

        ![Ícone de upload do HDInsight para mapeador, Visual Studio](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-upload-icon.png)

        Na caixa de diálogo **carregar novo arquivo** , em **nome do arquivo**, selecione **procurar**. Na caixa de diálogo **carregar blob** , vá para a pasta *bin\Debug* do projeto *mapeador* e escolha o arquivo *Mapper. exe* . Por fim, selecione **abrir** e, em seguida, **OK** para concluir o carregamento. 

    * Para **Azure data Lake Storage**, clique com o botão direito do mouse em uma área vazia na listagem de arquivos e, em seguida, selecione **carregar**. Por fim, selecione o arquivo *Mapper. exe* e, em seguida, selecione **abrir**.

    Após a conclusão do carregamento do *Mapper. exe* , repita o processo de carregamento para o arquivo *redutor. exe* .

## <a name="run-a-job-using-an-ssh-session"></a>Executar um trabalho: usando uma sessão SSH

O procedimento a seguir descreve como executar um trabalho MapReduce usando uma sessão SSH:

1. Use o SSH para se conectar ao cluster HDInsight. (Por exemplo, execute o comando `ssh sshuser@<clustername>-ssh.azurehdinsight.net`.) Para obter mais informações, consulte [usar SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Use um dos seguintes comandos para iniciar o trabalho MapReduce:

   * Se o armazenamento padrão for o **armazenamento do Azure**:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files wasb:///mapper.exe,wasb:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

    * Se o armazenamento padrão for **Data Lake Storage Gen1**:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files adl:///mapper.exe,adl:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

   * Se o armazenamento padrão for **Data Lake Storage Gen2**:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files abfs:///mapper.exe,abfs:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

   A lista a seguir descreve o que cada parâmetro e opção representa:

   * *Hadoop-streaming. jar*: especifica o arquivo JAR que contém a funcionalidade de streaming MapReduce.
   * `-files`: especifica os arquivos *Mapper. exe* e *redutor. exe* para esse trabalho. A declaração de protocolo `wasb:///`, `adl:///`ou `abfs:///` antes de cada arquivo ser o caminho para a raiz do armazenamento padrão para o cluster.
   * `-mapper`: especifica o arquivo que implementa o mapeador.
   * `-reducer`: especifica o arquivo que implementa o redutor.
   * `-input`: especifica os dados de entrada.
   * `-output`: especifica o diretório de saída.

3. Depois que o trabalho MapReduce for concluído, use o seguinte comando para exibir os resultados:

   ```bash
   hdfs dfs -text /example/wordcountout/part-00000
   ```

   O texto a seguir é um exemplo dos dados retornados por este comando:

   ```output
   you     1128
   young   38
   younger 1
   youngest        1
   your    338
   yours   4
   yourself        34
   yourselves      3
   youth   17
   ```

## <a name="run-a-job-using-powershell"></a>Executar um trabalho: usando o PowerShell

Use o script do PowerShell a seguir para executar um trabalho MapReduce e baixar os resultados.

[!code-powershell[main](../../../powershell_scripts/hdinsight/use-csharp-mapreduce/use-csharp-mapreduce.ps1?range=5-87)]

Esse script solicita o nome e a senha da conta de logon do cluster, juntamente com o nome do cluster HDInsight. Quando o trabalho for concluído, a saída será baixada em um arquivo chamado *output. txt*. O texto a seguir é um exemplo dos dados no arquivo de `output.txt`:

```output
you     1128
young   38
younger 1
youngest        1
your    338
yours   4
yourself        34
yourselves      3
youth   17
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre como usar o MapReduce com o HDInsight, consulte [usar o MapReduce no Apache Hadoop no hdinsight](hdinsight-use-mapreduce.md).

Para obter informações sobre C# como usar o com o hive e o Pig, consulte [usar uma C# função definida pelo usuário com Apache Hive e Apache Pig](apache-hadoop-hive-pig-udf-dotnet-csharp.md).

Para obter informações sobre C# como usar o com o Storm no hdinsight, consulte [desenvolver C# topologias para Apache Storm no hdinsight](../storm/apache-storm-develop-csharp-visual-studio-topology.md).
