---
title: Usar o Apache Hive com o PowerShell no HDInsight – Azure
description: Usar o PowerShell para executar consultas de Apache Hive no Apache Hadoop no Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/24/2019
ms.openlocfilehash: deaa934b257fab74830d75e308a283e7608dc590
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552598"
---
# <a name="run-apache-hive-queries-using-powershell"></a>Executar Apache Hive consultas usando o PowerShell

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Este documento fornece um exemplo de como usar Azure PowerShell para executar Apache Hive consultas em um Apache Hadoop cluster HDInsight.

> [!NOTE]  
> Este documento não fornece uma descrição detalhada do que são as instruções HiveQL usadas nos exemplos. Para obter informações sobre o HiveQL usado neste exemplo, consulte [usar Apache Hive com Apache Hadoop no HDInsight](hdinsight-use-hive.md).

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster Apache Hadoop no HDInsight. Consulte [introdução ao HDInsight no Linux](./apache-hadoop-linux-tutorial-get-started.md).

* O [módulo AZ](https://docs.microsoft.com/powershell/azure/overview) do PowerShell instalado.

## <a name="run-a-hive-query"></a>Executar uma consulta do Hive

Azure PowerShell fornece *cmdlets* que permitem executar remotamente consultas de Hive no HDInsight. Internamente, os cmdlets fazem chamadas REST para [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) no cluster HDInsight.

Os cmdlets a seguir são usados ao executar consultas do hive em um cluster HDInsight remoto:

* `Connect-AzAccount`: autentica Azure PowerShell para sua assinatura do Azure.
* `New-AzHDInsightHiveJobDefinition`: cria uma *definição de trabalho* usando as instruções HiveQL especificadas.
* `Start-AzHDInsightJob`: envia a definição de trabalho para o HDInsight e inicia o trabalho. Um objeto de *trabalho* é retornado.
* `Wait-AzHDInsightJob`: usa o objeto de trabalho para verificar o status do trabalho. Ele aguarda até que o trabalho seja concluído ou o tempo de espera seja excedido.
* `Get-AzHDInsightJobOutput`: usado para recuperar a saída do trabalho.
* `Invoke-AzHDInsightHiveJob`: usado para executar instruções HiveQL. Esse cmdlet bloqueia a conclusão da consulta e, em seguida, retorna os resultados.
* `Use-AzHDInsightCluster`: define o cluster atual a ser usado para o comando `Invoke-AzHDInsightHiveJob`.

As etapas a seguir demonstram como usar esses cmdlets para executar um trabalho em seu cluster HDInsight:

1. Usando um editor, salve o código a seguir como `hivejob.ps1`.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=5-42)]

2. Abra um novo prompt de comando **Azure PowerShell** . Altere os diretórios para o local do arquivo de `hivejob.ps1`, em seguida, use o seguinte comando para executar o script:

        .\hivejob.ps1

    Quando o script for executado, você será solicitado a inserir o nome do cluster e as credenciais da conta do administrador de HTTPS/cluster. Você também pode ser solicitado a entrar em sua assinatura do Azure.

3. Quando o trabalho é concluído, ele retorna informações semelhantes ao seguinte texto:

        Display the standard output...
        2012-02-03      18:35:34        SampleClass0    [ERROR] incorrect       id
        2012-02-03      18:55:54        SampleClass1    [ERROR] incorrect       id
        2012-02-03      19:25:27        SampleClass4    [ERROR] incorrect       id

4. Como mencionado anteriormente, `Invoke-Hive` pode ser usado para executar uma consulta e aguardar a resposta. Use o script a seguir para ver como o Invoke-Hive funciona:

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=50-71)]

    A saída é semelhante ao seguinte texto:

        2012-02-03    18:35:34    SampleClass0    [ERROR]    incorrect    id
        2012-02-03    18:55:54    SampleClass1    [ERROR]    incorrect    id
        2012-02-03    19:25:27    SampleClass4    [ERROR]    incorrect    id

   > [!NOTE]  
   > Para consultas HiveQL mais longas, você pode usar o cmdlet Azure PowerShell **Here-Strings** ou arquivos de script HiveQL. O trecho a seguir mostra como usar o cmdlet `Invoke-Hive` para executar um arquivo de script HiveQL. O arquivo de script HiveQL deve ser carregado em wasbs://.
   >
   > `Invoke-AzHDInsightHiveJob -File "wasbs://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
   >
   > Para obter mais informações sobre as **cadeias de caracteres aqui**, consulte <a href="https://technet.microsoft.com/library/ee692792.aspx" target="_blank">usando as cadeias de caracteres aqui do Windows PowerShell</a>.

## <a name="troubleshooting"></a>Resolução de problemas

Se nenhuma informação for retornada quando o trabalho for concluído, exiba os logs de erros. Para exibir informações de erro para esse trabalho, adicione o seguinte ao final do arquivo de `hivejob.ps1`, salve-o e execute-o novamente.

```powershell
# Print the output of the Hive job.
Get-AzHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Esse cmdlet retorna as informações gravadas em STDERR durante o processamento do trabalho.

## <a name="summary"></a>Resumo

Como você pode ver, o Azure PowerShell fornece uma maneira fácil de executar consultas de Hive em um cluster HDInsight, monitorar o status do trabalho e recuperar a saída.

## <a name="next-steps"></a>Passos seguintes

Para obter informações gerais sobre o hive no HDInsight:

* [Usar Apache Hive com Apache Hadoop no HDInsight](hdinsight-use-hive.md)

Para obter informações sobre outras maneiras que você pode trabalhar com o Hadoop no HDInsight:

* [Usar o MapReduce com o Apache Hadoop no HDInsight](hdinsight-use-mapreduce.md)
