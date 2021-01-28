---
title: O que é a Consulta Interativa em Azure HDInsight?
description: Uma introdução à Consulta Interativa, também chamada Apache Hive LLAP, In Azure HDInsight
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive
ms.date: 03/03/2020
ms.openlocfilehash: 2813554700e015c0ac34e47d632d16d97c948c4e
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98941074"
---
# <a name="what-is-interactive-query-in-azure-hdinsight"></a>O que é Consulta Interativa Em Azure HDInsight

A Consulta Interativa (também chamada Apache Hive LLAP, ou [Baixo Processamento Analítico de Latência)](https://cwiki.apache.org/confluence/display/Hive/LLAP)é um [tipo de cluster](../hdinsight-hadoop-provision-linux-clusters.md#cluster-type)Azure HDInsight . A Consulta Interativa suporta o caching na memória, o que torna as consultas de Hive Apache mais rápidas e muito mais interativas. Os clientes usam a Consulta Interativa para consultar dados armazenados no armazenamento Azure & Azure Data Lake Storage de forma super rápida. A consulta interativa facilita que os desenvolvedores e cientistas de dados trabalhem com os big data usando ferramentas BI que mais amam. A HDInsight Interactive Query suporta várias ferramentas para aceder aos big data de forma fácil.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

Um cluster de consultainterrupção é diferente de um aglomerado de Apache Hadoop. Contém apenas o serviço da Colmeia.

Você pode aceder ao serviço Hive no cluster de consultainterquial interativa apenas através de Apache Ambari Hive View, Beeline, e o controlador de conectividade de base de dados aberta da Microsoft Hive (Hive ODBC). Não é possível aceder através da consola Hive, Templeton, do Azure Classic CLI ou da Azure PowerShell.

## <a name="create-an-interactive-query-cluster"></a>Criar um cluster de consultainterativa

Para obter informações sobre a criação de um cluster HDInsight, consulte [os clusters Create Apache Hadoop em HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Escolha o tipo de cluster de consulta interativa.

> [!IMPORTANT]
> O tamanho mínimo do cabeçano para clusters de consultainterrupta é Standard_D13_v2. Consulte a [Tabela de Dimensionamento Azure VM](../../cloud-services/cloud-services-sizes-specs.md#dv2-series)para obter mais informações.

## <a name="execute-apache-hive-queries-from-interactive-query"></a>Executar consultas de Colmeia Apache da Consulta Interativa

Para executar consultas de Colmeia, tem as seguintes opções:

|Método |Descrição |
|---|---|
|Microsoft Power BI|Consulte [os dados da Hive Apache De Visualização Interativa com Power BI em Azure HDInsight,](./apache-hadoop-connect-hive-power-bi-directquery.md)e [Visualize big data com Power BI em Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).|
|Visual Studio|Consulte [Connect to Azure HDInsight e executar consultas apache hive usando ferramentas data lake para estúdio visual.](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-apache-hive-queries)|
|Visual Studio Code|Consulte [o Código do Estúdio Visual para Apache Hive, LLAP ou pySpark](../hdinsight-for-vscode.md).|
|Vista de Colmeia Apache Ambari|Ver [Use Apache Hive View com Apache Hadoop em Azure HDInsight](../hadoop/apache-hadoop-use-hive-ambari-view.md). Hive View não está disponível para HDInsight 4.0.|
|Abelha Apache|Ver [Use Apache Hive com Apache Hadoop em HDInsight com Beeline](../hadoop/apache-hadoop-use-hive-beeline.md). Pode utilizar o Beeline a partir do nó da cabeça ou de um nó de borda vazia. Recomendamos a utilização de Beeline a partir de um nó de borda vazia. Para obter informações sobre a criação de um cluster HDInsight utilizando um nó de borda vazia, consulte [Use nós de borda vazia em HDInsight](../hdinsight-apps-use-edge-node.md).|
|Hive ODBC|Consulte [o Connect Excel a Apache Hadoop com o controlador ODBC da Microsoft Hive](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).|

Para encontrar a cadeia de ligação Java Database Connectivity (JDBC):

1. A partir de um navegador web, navegue `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary` para, onde `CLUSTERNAME` está o nome do seu cluster.
1. Para copiar o URL, selecione o ícone da área de transferência:

   ![Consulta Interativa HDInsight Hadoop LLAP JDBC](./media/apache-interactive-query-get-started/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="next-steps"></a>Próximos passos

* Saiba como [criar clusters de consulta interativa em HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* Saiba como [visualizar big data com Power BI em Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).
* Aprenda a [usar Apache Zeppelin para executar consultas apache hive em Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
