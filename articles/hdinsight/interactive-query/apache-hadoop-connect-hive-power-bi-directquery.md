---
title: Consulte dados de Hive de Consulta Interativa com Power BI em Azure HDInsight
description: Utilize o Microsoft Power BI para visualizar dados de Hive de Consulta Interativa a partir do Azure HDInsight
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 06/17/2019
ms.openlocfilehash: 05199968339329632c2e68e9604e3f5308e8b12b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104871661"
---
# <a name="visualize-interactive-query-apache-hive-data-with-microsoft-power-bi-using-direct-query-in-hdinsight"></a>Visualizar dados de Hive Apache De Consulta Interativa com o Microsoft Power BI utilizando consulta direta em HDInsight

Este artigo descreve como ligar o Microsoft Power BI aos clusters de consulta interativa Azure HDInsight e visualizar dados da Criociação Apache usando consulta direta. O exemplo fornecido carrega os dados de uma `hivesampletable` tabela de Colmeia para o Power BI. A `hivesampletable` tabela Hive contém alguns dados de utilização do telemóvel. Depois, traça-se os dados de utilização num mapa mundial:

:::image type="content" source="./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-visualization.png" alt-text="HDInsight Power BI o relatório do mapa" border="true":::

Você pode aproveitar o [controlador Apache Hive ODBC](../hadoop/apache-hadoop-connect-hive-power-bi.md) para fazer importações através do conector ODBC genérico no Power BI Desktop. No entanto, não é recomendado para cargas de trabalho bi dada a natureza não interativa do motor de consulta hive. [O conector de consulta interativa HDInsight](./apache-hadoop-connect-hive-power-bi-directquery.md) e [o conector HDInsight Apache Spark](/power-bi/spark-on-hdinsight-with-direct-connect) são melhores escolhas para o seu desempenho.

## <a name="prerequisites"></a>Pré-requisitos
Antes de ler este artigo, deve ter os seguintes itens:

* **Cluster HDInsight**. O cluster pode ser um cluster HDInsight com a Hive Apache ou um cluster de consultainterna interativa recentemente lançado. Para criar clusters, consulte [Criar cluster.](../hadoop/apache-hadoop-linux-tutorial-get-started.md)
* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)**. Pode descarregar uma cópia do [Microsoft Download Center.](https://www.microsoft.com/download/details.aspx?id=45331)

## <a name="load-data-from-hdinsight"></a>Carregar dados a partir de HDInsight

A `hivesampletable` mesa Hive vem com todos os clusters HDInsight.

1. Inicie o Power BI Desktop.

2. Da barra de menus, navegue para **Home**  >  **Get Data**  >  **Mais...**.

    :::image type="content" source="./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-open-odbc.png" alt-text="HDInsight Power BI Obter mais dados" border="true":::

3. A partir da janela **Obter Dados,** insira **a visão hdinsight** na caixa de pesquisa.  

4. A partir dos resultados da pesquisa, selecione **a Consulta Interativa HDInsight** e, em seguida, selecione **Connect**.  Se não vir **a Consulta Interativa HDInsight,** tem de atualizar o seu Power BI Desktop para a versão mais recente.

5. **Selecione Continue** a fechar **o "Connecting" a um diálogo de serviço de terceiros.**

6. Na janela **de consulta interativa HDInsight,** introduza as seguintes informações e, em seguida, selecione **OK**:

    |Propriedade | Valor |
    |---|---|
    |Servidor |Introduza o nome do cluster, por *exemplo, myiqcluster.azurehdinsight.net*. .|
    |Base de Dados |Introduza **o predefinido** para este artigo.|
    |Modo de Conectividade de Dados |Selecione **DirectQuery** para este artigo.|

    :::image type="content" source="./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-connect.png" alt-text="Consulta interativa HDInsight Power BI DirectQuery connect" border="true":::

7. Introduza as credenciais HTTP e, em seguida, **selecione Connect**. O nome de utilizador predefinido é **administrador.**

8. A partir da janela **do Navegador** no painel esquerdo, selecione **hivesampletale**.

9. Selecione **Carregar** a partir da janela principal.

    :::image type="content" source="./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-hivesampletable.png" alt-text="Consulta interativa HDInsight Power BI hivesampletable" border="true":::

## <a name="visualize-data-on-a-map"></a>Visualizar dados num mapa

Continue a partir do último procedimento.

1. A partir do painel de Visualizações, selecione **Map,** o ícone do globo. Um mapa genérico aparece então na janela principal.

    :::image type="content" source="./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-customize.png" alt-text="HDInsight Power BI personaliza relatório" border="true":::

2. A partir do painel Fields, selecione **country** e **devicemake**. Um mapa mundial com os pontos de dados aparece na janela principal após alguns momentos.

3. Expanda o mapa.

## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu a visualizar dados a partir de HDInsight usando o Microsoft Power BI.  Para obter mais informações sobre a visualização de dados, consulte os seguintes artigos:

* [Visualize os dados da Apache Hive com o Microsoft Power BI utilizando o ODBC em Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md). 
* [Use Apache Zeppelin para executar consultas apache hive em Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Ligue o Excel ao HDInsight com o controlador ODBC da Microsoft Hive](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Ligue o Excel ao Apache Hadoop utilizando a Consulta de Potência](../hadoop/apache-hadoop-connect-excel-power-query.md).
* [Conecte-se ao Azure HDInsight e execute consultas apache hive usando ferramentas data lake para estúdio visual.](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
* [Utilize a ferramenta Azure HDInsight para código de estúdio visual](../hdinsight-for-vscode.md).
* [Enviar dados para HDInsight](./../hdinsight-upload-data.md).