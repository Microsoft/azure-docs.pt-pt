---
title: Use C# com MapReduce em Hadoop em HDInsight - Azure
description: Saiba como usar o C# para criar soluções MapReduce com Apache Hadoop em Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, seoapr2020, devx-track-csharp
ms.date: 04/28/2020
ms.openlocfilehash: 6e2758fd650547978924c25f8f3faf1e3e7194f6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101699390"
---
# <a name="use-c-with-mapreduce-streaming-on-apache-hadoop-in-hdinsight"></a>Use C# com mapReduce streaming em Apache Hadoop em HDInsight

Saiba como usar o C# para criar uma solução MapReduce no HDInsight.

O streaming Apache Hadoop permite-lhe executar trabalhos MapReduce usando um script ou executável. Aqui, .NET é usado para implementar o mapper e redutor para uma solução de contagem de palavras.

## <a name="net-on-hdinsight"></a>.NET em HDInsight

Os clusters HDInsight utilizam [Mono (para https://mono-project.com) ](https://mono-project.com) executar aplicações .NET. A versão mono 4.2.1 está incluída na versão HDInsight 3.6. Para obter mais informações sobre a versão de Mono incluída com HDInsight, consulte [os componentes Apache Hadoop disponíveis com as versões HDInsight](../hdinsight-component-versioning.md).

Para obter mais informações sobre a compatibilidade do Mono com as versões .NET Framework, consulte [Mono compatibilidade](https://www.mono-project.com/docs/about-mono/compatibility/).

## <a name="how-hadoop-streaming-works"></a>Como funciona o streaming de Hadoop

O processo básico utilizado para o streaming neste documento é o seguinte:

1. Hadoop transmite dados para o mapper *(mapper.exe* neste exemplo) no STDIN.
2. O mapper processa os dados e emite par de chaves/valor delimitados por separadores para STDOUT.
3. A saída é lida por Hadoop, e depois passada para o redutor *(reducer.exe* neste exemplo) em STDIN.
4. O redutor lê os pares de teclas/valor delimitados por separadores, processa os dados e, em seguida, emite o resultado como par de teclas/valor delimitados por separadores em STDOUT.
5. A saída é lida por Hadoop e escrita para o diretório de saída.

Para obter mais informações sobre o streaming, consulte [o Hadoop Streaming.](https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html)

## <a name="prerequisites"></a>Pré-requisitos

* Visual Studio.

* Uma familiaridade com a escrita e construção do código C# que visa .NET Framework 4.5.

* Uma forma de enviar ficheiros .exe para o cluster. Os passos deste documento utilizam as Ferramentas do Lago de Dados para o Estúdio Visual para fazer o upload dos ficheiros para o armazenamento primário do cluster.

* Se utilizar o PowerShell, necessitará do [Módulo Az](/powershell/azure/).

* Um aglomerado Apache Hadoop em HDInsight. Ver [Começar com HDInsight no Linux](../hadoop/apache-hadoop-linux-tutorial-get-started.md).

* O esquema URI para o armazenamento primário dos seus clusters. Este esquema seria `wasb://` para o Azure Storage, `abfs://` para a Azure Data Lake Storage Gen2 ou `adl://` para a Azure Data Lake Storage Gen1. Se a transferência segura estiver ativada para o Azure Storage ou data lake storage gen2, o URI será `wasbs://` `abfss://` ou, respectivamente.

## <a name="create-the-mapper"></a>Criar o mapper

No Visual Studio, crie uma nova aplicação de consola .NET Framework chamada *mapper*. Utilize o seguinte código para a aplicação:

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

No Visual Studio, crie uma nova aplicação de consola .NET Framework chamada *redutor*. Utilize o seguinte código para a aplicação:

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

Em seguida, você precisa carregar as aplicações *mapper* e *redutor* para o armazenamento HDInsight.

1. No Estúdio Visual, selecione **Ver**  >  **Explorador de Servidor**.

1. Clique com o botão direito **Azure**, **selecione Connect to Microsoft Azure Subscription...** e complete o processo de inscrição.

1. Expanda o cluster HDInsight para o que pretende implementar esta aplicação. Uma entrada com o texto **(Conta de Armazenamento Padrão)** é listada.

   ![Conta de armazenamento, cluster HDInsight, Server Explorer, Visual Studio](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-storage-account.png)

   * Se a entrada **(Conta de Armazenamento Predefinido)** puder ser expandida, está a utilizar uma **Conta de Armazenamento Azure** como armazenamento predefinido para o cluster. Para visualizar os ficheiros no armazenamento predefinido para o cluster, expanda a entrada e, em seguida, clique duas vezes **(Recipiente Padrão)**.

   * Se a entrada **(Conta de Armazenamento Predefinido)** não puder ser expandida, está a utilizar **o Azure Data Lake Storage** como o armazenamento predefinido para o cluster. Para visualizar os ficheiros no armazenamento predefinido para o cluster, clique duas vezes na entrada **(Conta de Armazenamento Predefinido).**

1. Para fazer o upload dos ficheiros .exe, utilize um dos seguintes métodos:

    * Se estiver a utilizar uma **conta de armazenamento Azure,** selecione o ícone **Upload Blob.**

        ![HdInsight ícone de upload para mapper, Visual Studio](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-upload-icon.png)

        Na caixa de diálogo **de novo ficheiro de upload,** em nome de **ficheiro,** selecione **Procurar**. Na caixa de diálogo **Upload Blob,** vá à pasta *bin\debug* para o projeto *do mapper* e, em seguida, escolha o ficheiro *mapper.exe.* Por fim, selecione **Open** e, em seguida, **OK** para completar o upload.

    * Para **o Azure Data Lake Storage,** clique com o botão direito numa área vazia na listagem de ficheiros e, em seguida, selecione **Upload**. Por fim, selecione o ficheiro *mapper.exe* e, em seguida, selecione **Abrir**.

    Uma vez terminada a *mapper.exe* upload, repita o processo de upload para o ficheiro *reducer.exe.*

## <a name="run-a-job-using-an-ssh-session"></a>Executar um trabalho: Usando uma sessão de SSH

O seguinte procedimento descreve como executar um trabalho MapReduce usando uma sessão de SSH:

1. Utilize [o comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para ligar ao seu cluster. Edite o comando abaixo substituindo o CLUSTERNAME pelo nome do seu cluster e, em seguida, insira o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Utilize um dos seguintes comandos para iniciar o trabalho MapReduce:

   * Se o armazenamento predefinido for **Azure Storage:**

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files wasbs:///mapper.exe,wasbs:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

    * Se o armazenamento predefinido for **data lake storage gen1:**

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files adl:///mapper.exe,adl:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

   * Se o armazenamento predefinido for **data lake storage gen2:**

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files abfs:///mapper.exe,abfs:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

   A lista que se segue descreve o que cada parâmetro e opção representa:

   |Parâmetro | Descrição |
   |---|---|
   |hadoop-streaming.jar|Especifica o ficheiro do frasco que contém a funcionalidade de streaming MapReduce.|
   |-ficheiros|Especifica os ficheiros *mapper.exe* e *reducer.exe* para este trabalho. A `wasbs:///` `adl:///` declaração , ou protocolo antes de cada `abfs:///` ficheiro é o caminho para a raiz do armazenamento predefinido para o cluster.|
   |-mapper|Especifica o ficheiro que implementa o mapper.|
   |-redutor|Especifica o ficheiro que implementa o redutor.|
   |-entrada|Especifica os dados de entrada.|
   |-saída|Especifica o diretório de saída.|

1. Uma vez concluído o trabalho mapReduce, utilize o seguinte comando para visualizar os resultados:

   ```bash
   hdfs dfs -text /example/wordcountout/part-00000
   ```

   O texto a seguir é um exemplo dos dados devolvidos por este comando:

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

## <a name="run-a-job-using-powershell"></a>Executar um trabalho: Usar o PowerShell

Use o seguinte script PowerShell para executar um trabalho MapReduce e descarregue os resultados.

[!code-powershell[main](../../../powershell_scripts/hdinsight/use-csharp-mapreduce/use-csharp-mapreduce.ps1?range=5-87)]

Este script solicita-lhe o nome e palavra-passe da conta de login do cluster, juntamente com o nome do cluster HDInsight. Uma vez concluído o trabalho, a saída é transferida para um ficheiro chamado *output.txt*. O texto a seguir é um exemplo dos dados do `output.txt` ficheiro:

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

* [Use MapReduce em Apache Hadoop em HDInsight](hdinsight-use-mapreduce.md).
* [Utilize uma função C# definida pelo utilizador com a Colmeia Apache e o Porco Apache](apache-hadoop-hive-pig-udf-dotnet-csharp.md).
* [Desenvolver programas Java MapReduce](apache-hadoop-develop-deploy-java-mapreduce-linux.md)