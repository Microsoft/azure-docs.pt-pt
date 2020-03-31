---
title: Boas práticas de gestão de clusters - Azure HDInsight
description: Aprenda as melhores práticas para gerir os clusters HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 3010c1d597907e7d5c7f82c8b42721dc1f934f6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74782024"
---
# <a name="hdinsight-cluster-management-best-practices"></a>Boas práticas de gestão de clusters HDInsight

Aprenda as melhores práticas para gerir os clusters HDInsight.

## <a name="how-do-i-create-hdinsight-clusters"></a>Como crio clusters HDInsight?

| Opção | Documentos |
|---|---|
| Azure Data Factory | [Criar clusters Apache Hadoop a pedido no HDInsight utilizando a Azure Data Factory](./hdinsight-hadoop-create-linux-clusters-adf.md) |
| Modelo de gestor de recursos personalizado | [Crie clusters Apache Hadoop em HDInsight usando modelos de Gestor de Recursos](./hdinsight-hadoop-create-linux-clusters-arm-templates.md) |
| Modelos de início rápido | [Modelos de quickstart HDInsight](https://azure.microsoft.com/resources/templates/?term=hdinsight) |
| exemplos do Azure | [Amostras HDInsight Azure](https://docs.microsoft.com/samples/browse/?products=azure-hdinsight) |
| Portal do Azure | [Crie clusters baseados em Linux no HDInsight utilizando o portal Azure](./spark/apache-spark-intellij-tool-plugin.md) |
| CLI do Azure | [Criar clusters HDInsight utilizando o Azure CLI](./hdinsight-hadoop-create-linux-clusters-azure-cli.md) |
| Azure PowerShell | [Crie clusters baseados em Linux no HDInsight usando o Azure PowerShell](./hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |
| cURL | [Crie aglomerados De Hadoop Apache usando a API Azure REST](./hdinsight-hadoop-create-linux-clusters-curl-rest.md) |
| SDKs (.NET, Python, Java) | [.NET,](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet) [Python,](https://docs.microsoft.com/python/api/overview/azure/hdinsight?view=azure-python) [Java,](https://docs.microsoft.com/java/api/overview/azure/hdinsight?view=azure-java-stable) [Go](https://docs.microsoft.com/azure/hdinsight/hdinsight-go-sdk-overview) |

> [!Note]
> Se estiver a criar um cluster e a reutilizar o nome do cluster a partir de um cluster previamente criado, aguarde até que a eliminação do cluster anterior seja concluída antes de criar o seu cluster.

## <a name="how-do-i-customize-hdinsight-clusters"></a>Como personalizar os clusters HDInsight?

| Opção | Documentos |
|---|---|
| Ações de script | [Personalize os clusters Azure HDInsight utilizando ações de script](./hdinsight-hadoop-customize-cluster-linux.md) |
| Arranque | [Personalize os clusters HDInsight usando bootstrap](./hdinsight-hadoop-customize-cluster-bootstrap.md) |
| Metalojas externas | [Use external metadata stores in Azure HDInsight](./hdinsight-use-external-metadata-stores.md) (Utilizar arquivos de metadados externos no Azure HDInsight) |
| Ambari DB Personalizado | [Configurar clusters HDInsight com um Ambari DB personalizado](./hdinsight-custom-ambari-db.md) |

## <a name="what-are-some-errors-i-might-face-when-creating-clusters"></a>Quais são alguns erros que posso enfrentar ao criar aglomerados?

| Erro | Mais informações |
|---|---|
| Sem quota | Existem quotas para o número de quotas que pode criar na sua subscrição em cada região. Para mais informações, consulte [O Planeamento da Capacidade: quotas.](./hdinsight-capacity-planning.md) |
| Não há mais endereços IP disponíveis | Cada VNet tem um número limitado de endereços IP. Quando cria um cluster HDInsight, cada nó (incluindo zookeeper e nós de gateway) utiliza alguns destes endereços IP atribuídos. Quando todos os endereços IP estiverem em uso, irá encontrar este erro.  |
| As regras do grupo de segurança de rede (NSG) não permitem a comunicação com os fornecedores de recursos HDInsight | Se utilizar NSGs ou rotas definidas pelo utilizador (UDRs) para controlar o tráfego de entrada no seu cluster HDInsight, deve garantir que o seu cluster pode comunicar com serviços críticos de saúde e gestão azure. Para mais informações, consulte as etiquetas de serviço do Grupo de [Segurança da Rede (NSG) para o Azure HDInsight](./hdinsight-service-tags.md) |
| Reutilização do nome do cluster | Quando utilizar um nome de cluster que já utilizou antes, tem de esperar pelo número X de minutos antes de recriar o cluster. Caso contrário, verá uma mensagem de que o recurso já existe. |

## <a name="how-do-i-manage-running-hdinsight-clusters"></a>Como posso gerir os clusters HDInsight?

| Opção | Documentos |
|---|---|
| Dimensionamento Automático | [Escala automaticamente os clusters Azure HDInsight](./hdinsight-autoscale-clusters.md) |
| Dimensionamento manual | [Clusters Scale Azure HDInsight](./hdinsight-scaling-best-practices.md) |
| Monitorização com Ambari| [Monitor desempenho do cluster em Azure HDInsight](./hdinsight-key-scenarios-to-monitor.md) |
| Monitorização com registos do Monitor Azure | [Use Azure Monitor logs to monitor HDInsight clusters](./hdinsight-hadoop-oms-log-analytics-tutorial.md) (Utilizar registos do Azure Monitor para monitorizar clusters do HDInsight) |

## <a name="how-do-i-check-on-deleted-hdinsight-clusters"></a>Como posso verificar os clusters HDInsight apagados?

### <a name="azure-monitor-logs"></a>Registos do Azure Monitor

Pode utilizar a seguinte consulta com os registos do Monitor Azure para monitorizar os clusters eliminados.

```loganalytics
AzureActivity
| where ResourceProvider == "Microsoft.HDInsight" and (OperationName == "Create or Update Cluster" or OperationName == "Delete Cluster") and ActivityStatus == "Succeeded"
```

## <a name="next-steps"></a>Passos seguintes

* [Planeamento da capacidade para clusters do HDInsight](./hdinsight-capacity-planning.md)
* [Quais são as configurações padrão e recomendadas para o Azure HDInsight?](./hdinsight-supported-node-configuration.md)