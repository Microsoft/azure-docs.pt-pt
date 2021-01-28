---
title: Consulte dados de Hive de Consulta Interativa com Power BI em Azure HDInsight
description: Utilize o Microsoft Power BI para visualizar dados de Hive de Consulta Interativa a partir do Azure HDInsight
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 06/17/2019
ms.openlocfilehash: 7f249bb0e81bf3a371b8743a304ef49baffaed7a
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98941387"
---
# <a name="visualize-interactive-query-apache-hive-data-with-microsoft-power-bi-using-direct-query-in-hdinsight"></a>Visualizar dados de Hive Apache De Consulta Interativa com o Microsoft Power BI utilizando consulta direta em HDInsight

Este artigo descreve como ligar o Microsoft Power BI aos clusters de consulta interativa Azure HDInsight e visualizar dados da Criociação Apache usando consulta direta. O exemplo fornecido carrega os dados de uma `hivesampletable` tabela de Colmeia para o Power BI. A `hivesampletable` tabela Hive contém alguns dados de utilização do telemóvel. Depois, traça-se os dados de utilização num mapa mundial:

![HDInsight Power BI o relatório do mapa](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-visualization.png)

Você pode aproveitar o [controlador Apache Hive ODBC](../hadoop/apache-hadoop-connect-hive-power-bi.md) para fazer importações através do conector ODBC genérico no Power BI Desktop. No entanto, não é recomendado para cargas de trabalho bi dada a natureza não interativa do motor de consulta hive. [O conector de consulta interativa HDInsight](./apache-hadoop-connect-hive-power-bi-directquery.md) e [o conector HDInsight Apache Spark](/power-bi/spark-on-hdinsight-with-direct-connect) são melhores escolhas para o seu desempenho.

## <a name="prerequisites"></a>Pré-requisitos
Antes de ler este artigo, deve ter os seguintes itens:

* **Cluster HDInsight**. O cluster pode ser um cluster HDInsight com a Hive Apache ou um cluster de consultainterna interativa recentemente lançado. Para criar clusters, consulte [Criar cluster.](../hadoop/apache-hadoop-linux-tutorial-get-started.md)
* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)**. Pode descarregar uma cópia do [Microsoft Download Center.](https://www.microsoft.com/download/details.aspx?id=45331)

## <a name="load-data-from-hdinsight"></a>Carregar dados a partir de HDInsight

A `hivesampletable` mesa Hive vem com todos os clusters HDInsight.

1. Inicie o Power BI Desktop.

2. Da barra de menus, navegue para **Home**  >  **Get Data**  >  **Mais...**.

    ![HDInsight Power BI Obter mais dados](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-open-odbc.png)

3. A partir da janela **Obter Dados,** insira **a visão hdinsight** na caixa de pesquisa.  

4. A partir dos resultados da pesquisa, selecione **a Consulta Interativa HDInsight** e, em seguida, selecione **Connect**.  Se não vir **a Consulta Interativa HDInsight,** tem de atualizar o seu Power BI Desktop para a versão mais recente.

5. **Selecione Continue** a fechar **o "Connecting" a um diálogo de serviço de terceiros.**

6. Na janela **de consulta interativa HDInsight,** introduza as seguintes informações e, em seguida, selecione **OK**:

    |Propriedade | Valor |
    |---|---|
    |Servidor |Introduza o nome do cluster, por *exemplo, myiqcluster.azurehdinsight.net*. .|
    |Base de Dados |Introduza **o predefinido** para este artigo.|
    |Modo de Conectividade de Dados |Selecione **DirectQuery** para este artigo.|

    ![Consulta interativa HDInsight Power BI DirectQuery connect](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-connect.png)

7. Introduza as credenciais HTTP e, em seguida, **selecione Connect**. O nome de utilizador predefinido é **administrador.**

8. A partir da janela **do Navegador** no painel esquerdo, selecione **hivesampletale**.

9. Selecione **Carregar** a partir da janela principal.

    ![Consulta interativa HDInsight Power BI hivesampletable](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-hivesampletable.png)

## <a name="visualize-data-on-a-map"></a>Visualizar dados num mapa

Continue a partir do último procedimento.

1. A partir do painel de Visualizações, selecione **Map,** o ícone do globo. Um mapa genérico aparece então na janela principal.

    ![HDInsight Power BI personaliza relatório](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-customize.png)

2. A partir do painel Fields, selecione **country** e **devicemake**. Um mapa mundial com os pontos de dados aparece na janela principal após alguns momentos.

3. Expanda o mapa.

## <a name="next-steps"></a>Próximos passos
Neste artigo, aprendeu a visualizar dados a partir de HDInsight usando o Microsoft Power BI.  Para obter mais informações sobre a visualização de dados, consulte os seguintes artigos:

* [Visualize os dados da Apache Hive com o Microsoft Power BI utilizando o ODBC em Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md). 
* [Use Apache Zeppelin para executar consultas apache hive em Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Ligue o Excel ao HDInsight com o controlador ODBC da Microsoft Hive](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Ligue o Excel ao Apache Hadoop utilizando a Consulta de Potência](../hadoop/apache-hadoop-connect-excel-power-query.md).
* [Conecte-se ao Azure HDInsight e execute consultas apache hive usando ferramentas data lake para estúdio visual.](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
* [Utilize a ferramenta Azure HDInsight para código de estúdio visual](../hdinsight-for-vscode.md).
* [Enviar dados para HDInsight](./../hdinsight-upload-data.md).