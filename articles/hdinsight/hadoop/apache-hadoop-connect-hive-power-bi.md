---
title: Visualize os dados do Apache Hive com o Power BI - Azure HDInsight
description: Saiba como utilizar o Microsoft Power BI para visualizar dados de Hive processados pelo Azure HDInsight.
keywords: hdinsight, hadoop, hive, interactive consultas interativas do hive, LLAP, odbc
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: hrasheed
ms.openlocfilehash: 1e0c043e484e4eaf2639f76c9af3fef15ad85047
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66237491"
---
# <a name="visualize-apache-hive-data-with-microsoft-power-bi-using-odbc-in-azure-hdinsight"></a>Visualize os dados do Apache Hive com o Microsoft Power BI com ODBC no Azure HDInsight

Saiba como ligar o Microsoft Power BI Desktop para o HDInsight do Azure com o ODBC e visualize os dados do Apache Hive.

>[!IMPORTANT]
> Pode tirar partido do controlador ODBC do Hive para importar através do conector ODBC genérico no Power BI Desktop. No entanto não é recomendado para cargas de trabalho de BI dadas a natureza não interativa do motor de consulta do Hive. [Conector do Interactive Query do HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) e [conector do Spark do HDInsight](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect) são opções melhor para seu desempenho.

Neste tutorial, carregue os dados a partir de um `hivesampletable` tabela do Hive para o Power BI. A tabela de Hive contém alguns dados de utilização do telefone celular. Em seguida, representar os dados de utilização num mapa-mundo:

![HDInsight Power BI, o relatório de mapa](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-visualization.png)

As informações também se aplica ao novo [Interactive Query](../interactive-query/apache-interactive-query-get-started.md) tipo de cluster. Para saber como ligar ao Interactive Query do HDInsight através da consulta direta, consulte [dados visualizar consulta interativa do Hive com o Microsoft Power BI através da consulta direta no Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de passar por este artigo, tem de ter os seguintes itens:

* **Cluster de HDInsight**. O cluster pode ser um cluster do HDInsight com o Hive ou um cluster do Interactive Query recém-lançada. Para criar clusters, veja [criar um cluster](apache-hadoop-linux-tutorial-get-started.md#create-cluster).

* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)** . Pode baixar uma cópia a partir da [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="create-hive-odbc-data-source"></a>Criar a origem de dados do ODBC do Hive

Ver [origem de dados criar ODBC do Hive](apache-hadoop-connect-excel-hive-odbc-driver.md#create-apache-hive-odbc-data-source).

## <a name="load-data-from-hdinsight"></a>Carregar dados do HDInsight

A tabela de Hive hivesampletable é fornecido com todos os clusters do HDInsight.

1. Inicie o ambiente de trabalho do Power BI.

2. A partir do menu superior, navegue para **home page** > **obter dados** > **mais...** .

    ![Dados de open HDInsight Power BI](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-open-odbc.png)

3. Do **obter dados** caixa de diálogo, selecione **outros** a partir do lado esquerdo, selecione **ODBC** da direita e, em seguida, selecione **Connect** na parte inferior.

4. Partir do **de ODBC** caixa de diálogo, selecione os dados que criou na última seção na lista pendente de nome da origem e, em seguida, selecionam **OK**.

5. Do **Navigator** caixa de diálogo, expanda **ODBC > HIVE > predefinição**, selecione **hivesampletable**e, em seguida, selecione **carga**.

6. Partir do **controlador ODBC** caixa de diálogo, selecione **predefinido ou personalizado**, em seguida, selecione **Connect**.

## <a name="visualize-data"></a>Visualizar os dados

Continue a partir do último procedimento.

1. No painel visualizações, selecione **mapa**.  É um ícone de globo.

    ![HDInsight Power BI personaliza o relatório](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-customize.png)
2. Partir do **campos** painel, selecione **país** e **devicemake**. Pode ver os dados desenhados no mapa.
3. Expanda o mapa.

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu a visualizar dados do HDInsight com o Power BI.  Para obter mais informações, consulte os artigos seguintes:

* [Utilizar Apache Zeppelin para executar consultas do Apache Hive no Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Ligar o Excel ao HDInsight com o controlador Microsoft Hive ODBC](./apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Ligar o Excel para Apache Hadoop com o Power Query](apache-hadoop-connect-excel-power-query.md).
* [Ligar ao Azure HDInsight e executar consultas do Apache Hive com o Data Lake Tools para Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Utilize a ferramenta do Azure HDInsight para Visual Studio Code](../hdinsight-for-vscode.md).
* [Carregar dados para o HDInsight](./../hdinsight-upload-data.md).
