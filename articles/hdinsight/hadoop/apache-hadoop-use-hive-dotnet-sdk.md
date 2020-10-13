---
title: Executar consultas de Hive Apache usando HDInsight .NET SDK - Azure
description: Saiba como submeter os empregos da Apache Hadoop à Azure HDInsight Apache Hadoop utilizando HDInsight .NET SDK.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 12/24/2019
ms.openlocfilehash: cdb2207ab834ab84cedd2ad23e5e26186ac44039
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89020478"
---
# <a name="run-apache-hive-queries-using-hdinsight-net-sdk"></a>Executar consultas de Hive Apache usando HDInsight .NET SDK

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Saiba como submeter consultas apache Hive usando HDInsight .NET SDK. Você escreve um programa C# para submeter uma consulta de Hive para listar tabelas de Colmeia, e mostrar os resultados.

> [!NOTE]  
> Os passos deste artigo devem ser realizados a partir de um cliente Windows. Para obter informações sobre a utilização de um cliente Linux, OS X ou Unix para trabalhar com a Hive, utilize o seletor de separadores mostrado no topo do artigo.

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este artigo, deve ter os seguintes itens:

* Um aglomerado Apache Hadoop em HDInsight. See [Get start using Linux-based Hadoop in HDInsight](apache-hadoop-linux-tutorial-get-started.md).

    > [!IMPORTANT]  
    > A partir de 15 de setembro de 2017, o HDInsight .NET SDK apenas suporta a devolução dos resultados da consulta da Hive a partir das contas de Armazenamento Azure. Se utilizar este exemplo com um cluster HDInsight que utiliza o Azure Data Lake Storage como armazenamento primário, não é possível obter resultados de pesquisa utilizando o .NET SDK.

* [Visual Studio](https://visualstudio.microsoft.com/vs/community/) 2013 e mais além. Deve ser instalada, pelo menos, o desenvolvimento da carga de trabalho **.NET.**

## <a name="run-a-hive-query"></a>Executar uma consulta de colmeia

O HDInsight .NET SDK fornece bibliotecas de clientes .NET, o que facilita o trabalho com clusters HDInsight de .NET.

1. Crie uma aplicação de consola C# no Visual Studio.

1. A partir da Consola Nuget Package Manager, executar o seguinte comando:

    ```console
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```

1. Editar o código abaixo para rubricar os valores para variáveis: `ExistingClusterName, ExistingClusterUsername, ExistingClusterPassword,DefaultStorageAccountName,DefaultStorageAccountKey,DefaultStorageContainerName` . Em seguida, use o código revisto como todo o conteúdo de **Program.cs** em Visual Studio.

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

A saída da aplicação deve ser semelhante:

![Saída de trabalho da DDInsight Hadoop Hive](./media/apache-hadoop-use-hive-dotnet-sdk/hdinsight-hadoop-use-hive-net-sdk-output.png)

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a submeter consultas de Hive Apache usando HDInsight .NET SDK. Para saber mais, leia os artigos seguintes:

* [Get started with Azure HDInsight (Introdução ao Azure HDInsight)](apache-hadoop-linux-tutorial-get-started.md)
* [Criar clusters Apache Hadoop em HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
* [HdInsight .NET SDK referência](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight)
* [Use Apache Sqoop com HDInsight](apache-hadoop-use-sqoop-mac-linux.md)
* [Criar aplicações do HDInsight de .NET de autenticação não interativa](../hdinsight-create-non-interactive-authentication-dotnet-applications.md)
