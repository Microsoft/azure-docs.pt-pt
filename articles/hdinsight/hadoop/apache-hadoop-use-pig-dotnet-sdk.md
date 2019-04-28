---
title: Executar tarefas do Apache Pig com o .NET SDK para Hadoop - Azure HDInsight
description: Saiba como utilizar o SDK de .NET para o Hadoop para submeter tarefas do Pig ao Hadoop no HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: hrasheed
ms.openlocfilehash: ebf1f2806a6606294c61860a24fb2f02033a4bf4
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62110961"
---
# <a name="run-apache-pig-jobs-using-the-net-sdk-for-apache-hadoop-in-hdinsight"></a>Executar tarefas do Apache Pig com o SDK .NET para o Apache Hadoop no HDInsight

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Saiba como utilizar o SDK de .NET para o Apache Hadoop para submeter tarefas do Apache Pig para o Hadoop no HDInsight do Azure.

O SDK de .NET do HDInsight fornece bibliotecas de cliente do .NET que torna mais fácil trabalhar com clusters do HDInsight de .NET. PIg permite-lhe criar operações de MapReduce por uma série de transformações de dados de modelagem. Neste documento, irá aprender a utilizar uma aplicação básica de c# para submeter uma tarefa do Pig a um cluster do HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir os passos neste artigo, precisa do seguinte.

* Um cluster do Azure HDInsight (Hadoop no HDInsight) (qualquer um dos Windows ou baseado em Linux).

  > [!IMPORTANT]
  > O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

* O Visual Studio 2012, 2013, 2015 ou 2017.

## <a name="create-the-application"></a>Criar a aplicação

O SDK de .NET do HDInsight fornece bibliotecas de cliente .NET, que torna mais fácil trabalhar com clusters do HDInsight de .NET.

1. Do **arquivo** menu no Visual Studio, selecione **New** e, em seguida, selecione **projeto**.

2. Para o novo projeto, escreva ou selecione os seguintes valores:

   | Propriedade | Value |
   | ------ | ------ |
   | Category | Templates/Visual C#/Windows |
   | Modelo | Aplicação de Consola |
   | Name | SubmitPigJob |

3. Clique em **OK** para criar o projeto.

4. Partir do **ferramentas** menu, selecione **Library Package Manager** ou **Gestor de pacotes NuGet**e, em seguida, selecione **Package Manager Console**.

5. Para instalar os pacotes do SDK do .NET, utilize o seguinte comando:

        Install-Package Microsoft.Azure.Management.HDInsight.Job

6. No Explorador de soluções, faça duplo clique em **Program.cs** para abri-lo. Substitua o código existente com o seguinte.

    ```csharp
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;

    namespace SubmitPigJob
    {
        class Program
        {
            private static HDInsightJobManagementClient _hdiJobManagementClient;

            private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
            private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
            private const string ExistingClusterUsername = "<Cluster Username>";
            private const string ExistingClusterPassword = "<Cluster User Password>";

            static void Main(string[] args)
            {
                System.Console.WriteLine("The application is running ...");

                var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);

                SubmitPigJob();

                System.Console.WriteLine("Press ENTER to continue ...");
                System.Console.ReadLine();
            }

            private static void SubmitPigJob()
            {
                var parameters = new PigJobSubmissionParameters
                {
                    Query = @"LOGS = LOAD '/example/data/sample.log';
                                LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
                                FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
                                GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
                                FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
                                RESULT = order FREQUENCIES by COUNT desc;
                                DUMP RESULT;"
                };

                System.Console.WriteLine("Submitting the Pig job to the cluster...");
                var response = _hdiJobManagementClient.JobManagement.SubmitPigJob(parameters);
                System.Console.WriteLine("Validating that the response is as expected...");
                System.Console.WriteLine("Response status code is " + response.StatusCode);
                System.Console.WriteLine("Validating the response object...");
                System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
            }
        }
    }
    ```

7. Para iniciar a aplicação, prima **F5**.

8. Para sair da aplicação, prima **ENTER**.

## <a name="next-steps"></a>Passos Seguintes

Para obter informações sobre o Pig no HDInsight, consulte [utilizar o Apache Pig com o Apache Hadoop no HDInsight](hdinsight-use-pig.md).

Para obter mais informações sobre como utilizar o Hadoop no HDInsight, consulte os seguintes documentos:

* [Utilizar o Apache Hive com o Apache Hadoop no HDInsight](hdinsight-use-hive.md)
* [Utilizar o MapReduce com o Apache Hadoop no HDInsight](hdinsight-use-mapreduce.md)

[preview-portal]: https://portal.azure.com/
