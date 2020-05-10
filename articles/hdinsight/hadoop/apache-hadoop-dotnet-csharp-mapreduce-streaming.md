---
title: Use C# com MapReduce no Hadoop em HDInsight - Azure
description: Aprenda a usar C# para criar soluções MapReduce com Apache Hadoop em Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/28/2020
ms.openlocfilehash: 28817489af535ee45a6cc06cc5fe9d4fde9da8eb
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2020
ms.locfileid: "82996839"
---
# <a name="use-c-with-mapreduce-streaming-on-apache-hadoop-in-hdinsight"></a>Use C# com MapReduce streaming no Apache Hadoop em HDInsight

Aprenda a usar C# para criar uma solução MapReduce no HDInsight.

O streaming Apache Hadoop permite-lhe executar o MapReduce jobs usando um script ou executável. Aqui, o .NET é utilizado para implementar o mapper e o redutor para uma solução de contagem de palavras.

## <a name="net-on-hdinsight"></a>.NET em HDInsight

Os clusters HDInsight usam [mono (parahttps://mono-project.com) ](https://mono-project.com) executar aplicações .NET. A versão mono 4.2.1 está incluída na versão 3.6 da HDInsight. Para obter mais informações sobre a versão de Mono incluída com hDInsight, consulte [os componentes Apache Hadoop disponíveis com diferentes versões HDInsight](../hdinsight-component-versioning.md#apache-components-available-with-different-hdinsight-versions).

Para obter mais informações sobre a compatibilidade de Mono com as versões .NET Framework, consulte a [compatibilidade mono](https://www.mono-project.com/docs/about-mono/compatibility/).

## <a name="how-hadoop-streaming-works"></a>Como funciona o streaming de Hadoop

O processo básico utilizado para o streaming neste documento é o seguinte:

1. Hadoop passa dados para o mapper *(mapper.exe* neste exemplo) no STDIN.
2. O mapper processa os dados e emite pardes de chave/valor delimitados por separador para STDOUT.
3. A saída é lida por Hadoop, e depois passada para o redutor *(redutor.exe* neste exemplo) no STDIN.
4. O redutor lê os pares de chaves/valor delimitados pelo separador, processa os dados e, em seguida, emite o resultado como par de chave/valor delimitado por separador no STDOUT.
5. A saída é lida por Hadoop e escrita para o diretório de saída.

Para obter mais informações sobre o streaming, consulte [O Streaming Hadoop](https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html).

## <a name="prerequisites"></a>Pré-requisitos

* Visual Studio.

* Uma familiaridade com a escrita e construção de código C# que visa .NET Framework 4.5.

* Uma forma de enviar ficheiros .exe para o cluster. Os passos neste documento utilizam as Ferramentas data lake para o Estúdio Visual para carregar os ficheiros para o armazenamento primário para o cluster.

* Se utilizar o PowerShell, necessitará do [Módulo Az](https://docs.microsoft.com/powershell/azure/overview).

* Um aglomerado Apache Hadoop no HDInsight. Ver [Começar com HDInsight no Linux](../hadoop/apache-hadoop-linux-tutorial-get-started.md).

* O esquema URI para o armazenamento primário dos seus clusters. Este esquema `wasb://` seria para o `abfs://` Armazenamento Azure, para `adl://` o Azure Data Lake Storage Gen2 ou para o Azure Data Lake Storage Gen1. Se a transferência segura estiver ativada para o Armazenamento de `wasbs://` Azure ou data Lake Storage Gen2, o URI seria ou, `abfss://`respectivamente.

## <a name="create-the-mapper"></a>Criar o mapper

No Estúdio Visual, crie uma nova aplicação de consola .NET Framework chamada *mapper*. Utilize o seguinte código para a aplicação:

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

Depois de criar a aplicação, construa-a para produzir o ficheiro */bin/Debug/mapper.exe* no diretório do projeto.

## <a name="create-the-reducer"></a>Criar o redutor

No Estúdio Visual, crie uma nova aplicação de consola .NET Framework chamada *redutor*. Utilize o seguinte código para a aplicação:

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

Depois de criar a aplicação, construa-a para produzir o ficheiro */bin/Debug/reducer.exe* no diretório do projeto.

## <a name="upload-to-storage"></a>Carregar para o armazenamento

Em seguida, é necessário fazer o upload das aplicações *do mapper* e *do redutor* para o armazenamento HDInsight.

1. No Estúdio Visual, selecione **'Ver** > **Server Explorer'.**

1. Clique no **Azure,** selecione **Connect to Microsoft Azure Subscription...**, e complete o processo de início de sessão.

1. Expanda o cluster HDInsight para o que pretende implementar esta aplicação. Está listada uma entrada com o texto (Conta de **Armazenamento Predefinido).**

   ![Conta de armazenamento, cluster HDInsight, Server Explorer, Estúdio Visual](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-storage-account.png)

   * Se a entrada (Conta de **Armazenamento Predefinido)** puder ser expandida, está a utilizar uma Conta de **Armazenamento Azure** como armazenamento predefinido para o cluster. Para ver os ficheiros no armazenamento predefinido do cluster, expanda a entrada e, em seguida, clique duas vezes **(Recipiente Predefinido)**.

   * Se a entrada (Conta de **Armazenamento Padrão)** não puder ser expandida, está a utilizar o Armazenamento do **Lago De dados Azure** como armazenamento predefinido para o cluster. Para ver os ficheiros no armazenamento predefinido do cluster, clique duas vezes na entrada (Conta de **Armazenamento Predefinido).**

1. Para fazer o upload dos ficheiros .exe, utilize um dos seguintes métodos:

    * Se estiver a utilizar uma conta de **armazenamento Azure,** selecione o ícone **'Upload Blob'.**

        ![Ícone de upload HDInsight para mapper, Estúdio Visual](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-upload-icon.png)

        Na caixa de diálogo **Upload New File,** sob o nome do **ficheiro,** selecione **Browse**. Na caixa de diálogo **Upload Blob,** vá à pasta *bin\debug* para o projeto *mapper* e, em seguida, escolha o ficheiro *mapper.exe.* Por fim, selecione **Open** e, em seguida, **OK** para completar o upload.

    * Para armazenamento de **lagos de dados azure,** clique à direita numa área vazia na listagem de ficheiros e, em seguida, selecione **Upload**. Por fim, selecione o ficheiro *mapper.exe* e, em seguida, **selecione Open**.

    Uma vez terminado o upload *mapper.exe,* repita o processo de upload para o ficheiro *redutor.exe.*

## <a name="run-a-job-using-an-ssh-session"></a>Executar um trabalho: Usando uma sessão de SSH

O seguinte procedimento descreve como executar um trabalho MapReduce usando uma sessão de SSH:

1. Utilize [o comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para se ligar ao seu cluster. Editar o comando abaixo substituindo CLUSTERNAME pelo nome do seu cluster e, em seguida, introduzir o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Utilize um dos seguintes comandos para iniciar o trabalho MapReduce:

   * Se o armazenamento predefinido for **o Armazenamento Azure:**

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files wasbs:///mapper.exe,wasbs:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

    * Se o armazenamento padrão for **Data Lake Storage Gen1:**

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files adl:///mapper.exe,adl:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

   * Se o armazenamento padrão for **Data Lake Storage Gen2:**

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files abfs:///mapper.exe,abfs:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

   A lista seguinte descreve o que cada parâmetro e opção representa:

   |Parâmetro | Descrição |
   |---|---|
   |hadoop-streaming.jar|Especifica o ficheiro do frasco que contém a funcionalidade mapReduce de streaming.|
   |-ficheiros|Especifica os ficheiros *mapper.exe* e *reducer.exe* para este trabalho. A `wasbs:///` `adl:///`declaração `abfs:///` de protocolo antes de cada ficheiro é o caminho para a raiz do armazenamento predefinido para o cluster.|
   |-mapper|Especifica o ficheiro que implementa o mapper.|
   |-redutor|Especifica o ficheiro que implementa o redutor.|
   |-entrada|Especifica os dados de entrada.|
   |-produção|Especifica o diretório de saída.|

1. Assim que o trabalho mapReduce terminar, utilize o seguinte comando para visualizar os resultados:

   ```bash
   hdfs dfs -text /example/wordcountout/part-00000
   ```

   O seguinte texto é um exemplo dos dados devolvidos por este comando:

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

## <a name="run-a-job-using-powershell"></a>Executar um trabalho: Usando powerShell

Utilize o seguinte script PowerShell para executar um trabalho MapReduce e baixar os resultados.

[!code-powershell[main](../../../powershell_scripts/hdinsight/use-csharp-mapreduce/use-csharp-mapreduce.ps1?range=5-87)]

Este script solicita-lhe o nome da conta de login do cluster e a palavra-passe, juntamente com o nome do cluster HDInsight. Uma vez concluída a função, a saída é descarregada para um ficheiro chamado *output.txt*. O seguinte texto é um exemplo `output.txt` dos dados no ficheiro:

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

* [Utilize mapReduce em Apache Hadoop no HDInsight](hdinsight-use-mapreduce.md).
* [Utilize uma função c# definida pelo utilizador com A Colmeia Apache e O Porco Apache](apache-hadoop-hive-pig-udf-dotnet-csharp.md).
* [Desenvolver programas Java MapReduce](apache-hadoop-develop-deploy-java-mapreduce-linux.md)