---
title: Utilizar o MapReduce e o PowerShell com o Apache Hadoop - Azure HDInsight
description: Saiba como utilizar o PowerShell para executar remotamente tarefas de MapReduce com o Apache Hadoop no HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: hrasheed
ms.openlocfilehash: 015728a43e091e36dcf02b5cc17f0135a64428ca
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58361953"
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

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-mapreduce/use-mapreduce.ps1?range=5-69)]

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
