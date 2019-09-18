---
title: O que é a consulta interativa no Azure HDInsight?
description: Uma introdução à consulta interativa, também chamada de Apache Hive LLAP, no Azure HDInsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: overview
ms.date: 09/17/2019
ms.openlocfilehash: 3e0fddc03ad880d36876276827198b0ac180a00d
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076363"
---
# <a name="what-is-interactive-query-in-azure-hdinsight"></a>O que é a consulta interativa no Azure HDInsight

A consulta interativa (também chamada de Apache Hive LLAP ou [processamento analítico de baixa latência](https://cwiki.apache.org/confluence/display/Hive/LLAP)) é um [tipo de cluster](../hdinsight-hadoop-provision-linux-clusters.md#cluster-types)do Azure HDInsight. A consulta interativa dá suporte ao cache na memória, o que torna Apache Hive consultas mais rapidamente e muito mais interativas. Os clientes usam a consulta interativa para consultar dados armazenados no armazenamento do Azure & Azure Data Lake Storage de maneira extremamente rápida. A consulta interativa facilita que os desenvolvedores e o cientista de dados trabalhem com o Big Data usando ferramentas de BI que mais adoram. A consulta interativa do HDInsight dá suporte a várias ferramentas para acessar Big Data de maneira fácil.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

Um cluster de consulta interativa é diferente de um cluster Apache Hadoop. Ele contém apenas o serviço Hive.

Você pode acessar o serviço do hive no cluster de consulta interativa somente via exibição do Ambari hive do Apache, beeline e o driver de conectividade de banco de dados aberto do Microsoft Hive (Hive ODBC). Você não pode acessá-lo por meio do console do hive, Templeton, CLI clássico do Azure ou Azure PowerShell.

## <a name="create-an-interactive-query-cluster"></a>Criar um cluster de consulta interativa

Para obter informações sobre como criar um cluster HDInsight, consulte [criar Apache Hadoop clusters no HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Escolha o tipo de cluster consulta interativa.

> [!IMPORTANT]
> O tamanho mínimo de cabeçalho para clusters de consulta interativa é Standard_D13_v2. Consulte o [gráfico de dimensionamento de VM do Azure](../../cloud-services/cloud-services-sizes-specs.md#dv2-series)para obter mais informações.

## <a name="execute-apache-hive-queries-from-interactive-query"></a>Executar consultas de Apache Hive da consulta interativa

Para executar consultas de Hive, você tem as seguintes opções:

* Usar o Microsoft Power BI

    Consulte [Visualizar consultas Interativas Apache Hive dados com Power bi no Azure hdinsight](./apache-hadoop-connect-hive-power-bi-directquery.md) consulte [visualizar Big data com Power bi no Azure hdinsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).

* Utilizar o Visual Studio

    Consulte [conectar-se ao Azure HDInsight e executar Apache Hive consultas usando o data Lake Tools para Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-apache-hive-queries).

* Usar Visual Studio Code

    Consulte [usar Visual Studio Code para Apache Hive, LLAP ou pySpark](../hdinsight-for-vscode.md).
* Execute Apache Hive usando o modo de exibição do Ambari hive do Apache.
  
    Consulte [usar Apache Hive exibição com Apache Hadoop no Azure HDInsight](../hadoop/apache-hadoop-use-hive-ambari-view.md).

* Execute Apache Hive usando beeline.
  
    Confira [usar Apache Hive com Apache Hadoop no HDInsight com beeline](../hadoop/apache-hadoop-use-hive-beeline.md).
  
    Você pode usar beeline do nó de cabeçalho ou de um nó de borda vazio. É recomendável usar beeline de um nó de borda vazio. Para obter informações sobre como criar um cluster HDInsight usando um nó de borda vazio, consulte [usar nós de borda vazios no HDInsight](../hdinsight-apps-use-edge-node.md).
* Execute Apache Hive usando o ODBC do hive.
  
    Consulte [conectar o Excel ao Apache Hadoop com o driver ODBC do Microsoft Hive](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).

Para localizar a cadeia de conexão Java Database Connectivity (JDBC):

1. Entre no Apache Ambari usando a seguinte URL: `https://<cluster name>.AzureHDInsight.net`.
2. No menu à esquerda, selecione **Hive**.
3. Para copiar a URL, selecione o ícone da área de transferência:

   ![JDBC LLAP de consulta interativa do HDInsight Hadoop](./media/apache-interactive-query-get-started/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="next-steps"></a>Passos Seguintes

* Saiba como [Criar clusters de consulta interativa no HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* Saiba como [visualizar Big data com Power bi no Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).
* Saiba como [usar o Apache Zeppelin para executar Apache Hive consultas no Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
