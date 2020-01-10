---
title: Executar Apache Hive consultas usando o SDK do .NET do HDInsight – Azure
description: Saiba como enviar Apache Hadoop trabalhos para o Azure HDInsight Apache Hadoop usando o SDK do .NET do HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/24/2019
ms.openlocfilehash: a9d71c8aebb9cc4a0adbd461aead6e2612bd13bd
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552496"
---
# <a name="run-apache-hive-queries-using-hdinsight-net-sdk"></a>Executar Apache Hive consultas usando o SDK do .NET do HDInsight

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Saiba como enviar Apache Hive consultas usando o SDK do .NET do HDInsight. Você escreve um C# programa para enviar uma consulta de Hive para listar tabelas do hive e exibir os resultados.

> [!NOTE]  
> As etapas neste artigo devem ser executadas em um cliente Windows. Para obter informações sobre como usar um cliente Linux, OS X ou UNIX para trabalhar com o Hive, use o seletor de guias mostrado na parte superior do artigo.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este artigo, você deve ter os seguintes itens:

* Um cluster Apache Hadoop no HDInsight. Confira [introdução ao uso do Hadoop baseado em Linux no HDInsight](apache-hadoop-linux-tutorial-get-started.md).

    > [!IMPORTANT]  
    > A partir de 15 de setembro de 2017, o SDK do .NET do HDInsight só dá suporte ao retorno de resultados de consulta do hive de contas de armazenamento do Azure. Se você usar este exemplo com um cluster HDInsight que usa Azure Data Lake Storage como armazenamento primário, não será possível recuperar os resultados da pesquisa usando o SDK do .NET.

* [Visual Studio](https://visualstudio.microsoft.com/vs/community/) 2013 e posterior. No mínimo, o **desenvolvimento de área de trabalho do .net** deve ser instalado.

## <a name="run-a-hive-query"></a>Executar uma consulta de Hive

O SDK do .NET do HDInsight fornece bibliotecas de cliente .NET, o que torna mais fácil trabalhar com clusters HDInsight do .NET.

1. Criar um C# aplicativo de console no Visual Studio.

1. No console do Gerenciador de pacotes NuGet, execute o seguinte comando:

        Install-Package Microsoft.Azure.Management.HDInsight.Job

1. Edite o código abaixo para inicializar os valores de variáveis: `ExistingClusterName, ExistingClusterUsername, ExistingClusterPassword,DefaultStorageAccountName,DefaultStorageAccountKey,DefaultStorageContainerName`. Em seguida, use o código revisado como todo o conteúdo de **Program.cs** no Visual Studio.

    ```csharp
    using System.Collections.Generic;
    using System.IO;
    using System.Text;
    using System.Threading;
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;

    namespace SubmitHDInsightJobDotNet
    {
        class Program
        {
            private static HDInsightJobManagementClient _hdiJobManagementClient;

            private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
            private const string ExistingClusterUsername = "<Cluster Username>";
            private const string ExistingClusterPassword = "<Cluster User Password>";

            // Only Azure Storage accounts are supported by the SDK
            private const string DefaultStorageAccountName = "<Default Storage Account Name>";
            private const string DefaultStorageAccountKey = "<Default Storage Account Key>";
            private const string DefaultStorageContainerName = "<Default Blob Container Name>";

            private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";

            static void Main(string[] args)
            {
                System.Console.WriteLine("The application is running ...");

                var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);

                SubmitHiveJob();

                System.Console.WriteLine("Press ENTER to continue ...");
                System.Console.ReadLine();
            }

            private static void SubmitHiveJob()
            {
                Dictionary<string, string> defines = new Dictionary<string, string> { { "hive.execution.engine", "tez" }, { "hive.exec.reducers.max", "1" } };
                List<string> args = new List<string> { { "argA" }, { "argB" } };
                var parameters = new HiveJobSubmissionParameters
                {
                    Query = "SHOW TABLES",
                    Defines = defines,
                    Arguments = args
                };

                System.Console.WriteLine("Submitting the Hive job to the cluster...");
                var jobResponse = _hdiJobManagementClient.JobManagement.SubmitHiveJob(parameters);
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
                var storageAccess = new AzureStorageAccess(DefaultStorageAccountName, DefaultStorageAccountKey,
                    DefaultStorageContainerName);
                var output = (jobDetail.ExitValue == 0)
                    ? _hdiJobManagementClient.JobManagement.GetJobOutput(jobId, storageAccess) // fetch stdout output in case of success
                    : _hdiJobManagementClient.JobManagement.GetJobErrorLogs(jobId, storageAccess); // fetch stderr output in case of failure

                System.Console.WriteLine("Job output is: ");

                using (var reader = new StreamReader(output, Encoding.UTF8))
                {
                    string value = reader.ReadToEnd();
                    System.Console.WriteLine(value);
                }
            }
        }
    }
    ```

1. Prima **F5** para executar a aplicação.

A saída do aplicativo deve ser semelhante a:

![Saída do trabalho do hive do HDInsight Hadoop](./media/apache-hadoop-use-hive-dotnet-sdk/hdinsight-hadoop-use-hive-net-sdk-output.png)

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu a enviar consultas de Apache Hive usando o SDK do .NET do HDInsight. Para saber mais, confira os seguintes artigos:

* [Introdução ao Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md)
* [Criar clusters Apache Hadoop no HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
* [Referência do SDK do .NET do HDInsight](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight)
* [Usar o Apache Sqoop com o HDInsight](apache-hadoop-use-sqoop-mac-linux.md)
* [Criar aplicações do HDInsight de .NET de autenticação não interativa](../hdinsight-create-non-interactive-authentication-dotnet-applications.md)
