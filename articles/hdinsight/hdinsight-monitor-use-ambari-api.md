---
title: Monitorizar clusters do Hadoop no HDInsight com a API do Ambari - Azure
description: Utilize as APIs do Apache Ambari para criar, gerir e monitorizar Hadoop clusters. Ferramentas de operação intuitivas e APIs de ocultar a complexidade do Hadoop.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/07/2017
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: ff6601042c82cef2b0101833117f17aca8b463dc
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58223262"
---
# <a name="monitor-apache-hadoop-clusters-in-hdinsight-using-the-apache-ambari-api"></a>Monitorizar clusters do Apache Hadoop no HDInsight usando a API do Apache Ambari
Saiba como monitorizar clusters do HDInsight ao utilizar as APIs do Apache Ambari.

> [!NOTE]  
> As informações neste artigo são principalmente para os clusters do HDInsight baseado em Windows, que fornecem uma versão só de leitura da API de REST do Ambari. Para clusters baseados em Linux, consulte [clusters do Apache Hadoop gerir com o Apache Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="what-is-ambari"></a>O que é o Ambari?
[Apache Ambari] [ ambari-home] é utilizado para aprovisionar, gerir e monitorizar clusters Apache Hadoop. Inclui uma coleção intuitiva de ferramentas de operador intuitiva e um conjunto robusto de APIs que ocultam a complexidade do Hadoop, simplificando a operação de clusters. Para obter mais informações sobre as APIs, veja [referência de API do Ambari][ambari-api-reference]. 

Atualmente, o HDInsight suporta apenas a funcionalidade de monitorização do Ambari. 1.0 da API de Ambari é suportada pelos clusters do HDInsight versão 3.0 e 2.1. Este artigo aborda ao aceder ao APIs do Ambari em clusters do HDInsight versão 3.1 e 2.1. A principal diferença entre as duas é que alguns dos componentes foram alteradas com a introdução de novos recursos (por exemplo, o servidor de histórico da tarefa). 

**Pré-requisitos**

Antes de começar este tutorial, tem de ter os seguintes itens:

* **Uma estação de trabalho com o Azure PowerShell**.
* (Opcional) [cURL][curl]. Para instalá-lo, veja [cURL versões e Downloads][curl-download].
  
  > [!NOTE]  
  > Quando utilizar o comando cURL no Windows, utilize aspas em vez de aspas de único passo para os valores de opção.

* **Um cluster do Azure HDInsight**. Para obter instruções sobre o aprovisionamento do cluster, consulte [introdução à utilização do HDInsight] [ hdinsight-get-started] ou [Provision HDInsight clusters][hdinsight-provision]. Terá de seguir o tutorial, os seguintes dados:
  
  | Propriedade do cluster | Nome da variável do PowerShell do Azure | Value | Descrição |
  | --- | --- | --- | --- |
  |   Nome de cluster do HDInsight |$clusterName | |O nome do cluster do HDInsight. |
  |   Nome de utilizador do cluster |$clusterUsername | |Nome de utilizador do cluster especificado quando o cluster foi criado. |
  |   Palavra-passe do cluster |$clusterPassword | |Palavra-passe de utilizador de cluster. |

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]


## <a name="jump-start"></a>Impulsione a
Existem várias formas de utilizar Ambari para monitorizar clusters do HDInsight.

**Utilizar o Azure PowerShell**

O seguinte script do Azure PowerShell obtém as informações de controlador de tarefa de MapReduce *num cluster do HDInsight 3.5.*  A principal diferença é que podemos extrair estes detalhes do serviço YARN (em vez de MapReduce).

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.net:443"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName/services/YARN/components/RESOURCEMANAGER"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus

    $response.metrics.'yarn.queueMetrics'

O seguinte script do PowerShell obtém as informações de controlador de tarefa de MapReduce *num cluster do HDInsight 2.1*:

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/mapreduce/components/jobtracker"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus

    $response.metrics.'mapred.JobTracker'

O resultado é:

![Saída de Jobtracker][img-jobtracker-output]

**Utilizar cURL**

