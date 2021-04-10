---
title: Use MapReduce e PowerShell com Apache Hadoop - Azure HDInsight
description: Aprenda a usar o PowerShell para executar remotamente trabalhos mapReduce com Apache Hadoop em HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/08/2020
ms.openlocfilehash: 16c6c5e317591b70c3a1300453093fc715e213fb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98939676"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-powershell"></a>Executar trabalhos de MapReduce com Apache Hadoop em HDInsight usando PowerShell

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Este documento fornece um exemplo de utilização do Azure PowerShell para executar um trabalho mapReduce num hadoop no cluster HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* Um aglomerado Apache Hadoop em HDInsight. Consulte [os clusters Apache Hadoop utilizando o portal Azure](../hdinsight-hadoop-create-linux-clusters-portal.md).

* O Módulo PowerShell [Az](/powershell/azure/) instalado.

## <a name="run-a-mapreduce-job"></a>Executar um trabalho mapReduce

A Azure PowerShell fornece *cmdlets* que lhe permitem executar remotamente trabalhos mapReduce em HDInsight. Internamente, powerShell faz chamadas DE REST para [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (anteriormente chamado Templeton) correndo no cluster HDInsight.

Os cmdlets seguintes são usados quando executam trabalhos MapReduce num cluster hdinsight remoto.

|Cmdlet | Descrição |
|---|---|
|Connect-AzAccount|Autentica a Azure PowerShell para a sua subscrição Azure.|
|New-AzHDInsightMapReduceJobDefinition|Cria uma nova *definição de emprego* utilizando as informações especificadas do MapReduce.|
|Start-AzHDInsightJob|Envia a definição de trabalho para HDInsight e começa o trabalho. Um objeto *de trabalho* é devolvido.|
|Wait-AzHDInsightJob|Usa o objeto de trabalho para verificar o estado do trabalho. Aguarda até que o trabalho termine ou o tempo de espera seja ultrapassado.|
|Get-AzHDInsightJobOutput|Usado para recuperar a saída do trabalho.|

Os passos a seguir demonstram como usar estes cmdlets para executar um trabalho no seu cluster HDInsight.

1. Utilizando um editor, guarde o seguinte código como **mapreducejob.ps1**.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-mapreduce/use-mapreduce.ps1?range=5-69)]

2. Abra um novo pedido de comando **Azure PowerShell.** Altere os diretórios para a localização do ficheiro **mapreducejob.ps1** e, em seguida, use o seguinte comando para executar o script:

    ```azurepowershell
    .\mapreducejob.ps1
    ```

    Quando executam o script, são solicitados o nome do cluster HDInsight e do login do cluster. Também pode ser solicitado a autenticar a sua subscrição Azure.

3. Quando o trabalho estiver concluído, recebe uma saída semelhante ao seguinte texto:

    ```output
    Cluster         : CLUSTERNAME
    ExitCode        : 0
    Name            : wordcount
    PercentComplete : map 100% reduce 100%
    Query           :
    State           : Completed
    StatusDirectory : f1ed2028-afe8-402f-a24b-13cc17858097
    SubmissionTime  : 12/5/2014 8:34:09 PM
    JobId           : job_1415949758166_0071
    ```

    Esta saída indica que o trabalho foi concluído com sucesso.

    > [!NOTE]  
    > Se o **Código de Saída** for um valor diferente de 0, consulte a [resolução de problemas](#troubleshooting).

    Este exemplo também armazena os ficheiros descarregados para um ficheiro **output.txt** no diretório do que executou o script.

### <a name="view-output"></a>Ver saída

Para ver as palavras e contagens produzidas pelo trabalho, abra o ficheiro **output.txt** num editor de texto.

> [!NOTE]  
> Os ficheiros de saída de um trabalho do MapReduce são imutáveis. Por isso, se voltar a fazer esta amostra, tem de alterar o nome do ficheiro de saída.

## <a name="troubleshooting"></a>Resolução de problemas

Se nenhuma informação for devolvida quando o trabalho estiver concluído, ver erros para o trabalho. Para visualizar informações de erro para este trabalho, adicione o seguinte comando ao fim do ficheiro **mapreducejob.ps1.** Em seguida, guarde o ficheiro e reexame o script.

```powershell
# Print the output of the WordCount job.
Write-Host "Display the standard output ..." -ForegroundColor Green
Get-AzHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $wordCountJob.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Este cmdlet devolve a informação que foi escrita à STDERR à medida que o trabalho funciona.

## <a name="next-steps"></a>Passos seguintes

Como pode ver, a Azure PowerShell fornece uma maneira fácil de executar trabalhos MapReduce num cluster HDInsight, monitorizar o estado do trabalho e recuperar a produção. Para obter informações sobre outras formas de trabalhar com Hadoop em HDInsight:

* [Use MapReduce em HDInsight Hadoop](hdinsight-use-mapreduce.md)
* [Use a Colmeia Apache com Apache Hadoop em HDInsight](hdinsight-use-hive.md)