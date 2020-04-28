---
title: Executar consultas de Hiv Apache usando HDInsight .NET SDK - Azure
description: Saiba como submeter os empregos de Apache Hadoop ao Azure HDInsight Apache Hadoop utilizando hDInsight .NET SDK.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/24/2019
ms.openlocfilehash: a9d71c8aebb9cc4a0adbd461aead6e2612bd13bd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75552496"
---
# <a name="run-apache-hive-queries-using-hdinsight-net-sdk"></a>Executar consultas de Hiv Apache usando HDInsight .NET SDK

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Saiba como submeter consultas apache hive usando HDInsight .NET SDK. Escreve um programa C# para submeter uma consulta da Hive para listar tabelas da Hive e exibir os resultados.

> [!NOTE]  
> Os passos deste artigo devem ser realizados a partir de um cliente windows. Para obter informações sobre a utilização de um cliente Linux, OS X ou Unix para trabalhar com a Hive, utilize o seletor de separadores mostrado na parte superior do artigo.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este artigo, deve ter os seguintes itens:

* Um aglomerado Apache Hadoop no HDInsight. Ver [Get começou a usar Hadoop baseado em Linux no HDInsight](apache-hadoop-linux-tutorial-get-started.md).

    > [!IMPORTANT]  
    > A partir de 15 de setembro de 2017, o HDInsight .NET SDK apenas suporta a devolução dos resultados da consulta da Hive das contas de Armazenamento Azure. Se utilizar este exemplo com um cluster HDInsight que utiliza o Armazenamento de Lagos De Dados Azure como armazenamento primário, não é possível recuperar os resultados da pesquisa utilizando o .NET SDK.

* [Estúdio Visual](https://visualstudio.microsoft.com/vs/community/) 2013 e mais além. Pelo menos deve ser instalado o desenvolvimento do ambiente de **trabalho .NET.**

## <a name="run-a-hive-query"></a>Executar uma consulta de colmeia

O HDInsight .NET SDK fornece bibliotecas de clientes .NET, o que facilita o trabalho com clusters HDInsight de .NET.

1. Crie uma aplicação de consola C# no Estúdio Visual.

1. A partir da Consola nuget Package Manager, executar o seguinte comando:

        Install-Package Microsoft.Azure.Management.HDInsight.Job

1. Editar o código abaixo para inicializar `ExistingClusterName, ExistingClusterUsername, ExistingClusterPassword,DefaultStorageAccountName,DefaultStorageAccountKey,DefaultStorageContainerName`os valores das variáveis: . Em seguida, use o código revisto como todo o conteúdo de **Program.cs** no Estúdio Visual.

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

A saída do pedido deve ser semelhante a:

![Saída de emprego da Colmeia Hadoop HDInsight](./media/apache-hadoop-use-hive-dotnet-sdk/hdinsight-hadoop-use-hive-net-sdk-output.png)

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a submeter consultas apache hive usando HDInsight .NET SDK. Para saber mais, consulte os seguintes artigos:

* [Get started with Azure HDInsight (Introdução ao Azure HDInsight)](apache-hadoop-linux-tutorial-get-started.md)
* [Criar clusters Apache Hadoop em HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
* [Referência HDInsight .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight)
* [Use Apache Sqoop com HDInsight](apache-hadoop-use-sqoop-mac-linux.md)
* [Criar aplicações do HDInsight de .NET de autenticação não interativa](../hdinsight-create-non-interactive-authentication-dotnet-applications.md)
