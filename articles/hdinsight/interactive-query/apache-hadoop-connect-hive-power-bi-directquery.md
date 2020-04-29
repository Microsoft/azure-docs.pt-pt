---
title: Ver dados interativos da Hive com Power BI em Azure HDInsight
description: Use o Microsoft Power BI para visualizar dados da Hive de Consulta Interativa do Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/17/2019
ms.openlocfilehash: adcd4d9e81eecad9540a4ef1be5e675f940ffb8d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79367997"
---
# <a name="visualize-interactive-query-apache-hive-data-with-microsoft-power-bi-using-direct-query-in-hdinsight"></a>Visualizar dados interativos de consulta Apache Hive com microsoft Power BI usando consulta direta no HDInsight

Este artigo descreve como ligar o Microsoft Power BI aos clusters de consulta interativa Azure HDInsight e visualizar os dados da Apache Hive usando consulta direta. O exemplo forneceu os `hivesampletable` dados de uma tabela da Colmeia para o Power BI. A `hivesampletable` tabela Hive contém alguns dados de utilização do telemóvel. Em seguida, planeia os dados de uso num mapa mundial:

![HDInsight Power BI o relatório do mapa](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-visualization.png)

Pode aproveitar o [controlador Apache Hive ODBC](../hadoop/apache-hadoop-connect-hive-power-bi.md) para importar através do conector genérico ODBC no Power BI Desktop. No entanto, não é recomendado para cargas de trabalho bi dada a natureza não interativa do motor de consulta da Colmeia. [O conector de consulta interativa HDInsight](./apache-hadoop-connect-hive-power-bi-directquery.md) e o [conector HDInsight Apache Spark](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect) são melhores escolhas para o seu desempenho.

## <a name="prerequisites"></a>Pré-requisitos
Antes de ler este artigo, deve ter os seguintes itens:

* **Cluster HDInsight**. O cluster pode ser um cluster HDInsight com Apache Hive ou um cluster de consulta interativa recém-lançado. Para criar clusters, consulte [Criar cluster](../hadoop/apache-hadoop-linux-tutorial-get-started.md).
* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)**. Pode descarregar uma cópia do [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="load-data-from-hdinsight"></a>Dados de carga do HDInsight

A `hivesampletable` tabela Hive vem com todos os clusters HDInsight.

1. Inicie o Power BI Desktop.

2. Da barra de menus, navegue até **Home** > **Get Data** > **More...**.

    ![HDInsight Power BI obter dados mais](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-open-odbc.png)

3. A partir da janela **Obter Dados,** introduza o **hdinsight** na caixa de pesquisa.  

4. A partir dos resultados da pesquisa, selecione **HDInsight Interactive Query**, e, em seguida, selecione **Connect**.  Se não vir a **Consulta Interativa HDInsight,** precisa de atualizar o seu Power BI Desktop para a versão mais recente.

5. Selecione **Continuar** a fechar a Ligação a um diálogo **de serviço de terceiros.**

6. Na janela **De consulta interativa HDInsight,** introduza as seguintes informações e, em seguida, selecione **OK:**

    |Propriedade | Valor |
    |---|---|
    |Server |Introduza o nome do cluster, por *exemplo, myiqcluster.azurehdinsight.net*.|
    |Base de Dados |Introduza **o predefinido** para este artigo.|
    |Modo de Conectividade de Dados |Selecione **DirectQuery** para este artigo.|

    ![Ligação interativa HDInsight Power BI DirectQuery](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-connect.png)

7. Introduza as credenciais HTTP e, em seguida, selecione **Connect**. O nome de utilizador predefinido é **administrador**.

8. A partir da janela **Navigator** no painel esquerdo, selecione **hivesampletale**.

9. Selecione **Carregar** a partir da janela principal.

    ![HDInsight consulta interativa Power BI hivesampletable](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-hivesampletable.png)

## <a name="visualize-data-on-a-map"></a>Visualizar dados num mapa

Continue a partir do último procedimento.

1. A partir do painel de Visualizações, selecione **Map,** o ícone do globo. Um mapa genérico aparece então na janela principal.

    ![HDInsight Power BI personaliza relatório](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-customize.png)

2. Do painel Fields, selecione **país** e **dispositivos.** Um mapa mundial com os pontos de dados aparece na janela principal após alguns momentos.

3. Expanda o mapa.

## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu a visualizar dados do HDInsight usando o Microsoft Power BI.  Para obter mais informações sobre visualização de dados, consulte os seguintes artigos:

* [Visualizar os dados da Hive Apache com o Microsoft Power BI utilizando o ODBC no Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md). 
* [Use Apache Zeppelin para executar consultas apache hive em Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Ligue o Excel ao HDInsight com o condutor microsoft Hive ODBC](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Ligue excel a Apache Hadoop utilizando](../hadoop/apache-hadoop-connect-excel-power-query.md)power query .
* [Ligue-se ao Azure HDInsight e execute consultas apache hive usando ferramentas de data lake para estúdio visual](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).
* [Utilize a ferramenta Azure HDInsight para o Código](../hdinsight-for-vscode.md)do Estúdio Visual .
* [Enviar dados para HDInsight](./../hdinsight-upload-data.md).
