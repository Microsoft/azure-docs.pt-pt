---
title: Conectar o Excel ao Apache Hadoop com o Power Query-Azure HDInsight
description: Saiba como aproveitar os componentes business intelligence e usar Power Query para que o Excel acesse os dados armazenados no Hadoop no HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.openlocfilehash: e64905cdfeac8d507df1c3dd92c245cb910a79b2
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71033576"
---
# <a name="connect-excel-to-apache-hadoop-by-using-power-query"></a>Conectar o Excel ao Apache Hadoop usando Power Query
Um dos principais recursos da solução de Big data da Microsoft é a integração dos componentes do Microsoft business intelligence (BI) com clusters Apache Hadoop no Azure HDInsight. Um exemplo primário é a capacidade de conectar o Excel à conta de armazenamento do Azure que contém os dados associados ao cluster Hadoop usando o suplemento Microsoft Power Query para Excel. Este artigo orienta você sobre como configurar e usar Power Query para consultar dados associados a um cluster Hadoop gerenciado com o HDInsight.

### <a name="prerequisites"></a>Pré-requisitos
Antes de começar este artigo, você deve ter os seguintes itens:

* **Cluster HDInsight an**. Para configurar um, consulte Introdução [ao Azure HDInsight](./apache-hadoop-linux-tutorial-get-started.md).
* **Uma estação de trabalho** que esteja executando o Windows 7, o windows Server 2008 R2 ou um sistema operacional posterior.
* **Office 2016, office 2013 Professional Plus, office 365 ProPlus, Excel 2013 autônomo ou Office 2010 Professional Plus**.

## <a name="install-power-query"></a>Instalar Power Query
Power Query pode importar dados que tenham sido gerados ou que tenha sido gerado por um trabalho do Hadoop em execução em um cluster HDInsight.

No Excel 2016, o Power Query foi integrado à faixa de de dados na seção obter & transformação. Para versões mais antigas do Excel, baixe Microsoft Power Query para Excel no [centro de download da Microsoft](https://go.microsoft.com/fwlink/?LinkID=286689) e instale-o.

## <a name="import-hdinsight-data-into-excel"></a>Importar dados do HDInsight para o Excel
O suplemento Power Query para Excel facilita a importação de dados do cluster HDInsight para o Excel, em que as ferramentas de BI, como o PowerPivot e Power Map, podem ser usadas para inspecionar, analisar e apresentar os dados.

**Para importar dados de um cluster HDInsight**

1. Abra o Excel.
2. Crie uma nova pasta de trabalho em branco.
3. Execute as seguintes etapas com base na versão do Excel:

   - Excel 2016

     - Clique no **menu dados** , clique em **obter dados** na faixa de **& obter dados de transformação** , clique em **do Azure**e, em seguida, clique em **do Azure HDInsight (HDFS)** .

       ![HDI. PowerQuery. SelectHdiSource. 2016](./media/apache-hadoop-connect-excel-power-query/powerquery-selecthdisource-excel2016.png)

   - Excel 2013/2010

     - Clique no menu **Power Query** , clique em **do Azure**e, em seguida, clique em **de Microsoft Azure HDInsight**.
   
       ![HDI.PowerQuery.SelectHdiSource](./media/apache-hadoop-connect-excel-power-query/powerquery-selecthdisource.png)
       
       **Nota:** Se você não vir o menu de **Power Query** , vá para **arquivo** > **Opções** > **suplementos**e selecione suplementos de **com** na caixa suspensa **gerenciar** na parte inferior da página. Selecione o botão **go...** e verifique se a caixa do Power Query para o suplemento do Excel foi verificada.
       
       **Nota:** Power Query também permite importar dados do HDFS clicando em **de outras fontes**.
4. Para **nome da conta**, insira o nome da conta de armazenamento de BLOBs do Azure associada ao cluster e clique em **OK**. Essa conta pode ser a conta de armazenamento padrão ou uma conta de armazenamento vinculada.  O formato é *https://&lt;StorageAccountName >. blob. Core. Windows. net/* .
5. Para **chave de conta**, insira a chave para a conta de armazenamento de BLOBs e clique em **salvar**. (Você precisa inserir as informações da conta somente na primeira vez que acessar esse armazenamento.)
6. No painel **navegador** à esquerda do editor de consultas, clique duas vezes no nome do contêiner de armazenamento de BLOBs. Por padrão, o nome do contêiner é o mesmo nome que o nome do cluster.
7. Localizar **HiveSampleData.txt** no **nome** coluna (o caminho da pasta é **../ hive/armazém/hivesampletable/** ) e, em seguida, clique em **binário** à esquerda do HiveSampleData.txt. HiveSampleData. txt vem com todo o cluster. Opcionalmente, você pode usar seu próprio arquivo.

    ![Importar dados do HDI Excel Power Query](./media/apache-hadoop-connect-excel-power-query/powerquery-importdata.png)

8. Se desejar, você poderá renomear os nomes das colunas. Quando estiver pronto, clique em **fechar & carregar**.  Os dados foram carregados em sua pasta de trabalho:

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
