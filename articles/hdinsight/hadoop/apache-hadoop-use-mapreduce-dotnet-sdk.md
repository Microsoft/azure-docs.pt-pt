---
title: Submeter tarefas de MapReduce com o SDK .NET do HDInsight - Azure
description: Aprenda a submeter tarefas de MapReduce para o Azure HDInsight Apache Hadoop com o SDK de .NET do HDInsight.
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: 1ac2dda20ba1219c9f62e834b5cd2cfba8a50086
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64718952"
---
# <a name="run-mapreduce-jobs-using-hdinsight-net-sdk"></a>Executar tarefas de MapReduce com o HDInsight .NET SDK
[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Aprenda a submeter tarefas de MapReduce com o SDK de .NET do HDInsight. HDInsight clusters vêm com um ficheiro. jar com alguns exemplos de MapReduce. É o ficheiro jar */example/jars/hadoop-mapreduce-examples.jar*.  Um dos exemplos é *wordcount*. Desenvolver uma aplicação de consola c# para submeter um trabalho de wordcount.  A tarefa lê a */example/data/gutenberg/davinci.txt* de ficheiros e produz os resultados */example/data/davinciwordcount*.  Se pretender voltar a executar a aplicação, tem de limpar a pasta de saída.

> [!NOTE]  
> Os passos neste artigo devem ser executados a partir de um cliente do Windows. Para obter informações sobre como usar um Linux, o OS X ou o cliente do Unix para trabalhar com o Hive, utilize o Seletor de separador apresentado na parte superior do artigo.
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este artigo, tem de ter os seguintes itens:

* **Um cluster do Hadoop no HDInsight**. Ver [começar a utilizar o Apache Hadoop baseado em Linux no HDInsight](apache-hadoop-linux-tutorial-get-started.md).
* **Visual Studio 2013/2015/2017**.

## <a name="submit-mapreduce-jobs-using-hdinsight-net-sdk"></a>Submeter tarefas de MapReduce com o HDInsight .NET SDK
O SDK de .NET do HDInsight fornece bibliotecas de cliente .NET, que torna mais fácil trabalhar com clusters do HDInsight de .NET. 

**Submeter tarefas**

1. Crie uma aplicação de consola c# no Visual Studio.
2. A partir da consola do Gestor de pacotes NuGet, execute o seguinte comando:

    ```   
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```
3. Utilize o seguinte código:

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
            private const string existingClusterUri = existingClusterName + ".azurehdinsight.net";
            private const string existingClusterUsername = "<Cluster Username>";
            private const string existingClusterPassword = "<Cluster User Password>";

            private const string defaultStorageAccountName = "<Default Storage Account Name>"; //<StorageAccountName>.blob.core.windows.net
            private const string defaultStorageAccountKey = "<Default Storage Account Key>";
            private const string defaultStorageContainerName = "<Default Blob Container Name>";

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

4. Prima **F5** para executar a aplicação.

Para executar novamente a tarefa, tem de alterar o nome da pasta de saída do trabalho, no exemplo, é "/ exemplo/dados/davinciwordcount".

Quando a tarefa for concluída com êxito, o aplicativo imprime o conteúdo do ficheiro de saída "parte-r-00000".

## <a name="next-steps"></a>Passos Seguintes
Neste artigo, aprendeu a várias formas de criar um cluster do HDInsight. Para obter mais informações, consulte os artigos seguintes:

* Para submeter uma tarefa do Hive, consulte [executar Apache consultas do Hive com o HDInsight .NET SDK](apache-hadoop-use-hive-dotnet-sdk.md).
* Para criar clusters do HDInsight, consulte [Apache Hadoop baseado em Linux criar clusters no HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* Para gerir clusters do HDInsight, consulte [Apache Hadoop gerir clusters no HDInsight](../hdinsight-administer-use-portal-linux.md).
* Para aprender o SDK de .NET do HDInsight, consulte [referência do SDK de .NET do HDInsight](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight).
* Para não-interativa autenticar para o Azure, consulte [criar aplicações de HDInsight de .NET de autenticação não interativa](../hdinsight-create-non-interactive-authentication-dotnet-applications.md).

