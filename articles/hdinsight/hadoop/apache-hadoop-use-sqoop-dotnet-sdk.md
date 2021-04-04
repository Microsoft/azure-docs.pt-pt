---
title: Executar trabalhos apache Sqoop usando .NET e HDInsight - Azure
description: Saiba como utilizar o HDInsight .NET SDK para gerir a importação e exportação da Apache Sqoop entre um cluster Apache Hadoop e uma Base de Dados Azure SQL.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, hdiseo17may2017, devx-track-csharp
ms.date: 01/14/2020
ms.openlocfilehash: a06326f5b4cf34a06475c10e368c6dcbb49ca9e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98928340"
---
# <a name="run-apache-sqoop-jobs-by-using-net-sdk-for-apache-hadoop-in-hdinsight"></a>Executar apaches sqoop empregos usando .NET SDK para Apache Hadoop em HDInsight

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Saiba como utilizar o Azure HDInsight .NET SDK para executar empregos Apache Sqoop em HDInsight para importar e exportar entre um cluster HDInsight e uma base de dados Azure SQL Database ou SQL Server.

## <a name="prerequisites"></a>Pré-requisitos

* Conclusão do ambiente de teste de [configuração](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) da [Utilização de Apache Sqoop com Hadoop em HDInsight](./hdinsight-use-sqoop.md).

* [Estúdio Visual](https://visualstudio.microsoft.com/vs/community/).

* Familiaridade com Sqoop. Para mais informações, consulte [o Guia do Utilizador sqoop.](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html)

## <a name="use-sqoop-on-hdinsight-clusters-with-the-net-sdk"></a>Utilize o Sqoop em clusters HDInsight com o .NET SDK

O HDInsight .NET SDK fornece bibliotecas de clientes .NET, de modo que é mais fácil trabalhar com clusters HDInsight de .NET. Nesta secção, cria-se uma aplicação de consola C# para exportar `hivesampletable` para a tabela de base de dados Azure SQL que criou a partir dos pré-requisitos.

## <a name="set-up"></a>Configurar

1. Inicie o Visual Studio e crie uma aplicação de consola C#.

1. Navegue para **ferramentas**  >  **nuGet package manager** package manager  >  **consola** e executar o seguinte comando:

    ```
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```

## <a name="sqoop-export"></a>Exportação de Sqoop

Da Colmeia ao SQL Server.  Este exemplo exporta dados da tabela Hive `hivesampletable` para a tabela na Base de `mobiledata` Dados SQL.

1. Utilize o seguinte código no ficheiro .cs Programa. Editar o código para definir os valores para `ExistingClusterName` , e `ExistingClusterPassword` .

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

Do SQL Server ao Azure Storage. Este exemplo depende da exportação acima referida.  Este exemplo importa dados da `mobiledata` tabela na Base de Dados SQL para o `wasb:///tutorials/usesqoop/importeddata` diretório na Conta de Armazenamento padrão do cluster.

1. Substitua o código acima no `//sqoop start //sqoop end` bloco pelo seguinte código:

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

A HDInsight baseada em Linux apresenta as seguintes limitações:

* Exportação a granel: O conector Sqoop que é utilizado para exportar dados para o Microsoft SQL Server ou Azure SQL Database não suporta atualmente inserções a granel.

* Lote: Ao utilizar o interruptor, a `-batch` Sqoop executa múltiplas inserções em vez de emqualamento das operações de inserção.

## <a name="next-steps"></a>Passos seguintes

Agora aprendeste a usar o Sqoop. Para saber mais, veja:

* [Use Apache Oozie com HDInsight](../hdinsight-use-oozie-linux-mac.md): Use a ação Sqoop num fluxo de trabalho Oozie.
* [Faça upload de dados para HDInsight](../hdinsight-upload-data.md): Encontre outros métodos para enviar dados para o armazenamento de HDInsight ou Azure Blob.
