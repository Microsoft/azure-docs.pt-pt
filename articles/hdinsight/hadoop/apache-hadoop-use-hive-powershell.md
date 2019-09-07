---
title: Usar o Apache Hive com o PowerShell no HDInsight – Azure
description: Usar o PowerShell para executar consultas de Apache Hive no Apache Hadoop no Azure HDInsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: hrasheed
ms.openlocfilehash: 89fa7976b922ba0e40e97b72de5d4eb9a02f0dfd
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70736071"
---
# <a name="run-apache-hive-queries-using-powershell"></a>Executar Apache Hive consultas usando o PowerShell
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Este documento fornece um exemplo de como usar Azure PowerShell no modo de grupo de recursos do Azure para executar consultas de Hive em um Apache Hadoop no cluster HDInsight.

> [!NOTE]  
> Este documento não fornece uma descrição detalhada do que são as instruções HiveQL usadas nos exemplos. Para obter informações sobre o HiveQL usado neste exemplo, consulte [usar Apache Hive com Apache Hadoop no HDInsight](hdinsight-use-hive.md).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* Um Apache Hadoop baseado em Linux no cluster HDInsight versão 3,4 ou superior.

* Um cliente com Azure PowerShell.

[!INCLUDE [upgrade-powershell](../../../includes/hdinsight-use-latest-powershell.md)]

## <a name="run-a-hive-query"></a>Executar uma consulta do Hive

Azure PowerShell fornece *cmdlets* que permitem executar remotamente consultas de Hive no HDInsight. Internamente, os cmdlets fazem chamadas REST para [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) no cluster HDInsight.

Os cmdlets a seguir são usados ao executar consultas do hive em um cluster HDInsight remoto:

* `Connect-AzAccount`: Autentica Azure PowerShell à sua assinatura do Azure.
* `New-AzHDInsightHiveJobDefinition`: Cria uma *definição de trabalho* usando as instruções HiveQL especificadas.
* `Start-AzHDInsightJob`: Envia a definição de trabalho para o HDInsight e inicia o trabalho. Um objeto de *trabalho* é retornado.
* `Wait-AzHDInsightJob`: Usa o objeto de trabalho para verificar o status do trabalho. Ele aguarda até que o trabalho seja concluído ou o tempo de espera seja excedido.
* `Get-AzHDInsightJobOutput`: Usado para recuperar a saída do trabalho.
* `Invoke-AzHDInsightHiveJob`: Usado para executar instruções HiveQL. Esse cmdlet bloqueia a conclusão da consulta e, em seguida, retorna os resultados.
* `Use-AzHDInsightCluster`: Define o cluster atual a ser usado para `Invoke-AzHDInsightHiveJob` o comando.

As etapas a seguir demonstram como usar esses cmdlets para executar um trabalho em seu cluster HDInsight:

1. Usando um editor, salve o código a seguir `hivejob.ps1`como.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=5-42)]

2. Abra um novo prompt de comando **Azure PowerShell** . Altere os diretórios para o local do `hivejob.ps1` arquivo e use o seguinte comando para executar o script:

        .\hivejob.ps1

    Quando o script for executado, será solicitado que você insira o nome do cluster e as credenciais da conta do administrador de HTTPS/cluster. Você também pode ser solicitado a entrar em sua assinatura do Azure.

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
   > Para consultas HiveQL mais longas, você pode usar o cmdlet Azure PowerShell **Here-Strings** ou arquivos de script HiveQL. O trecho a seguir mostra como usar o `Invoke-Hive` cmdlet para executar um arquivo de script HiveQL. O arquivo de script HiveQL deve ser carregado em wasb://.
   >
   > `Invoke-AzHDInsightHiveJob -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
   >
   > Para obter mais informações sobre as **cadeias de caracteres aqui**, consulte <a href="https://technet.microsoft.com/library/ee692792.aspx" target="_blank">usando as cadeias de caracteres aqui do Windows PowerShell</a>.

## <a name="troubleshooting"></a>Resolução de problemas

Se nenhuma informação for retornada quando o trabalho for concluído, exiba os logs de erros. Para exibir informações de erro para este trabalho, adicione o seguinte ao final do `hivejob.ps1` arquivo, salve-o e execute-o novamente.

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

* [Usar o Apache Pig com o Apache Hadoop no HDInsight](hdinsight-use-pig.md)
* [Usar o MapReduce com o Apache Hadoop no HDInsight](hdinsight-use-mapreduce.md)
