---
title: Visualizar dados da Colmeia Apache com Power BI - Azure HDInsight
description: Saiba como usar o Microsoft Power BI para visualizar os dados da Hive processados pelo Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: e66329b93ebd7d90258ab4670b77ca849a28189b
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92548001"
---
# <a name="visualize-apache-hive-data-with-microsoft-power-bi-using-odbc-in-azure-hdinsight"></a>Visualizar o dados do Apache Hive com o Microsoft Power BI através do ODBC no Azure HDInsight

Saiba como ligar o Microsoft Power BI Desktop ao Azure HDInsight utilizando o ODBC e visualizar os dados da Hive Apache.

> [!IMPORTANT]
> Pode aproveitar o controlador Hive ODBC para fazer a importação através do conector ODBC genérico no Power BI Desktop. No entanto, não é recomendado para cargas de trabalho bi dada a natureza não interativa do motor de consulta hive. [O conector de consulta interativa HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) e [o conector HDInsight Spark](/power-bi/spark-on-hdinsight-with-direct-connect) são melhores escolhas para o seu desempenho.

Neste artigo, você carrega os dados de uma `hivesampletable` tabela De Colmeia para Power BI. A tabela Hive contém alguns dados de utilização do telemóvel. Depois, traça-se os dados de utilização num mapa mundial:

![HDInsight Power BI o relatório do mapa](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-visualization.png)

A informação aplica-se também ao novo tipo de cluster [de consultaintere interação.](../interactive-query/apache-interactive-query-get-started.md) Para saber como ligar à Consulta Interativa HDInsight utilizando consulta direta, consulte [visualize dados de Hive de Consulta Interativa com o Microsoft Power BI utilizando consulta direta em Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de ler este artigo, deve ter os seguintes itens:

* Cluster HDInsight. O cluster pode ser um cluster HDInsight com Hive ou um cluster de consultainterna interactive recentemente lançado. Para criar clusters, consulte [Criar cluster.](apache-hadoop-linux-tutorial-get-started.md)

* [Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/). Pode descarregar uma cópia do [Microsoft Download Center.](https://www.microsoft.com/download/details.aspx?id=45331)

## <a name="create-hive-odbc-data-source"></a>Criar a origem de dados do ODBC do Hive

Consulte [a fonte de dados da Hive ODBC](apache-hadoop-connect-excel-hive-odbc-driver.md#create-apache-hive-odbc-data-source).

## <a name="load-data-from-hdinsight"></a>Carregar dados a partir de HDInsight

A mesa **hive hive hive hive hive** vem com todos os clusters HDInsight.

1. Inicie o Power BI Desktop.

1. Do menu superior, navegue até **home**  >  **obter dados**  >  **mais...** .

    ![HdInsight Excel Power BI abre dados](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-open-odbc.png)

1. A partir do diálogo **Obter Dados,** selecione **Outro** a partir da esquerda, selecione **ODBC** pela direita e, em seguida, selecione **Connect** on the bottom.

1. A partir do diálogo **ODBC,** selecione o nome de origem de dados criado na última secção a partir da lista de drop-down. Em seguida, selecione **OK** .

1. Para a primeira utilização, abre-se um diálogo do **controlador ODBC.** Selecione **Predefinido ou Personalizado** a partir do menu esquerdo. Em seguida, **selecione Connect** para abrir **o Navegador** .

1. A partir do diálogo **Navigator,** expanda o **ODBC > HIVE > padrão,** selecione **hivesmpletable** , e, em seguida, selecione **Load** .

## <a name="visualize-data"></a>Visualizar os dados

Continue a partir do último procedimento.

1. A partir do painel de Visualizações, selecione **Map,** é um ícone do globo.

    ![HDInsight Power BI personaliza relatório](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-customize.png)

1. A partir do painel **Fields,** selecione **country** e **devicemake** . Pode ver os dados traçados no mapa.

1. Expanda o mapa.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a visualizar dados a partir de HDInsight usando Power BI.  Para saber mais, leia os artigos seguintes:

* [Ligue o Excel ao HDInsight com o controlador ODBC da Microsoft Hive](./apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Ligue o Excel ao Apache Hadoop utilizando a Consulta de Potência](apache-hadoop-connect-excel-power-query.md).
* [Visualizar dados de Hive Apache De Consulta Interativa com o Microsoft Power BI utilizando consulta direta](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)