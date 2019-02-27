---
title: Utilizar o Interactive Query com o Azure HDInsight
description: Saiba como utilizar o Interactive Query (LLAP do Hive) com o HDInsight.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/22/2018
ms.openlocfilehash: ae0264c918bb550affe8e694cdb3b9891edea531
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56875451"
---
# <a name="use-interactive-query-with-hdinsight"></a>Utilizar uma consulta interativa com o HDInsight
Consulta interativa (também denominado LLAP do Hive do Apache, ou [processamento analítico de baixa latência](https://cwiki.apache.org/confluence/display/Hive/LLAP)) é um HDInsight do Azure [tipo de cluster](../hdinsight-hadoop-provision-linux-clusters.md#cluster-types). Consulta interativa suporta a colocação em cache na memória, que torna o Apache Hive consultas muito mais rápidas e interativas.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)] 

Um cluster do Interactive Query é diferente de um cluster do Apache Hadoop. Ele contém apenas o serviço do Hive. 

> [!NOTE]  
> Pode acessar o serviço do Hive no cluster do Interactive Query apenas através do Apache Ambari Hive View, Beeline e o controlador Microsoft Hive Open Database Connectivity (ODBC do Hive). Não é possível acessá-lo através da consola do Hive, Templeton, a CLI clássica do Azure ou do Azure PowerShell. 

## <a name="create-an-interactive-query-cluster"></a>Criar um cluster do Interactive Query
Para obter informações sobre como criar um cluster do HDInsight, consulte [Apache Hadoop criar clusters no HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Escolha o tipo de cluster do Interactive Query.

## <a name="execute-apache-hive-queries-from-interactive-query"></a>Executar consultas do Apache Hive no Interactive Query
Para executar consultas do Hive, tem as seguintes opções:

* Utilize o Microsoft Power BI

    Ver [visualizar Interactive Query Apache Hive dados com o Power BI no Azure HDInsight](./apache-hadoop-connect-hive-power-bi-directquery.md) veja [visualizar grandes volumes de dados com o Power BI no Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).
 
* Utilizar Apache Zeppelin

    Ver [utilização Apache Zeppelin para executar consultas do Apache Hive no Azure HDInsight](../hdinsight-connect-hive-zeppelin.md).

* Utilizar o Visual Studio

    Ver [ligue-se ao Azure HDInsight e executar consultas do Apache Hive com o Data Lake Tools para Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-apache-hive-queries).

* Utilizar o Visual Studio Code

    Ver [utilização Visual Studio Code para o Apache Hive, LLAP ou pySpark](../hdinsight-for-vscode.md).
* Execute o Apache Hive com o Apache Ambari Hive View.
  
    Ver [utilizar o modo de exibição do Apache Hive com o Apache Hadoop no HDInsight do Azure](../hadoop/apache-hadoop-use-hive-ambari-view.md).

* Execute o Apache Hive com o Beeline.
  
    Ver [utilizar o Apache Hive com o Apache Hadoop no HDInsight com o Beeline](../hadoop/apache-hadoop-use-hive-beeline.md).
  
    Pode utilizar Beeline do nó principal ou a partir de um nó de extremidade vazio. Recomendamos que utilize o Beeline de um nó de extremidade em branco. Para obter informações sobre como criar um cluster do HDInsight ao utilizar um nó de extremidade em branco, consulte [utilizar nós de extremidade vazios no HDInsight](../hdinsight-apps-use-edge-node.md).
* Execute o Apache Hive através de ODBC do Hive.
  
    Ver [ligar o Excel para Apache Hadoop com o controlador Microsoft Hive ODBC](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).

Para localizar a cadeia de ligação de conectividade de banco de dados de Java (JDBC):

1. Inicie sessão no Apache Ambari, com o seguinte URL: https://\<nome do cluster\>. AzureHDInsight.net.
2. No menu da esquerda, selecione **Hive**.
3. Para copiar o URL, selecione o ícone de área de transferência:
   
   ![Hadoop do HDInsight Interactive Query LLAP JDBC](./media/apache-interactive-query-get-started/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="next-steps"></a>Passos Seguintes

* Saiba como [criar clusters do Interactive Query no HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* Saiba como [visualizar grandes volumes de dados com o Power BI no Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).
* Saiba como [utilizar o Apache Zeppelin para executar consultas do Apache Hive no Azure HDInsight](../hdinsight-connect-hive-zeppelin.md).
* Saiba como [executar consultas do Apache Hive com o Data Lake Tools para Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-apache-hive-queries).
* Saiba como [utilizar ferramentas do HDInsight para Visual Studio Code](../hdinsight-for-vscode.md).
* Saiba como [utilizar o modo de exibição do Apache Hive com o Apache Hadoop no HDInsight](../hadoop/apache-hadoop-use-hive-ambari-view.md)
* Saiba como [utilizar Beeline para submeter consultas do Apache Hive no HDInsight](../hadoop/apache-hadoop-use-hive-beeline.md).
* Saiba como [ligar o Excel para Apache Hadoop com o controlador Microsoft Hive ODBC](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).

