---
title: Conectar o Excel ao Apache Hadoop com o Power Query-Azure HDInsight
description: Saiba como aproveitar os componentes business intelligence e usar Power Query para que o Excel acesse os dados armazenados no Hadoop no HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: e643c7fe7b18eed30843e7cab3977036435d2112
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435793"
---
# <a name="connect-excel-to-apache-hadoop-by-using-power-query"></a>Conectar o Excel ao Apache Hadoop usando Power Query

Um dos principais recursos da solução de Big data da Microsoft é a integração dos componentes do Microsoft business intelligence (BI) com clusters Apache Hadoop no Azure HDInsight. Um exemplo primário é a capacidade de conectar o Excel à conta de armazenamento do Azure que contém os dados associados ao cluster Hadoop usando o suplemento Microsoft Power Query para Excel. Este artigo orienta você sobre como configurar e usar Power Query para consultar dados associados a um cluster Hadoop gerenciado com o HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster Apache Hadoop no HDInsight. Consulte [introdução ao HDInsight no Linux](./apache-hadoop-linux-tutorial-get-started.md).
* Uma estação de trabalho que esteja executando o Windows 10, 7, Windows Server 2008 R2 ou um sistema operacional posterior.
* Office 2016, Office 2013 Professional Plus, Office 365 ProPlus, Excel 2013 autônomo ou Office 2010 Professional Plus.

## <a name="install-microsoft-power-query"></a>Instalar o Microsoft Power Query

Power Query pode importar dados que tenham sido gerados ou que tenha sido gerado por um trabalho do Hadoop em execução em um cluster HDInsight.

No Excel 2016, o Power Query foi integrado à faixa de de dados na seção obter & transformação. Para versões mais antigas do Excel, baixe Microsoft Power Query para Excel no [centro de download da Microsoft](https://go.microsoft.com/fwlink/?LinkID=286689) e instale-o.

## <a name="import-hdinsight-data-into-excel"></a>Importar dados do HDInsight para o Excel

O suplemento Power Query para Excel facilita a importação de dados do cluster HDInsight para o Excel, em que as ferramentas de BI, como o PowerPivot e Power Map, podem ser usadas para inspecionar, analisar e apresentar os dados.

1. Inicie o Excel.

1. Crie uma nova pasta de trabalho em branco.

1. Execute as seguintes etapas com base na versão do Excel:

   * Excel 2016

     * Selecione > **dados** > **obter dados** > **de** > do Azure **do Azure HDInsight (HDFS)** .

       ![HDI. PowerQuery. SelectHdiSource. 2016](./media/apache-hadoop-connect-excel-power-query/powerquery-selecthdisource-excel2016.png)

   * Excel 2013/2010

     * Selecione **Power Query** > **do** > do Azure **do Microsoft Azure HDInsight**.

       ![HDI.PowerQuery.SelectHdiSource](./media/apache-hadoop-connect-excel-power-query/powerquery-selecthdisource.png)

       **Observação:** Se você não vir o menu de **Power Query** , vá **para arquivo** > **Opções** > **suplementos**e selecione suplementos de **com** na caixa suspensa **gerenciar** na parte inferior da página. Selecione o botão **go...** e verifique se a caixa do Power Query para o suplemento do Excel foi verificada.

       **Observação:** Power Query também permite importar dados do HDFS selecionando **de outras fontes**.

1. No diálogo **Azure HDInsight (HDFS)** , na caixa de texto **nome da conta ou URL** , digite o nome da conta de armazenamento de BLOBs do Azure associada ao cluster. Em seguida, selecione **OK**. Essa conta pode ser a conta de armazenamento padrão ou uma conta de armazenamento vinculada.  O formato é `https://StorageAccountName.blob.core.windows.net/`.

1. Para **chave de conta**, insira a chave para a conta de armazenamento de BLOBs e, em seguida, selecione **conectar**. (Você precisa inserir as informações da conta somente na primeira vez que acessar esse armazenamento.)

1. No painel **navegador** à esquerda do editor de consultas, clique duas vezes no nome do contêiner de armazenamento de blob associado ao cluster. Por padrão, o nome do contêiner é o mesmo nome que o nome do cluster.

1. Localize **HiveSampleData. txt** na coluna **nome** (o caminho da pasta é **.. /Hive/warehouse/hivesampletable/** ) e, em seguida, selecione **Binary** à esquerda de HiveSampleData. txt. HiveSampleData. txt vem com todo o cluster. Opcionalmente, você pode usar seu próprio arquivo.

    ![Importar dados do HDI Excel Power Query](./media/apache-hadoop-connect-excel-power-query/powerquery-importdata.png)

1. Se desejar, você poderá renomear os nomes das colunas. Quando estiver pronto, selecione **fechar & carregar**.  Os dados foram carregados em sua pasta de trabalho:

    ![Tabela importada do HDI Excel Power Query](./media/apache-hadoop-connect-excel-power-query/powerquery-importedtable.png)

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu a usar Power Query para recuperar dados do HDInsight para o Excel. Da mesma forma, você pode recuperar dados do HDInsight no banco de dados SQL do Azure. Também é possível carregar dados no HDInsight. Para saber mais, confira os seguintes artigos:

* [Visualize Apache Hive dados com o Microsoft Power bi no Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Visualize dados de hive de consulta interativa com Power bi no Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Use o Apache Zeppelin para executar Apache Hive consultas no Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Conecte o Excel ao HDInsight com o driver ODBC do Microsoft Hive](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Conecte-se ao Azure HDInsight e execute Apache Hive consultas usando o data Lake Tools para Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Use a ferramenta Azure HDInsight para Visual Studio Code](../hdinsight-for-vscode.md).
* [Carregar dados no HDInsight](./../hdinsight-upload-data.md).