O exemplo seguinte obtém as informações de cluster com o cURL:

    curl -u <username>:<password> -k https://<ClusterName>.azurehdinsight.net:443/ambari/api/v1/clusters/<ClusterName>.azurehdinsight.net

O resultado é:

    {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/",
     "Clusters":{"cluster_name":"hdi0211v2.azurehdinsight.net","version":"2.1.3.0.432823"},
     "services"[
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/hdfs",
        "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"hdfs"}},
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/mapreduce",
        "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"mapreduce"}}],
     "hosts":[
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/headnode0",
        "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
                 "host_name":"headnode0"}},
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/workernode0",
        "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
                 "host_name":"headnode0.{ClusterDNS}.azurehdinsight.net"}}]}

**Para a versão 10/8/2014**:

Ao utilizar o ponto de extremidade do Ambari, "https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname}", o *host_name* campo Devolve o nome de domínio completamente qualificado (FQDN) do nó em vez do nome de anfitrião. Antes do lançamento 10/8/2014, neste exemplo devolvido simplesmente "**headnode0**". Após o lançamento de 10/8/2014, obtenha o FQDN "**headnode0. { . Azurehdinsight.NET de ClusterDNS} .net**", conforme mostrado no exemplo anterior. Esta alteração era necessária para facilitar cenários onde vários tipos de cluster (por exemplo, HBase e o Hadoop) podem ser implementados numa rede virtual (VNET). Isto acontece, por exemplo, quando utilizar o HBase como uma plataforma de back-end para o Hadoop.

## <a name="ambari-monitoring-apis"></a>Monitorizar as APIs do Ambari
A tabela seguinte lista algumas do monitorização de chamadas de API de Ambari mais comuns. Para obter mais informações sobre a API, consulte [referência da API do Apache Ambari][ambari-api-reference].

| Chamada de API do monitor | URI | Descrição |
| --- | --- | --- |
| Obter clusters |`/api/v1/clusters` | |
| Obter as informações de cluster. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net` |clusters, serviços, anfitriões |
| Obter serviços |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services` |Os serviços incluem: hdfs, mapreduce |
| Obter as informações de serviços. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>` | |
| Obter os componentes do serviço |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components` |HDFS: namenode, datanodeMapReduce: jobtracker; tasktracker |
| Obter as informações de componente. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components/<ComponentName>` |ServiceComponentInfo, host-components, metrics |
| Obter anfitriões |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts` |headnode0, workernode0 |
| Obtenha informações do anfitrião. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>` | |
| Obter os componentes do anfitrião |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components` |namenode, resourcemanager |
| Obter informações de componente de anfitrião. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components/<ComponentName>` |HostRoles, componente, anfitrião, métricas |
| Obter configurações |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations` |Tipos de configuração: site principal, o site de hdfs, o mapred-site, o site do hive |
| Obtenha informações de configuração. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations?type=<ConfigType>&tag=<VersionName>` |Tipos de configuração: site principal, o site de hdfs, o mapred-site, o site do hive |

## <a name="next-steps"></a>Próximos Passos
Agora aprendeu a utilizar o Apache Ambari a monitorização de chamadas de API. Para saber mais, consulte:

* [Gerir clusters do Apache Hadoop no HDInsight com o portal do Azure](hdinsight-administer-use-portal-linux.md)
* [Gerir clusters do HDInsight com o Azure PowerShell][hdinsight-admin-powershell]
* [Gerir clusters do HDInsight utilizando a interface de linha de comandos][hdinsight-admin-cli]
* [Documentação do HDInsight][hdinsight-documentation]
* [Introdução ao HDInsight][hdinsight-get-started]

[ambari-home]: https://ambari.apache.org/
[ambari-api-reference]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[curl]: https://curl.haxx.se
[curl-download]: https://curl.haxx.se/download.html

[microsoft-hadoop-SDK]: https://hadoopsdk.codeplex.com/wikipage?title=Ambari%20Monitoring%20Client

[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: https://technet.microsoft.com/library/ee176949.aspx

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-documentation]: https://docs.microsoft.com/azure/hdinsight/
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md

[img-jobtracker-output]: ./media/hdinsight-monitor-use-ambari-api/hdi.ambari.monitor.jobtracker.output.png
