---
title: Use mapReduce e PowerShell com Apache Hadoop - Azure HDInsight
description: Aprenda a usar o PowerShell para executar remotamente o MapReduce jobs com Apache Hadoop no HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/08/2020
ms.openlocfilehash: b3c1abb7bff54e3e2d294b073b867c6c0e06f482
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75830076"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-powershell"></a>Executar mapeie empregos com Apache Hadoop no HDInsight usando PowerShell

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Este documento fornece um exemplo de utilização do Azure PowerShell para executar um trabalho MapReduce num hadoop no cluster HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* Um aglomerado Apache Hadoop no HDInsight. Consulte os [clusters De Apache Hadoop utilizando o portal Azure](../hdinsight-hadoop-create-linux-clusters-portal.md).

* O Módulo PowerShell [Az](https://docs.microsoft.com/powershell/azure/overview) instalado.

## <a name="run-a-mapreduce-job"></a>Executar um trabalho mapReduce

O Azure PowerShell fornece *cmdlets* que lhe permitem executar remotamente o MapReduce jobs no HDInsight. Internamente, a PowerShell faz chamadas REST para [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (anteriormente chamado Templeton) em execução no cluster HDInsight.

Os seguintes cmdlets são usados ao executar MapReduce trabalhos num cluster HDInsight remoto.

|Cmdlet | Descrição |
|---|---|
|Connect-AzAccount|Autentica a Azure PowerShell na sua subscrição Azure.|
|New-AzHDInsightMapReduceJobDefinition|Cria uma nova *definição de trabalho* utilizando a informação mapReduce especificada.|
|Start-AzHDInsightJob|Envia a definição de trabalho para o HDInsight e inicia o trabalho. Um objeto *de trabalho* é devolvido.|
|Wait-AzHDInsightJob|Usa o objeto de trabalho para verificar o estado do trabalho. Espera até que o trabalho termine ou o tempo de espera seja ultrapassado.|
|Get-AzHDInsightJobOutput|Usado para recuperar a saída do trabalho.|

Os seguintes passos demonstram como usar estes cmdlets para executar um trabalho no seu cluster HDInsight.

1. Utilizando um editor, guarde o seguinte código como **mapreducejob.ps1**.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-mapreduce/use-mapreduce.ps1?range=5-69)]

2. Abra um novo pedido de comando **Azure PowerShell.** Mude os diretórios para a localização do ficheiro **mapreducejob.ps1** e, em seguida, use o seguinte comando para executar o script:

        .\mapreducejob.ps1

    Quando executa o script, é solicitado o nome do cluster HDInsight e o login do cluster. Pode também ser solicitado a autenticar a sua subscrição Azure.

3. Quando o trabalho estiver concluído, recebe uma saída semelhante ao seguinte texto:

        Cluster         : CLUSTERNAME
        ExitCode        : 0
        Name            : wordcount
        PercentComplete : map 100% reduce 100%
        Query           :
        State           : Completed
        StatusDirectory : f1ed2028-afe8-402f-a24b-13cc17858097
        SubmissionTime  : 12/5/2014 8:34:09 PM
        JobId           : job_1415949758166_0071

    Esta saída indica que o trabalho foi concluído com sucesso.

    > [!NOTE]  
    > Se o Código de **Saída** for um valor diferente de 0, consulte [A resolução de problemas](#troubleshooting).

    Este exemplo também armazena os ficheiros descarregados para um ficheiro **output.txt** no diretório do que executa o script.

### <a name="view-output"></a>Ver saída

Para ver as palavras e contagens produzidas pelo trabalho, abra o ficheiro **output.txt** num editor de texto.

> [!NOTE]  
> Os ficheiros de saída de um trabalho MapReduce são imutáveis. Por isso, se reproduzir esta amostra, terá de alterar o nome do ficheiro de saída.

## <a name="troubleshooting"></a>Resolução de problemas

Se nenhuma informação for devolvida quando o trabalho estiver concluído, veja erros para o trabalho. Para visualizar as informações de erro para este trabalho, adicione o seguinte comando ao fim do ficheiro **mapreducejob.ps1.** Em seguida, guarde o ficheiro e refaça o script.

```powershell
# Print the output of the WordCount job.
Write-Host "Display the standard output ..." -ForegroundColor Green
Get-AzHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $wordCountJob.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Este cmdlet devolve a informação que foi escrita para stderr enquanto o trabalho corre.

## <a name="next-steps"></a>Passos seguintes

Como pode ver, o Azure PowerShell fornece uma maneira fácil de executar mapReduce empregos num cluster HDInsight, monitorizar o estado de trabalho e recuperar a saída. Para obter informações sobre outras formas de trabalhar com Hadoop no HDInsight:

* [Utilizar mapeiaReduzir no Hadoop HDInsight](hdinsight-use-mapreduce.md)
* [Use a Colmeia Apache com Hadoop Apache no HDInsight](hdinsight-use-hive.md)
