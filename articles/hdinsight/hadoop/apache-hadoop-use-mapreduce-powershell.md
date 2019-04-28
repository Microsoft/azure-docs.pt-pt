---
title: Utilizar o MapReduce e o PowerShell com o Apache Hadoop - Azure HDInsight
description: Saiba como utilizar o PowerShell para executar remotamente tarefas de MapReduce com o Apache Hadoop no HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 21b56d32-1785-4d44-8ae8-94467c12cfba
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
origin.date: 05/09/2018
ms.date: 04/15/2019
ms.author: v-yiso
ms.openlocfilehash: 29e23d5919a953566c803f2b7825a75a2993723c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62129030"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-powershell"></a>Executar tarefas de MapReduce com o Apache Hadoop no HDInsight com o PowerShell

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]



Este documento fornece um exemplo de utilização do Azure PowerShell para executar uma tarefa de MapReduce num Hadoop num cluster do HDInsight.

## <a id="prereq"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* **Um cluster do Azure HDInsight (Hadoop no HDInsight)**

  > [!IMPORTANT]
  > O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

* **Uma estação de trabalho com o Azure PowerShell**.

## <a id="powershell"></a>Executar uma tarefa de MapReduce

O Azure PowerShell disponibiliza *cmdlets* que permitem-lhe executar remotamente tarefas de MapReduce no HDInsight. Internamente, o PowerShell faz chamadas REST para [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (anteriormente denominadas Templeton) em execução no cluster do HDInsight.

Os cmdlets seguintes são utilizados quando executar tarefas de MapReduce num cluster do HDInsight remoto.

* **Connect-AzAccount**: Efetua a autenticação do Azure PowerShell para a sua subscrição do Azure.

* **New-AzHDInsightMapReduceJobDefinition**: Cria uma nova *definição de tarefa* usando as informações de MapReduce especificadas.

* **Start-AzHDInsightJob**: Envia a definição de tarefa ao HDInsight e inicia a tarefa. R *tarefa* objeto é devolvido.

* **Wait-AzHDInsightJob**: Usa o objeto de tarefa para verificar o estado da tarefa. Ele aguarda até que a tarefa é concluída ou o tempo de espera foi excedido.

* **Get-AzHDInsightJobOutput**: Usada para recuperar o resultado da tarefa.

Os passos seguintes demonstram como utilizar estes cmdlets para executar uma tarefa no seu cluster do HDInsight.

1. Com um editor, guarde o código a seguir como **mapreducejob.ps1**.

    ```powershell
    # Login to your Azure subscription
    # Is there an active Azure subscription?
    $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Add-AzureRmAccount -EnvironmentName AzureChinaCloud
    }

    # Get cluster info
    $clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
    $creds=Get-Credential -Message "Enter the login for the cluster"

    #Get the cluster info so we can get the resource group, storage, etc.
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroup = $clusterInfo.ResourceGroup
    $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
    $container=$clusterInfo.DefaultStorageContainer
    #NOTE: This assumes that the storage account is in the same resource
    #      group as the cluster. If it is not, change the
    #      --ResourceGroupName parameter to the group that contains storage.
    $storageAccountKey=(Get-AzureRmStorageAccountKey `
        -Name $storageAccountName `
    -ResourceGroupName $resourceGroup)[0].Value

    #Create a storage context
    $context = New-AzureStorageContext `
        -StorageAccountName $storageAccountName `
        -StorageAccountKey $storageAccountKey

    #Define the MapReduce job
    #NOTE: If using an HDInsight 2.0 cluster, use hadoop-examples.jar instead.
    # -JarFile = the JAR containing the MapReduce application
    # -ClassName = the class of the application
    # -Arguments = The input file, and the output directory
    $wordCountJobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
        -JarFile "/example/jars/hadoop-mapreduce-examples.jar" `
        -ClassName "wordcount" `
        -Arguments `
            "/example/data/gutenberg/davinci.txt", `
            "/example/data/WordCountOutput"

    #Submit the job to the cluster
    Write-Host "Start the MapReduce job..." -ForegroundColor Green
    $wordCountJob = Start-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $wordCountJobDefinition `
        -HttpCredential $creds

    #Wait for the job to complete
    Write-Host "Wait for the job to complete..." -ForegroundColor Green
    Wait-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobId $wordCountJob.JobId `
        -HttpCredential $creds
    # Download the output
    Get-AzureStorageBlobContent `
        -Blob 'example/data/WordCountOutput/part-r-00000' `
        -Container $container `
        -Destination output.txt `
        -Context $context
    # Print the output of the job.
    Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $wordCountJob.JobId `
        -HttpCredential $creds
    ```

2. Abra uma nova **do Azure PowerShell** prompt de comando. Altere os diretórios para a localização do **mapreducejob.ps1** de ficheiros, em seguida, utilize o seguinte comando para executar o script:

        .\mapreducejob.ps1

    Ao executar o script, lhe for pedido para o nome do HDInsight cluster e o início de sessão do cluster. Também poderá ser solicitado para autenticar a sua subscrição do Azure.

3. Quando a tarefa estiver concluída, recebe um resultado semelhante ao seguinte texto:

        Cluster         : CLUSTERNAME
        ExitCode        : 0
        Name            : wordcount
        PercentComplete : map 100% reduce 100%
        Query           :
        State           : Completed
        StatusDirectory : f1ed2028-afe8-402f-a24b-13cc17858097
        SubmissionTime  : 12/5/2014 8:34:09 PM
        JobId           : job_1415949758166_0071

    Este resultado indica que a tarefa foi concluída com êxito.

    > [!NOTE]
    > Se o **ExitCode** é um valor diferente de 0, veja [resolução de problemas](#troubleshooting).

    Este exemplo também armazena os ficheiros transferidos para uma **output.txt** ficheiro no diretório que executar o script.

### <a name="view-output"></a>Saída do modo de exibição

Para ver as palavras e contagens de produzido pela tarefa, abra a **output.txt** ficheiro num editor de texto.

> [!NOTE]
> Os ficheiros de saída de uma tarefa de MapReduce são imutáveis. Portanto, se voltar a executar este exemplo, terá de alterar o nome do ficheiro de saída.

## <a id="troubleshooting"></a>Resolução de Problemas

Se nenhuma informação é retornada quando a tarefa é concluída, ver os erros para a tarefa. Para ver informações de erro para esta tarefa, adicione o seguinte comando ao final dos **mapreducejob.ps1** de ficheiros, guarde-o e, em seguida, execute-o novamente.

```powershell
# Print the output of the WordCount job.
Write-Host "Display the standard output ..." -ForegroundColor Green
Get-AzHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $wordCountJob.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Este cmdlet devolve as informações de que foi escritas para STDERR como a execução da tarefa.

## <a id="summary"></a>Resumo

Como pode ver, o Azure PowerShell fornece uma forma fácil de executar tarefas de MapReduce num cluster do HDInsight, monitorizar o estado da tarefa e obter o resultado.

## <a id="nextsteps"></a>Passos seguintes

Para obter informações gerais sobre tarefas de MapReduce no HDInsight:

* [Utilizar o MapReduce no HDInsight Hadoop](hdinsight-use-mapreduce.md)

Para obter informações sobre outras formas pode trabalhar com o Hadoop no HDInsight:

* [Utilizar o Apache Hive com o Apache Hadoop no HDInsight](hdinsight-use-hive.md)
* [Utilizar o Apache Pig com o Apache Hadoop no HDInsight](hdinsight-use-pig.md)
