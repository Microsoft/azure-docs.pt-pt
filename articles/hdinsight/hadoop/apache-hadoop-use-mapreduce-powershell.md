---
title: Usar o MapReduce e o PowerShell com o Apache Hadoop-Azure HDInsight
description: Saiba como usar o PowerShell para executar remotamente trabalhos do MapReduce com o Apache Hadoop no HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/08/2020
ms.openlocfilehash: b3c1abb7bff54e3e2d294b073b867c6c0e06f482
ms.sourcegitcommit: 8b37091efe8c575467e56ece4d3f805ea2707a64
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75830076"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-powershell"></a>Executar trabalhos MapReduce com Apache Hadoop no HDInsight usando o PowerShell

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Este documento fornece um exemplo de como usar Azure PowerShell para executar um trabalho MapReduce em um cluster Hadoop no HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster Apache Hadoop no HDInsight. Consulte [criar Apache Hadoop clusters usando o portal do Azure](../hdinsight-hadoop-create-linux-clusters-portal.md).

* O [módulo AZ](https://docs.microsoft.com/powershell/azure/overview) do PowerShell instalado.

## <a name="run-a-mapreduce-job"></a>Executar um trabalho MapReduce

Azure PowerShell fornece *cmdlets* que permitem executar remotamente trabalhos do MapReduce no HDInsight. Internamente, o PowerShell faz chamadas REST para [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (anteriormente chamado de Templeton) em execução no cluster HDInsight.

Os cmdlets a seguir são usados ao executar trabalhos MapReduce em um cluster HDInsight remoto.

|Cmdlet | Descrição |
|---|---|
|Connect-AzAccount|Autentica Azure PowerShell à sua assinatura do Azure.|
|New-AzHDInsightMapReduceJobDefinition|Cria uma nova *definição de trabalho* usando as informações de MapReduce especificadas.|
|Start-AzHDInsightJob|Envia a definição de trabalho para o HDInsight e inicia o trabalho. Um objeto de *trabalho* é retornado.|
|Wait-AzHDInsightJob|Usa o objeto de trabalho para verificar o status do trabalho. Ele aguarda até que o trabalho seja concluído ou o tempo de espera seja excedido.|
|Get-AzHDInsightJobOutput|Usado para recuperar a saída do trabalho.|

As etapas a seguir demonstram como usar esses cmdlets para executar um trabalho em seu cluster HDInsight.

1. Usando um editor, salve o código a seguir como **mapreducejob. ps1**.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-mapreduce/use-mapreduce.ps1?range=5-69)]

2. Abra um novo prompt de comando **Azure PowerShell** . Altere os diretórios para o local do arquivo **mapreducejob. ps1** e use o seguinte comando para executar o script:

        .\mapreducejob.ps1

    Ao executar o script, você será solicitado a fornecer o nome do cluster HDInsight e o logon do cluster. Você também pode ser solicitado a autenticar para sua assinatura do Azure.

3. Quando o trabalho for concluído, você receberá uma saída semelhante ao seguinte texto:

        Cluster         : CLUSTERNAME
        ExitCode        : 0
        Name            : wordcount
        PercentComplete : map 100% reduce 100%
        Query           :
        State           : Completed
        StatusDirectory : f1ed2028-afe8-402f-a24b-13cc17858097
        SubmissionTime  : 12/5/2014 8:34:09 PM
        JobId           : job_1415949758166_0071

    Essa saída indica que o trabalho foi concluído com êxito.

    > [!NOTE]  
    > Se **ExitCode** for um valor diferente de 0, consulte [solução de problemas](#troubleshooting).

    Este exemplo também armazena os arquivos baixados em um arquivo **output. txt** no diretório do qual você executa o script.

### <a name="view-output"></a>Exibir saída

Para ver as palavras e contagens produzidas pelo trabalho, abra o arquivo **output. txt** em um editor de texto.

> [!NOTE]  
> Os arquivos de saída de um trabalho MapReduce são imutáveis. Portanto, se você executar novamente este exemplo, precisará alterar o nome do arquivo de saída.

## <a name="troubleshooting"></a>Resolução de problemas

Se nenhuma informação for retornada quando o trabalho for concluído, exiba os erros para o trabalho. Para exibir informações de erro para esse trabalho, adicione o seguinte comando ao final do arquivo **mapreducejob. ps1** . Em seguida, salve o arquivo e execute o script novamente.

```powershell
# Print the output of the WordCount job.
Write-Host "Display the standard output ..." -ForegroundColor Green
Get-AzHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $wordCountJob.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Esse cmdlet retorna as informações que foram gravadas em STDERR à medida que o trabalho é executado.

## <a name="next-steps"></a>Passos seguintes

Como você pode ver, o Azure PowerShell fornece uma maneira fácil de executar trabalhos MapReduce em um cluster HDInsight, monitorar o status do trabalho e recuperar a saída. Para obter informações sobre outras maneiras que você pode trabalhar com o Hadoop no HDInsight:

* [Usar o MapReduce no Hadoop do HDInsight](hdinsight-use-mapreduce.md)
* [Usar Apache Hive com Apache Hadoop no HDInsight](hdinsight-use-hive.md)
