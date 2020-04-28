---
title: Use a Colmeia Apache com PowerShell em HDInsight - Azure
description: Use powerShell para executar consultas apache hive em Apache Hadoop em Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/24/2019
ms.openlocfilehash: deaa934b257fab74830d75e308a283e7608dc590
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75552598"
---
# <a name="run-apache-hive-queries-using-powershell"></a>Executar consultas de Hiv Apache usando PowerShell

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Este documento fornece um exemplo de utilização do Azure PowerShell para executar consultas apache hive em um Apache Hadoop em cluster HDInsight.

> [!NOTE]  
> Este documento não fornece uma descrição detalhada do que as declarações da HiveQL são usadas nos exemplos. Para obter informações sobre o HiveQL que é usado neste exemplo, consulte [Use Apache Hive com Apache Hadoop no HDInsight](hdinsight-use-hive.md).

## <a name="prerequisites"></a>Pré-requisitos

* Um aglomerado Apache Hadoop no HDInsight. Ver [Começar com HDInsight no Linux](./apache-hadoop-linux-tutorial-get-started.md).

* O Módulo PowerShell [Az](https://docs.microsoft.com/powershell/azure/overview) instalado.

## <a name="run-a-hive-query"></a>Executar uma consulta do Hive

O Azure PowerShell fornece *cmdlets* que lhe permitem executar remotamente consultas da Hive no HDInsight. Internamente, os cmdlets fazem chamadas REST para [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) no cluster HDInsight.

Os seguintes cmdlets são usados ao executar consultas da Hive num aglomerado remoto HDInsight:

* `Connect-AzAccount`: Autentica a Azure PowerShell na sua subscrição Azure.
* `New-AzHDInsightHiveJobDefinition`: Cria uma *definição* de trabalho utilizando as declarações específicas da HiveQL.
* `Start-AzHDInsightJob`: Envia a definição de trabalho para o HDInsight e inicia o trabalho. Um objeto *de trabalho* é devolvido.
* `Wait-AzHDInsightJob`: Utiliza o objeto de trabalho para verificar o estado do trabalho. Espera até que o trabalho termine ou o tempo de espera seja ultrapassado.
* `Get-AzHDInsightJobOutput`: Utilizado para recuperar a saída do trabalho.
* `Invoke-AzHDInsightHiveJob`: Usado para executar declarações da HiveQL. Este cmdlet bloqueia a consulta completa e, em seguida, devolve os resultados.
* `Use-AzHDInsightCluster`: Define o aglomerado atual `Invoke-AzHDInsightHiveJob` para o comando.

Os seguintes passos demonstram como usar estes cmdlets para executar um trabalho no seu cluster HDInsight:

1. Utilizando um editor, guarde `hivejob.ps1`o seguinte código como .

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=5-42)]

2. Abra um novo pedido de comando **Azure PowerShell.** Mude os diretórios `hivejob.ps1` para a localização do ficheiro e, em seguida, use o seguinte comando para executar o script:

        .\hivejob.ps1

    Quando o script é executado, é-lhe solicitado que introduza o nome do cluster e as credenciais de conta HTTPS/Cluster Admin. Pode também ser solicitado a iniciar sessão na sua subscrição Azure.

3. Quando o trabalho estiver concluído, devolve informações semelhantes ao seguinte texto:

        Display the standard output...
        2012-02-03      18:35:34        SampleClass0    [ERROR] incorrect       id
        2012-02-03      18:55:54        SampleClass1    [ERROR] incorrect       id
        2012-02-03      19:25:27        SampleClass4    [ERROR] incorrect       id

4. Como mencionado anteriormente, `Invoke-Hive` pode ser usado para fazer uma consulta e esperar pela resposta. Utilize o seguinte guião para ver como funciona a Invoke-Hive:

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=50-71)]

    A saída parece com o seguinte texto:

        2012-02-03    18:35:34    SampleClass0    [ERROR]    incorrect    id
        2012-02-03    18:55:54    SampleClass1    [ERROR]    incorrect    id
        2012-02-03    19:25:27    SampleClass4    [ERROR]    incorrect    id

   > [!NOTE]  
   > Para consultas mais longas do HiveQL, pode utilizar os **ficheiros** de script Sem Fios Azure PowerShell ou HiveQL. O seguinte corte mostra como `Invoke-Hive` usar o cmdlet para executar um ficheiro de script HiveQL. O ficheiro de script hiveQL deve ser carregado para wasbs://.
   >
   > `Invoke-AzHDInsightHiveJob -File "wasbs://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
   >
   > Para obter mais informações sobre **as Cadeias Aqui,** consulte <a href="https://technet.microsoft.com/library/ee692792.aspx" target="_blank">a utilização de cadeias aqui do Windows PowerShell</a>.

## <a name="troubleshooting"></a>Resolução de problemas

Se nenhuma informação for devolvida quando o trabalho estiver concluído, veja os registos de erro. Para visualizar as informações de erro para `hivejob.ps1` este trabalho, adicione o seguinte à extremidade do ficheiro, guarde-o e, em seguida, execute-o novamente.

```powershell
# Print the output of the Hive job.
Get-AzHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Este cmdlet devolve a informação que é escrita ao STDERR durante o processamento de trabalho.

## <a name="summary"></a>Resumo

Como pode ver, o Azure PowerShell fornece uma maneira fácil de executar consultas da Hive num cluster HDInsight, monitorizar o estado de trabalho e recuperar a saída.

## <a name="next-steps"></a>Passos seguintes

Para informações gerais sobre a Hive no HDInsight:

* [Use a Colmeia Apache com Hadoop Apache no HDInsight](hdinsight-use-hive.md)

Para obter informações sobre outras formas de trabalhar com Hadoop no HDInsight:

* [Use mapReduce com Hadoop Apache no HDInsight](hdinsight-use-mapreduce.md)
