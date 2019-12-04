---
title: Práticas recomendadas de gerenciamento de cluster-Azure HDInsight
description: Conheça as práticas recomendadas para o gerenciamento de clusters HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 3010c1d597907e7d5c7f82c8b42721dc1f934f6f
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74782024"
---
# <a name="hdinsight-cluster-management-best-practices"></a>Práticas recomendadas de gerenciamento de cluster HDInsight

Conheça as práticas recomendadas para o gerenciamento de clusters HDInsight.

## <a name="how-do-i-create-hdinsight-clusters"></a>Como fazer criar clusters HDInsight?

| Opção | Documentos |
|---|---|
| Azure Data Factory | [Criar clusters de Apache Hadoop sob demanda no HDInsight usando Azure Data Factory](./hdinsight-hadoop-create-linux-clusters-adf.md) |
| Modelo personalizado do Resource Manager | [Criar clusters Apache Hadoop no HDInsight usando modelos do Resource Manager](./hdinsight-hadoop-create-linux-clusters-arm-templates.md) |
| Modelos de início rápido | [Modelos de início rápido do HDInsight](https://azure.microsoft.com/resources/templates/?term=hdinsight) |
| exemplos do Azure | [Exemplos do HDInsight no Azure](https://docs.microsoft.com/samples/browse/?products=azure-hdinsight) |
| Portal do Azure | [Criar clusters baseados em Linux no HDInsight usando o portal do Azure](./spark/apache-spark-intellij-tool-plugin.md) |
| CLI do Azure | [Criar clusters HDInsight usando o CLI do Azure](./hdinsight-hadoop-create-linux-clusters-azure-cli.md) |
| Azure PowerShell | [Criar clusters baseados em Linux no HDInsight usando Azure PowerShell](./hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |
| cURL | [Criar clusters Apache Hadoop usando a API REST do Azure](./hdinsight-hadoop-create-linux-clusters-curl-rest.md) |
| SDKs (.NET, Python, Java) | [.Net](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet), [Python](https://docs.microsoft.com/python/api/overview/azure/hdinsight?view=azure-python), [Java](https://docs.microsoft.com/java/api/overview/azure/hdinsight?view=azure-java-stable), [go](https://docs.microsoft.com/azure/hdinsight/hdinsight-go-sdk-overview) |

> [!Note]
> Se você estiver criando um cluster e reutilizando o nome do cluster de um cluster criado anteriormente, aguarde até que a exclusão anterior do cluster seja concluída antes de criar o cluster.

## <a name="how-do-i-customize-hdinsight-clusters"></a>Como fazer personalizar os clusters HDInsight?

| Opção | Documentos |
|---|---|
| Ações de script | [Personalizar clusters do Azure HDInsight usando ações de script](./hdinsight-hadoop-customize-cluster-linux.md) |
| Inicialização | [Personalizar os clusters HDInsight usando a inicialização](./hdinsight-hadoop-customize-cluster-bootstrap.md) |
| Metastores externos | [Use external metadata stores in Azure HDInsight](./hdinsight-use-external-metadata-stores.md) (Utilizar arquivos de metadados externos no Azure HDInsight) |
| Ambari DB Personalizado | [Configurar clusters do HDInsight com um Ambari DB personalizado](./hdinsight-custom-ambari-db.md) |

## <a name="what-are-some-errors-i-might-face-when-creating-clusters"></a>Quais são alguns erros que eu poderia enfrentar ao criar clusters?

| Erro | Mais informações |
|---|---|
| Sem cota | Há cotas para o número de cotas que você pode criar em sua assinatura em cada região. Para obter mais informações, consulte [planejamento de capacidade: cotas](./hdinsight-capacity-planning.md). |
| Não há mais endereços IP disponíveis | Cada VNet tem um número limitado de endereços IP. Quando você cria um cluster HDInsight, cada nó (incluindo Zookeeper e nós de gateway) usa alguns desses endereços IP alocados. Quando todos os endereços IP estiverem em uso, você encontrará esse erro.  |
| As regras do NSG (grupo de segurança de rede) não permitem a comunicação com provedores de recursos do HDInsight | Se você usar NSGs ou UDRs (rotas definidas pelo usuário) para controlar o tráfego de entrada para o cluster HDInsight, deverá garantir que o cluster possa se comunicar com os serviços críticos de integridade e gerenciamento do Azure. Para obter mais informações, consulte [marcas de serviço do NSG (grupo de segurança de rede) para o Azure HDInsight](./hdinsight-service-tags.md) |
| Reutilização do nome do cluster | Ao usar um nome de cluster que você usou antes, você precisa aguardar X número de minutos antes de recriar o cluster. Caso contrário, você verá uma mensagem informando que o recurso já existe. |

## <a name="how-do-i-manage-running-hdinsight-clusters"></a>Como fazer gerenciar clusters HDInsight em execução?

| Opção | Documentos |
|---|---|
| Dimensionamento automático | [Dimensionar automaticamente os clusters do Azure HDInsight](./hdinsight-autoscale-clusters.md) |
| Dimensionamento manual | [Dimensionar clusters do Azure HDInsight](./hdinsight-scaling-best-practices.md) |
| Monitoramento com o Ambari| [Monitorar o desempenho do cluster no Azure HDInsight](./hdinsight-key-scenarios-to-monitor.md) |
| Monitoramento com logs de Azure Monitor | [Use Azure Monitor logs to monitor HDInsight clusters](./hdinsight-hadoop-oms-log-analytics-tutorial.md) (Utilizar registos do Azure Monitor para monitorizar clusters do HDInsight) |

## <a name="how-do-i-check-on-deleted-hdinsight-clusters"></a>Como fazer verificar em clusters HDInsight excluídos?

### <a name="azure-monitor-logs"></a>Registos do Azure Monitor

Você pode usar a consulta a seguir com os logs de Azure Monitor para monitorar os clusters excluídos.

```loganalytics
AzureActivity
| where ResourceProvider == "Microsoft.HDInsight" and (OperationName == "Create or Update Cluster" or OperationName == "Delete Cluster") and ActivityStatus == "Succeeded"
```

## <a name="next-steps"></a>Passos seguintes

* [Planeamento da capacidade para clusters do HDInsight](./hdinsight-capacity-planning.md)
* [Quais são as configurações de nó padrão e recomendadas para o Azure HDInsight?](./hdinsight-supported-node-configuration.md)