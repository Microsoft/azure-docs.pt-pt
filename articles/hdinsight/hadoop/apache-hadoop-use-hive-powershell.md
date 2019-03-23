---
title: Utilizar o Apache Hive com o PowerShell no HDInsight - Azure
description: Utilize o PowerShell para executar consultas do Hive no Apache Hadoop no HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: hrasheed
ms.openlocfilehash: 77d2d0b5b9f994668abdd02640a9c6d5f463e137
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58360763"
---
# <a name="run-apache-hive-queries-using-powershell"></a>Executar consultas do Apache Hive com o PowerShell
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Este documento fornece um exemplo de utilização do Azure PowerShell no modo de grupo de recursos do Azure para executar consultas do Hive num Apache Hadoop num cluster do HDInsight.

> [!NOTE]  
> Este documento não fornece uma descrição detalhada do que fazem as declarações HiveQL que são utilizadas nos exemplos. Para obter informações sobre o HiveQL que é utilizado neste exemplo, consulte [utilizar o Apache Hive com o Apache Hadoop no HDInsight](hdinsight-use-hive.md).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* Um baseado em Linux Apache Hadoop em clusters do HDInsight versão 3.4 ou superior.

  > [!IMPORTANT]  
  > O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

* Um cliente com o Azure PowerShell.

[!INCLUDE [upgrade-powershell](../../../includes/hdinsight-use-latest-powershell.md)]

## <a name="run-a-hive-query"></a>Executar uma consulta do Hive

O Azure PowerShell disponibiliza *cmdlets* que permitem-lhe executar remotamente as consultas do Hive no HDInsight. Internamente, os cmdlets fazer chamadas REST para [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) no cluster do HDInsight.

Os cmdlets seguintes são utilizados durante a execução de consultas do Hive num cluster HDInsight remoto:

* `Connect-AzAccount`: Efetua a autenticação do Azure PowerShell para a sua subscrição do Azure.
* `New-AzHDInsightHiveJobDefinition`: Cria um *definição de tarefa* ao utilizar as declarações HiveQL especificadas.
* `Start-AzHDInsightJob`: Envia a definição de tarefa ao HDInsight e inicia a tarefa. R *tarefa* objeto é devolvido.
* `Wait-AzHDInsightJob`: Usa o objeto de tarefa para verificar o estado da tarefa. Ele aguarda até que a tarefa é concluída ou o tempo de espera foi excedido.
* `Get-AzHDInsightJobOutput`: Usada para recuperar o resultado da tarefa.
* `Invoke-AzHDInsightHiveJob`: Utilizada para executar declarações HiveQL. Este cmdlet de blocos a consulta estiver concluída, em seguida, devolve os resultados.
* `Use-AzHDInsightCluster`: Define o cluster atual para utilizar para o `Invoke-AzHDInsightHiveJob` comando.

Os passos seguintes demonstram como utilizar estes cmdlets para executar uma tarefa no seu cluster do HDInsight:

1. Com um editor, guarde o código a seguir como `hivejob.ps1`.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=5-42)]

2. Abra uma nova **do Azure PowerShell** prompt de comando. Altere os diretórios para a localização do `hivejob.ps1` de ficheiros, em seguida, utilize o seguinte comando para executar o script:

        .\hivejob.ps1

    Quando o script é executado, lhe for pedido para introduzir o nome do cluster e as credenciais da conta de administrador de HTTPS/Cluster. Poderá também ser-lhe pedido para iniciar sessão na sua subscrição do Azure.

3. Quando a tarefa é concluída, ele retorna informações semelhantes ao seguinte texto:

        Display the standard output...
        2012-02-03      18:35:34        SampleClass0    [ERROR] incorrect       id
        2012-02-03      18:55:54        SampleClass1    [ERROR] incorrect       id
        2012-02-03      19:25:27        SampleClass4    [ERROR] incorrect       id

4. Conforme mencionado anteriormente, `Invoke-Hive` pode ser utilizado para executar uma consulta e aguardar a resposta. Utilize o seguinte script para ver como funciona o Invoke-Hive:

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=50-71)]

    A saída é parecido com o seguinte texto:

        2012-02-03    18:35:34    SampleClass0    [ERROR]    incorrect    id
        2012-02-03    18:55:54    SampleClass1    [ERROR]    incorrect    id
        2012-02-03    19:25:27    SampleClass4    [ERROR]    incorrect    id

   > [!NOTE]  
   > Para consultas de HiveQL mais tempo, pode utilizar o Azure PowerShell **cadeias de caracteres aqui** cmdlet ou HiveQL arquivos de script. O fragmento seguinte mostra como utilizar o `Invoke-Hive` cmdlet para executar um arquivo de script de HiveQL. Tem de ser carregado o ficheiro de script de HiveQL para wasb: / /.
   >
   > `Invoke-AzHDInsightHiveJob -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
   >
   > Para obter mais informações sobre **cadeias de caracteres aqui**, consulte <a href="https://technet.microsoft.com/library/ee692792.aspx" target="_blank">usando o Windows PowerShell aqui cadeias de caracteres</a>.

## <a name="troubleshooting"></a>Resolução de problemas

Se nenhuma informação é retornada quando a tarefa é concluída, ver os registos de erros. Para ver informações de erro para esta tarefa, adicione o seguinte ao final do `hivejob.ps1` de ficheiros, guarde-o e, em seguida, execute-o novamente.

```powershell
# Print the output of the Hive job.
Get-AzHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Este cmdlet devolve as informações que são escritas STDERR durante o processamento da tarefa.

## <a name="summary"></a>Resumo

Como pode ver, o Azure PowerShell fornece uma forma fácil de executar consultas do Hive num cluster do HDInsight, monitorizar o estado da tarefa e obter o resultado.

## <a name="next-steps"></a>Passos Seguintes

Para obter informações gerais sobre o Hive no HDInsight:

* [Utilizar o Apache Hive com o Apache Hadoop no HDInsight](hdinsight-use-hive.md)

Para obter informações sobre outras formas pode trabalhar com o Hadoop no HDInsight:

* [Utilizar o Apache Pig com o Apache Hadoop no HDInsight](hdinsight-use-pig.md)
* [Utilizar o MapReduce com o Apache Hadoop no HDInsight](hdinsight-use-mapreduce.md)
