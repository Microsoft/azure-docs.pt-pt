---
title: Visualizar os dados da Hive Apache com Power BI - Azure HDInsight
description: Saiba como utilizar o Microsoft Power BI para visualizar os dados da Hive processados pelo Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/02/2020
ms.openlocfilehash: d9b64785dbd82842479eb3f313b8394f9f25b40b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370003"
---
# <a name="visualize-apache-hive-data-with-microsoft-power-bi-using-odbc-in-azure-hdinsight"></a>Visualizar o dados do Apache Hive com o Microsoft Power BI através do ODBC no Azure HDInsight

Saiba como ligar o Microsoft Power BI Desktop ao Azure HDInsight usando o ODBC e visualizar os dados da Apache Hive.

> [!IMPORTANT]
> Pode aproveitar o controlador Hive ODBC para importar através do conector genérico oDBC no Power BI Desktop. No entanto, não é recomendado para cargas de trabalho bi dada a natureza não interativa do motor de consulta da Colmeia. [O conector de consulta interativa HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) e o [conector HDInsight Spark](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect) são melhores escolhas para o seu desempenho.

Neste artigo, você carrega os `hivesampletable` dados de uma tabela da Colmeia para Power BI. A tabela Hive contém alguns dados de utilização do telemóvel. Em seguida, planeia os dados de uso num mapa mundial:

![HDInsight Power BI o relatório do mapa](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-visualization.png)

A informação aplica-se também ao novo tipo de cluster [de consulta interativa.](../interactive-query/apache-interactive-query-get-started.md) Para como se conectar à HDInsight Interactive Query utilizando consulta direta, consulte [Visualize dados de Hive de Consulta Interativa com o Microsoft Power BI utilizando consulta direta no Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de ler este artigo, deve ter os seguintes itens:

* Cluster HDInsight. O cluster pode ser um cluster HDInsight com Hive ou um cluster de consulta interativa recém-lançado. Para criar clusters, consulte [Criar cluster](apache-hadoop-linux-tutorial-get-started.md).

* [Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/). Pode descarregar uma cópia do [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="create-hive-odbc-data-source"></a>Criar a origem de dados do ODBC do Hive

Consulte [criar a fonte de dados da Hive ODBC](apache-hadoop-connect-excel-hive-odbc-driver.md#create-apache-hive-odbc-data-source).

## <a name="load-data-from-hdinsight"></a>Dados de carga do HDInsight

A mesa **colmeia da colmeia vem** com todos os clusters HDInsight.

1. Inicie o Power BI Desktop.

1. Do menu superior, navegue até **Home** > **Get Data** > **More...**.

    ![HDInsight Excel Power BI dados abertos](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-open-odbc.png)

1. A partir do diálogo **Get Data,** selecione **Outro** da esquerda, selecione **ODBC** a partir da direita e, em seguida, selecione **Connect** na parte inferior.

1. A partir do diálogo **ODBC,** selecione o nome de origem de dados que criou na última secção da lista de lançamentos. Em seguida, selecione **OK**.

1. Para a primeira utilização, abrir-se-á um diálogo do **condutor DaDBC.** Selecione **Padrão ou Custom** a partir do menu esquerdo. Em seguida, selecione **Ligar** para abrir o **Navigator**.

1. A partir do diálogo **Navigator,** expanda **o ODBC > HIVE > padrão**, selecione a **colmeia,** e, em seguida, selecione **Load**.

## <a name="visualize-data"></a>Visualizar os dados

Continue a partir do último procedimento.

1. Do painel de Visualizações, selecione **Map,** é um ícone do globo.

    ![HDInsight Power BI personaliza relatório](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-customize.png)

1. Do painel **Fields,** selecione **país** e **dispositivos.** Pode ver os dados traçados no mapa.

1. Expanda o mapa.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a visualizar dados do HDInsight usando o Power BI.  Para saber mais, consulte os seguintes artigos:

* [Use Apache Zeppelin para executar consultas apache hive em Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Ligue o Excel ao HDInsight com o condutor microsoft Hive ODBC](./apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Ligue excel a Apache Hadoop utilizando](apache-hadoop-connect-excel-power-query.md)power query .
* [Ligue-se ao Azure HDInsight e execute consultas apache hive usando ferramentas de data lake para estúdio visual](apache-hadoop-visual-studio-tools-get-started.md).
* [Utilize a ferramenta Azure HDInsight para o Código](../hdinsight-for-vscode.md)do Estúdio Visual .
* [Enviar dados para HDInsight](./../hdinsight-upload-data.md).
