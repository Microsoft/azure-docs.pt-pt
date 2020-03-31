---
title: Executar trabalhos Apache Sqoop usando .NET e HDInsight - Azure
description: Aprenda a utilizar o HDInsight .NET SDK para executar a importação e exportação de Apache Sqoop entre um cluster Apache Hadoop e uma Base de Dados Azure SQL.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 01/14/2020
ms.openlocfilehash: f0f767273a40bc91b1d49477c896b0b157623106
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76157071"
---
# <a name="run-apache-sqoop-jobs-by-using-net-sdk-for-apache-hadoop-in-hdinsight"></a>Executar empregos Apache Sqoop usando .NET SDK para Apache Hadoop em HDInsight

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Saiba como utilizar o Azure HDInsight .NET SDK para executar empregos Apache Sqoop no HDInsight para importar e exportar entre um cluster HDInsight e uma base de dados Azure SQL ou sQL Server.

## <a name="prerequisites"></a>Pré-requisitos

* Conclusão do ambiente de teste de [configuração](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) a partir de [Use Apache Sqoop com Hadoop em HDInsight](./hdinsight-use-sqoop.md).

* [Estúdio Visual.](https://visualstudio.microsoft.com/vs/community/)

* Familiaridade com Sqoop. Para mais informações, consulte o [Guia do Utilizador da Sqoop](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html).

## <a name="use-sqoop-on-hdinsight-clusters-with-the-net-sdk"></a>Utilize Sqoop em clusters HDInsight com o .NET SDK

O HDInsight .NET SDK fornece bibliotecas de clientes .NET, de modo que é mais fácil trabalhar com clusters HDInsight de .NET. Nesta secção, cria-se uma aplicação `hivesampletable` de consola C# para exportar para a tabela base de dados Azure SQL que criou a partir dos pré-requisitos.

## <a name="set-up"></a>Configurar

1. Inicie o Visual Studio e crie uma aplicação de consola C#.

1. Navegue para **ferramentas** > **NuGet Package Manager** > **Manager Console** e execute o seguinte comando:

    ```
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```

## <a name="sqoop-export"></a>Exportação de Sqoop

Da Colmeia ao Servidor SQL.  Este exemplo exporta dados `hivesampletable` da `mobiledata` tabela Da Colmeia para a tabela na Base de Dados SQL.

1. Utilize o seguinte código no ficheiro Program.cs. Editar o código para `ExistingClusterName`definir `ExistingClusterPassword`os valores para, e .

    ```csharp
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;
    
    namespace SubmitHDInsightJobDotNet
    {
        class Program
        {
            private static HDInsightJobManagementClient _hdiJobManagementClient;
    
            private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
            private const string ExistingClusterPassword = "<Cluster User Password>";
            private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
            private const string ExistingClusterUsername = "admin";
    
            static void Main(string[] args)
            {
                System.Console.WriteLine("The application is running ...");
    
                var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
    
                SubmitSqoopJob();
    
                System.Console.WriteLine("Press ENTER to continue ...");
                System.Console.ReadLine();
            }
    
            private static void SubmitSqoopJob()
            {
                var sqlDatabaseServerName = ExistingClusterName + "dbserver";
                var sqlDatabaseLogin = "sqluser";
                var sqlDatabaseLoginPassword = ExistingClusterPassword;
                var sqlDatabaseDatabaseName = ExistingClusterName + "db";
    
                // Connection string for using Azure SQL Database; Comment if using SQL Server
                var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
    
                // Connection string for using SQL Server; Uncomment if using SQL Server
                // var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
    
                //sqoop start
                var tableName = "mobiledata";
    
                var parameters = new SqoopJobSubmissionParameters
                {
                     Command = "export --connect " + connectionString + " --table " + tableName + " --hcatalog-table hivesampletable"
                };
                //sqoop end
    
                System.Console.WriteLine("Submitting the Sqoop job to the cluster...");
                var response = _hdiJobManagementClient.JobManagement.SubmitSqoopJob(parameters);
                System.Console.WriteLine("Validating that the response is as expected...");
                System.Console.WriteLine("Response status code is " + response.StatusCode);
                System.Console.WriteLine("Validating the response object...");
                System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
            }
        }
    }
    ```

1. Para executar o programa, selecione a tecla **F5.**

## <a name="sqoop-import"></a>Importação de Sqoop

Do Servidor SQL ao Armazenamento Azure. Este exemplo depende da exportação acima referida ter sido realizada.  Este exemplo importa dados `mobiledata` da tabela na Base `wasb:///tutorials/usesqoop/importeddata` de Dados SQL para o diretório na conta de armazenamento padrão do cluster.

1. Substitua o código `//sqoop start //sqoop end` acima no bloco pelo seguinte código:

    ```csharp
    var tableName = "mobiledata";
    var exportDir = "/tutorials/usesqoop/importeddata";
    
    var parameters = new SqoopJobSubmissionParameters
    {
        Command = "import --connect " + connectionString + " --table " + tableName + " --target-dir " +  exportDir + " --fields-terminated-by \\t --lines-terminated-by \\n -m 1"
    };
    ```

1. Para executar o programa, selecione a tecla **F5.**

## <a name="limitations"></a>Limitações

HDInsight baseado em Linux apresenta as seguintes limitações:

* Exportação a granel: O conector Sqoop que é usado para exportar dados para o Microsoft SQL Server ou Azure SQL Database não suporta atualmente inserções a granel.

* Loteamento: Ao `-batch` utilizar o interruptor, o Sqoop executa várias inserções em vez de emlotar as operações de inserção.

## <a name="next-steps"></a>Passos seguintes

Agora aprendeste a usar o Sqoop. Para saber mais, consulte:

* [Utilize o Apache Oozie com hDInsight](../hdinsight-use-oozie-linux-mac.md): Use a ação Sqoop num fluxo de trabalho Oozie.
* [Upload de dados para HDInsight](../hdinsight-upload-data.md): Encontre outros métodos para o upload de dados para armazenamento HDInsight ou Azure Blob.
