---
title: Visualizar dados de Apache Hive com o Power BI-Azure HDInsight
description: Saiba como usar o Microsoft Power BI para visualizar os dados do hive processados pelo Azure HDInsight.
keywords: hdinsight, Hadoop, Hive, consulta interativa, Hive interativo, LLAP, ODBC
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 9b99e24d00441f0caab4b55a46e5ccc8be9cdd64
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72529531"
---
# <a name="visualize-apache-hive-data-with-microsoft-power-bi-using-odbc-in-azure-hdinsight"></a>Visualizar dados de Apache Hive com o Microsoft Power BI usando ODBC no Azure HDInsight

Saiba como conectar o Microsoft Power BI Desktop ao Azure HDInsight usando o ODBC e visualize Apache Hive dados.

> [!IMPORTANT]
> Você pode aproveitar o driver ODBC do hive para fazer a importação por meio do conector ODBC genérico no Power BI Desktop. No entanto, não é recomendável para cargas de trabalho de BI dadas a natureza não interativa do mecanismo de consulta do hive. O [conector de consulta interativa do hdinsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) e o conector do [hdinsight Spark](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect) são melhores opções para seu desempenho.

Neste artigo, você carrega os dados de uma tabela `hivesampletable` Hive para Power BI. A tabela Hive contém alguns dados de uso de telefone celular. Em seguida, você plota os dados de uso em um mapa do mundo:

![HDInsight Power BI o relatório de mapa](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-visualization.png)

As informações também se aplicam ao novo tipo de cluster de [consulta interativa](../interactive-query/apache-interactive-query-get-started.md) . Para saber como se conectar à consulta interativa do HDInsight usando a consulta direta, consulte [Visualizar dados do hive de consulta interativa com o Microsoft Power bi usando a consulta direta no Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de passar por este artigo, você deve ter os seguintes itens:

* **Cluster HDInsight**. O cluster pode ser um cluster HDInsight com hive ou um cluster de consulta interativa recém liberado. Para criar clusters, consulte [criar cluster](apache-hadoop-linux-tutorial-get-started.md#create-cluster).

* **[Microsoft Power bi desktop](https://powerbi.microsoft.com/desktop/)** . Você pode baixar uma cópia do [centro de download da Microsoft](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="create-hive-odbc-data-source"></a>Criar a origem de dados do ODBC do Hive

Consulte [criar fonte de dados ODBC do hive](apache-hadoop-connect-excel-hive-odbc-driver.md#create-apache-hive-odbc-data-source).

## <a name="load-data-from-hdinsight"></a>Carregar dados do HDInsight

A tabela hive do **hivesampletable** vem com todos os clusters HDInsight.

1. Iniciar Power BI Desktop.

1. No menu superior, navegue até **página inicial**  > **obter dados**  > **mais...** .

    ![HDInsight Excel Power BI abrir dados](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-open-odbc.png)

1. Na caixa de diálogo **obter dados** , selecione **outro** da esquerda, selecione **ODBC** à direita e, em seguida, selecione **conectar** na parte inferior.

1. Na caixa **de diálogo do ODBC** , selecione o nome da fonte de dados que você criou na última seção na lista suspensa. Em seguida, selecione **OK**.

1. Para o primeiro uso, uma caixa de diálogo do **driver ODBC** será aberta. Selecione **padrão ou personalizado** no menu à esquerda. Em seguida, selecione **conectar-se** para abrir o **navegador**.

1. Na caixa de diálogo **navegador** , expanda **ODBC > Hive > padrão**, selecione **hivesampletable**e, em seguida, selecione **carregar**.

## <a name="visualize-data"></a>Visualizar os dados

Continue do último procedimento.

1. No painel visualizações, selecione **mapa**, é um ícone de globo.

    ![O HDInsight Power BI personaliza o relatório](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-customize.png)

1. No painel **campos** , selecione **país** e **devicemake**. Você pode ver os dados plotados no mapa.

1. Expanda o mapa.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu a Visualizar dados do HDInsight usando o Power BI.  Para saber mais, confira os seguintes artigos:

* [Use o Apache Zeppelin para executar Apache Hive consultas no Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Conecte o Excel ao HDInsight com o driver ODBC do Microsoft Hive](./apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Conecte o Excel ao Apache Hadoop usando Power Query](apache-hadoop-connect-excel-power-query.md).
* [Conecte-se ao Azure HDInsight e execute Apache Hive consultas usando o data Lake Tools para Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Use a ferramenta Azure HDInsight para Visual Studio Code](../hdinsight-for-vscode.md).
* [Carregar dados no HDInsight](./../hdinsight-upload-data.md).
