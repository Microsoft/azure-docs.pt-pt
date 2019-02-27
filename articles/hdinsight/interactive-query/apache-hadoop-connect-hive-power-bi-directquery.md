---
title: Visualizar dados de consulta interativa do Hive com o Power BI no Azure HDInsight
description: Utilizar o Microsoft Power BI para visualizar dados de consulta interativa do Hive do HDInsight do Azure
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/25/2018
ms.openlocfilehash: d9639a4a116e06e17005ebddbb26379882491b33
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56867835"
---
# <a name="visualize-interactive-query-apache-hive-data-with-microsoft-power-bi-using-direct-query-in-azure-hdinsight"></a>Visualizar dados de Interactive Query Apache Hive com o Microsoft Power BI através da consulta direta no Azure HDInsight

Este artigo descreve como ligar o Microsoft Power BI para clusters do Azure HDInsight Interactive Query e visualize os dados do Apache Hive através da consulta direta. O exemplo fornecido carrega os dados de um `hivesampletable` tabela do Hive para o Power BI. O `hivesampletable` tabela do Hive contém alguns dados de utilização do telefone celular. Em seguida, representar os dados de utilização num mapa-mundo:

![HDInsight Power BI, o relatório de mapa](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-visualization.png)

Pode aproveitar o [controlador ODBC do Hive do Apache](../hadoop/apache-hadoop-connect-hive-power-bi.md) para importar através do conector ODBC genérico no Power BI Desktop. No entanto não é recomendado para cargas de trabalho de BI dadas a natureza não interativa do motor de consulta do Hive. [Conector do Interactive Query do HDInsight](./apache-hadoop-connect-hive-power-bi-directquery.md) e [conector do Spark do HDInsight Apache](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect) são opções melhor para seu desempenho.

## <a name="prerequisites"></a>Pré-requisitos
Antes de passar por este artigo, tem de ter os seguintes itens:

* **Cluster de HDInsight**. O cluster pode ser um cluster do HDInsight com o Apache Hive ou um cluster do Interactive Query recém-lançada. Para criar clusters, veja [criar um cluster](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).
* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)**. Pode baixar uma cópia a partir da [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="load-data-from-hdinsight"></a>Carregar dados do HDInsight

O `hivesampletable` tabela do Hive vem com todos os clusters do HDInsight.

1. Inicie o ambiente de trabalho do Power BI.

2. A partir da barra de menus, navegue para **home page** > **obter dados** > **mais...** .

    ![Dados de open HDInsight Power BI](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-open-odbc.png)

3. Partir do **obter dados** janela, introduza **hdinsight** na caixa de pesquisa.  

4. Resultados da pesquisa, selecione **Interactive Query do HDInsight**e, em seguida, selecione **Connect**.  Se não vir **Interactive Query do HDInsight**, tem de atualizar o seu ambiente de trabalho do Power BI para a versão mais recente.

5. Selecione **continuar** para fechar a **ligar a um serviço de terceiros** caixa de diálogo.

6. Na **Interactive Query do HDInsight** janela, introduza as seguintes informações e, em seguida, selecione **OK**:

    |Propriedade | Valor |
    |---|---|
    |Servidor |Introduza o nome do cluster, por exemplo *myiqcluster.azurehdinsight.net*.|
    |Base de Dados |Introduza **predefinição** deste artigo.|
    |Modo de conectividade de dados |Selecione **DirectQuery** deste artigo.|

    ![Ligue-se do interactive query do HDInsight DirectQuery do Power BI](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-connect.png)

7. Introduza as credenciais HTTP e, em seguida, selecione **Connect**. O nome de utilizador predefinido é **administrador**.

8. Partir do **Navigator** janela no painel esquerdo, selecione **hivesampletale**.

9. Selecione **carga** da janela principal.

    ![Hivesampletable de Power BI de uma consulta interativa do HDInsight](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-hivesampletable.png)

## <a name="visualize-data-on-a-map"></a>Visualize os dados num mapa

Continue a partir do último procedimento.

1. No painel visualizações, selecione **mapa**, o ícone de globo. Um mapa genérico, em seguida, é apresentada na janela principal.

    ![HDInsight Power BI personaliza o relatório](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-customize.png)

2. No painel campos, selecione **país** e **devicemake**. Um mapa-mundo com os pontos de dados é apresentado na janela principal após alguns instantes.

3. Expanda o mapa.

## <a name="next-steps"></a>Passos Seguintes
Neste artigo, aprendeu a visualizar dados do HDInsight através do Microsoft Power BI.  Para obter mais informações sobre a visualização de dados, consulte os artigos seguintes:

* [Visualize os dados do Apache Hive com o Microsoft Power BI com ODBC no Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md). 
* [Utilizar Apache Zeppelin para executar consultas do Apache Hive no Azure HDInsight](./../hdinsight-connect-hive-zeppelin.md).
* [Ligar o Excel ao HDInsight com o controlador Microsoft Hive ODBC](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Ligar o Excel para Apache Hadoop com o Power Query](../hadoop/apache-hadoop-connect-excel-power-query.md).
* [Ligar ao Azure HDInsight e executar consultas do Apache Hive com o Data Lake Tools para Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).
* [Utilize a ferramenta do Azure HDInsight para Visual Studio Code](../hdinsight-for-vscode.md).
* [Carregar dados para o HDInsight](./../hdinsight-upload-data.md).
