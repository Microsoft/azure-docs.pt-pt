---
title: O que é consulta interativa no Azure HDInsight?
description: Uma introdução à Consulta Interativa, também chamada Apache Hive LLAP, In Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive
ms.date: 03/03/2020
ms.openlocfilehash: e133e08e333cb478269a93cce963566e195d6949
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2020
ms.locfileid: "78271961"
---
# <a name="what-is-interactive-query-in-azure-hdinsight"></a>O que é consulta interativa no Azure HDInsight

Consulta interativa (também chamada Apache Hive LLAP, ou [Processamento Analítico de Baixa Latência)](https://cwiki.apache.org/confluence/display/Hive/LLAP)é um tipo de [cluster](../hdinsight-hadoop-provision-linux-clusters.md#cluster-type)Azure HDInsight . A Consulta Interativa suporta o cache na memória, o que torna as consultas da Apache Hive mais rápidas e muito mais interativas. Os clientes usam a Consulta Interativa para consultar os dados armazenados no armazenamento e armazenamento do Lago Azure data de forma super rápida. A consulta interativa facilita que os desenvolvedores e cientistas de dados trabalhem com os big data usando ferramentas BI que mais amam. A HDInsight Interactive Query suporta várias ferramentas para aceder aos big data de forma fácil.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

Um cluster de consulta interativa é diferente de um aglomerado Apache Hadoop. Contém apenas o serviço da Colmeia.

Você pode aceder ao serviço Hive no cluster Deconsulta Interativa apenas através de Apache Ambari Hive View, Beeline, e o controlador de conectividade Microsoft Hive Open Database (Hive ODBC). Não é possível aceder através da consola Hive, Templeton, do Azure Classic CLI ou do Azure PowerShell.

## <a name="create-an-interactive-query-cluster"></a>Criar um cluster de consulta interativa

Para obter informações sobre a criação de um cluster HDInsight, consulte [Create Apache Hadoop clusters em HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Escolha o tipo de cluster de consulta interativa.

> [!IMPORTANT]
> O tamanho mínimo do nó de cabeça para aglomerados de consulta interativa é Standard_D13_v2. Consulte o Gráfico de [Dimensionamento Azure VM](../../cloud-services/cloud-services-sizes-specs.md#dv2-series)para obter mais informações.

## <a name="execute-apache-hive-queries-from-interactive-query"></a>Executar consultas de Hive Apache da Consulta Interativa

Para executar consultas da Hive, tem as seguintes opções:

|Método |Descrição |
|---|---|
|Microsoft Power BI|Consulte [a Visualização de dados interativos da Hive Apache com Power BI em Azure HDInsight](./apache-hadoop-connect-hive-power-bi-directquery.md), e [visualize os grandes dados com power BI no Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).|
|Visual Studio|Consulte [a Connect to Azure HDInsight e execute consultas apache hive usando ferramentas de data lake para estúdio visual](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-apache-hive-queries).|
|Visual Studio Code|Consulte o Código do [Estúdio Visual para Apache Hive, LLAP ou pySpark](../hdinsight-for-vscode.md).|
|Vista da colmeia Apache Ambari|Consulte [a vista apache hive com Apache Hadoop em Azure HDInsight](../hadoop/apache-hadoop-use-hive-ambari-view.md). A Hive View não está disponível para HDInsight 4.0.|
|Apache Beeline|Consulte [A Utilização da Colmeia Apache com Hadoop Apache em HDInsight com Beeline](../hadoop/apache-hadoop-use-hive-beeline.md). Pode utilizar beeline a partir do nó da cabeça ou de um nó de borda vazia. Recomendamos a utilização de Beeline a partir de um nó de borda vazia. Para obter informações sobre a criação de um cluster HDInsight utilizando um nó de borda vazia, consulte [Use os nós de borda vazia no HDInsight](../hdinsight-apps-use-edge-node.md).|
|Colmeia ODBC|Consulte [o Connect Excel a Apache Hadoop com o condutor microsoft Hive ODBC](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).|

Para encontrar a linha de ligação Java Database Connectivity (JDBC):

1. De um navegador web, navegue até `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary`, onde `CLUSTERNAME` é o nome do seu cluster.
1. Para copiar o URL, selecione o ícone da área de sobre-colar:

   ![HDInsight Hadoop Consulta Interativa LLAP JDBC](./media/apache-interactive-query-get-started/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="next-steps"></a>Passos seguintes

* Aprenda a [criar clusters de consulta interativa no HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* Saiba como [visualizar os big data com o Power BI no Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).
* Aprenda a [usar Apache Zeppelin para executar consultas apache hive em Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
