---
title: Enviar trabalhos MapReduce usando o SDK do .NET do HDInsight – Azure
description: Saiba como enviar trabalhos MapReduce para o Azure HDInsight Apache Hadoop usando o SDK do .NET do HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/15/2020
ms.openlocfilehash: e50510f2420d69be37af584a2648a794e1561ee3
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76157056"
---
# <a name="run-mapreduce-jobs-using-hdinsight-net-sdk"></a>Executar trabalhos MapReduce usando o SDK do .NET do HDInsight

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Saiba como enviar trabalhos MapReduce usando o SDK do .NET do HDInsight. Os clusters HDInsight vêm com um arquivo jar com alguns exemplos do MapReduce. O arquivo jar está `/example/jars/hadoop-mapreduce-examples.jar`.  Um dos exemplos é **WordCount**. Você desenvolve um C# aplicativo de console para enviar um trabalho do WordCount.  O trabalho lê o arquivo de `/example/data/gutenberg/davinci.txt` e gera os resultados para `/example/data/davinciwordcount`.  Se você quiser executar novamente o aplicativo, deverá limpar a pasta de saída.

> [!NOTE]  
> As etapas neste artigo devem ser executadas em um cliente Windows. Para obter informações sobre como usar um cliente Linux, OS X ou UNIX para trabalhar com o Hive, use o seletor de guias mostrado na parte superior do artigo.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster Apache Hadoop no HDInsight. Consulte [criar Apache Hadoop clusters usando o portal do Azure](../hdinsight-hadoop-create-linux-clusters-portal.md).

* [Visual Studio](https://visualstudio.microsoft.com/vs/community/).

## <a name="submit-mapreduce-jobs-using-hdinsight-net-sdk"></a>Enviar trabalhos MapReduce usando o SDK do .NET do HDInsight

O SDK do .NET do HDInsight fornece bibliotecas de cliente .NET, que facilitam o trabalho com clusters HDInsight do .NET.

1. Inicie o Visual Studio e crie C# um aplicativo de console.

1. Navegue até **ferramentas** > **Gerenciador de pacotes NuGet** > **console do Gerenciador de pacotes** e digite o seguinte comando:

    ```   
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```

1. Copie o código abaixo em **Program.cs**. Em seguida, edite o código definindo os valores para: `existingClusterName`, `existingClusterPassword`, `defaultStorageAccountName`, `defaultStorageAccountKey`e `defaultStorageContainerName`.

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

Para executar o trabalho novamente, você deve alterar o nome da pasta de saída do trabalho, no exemplo `/example/data/davinciwordcount`.

Quando o trabalho for concluído com êxito, o aplicativo imprime o conteúdo do arquivo de saída `part-r-00000`.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu várias maneiras de criar um cluster HDInsight. Para saber mais, confira os seguintes artigos:

* Para enviar um trabalho do hive, consulte [executar consultas de Apache Hive usando o SDK do .net do HDInsight](apache-hadoop-use-hive-dotnet-sdk.md).
* Para criar clusters HDInsight, confira [Criar clusters de Apache Hadoop baseados em Linux no HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* Para gerenciar clusters HDInsight, consulte [gerenciar clusters de Apache Hadoop no HDInsight](../hdinsight-administer-use-portal-linux.md).
* Para aprender sobre o SDK do .NET do HDInsight, consulte [referência do SDK do .net do hdinsight](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight).
* Para autenticar não interativo no Azure, consulte [criar aplicativos .net HDInsight de autenticação não interativa](../hdinsight-create-non-interactive-authentication-dotnet-applications.md).