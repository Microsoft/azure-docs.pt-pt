---
title: Enviar trabalhos mapReduce usando HDInsight .NET SDK - Azure
description: Saiba como submeter os trabalhos de MapReduce ao Azure HDInsight Apache Hadoop usando HDInsight .NET SDK.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 01/15/2020
ms.openlocfilehash: 3de7101eb998d0f9b3b57afba6d19595738cbb63
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89020410"
---
# <a name="run-mapreduce-jobs-using-hdinsight-net-sdk"></a>Executar tarefas MapReduce com o SDK .NET do HDInsight

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Saiba como submeter trabalhos mapReduce usando HDInsight .NET SDK. Os clusters HDInsight vêm com um ficheiro de frasco com algumas amostras mapreduce. O ficheiro do frasco `/example/jars/hadoop-mapreduce-examples.jar` é.  Uma das amostras é **a contagem de palavras.** Desenvolve-se uma aplicação de consola C# para submeter um trabalho de contagem de palavras.  O trabalho lê o `/example/data/gutenberg/davinci.txt` ficheiro e produz os resultados para `/example/data/davinciwordcount` .  Se quiser refazer a aplicação, tem de limpar a pasta de saída.

> [!NOTE]  
> Os passos deste artigo devem ser realizados a partir de um cliente Windows. Para obter informações sobre a utilização de um cliente Linux, OS X ou Unix para trabalhar com a Hive, utilize o seletor de separadores mostrado no topo do artigo.

## <a name="prerequisites"></a>Pré-requisitos

* Um aglomerado Apache Hadoop em HDInsight. Consulte [os clusters Apache Hadoop utilizando o portal Azure](../hdinsight-hadoop-create-linux-clusters-portal.md).

* [Estúdio Visual](https://visualstudio.microsoft.com/vs/community/).

## <a name="submit-mapreduce-jobs-using-hdinsight-net-sdk"></a>Enviar trabalhos mapReduce usando HDInsight .NET SDK

O HDInsight .NET SDK fornece bibliotecas de clientes .NET, que facilitam o trabalho com clusters HDInsight de .NET.

1. Inicie o Visual Studio e crie uma aplicação de consola C#.

1. Navegue para **ferramentas**  >  **nuGet package manager**package manager  >  **consola** e insira o seguinte comando:

    ```   
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```

1. Copie o código abaixo em **Program.cs**. Em seguida, edite o código definindo os valores para: `existingClusterName` , , , e `existingClusterPassword` `defaultStorageAccountName` `defaultStorageAccountKey` `defaultStorageContainerName` .

    ```csharp
    using System.Collections.Generic;
    using System.IO;
    using System.Text;
    using System.Threading;
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    
    namespace SubmitHDInsightJobDotNet
    {
        class Program
        {
            private static HDInsightJobManagementClient _hdiJobManagementClient;
    
            private const string existingClusterName = "<Your HDInsight Cluster Name>";
            private const string existingClusterPassword = "<Cluster User Password>";
            private const string defaultStorageAccountName = "<Default Storage Account Name>"; 
            private const string defaultStorageAccountKey = "<Default Storage Account Key>";
            private const string defaultStorageContainerName = "<Default Blob Container Name>";
    
            private const string existingClusterUsername = "admin";
            private const string existingClusterUri = existingClusterName + ".azurehdinsight.net";
            private const string sourceFile = "/example/data/gutenberg/davinci.txt";
            private const string outputFolder = "/example/data/davinciwordcount";
    
            static void Main(string[] args)
            {
                System.Console.WriteLine("The application is running ...");
    
                var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = existingClusterUsername, Password = existingClusterPassword };
                _hdiJobManagementClient = new HDInsightJobManagementClient(existingClusterUri, clusterCredentials);
    
                SubmitMRJob();
    
                System.Console.WriteLine("Press ENTER to continue ...");
                System.Console.ReadLine();
            }
    
            private static void SubmitMRJob()
            {
                List<string> args = new List<string> { { "/example/data/gutenberg/davinci.txt" }, { "/example/data/davinciwordcount" } };
    
                var paras = new MapReduceJobSubmissionParameters
                {
                    JarFile = @"/example/jars/hadoop-mapreduce-examples.jar",
                    JarClass = "wordcount",
                    Arguments = args
                };
    
                System.Console.WriteLine("Submitting the MR job to the cluster...");
                var jobResponse = _hdiJobManagementClient.JobManagement.SubmitMapReduceJob(paras);
                var jobId = jobResponse.JobSubmissionJsonResponse.Id;
                System.Console.WriteLine("Response status code is " + jobResponse.StatusCode);
                System.Console.WriteLine("JobId is " + jobId);
    
                System.Console.WriteLine("Waiting for the job completion ...");
    
                // Wait for job completion
                var jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                while (!jobDetail.Status.JobComplete)
                {
                    Thread.Sleep(1000);
                    jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                }
    
                // Get job output
                System.Console.WriteLine("Job output is: ");
                var storageAccess = new AzureStorageAccess(defaultStorageAccountName, defaultStorageAccountKey,
                    defaultStorageContainerName);
    
                if (jobDetail.ExitValue == 0)
                {
                    // Create the storage account object
                    CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=" +
                        defaultStorageAccountName +
                        ";AccountKey=" + defaultStorageAccountKey);
    
                    // Create the blob client.
                    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
                    // Retrieve reference to a previously created container.
                    CloudBlobContainer container = blobClient.GetContainerReference(defaultStorageContainerName);
    
                    CloudBlockBlob blockBlob = container.GetBlockBlobReference(outputFolder.Substring(1) + "/part-r-00000");
    
                    using (var stream = blockBlob.OpenRead())
                    {
                        using (StreamReader reader = new StreamReader(stream))
                        {
                            while (!reader.EndOfStream)
                            {
                                System.Console.WriteLine(reader.ReadLine());
                            }
                        }
                    }
                }
                else
                {
                    // fetch stderr output in case of failure
                    var output = _hdiJobManagementClient.JobManagement.GetJobErrorLogs(jobId, storageAccess);
    
                    using (var reader = new StreamReader(output, Encoding.UTF8))
                    {
                        string value = reader.ReadToEnd();
                        System.Console.WriteLine(value);
                    }
    
                }
            }
        }
    }

    ```

1. Prima **F5** para executar a aplicação.

Para voltar a gerir o trabalho, tem de alterar o nome da pasta de saída de trabalho, na amostra que `/example/data/davinciwordcount` é.

Quando o trabalho termina com sucesso, a aplicação imprime o conteúdo do ficheiro de saída `part-r-00000` .

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu várias formas de criar um cluster HDInsight. Para saber mais, leia os artigos seguintes:

* Para submeter um trabalho de Colmeia, consulte [consultas de Hive Run Apache utilizando HDInsight .NET SDK](apache-hadoop-use-hive-dotnet-sdk.md).
* Para criar clusters HDInsight, consulte [criar clusters Apache Hadoop baseados em Linux em HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* Para gerir clusters HDInsight, consulte [gerencie os clusters Apache Hadoop em HDInsight](../hdinsight-administer-use-portal-linux.md).
* Para aprender o HDInsight .NET SDK, consulte [a referência HDInsight .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight).
* Para autenticação não interativa para Azure, consulte [Criar aplicações de autenticação não interativa .NET HDInsight](../hdinsight-create-non-interactive-authentication-dotnet-applications.md).
