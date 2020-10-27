---
title: Use a Colmeia Apache com PowerShell em HDInsight - Azure
description: Use PowerShell para executar consultas apache colmeia em Apache Hadoop em Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/24/2019
ms.openlocfilehash: 013eb8215064173e002aea23043bc2e9ba92b525
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545247"
---
# <a name="run-apache-hive-queries-using-powershell"></a>Executar consultas de Colmeia Apache usando PowerShell

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Este documento fornece um exemplo de utilização da Azure PowerShell para executar consultas de Hive Apache num Apache Hadoop no cluster HDInsight.

> [!NOTE]  
> Este documento não fornece uma descrição detalhada do que as declarações da HiveQL que são usadas nos exemplos fazem. Para obter informações sobre o HiveQL que é usado neste exemplo, consulte [Use Apache Hive com Apache Hadoop em HDInsight](hdinsight-use-hive.md).

## <a name="prerequisites"></a>Pré-requisitos

* Um aglomerado Apache Hadoop em HDInsight. Ver [Começar com HDInsight no Linux](./apache-hadoop-linux-tutorial-get-started.md).

* O Módulo PowerShell [Az](/powershell/azure/) instalado.

## <a name="run-a-hive-query"></a>Executar uma consulta do Hive

A azure PowerShell fornece *cmdlets* que lhe permitem executar remotamente consultas de Hive em HDInsight. Internamente, os cmdlets fazem chamadas REST para [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) no cluster HDInsight.

Os cmdlets seguintes são utilizados quando executam consultas de Colmeia num cluster hdinsight remoto:

* `Connect-AzAccount`: Autentica a Azure PowerShell para a sua subscrição Azure.
* `New-AzHDInsightHiveJobDefinition`: Cria uma *definição* de trabalho utilizando as declarações especificadas da HiveQL.
* `Start-AzHDInsightJob`: Envia a definição de trabalho para HDInsight e inicia o trabalho. Um objeto *de trabalho* é devolvido.
* `Wait-AzHDInsightJob`: Utiliza o objeto de trabalho para verificar o estado do trabalho. Aguarda até que o trabalho termine ou o tempo de espera seja ultrapassado.
* `Get-AzHDInsightJobOutput`: Usado para recuperar a saída do trabalho.
* `Invoke-AzHDInsightHiveJob`: Usado para executar declarações de HiveQL. Este cmdlet bloqueia a consulta completa e, em seguida, devolve os resultados.
* `Use-AzHDInsightCluster`: Define o cluster atual para o `Invoke-AzHDInsightHiveJob` comando.

Os seguintes passos demonstram como usar estes cmdlets para executar um trabalho no seu cluster HDInsight:

1. Utilizando um editor, guarde o seguinte código como `hivejob.ps1` .

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=5-42)]

2. Abra um novo pedido de comando **Azure PowerShell.** Altere os diretórios para a localização do `hivejob.ps1` ficheiro e, em seguida, use o seguinte comando para executar o script:

    ```azurepowershell
    .\hivejob.ps1
    ```

    Quando o script é executado, é solicitado a introduzir o nome do cluster e as credenciais de conta HTTPS/Cluster Admin. Também pode ser solicitado a iniciar súm na sua assinatura Azure.

3. Quando o trabalho termina, devolve informações semelhantes ao seguinte texto:

    ```output
    Display the standard output...
    2012-02-03      18:35:34        SampleClass0    [ERROR] incorrect       id
    2012-02-03      18:55:54        SampleClass1    [ERROR] incorrect       id
    2012-02-03      19:25:27        SampleClass4    [ERROR] incorrect       id
    ```

4. Como mencionado anteriormente, `Invoke-Hive` pode ser usado para executar uma consulta e esperar pela resposta. Use o seguinte script para ver como funciona Invoke-Hive:

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=50-71)]

    A saída parece o seguinte texto:

    ```output
    2012-02-03    18:35:34    SampleClass0    [ERROR]    incorrect    id
    2012-02-03    18:55:54    SampleClass1    [ERROR]    incorrect    id
    2012-02-03    19:25:27    SampleClass4    [ERROR]    incorrect    id
    ```

   > [!NOTE]  
   > Para consultas mais longas da HiveQL, pode utilizar os ficheiros de cmdlet Azure PowerShell **Here-Strings** ou os ficheiros de script HiveQL. O seguinte corte mostra como usar o `Invoke-Hive` cmdlet para executar um ficheiro de script HiveQL. O ficheiro de script HiveQL deve ser enviado para wasbs://.
   >
   > `Invoke-AzHDInsightHiveJob -File "wasbs://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
   >
   > Para obter mais informações sobre **as cordas aqui,** consulte [AQUI-STRINGS](/powershell/module/microsoft.powershell.core/about/about_quoting_rules#here-strings).

## <a name="troubleshooting"></a>Resolução de problemas

Se nenhuma informação for devolvida quando o trabalho estiver concluído, consulte os registos de erro. Para visualizar informações de erro para este trabalho, adicione o seguinte ao fim do `hivejob.ps1` ficheiro, guarde-o e, em seguida, volte a executá-lo.

```powershell
# Print the output of the Hive job.
Get-AzHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Este cmdlet devolve as informações escritas à STDERR durante o processamento de trabalho.

## <a name="summary"></a>Resumo

Como pode ver, o Azure PowerShell fornece uma maneira fácil de executar consultas de Hive num cluster HDInsight, monitorizar o estado do trabalho e recuperar a saída.

## <a name="next-steps"></a>Passos seguintes

Para informações gerais sobre a Colmeia em HDInsight:

* [Use a Colmeia Apache com Apache Hadoop em HDInsight](hdinsight-use-hive.md)

Para obter informações sobre outras formas de trabalhar com Hadoop em HDInsight:

* [Use MapReduce com Apache Hadoop em HDInsight](hdinsight-use-mapreduce.md)
