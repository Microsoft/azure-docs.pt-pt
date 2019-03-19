---
title: Utilizar c# com o MapReduce do Hadoop no HDInsight - Azure
description: Saiba como utilizar C# para criar soluções de MapReduce com o Apache Hadoop no HDInsight do Azure.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: hrasheed
ms.openlocfilehash: 09b652b236e1fbe68d93298f0f8793854e411aad
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58095675"
---
# <a name="use-c-with-mapreduce-streaming-on-apache-hadoop-in-hdinsight"></a>Utilize C# com o MapReduce de transmissão em fluxo no Apache Hadoop no HDInsight

Saiba como utilizar c# para criar uma solução de MapReduce no HDInsight.

> [!IMPORTANT]
> O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, consulte [controlo de versões de componente de HDInsight](../hdinsight-component-versioning.md).

Transmissão em fluxo do Apache Hadoop é um utilitário que permite-lhe executar tarefas de MapReduce com um script ou executável. Neste exemplo, o .NET é utilizado para implementar o mapeador de pontos e reducer para uma solução de contagem do word.

## <a name="net-on-hdinsight"></a>.NET no HDInsight

__HDInsight baseado em Linux__ utilização de clusters [Mono (https://mono-project.com) ](https://mono-project.com) para executar aplicações de .NET. Versão mono 4.2.1 está incluída com o HDInsight versão 3.6. Para obter mais informações sobre a versão do Mono incluído com o HDInsight, consulte [versões de componente de HDInsight](../hdinsight-component-versioning.md). Para utilizar uma versão específica do Mono, consulte a [instalar ou atualizar o Mono](../hdinsight-hadoop-install-mono.md) documento.

Para obter mais informações sobre a Mono compatibilidade com versões do .NET Framework, consulte [Mono compatibility](https://www.mono-project.com/docs/about-mono/compatibility/).

## <a name="how-hadoop-streaming-works"></a>Como funciona a transmissão em fluxo do Hadoop

O processo básico usado para transmissão em fluxo neste documento é o seguinte:

1. Hadoop transmite dados para o mapeador de pontos (mapper.exe neste exemplo) em STDIN.
2. O mapeador de pontos processa os dados e emite pares de chave/valor delimitado por tabulação para STDOUT.
3. A saída é lido pelo Hadoop e, em seguida, passada para o reducer (reducer.exe neste exemplo) em STDIN.
4. O reducer lê os pares chave/valor delimitado por tabulação, processa os dados e, em seguida, emite o resultado como pares de chave/valor delimitado por tabulação no STDOUT.
5. O resultado é de leitura ao Hadoop e gravado no diretório de saída.

Para obter mais informações sobre a transmissão em fluxo, consulte [transmissão em fluxo do Hadoop](https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html).

## <a name="prerequisites"></a>Pré-requisitos

* Familiaridade com a escrita e criação de código c# que tenha como destino o .NET Framework 4.5. Os passos neste documento utilizam o Visual Studio 2017.

* Uma forma de carregar ficheiros de .exe para o cluster. Os passos neste documento utilizam o Data Lake Tools para Visual Studio para carregar os ficheiros para o armazenamento primário para o cluster.

* O Azure PowerShell ou um cliente SSH.

* Um Hadoop num cluster do HDInsight. Para obter mais informações sobre como criar um cluster, consulte [criar um cluster do HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="create-the-mapper"></a>Criar o mapeador de pontos

No Visual Studio, crie um novo __aplicação de consola__ com o nome __mapeador__. Utilize o seguinte código para a aplicação:

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

Depois de criar a aplicação, crie-o para produzir o `/bin/Debug/mapper.exe` ficheiro no diretório do projeto.

## <a name="create-the-reducer"></a>Criar o reducer

No Visual Studio, crie um novo __aplicação de consola__ com o nome __reducer__. Utilize o seguinte código para a aplicação:

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

Depois de criar a aplicação, crie-o para produzir o `/bin/Debug/reducer.exe` ficheiro no diretório do projeto.

## <a name="upload-to-storage"></a>Carregar para o armazenamento

1. No Visual Studio, abra **Explorador de servidores**.

2. Expanda **Azure** e, em seguida, expanda **HDInsight**.

3. Se lhe for pedido, introduza as credenciais de subscrição do Azure e, em seguida, clique em **sessão**.

4. Expanda o cluster do HDInsight que pretende implementar esta aplicação. Uma entrada com o texto __(conta de armazenamento predefinida)__ está listado.

    ![Explorador de servidores que mostra a conta de armazenamento para o cluster](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/storage.png)

    * Se esta entrada pode ser expandida, estiver a utilizar um __conta de armazenamento do Azure__ como armazenamento predefinido para o cluster. Para ver os ficheiros no armazenamento padrão para o cluster, expanda a entrada e, em seguida, faça duplo clique nas __(contentor predefinido)__.

    * Se esta entrada não pode ser expandida, estiver a utilizar __armazenamento do Azure Data Lake__ como armazenamento predefinido para o cluster. Para ver os ficheiros no armazenamento padrão para o cluster, clique duas vezes o __(conta de armazenamento predefinida)__ entrada.

5. Para carregar os ficheiros de .exe, utilize um dos seguintes métodos:

   * Se utilizar um __conta de armazenamento do Azure__, clique no ícone de carregamento e, em seguida, navegue para o **bin\debug** pasta para o **mapeador** projeto. Por fim, selecione o **mapper.exe** do ficheiro e clique em **Ok**.

       ![carregar ícone](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/upload.png)
    
   * Se utilizar __armazenamento do Azure Data Lake__, uma área vazia na listagem de ficheiros com o botão direito e, em seguida, selecione __carregar__. Por fim, selecione o **mapper.exe** do ficheiro e clique em **aberto**.

     Uma vez a __mapper.exe__ carregamento for concluído, repita o processo de carregamento para o __reducer.exe__ ficheiro.

## <a name="run-a-job-using-an-ssh-session"></a>Execute uma tarefa: Utilizar uma sessão SSH

1. Utilize o SSH para ligar ao cluster do HDInsight. Para obter mais informações, veja [Utilizar SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Utilize um dos seguintes comandos para iniciar a tarefa de MapReduce:

   * Se utilizar __geração 2 de armazenamento do Data Lake__ como armazenamento predefinido:

       ```bash
       yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files abfs:///mapper.exe,abfs:///reducer.exe -mapper mapper.exe -reducer reducer.exe -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
       ```

   * Se utilizar __geração 1 de armazenamento do Data Lake__ como armazenamento predefinido:

       ```bash
       yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files adl:///mapper.exe,adl:///reducer.exe -mapper mapper.exe -reducer reducer.exe -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
       ```
    
   * Se utilizar __armazenamento do Azure__ como armazenamento predefinido:

       ```bash
       yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files wasb:///mapper.exe,wasb:///reducer.exe -mapper mapper.exe -reducer reducer.exe -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
       ```

     A lista seguinte descreve o que faz cada parâmetro:

   * `hadoop-streaming.jar`: O ficheiro jar que contém a funcionalidade de MapReduce de transmissão em fluxo.
   * `-files`: Adiciona os `mapper.exe` e `reducer.exe` ficheiros para esta tarefa. O `abfs:///`,`adl:///` ou `wasb:///` antes de cada arquivo é o caminho para a raiz do armazenamento de predefinido para o cluster.
   * `-mapper`: Especifica qual arquivo implementa o mapeador de pontos.
   * `-reducer`: Especifica qual arquivo implementa a reducer.
   * `-input`: Os dados de entrada.
   * `-output`: O diretório de saída.

3. Depois de concluída a tarefa de MapReduce, utilize o seguinte para ver os resultados:

    ```bash
    hdfs dfs -text /example/wordcountout/part-00000
    ```

    O texto seguinte é um exemplo dos dados retornados por este comando:

        you     1128
        young   38
        younger 1
        youngest        1
        your    338
        yours   4
        yourself        34
        yourselves      3
        youth   17

## <a name="run-a-job-using-powershell"></a>Execute uma tarefa: Com o PowerShell

Utilize o seguinte script do PowerShell para executar uma tarefa de MapReduce e transferir os resultados.

[!code-powershell[main](../../../powershell_scripts/hdinsight/use-csharp-mapreduce/use-csharp-mapreduce.ps1?range=5-87)]

Este script pede-lhe o nome de conta de início de sessão do cluster e a palavra-passe, junto com o nome de cluster do HDInsight. Assim que a tarefa é concluída, a saída é transferida para um arquivo chamado `output.txt`. O texto seguinte é um exemplo dos dados no `output.txt` ficheiro:

    you     1128
    young   38
    younger 1
    youngest        1
    your    338
    yours   4
    yourself        34
    yourselves      3
    youth   17

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre como utilizar o MapReduce com o HDInsight, consulte [utilizar o MapReduce com o HDInsight](hdinsight-use-mapreduce.md).

Para obter informações sobre como utilizar o C# com o Hive e Pig, consulte [utilize um C# função definida pelo utilizador com o Apache Hive e Apache Pig](apache-hadoop-hive-pig-udf-dotnet-csharp.md).

Para obter informações sobre como utilizar o C# com o Storm no HDInsight, consulte [desenvolver C# topologias do Apache Storm no HDInsight](../storm/apache-storm-develop-csharp-visual-studio-topology.md).
