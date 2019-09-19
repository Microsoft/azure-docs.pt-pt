---
title: Visualizar dados do hive de consulta interativa com Power BI no Azure HDInsight
description: Usar o Microsoft Power BI para visualizar dados de hive de consulta interativa do Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/17/2019
ms.openlocfilehash: 0f273f75c33362bc99efbd7ac6bc46c3778ae88b
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71123026"
---
# <a name="visualize-interactive-query-apache-hive-data-with-microsoft-power-bi-using-direct-query-in-hdinsight"></a>Visualizar dados interativos Apache Hive de consulta com o Microsoft Power BI usando a consulta direta no HDInsight

Este artigo descreve como conectar o Microsoft Power BI a clusters de consulta interativa do Azure HDInsight e Visualizar dados de Apache Hive usando a consulta direta. O exemplo fornecido carrega os dados de uma `hivesampletable` tabela do hive para Power bi. A `hivesampletable` tabela Hive contém alguns dados de uso de telefone celular. Em seguida, você plota os dados de uso em um mapa do mundo:

![HDInsight Power BI o relatório de mapa](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-visualization.png)

Você pode aproveitar o [Driver odbc Apache Hive](../hadoop/apache-hadoop-connect-hive-power-bi.md) para fazer a importação por meio do conector ODBC genérico no Power bi desktop. No entanto, não é recomendável para cargas de trabalho de BI dadas a natureza não interativa do mecanismo de consulta do hive. O [conector de consulta interativa do hdinsight](./apache-hadoop-connect-hive-power-bi-directquery.md) e o conector de Apache Spark do [hdinsight](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect) são melhores opções para seu desempenho.

## <a name="prerequisites"></a>Pré-requisitos
Antes de passar por este artigo, você deve ter os seguintes itens:

* **Cluster HDInsight**. O cluster pode ser um cluster HDInsight com Apache Hive ou um cluster de consulta interativa recentemente liberado. Para criar clusters, consulte [criar cluster](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).
* **[Microsoft Power bi desktop](https://powerbi.microsoft.com/desktop/)** . Você pode baixar uma cópia do [centro de download da Microsoft](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="load-data-from-hdinsight"></a>Carregar dados do HDInsight

A `hivesampletable` tabela Hive vem com todos os clusters HDInsight.

1. Iniciar Power BI Desktop.

2. Na barra de menus, navegue até **página inicial** > **obter dados** > **mais...** .

    ![O HDInsight Power BI obter mais dados](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-open-odbc.png)

3. Na janela **obter dados** , insira **hdinsight** na caixa de pesquisa.  

4. Nos resultados da pesquisa, selecione **consulta interativa do HDInsight**e, em seguida, selecione **conectar**.  Se você não vir a **consulta interativa do HDInsight**, precisará atualizar seu Power bi desktop para a versão mais recente.

5. Selecione **continuar** para fechar a caixa de diálogo **conectando a um serviço** de terceiros.

6. Na janela de **consulta interativa do HDInsight** , insira as seguintes informações e, em seguida, selecione **OK**:

    |Propriedade | Valor |
    |---|---|
    |Servidor |Insira o nome do cluster, por exemplo, *myiqcluster.azurehdinsight.net*.|
    |Base de Dados |Insira o **padrão** para este artigo.|
    |Modo de conectividade de dados |Selecione **DirectQuery** para este artigo.|

    ![Consulta interativa do HDInsight Power BI o DirectQuery Connect](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-connect.png)

7. Insira as credenciais HTTP e, em seguida, selecione **conectar**. O nome de usuário padrão é **admin**.

8. Na janela **navegador** no painel esquerdo, selecione **hivesampletale**.

9. Selecione **carregar** na janela principal.

    ![Consulta interativa do HDInsight Power BI hivesampletable](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-hivesampletable.png)

## <a name="visualize-data-on-a-map"></a>Visualizar dados em um mapa

Continue do último procedimento.

1. No painel visualizações, selecione **mapa**, o ícone de globo. Um mapa genérico aparece na janela principal.

    ![O HDInsight Power BI personaliza o relatório](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-customize.png)

2. No painel campos, selecione **país** e **devicemake**. Um mapa mundial com os pontos de dados aparece na janela principal após alguns instantes.

3. Expanda o mapa.

## <a name="next-steps"></a>Passos seguintes
Neste artigo, você aprendeu a Visualizar dados do HDInsight usando o Microsoft Power BI.  Para obter mais informações sobre a visualização de dados, consulte os seguintes artigos:

* [Visualize Apache Hive dados com o Microsoft Power bi usando ODBC no Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md). 
* [Use o Apache Zeppelin para executar Apache Hive consultas no Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Conecte o Excel ao HDInsight com o driver ODBC do Microsoft Hive](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Conecte o Excel ao Apache Hadoop usando Power Query](../hadoop/apache-hadoop-connect-excel-power-query.md).
* [Conecte-se ao Azure HDInsight e execute Apache Hive consultas usando o data Lake Tools para Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).
* [Use a ferramenta Azure HDInsight para Visual Studio Code](../hdinsight-for-vscode.md).
* [Carregar dados no HDInsight](./../hdinsight-upload-data.md).
