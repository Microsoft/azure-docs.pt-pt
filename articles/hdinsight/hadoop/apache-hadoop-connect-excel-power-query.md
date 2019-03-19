---
title: Ligar o Excel para Apache Hadoop com o Power Query - Azure HDInsight
description: Saiba como tirar partido dos componentes de inteligência de negócios e utilize o Power Query para Excel para acessar dados armazenados no Hadoop no HDInsight.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.openlocfilehash: 2ed866a0406de2421c5c98fc09849d0dfabaa464
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58094505"
---
# <a name="connect-excel-to-apache-hadoop-by-using-power-query"></a>Ligar o Excel para Apache Hadoop com o Power Query
Uma funcionalidade-chave da solução de grandes volumes de dados do Microsoft é a integração de componentes do Microsoft business intelligence (BI) com clusters do Apache Hadoop no HDInsight do Azure. Um exemplo principal é a capacidade de ligar o Excel à conta de armazenamento do Azure que contém os dados associados a seu cluster do Hadoop ao utilizar o Microsoft Power Query para o suplemento do Excel. Este artigo explica como configurar e utilizar o Power Query para consultar dados associados a um cluster de Hadoop gerido com o HDInsight.

### <a name="prerequisites"></a>Pré-requisitos
Antes de começar este artigo, tem de ter os seguintes itens:

* **Um cluster do HDInsight**. Para configurar um, consulte [introdução ao Azure HDInsight] [hdinsight-get-started].
* **Uma estação de trabalho** que está a executar o Windows 7, Windows Server 2008 R2 ou um sistema operativo posterior.
* **Office 2016, Office 2013 Professional Plus, Office 365 ProPlus, Excel 2013 Standalone ou o Office 2010 Professional Plus**.

## <a name="install-power-query"></a>Instalar o Power Query
Power Query pode importar os dados que tenha sido saída ou que tenha sido gerado por uma tarefa de Hadoop em execução num cluster do HDInsight.

No Excel 2016, Power Query foi integrado ao Friso na secção de obter e transformar dados. Para versões mais antigas do Excel, Baixe o Microsoft Power Query para Excel a partir da [Microsoft Download Center] [ powerquery-download] e instalá-lo.

## <a name="import-hdinsight-data-into-excel"></a>Importar dados do HDInsight para o Excel
O suplemento Power Query para Excel torna mais fácil importar dados do seu cluster do HDInsight para o Excel, em que as ferramentas de BI, como o PowerPivot e Power Map podem ser utilizado para inspecionar, analisar e apresentam os dados.

**Para importar dados a partir de um cluster do HDInsight**

1. Abra o Excel.
2. Crie uma nova pasta de trabalho em branco.
3. Execute os passos seguintes com base na versão do Excel:

   - Excel 2016

     - Clique nas **dados** menu, clique em **obter dados** do **obter e transformar dados** Friso, clique em **de Azure**e, em seguida, clique em **Do Azure HDInsight(HDFS)**.

       ![HDI.PowerQuery.SelectHdiSource](./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.selecthdisource.excel2016.png)

   - Excel 2013/2010

     - Clique nas **Power Query** menu, clique em **do Azure**e, em seguida, clique em **do Microsoft Azure HDInsight**.
   
       ![HDI.PowerQuery.SelectHdiSource][image-hdi-powerquery-hdi-source]
       
       **Nota:** Se não vir a **Power Query** menu, aceda à **ficheiro** > **opções** > **suplementos**e selecione  **COM Add-ins** na lista suspensa **gerir** caixa na parte inferior da página. Selecione o **vá...**  botão e certifique-se de que a caixa para o Power Query para o suplemento do Excel foi verificada.
       
       **Nota:** Power Query também permite que importe dados do HDFS clicando **de outras origens**.
4. Para **nome da conta**, introduza o nome da conta de armazenamento de Blobs do Azure associado com o seu cluster e, em seguida, clique em **OK**. Esta conta pode ser o [predefinido a conta de armazenamento](../hdinsight-administer-use-management-portal.md#find-the-default-storage-account) ou uma conta de armazenamento ligada.  O formato é *https://&lt;StorageAccountName >.blob.core.windows.net/*.
5. Para **chave de conta**, introduza a chave para a conta de armazenamento de BLOBs e, em seguida, clique em **guardar**. (Tem de introduzir o tempo de informações apenas a primeira conta que aceder a esse armazenamento.)
6. Na **navegador** painel à esquerda do Editor de consultas, faça duplo clique o nome de contentor de armazenamento de Blobs. Por predefinição, o nome do contentor é o mesmo nome que o nome do cluster.
7. Localizar **HiveSampleData.txt** no **nome** coluna (o caminho da pasta é **../ hive/armazém/hivesampletable/**) e, em seguida, clique em **binário** à esquerda do HiveSampleData.txt. HiveSampleData.txt vem com todos os do cluster. Opcionalmente, pode utilizar o seu próprio ficheiro.
   
    ![HDI.PowerQuery.ImportData][image-hdi-powerquery-importdata]
8. Se desejar, pode renomear os nomes das colunas. Quando estiver pronto, clique em **fechar e carregar**.  Os dados terem sido carregados ao seu livro:
   
    ![HDI.PowerQuery.ImportedTable][image-hdi-powerquery-imported-table]

## <a name="next-steps"></a>Passos Seguintes
Neste artigo, aprendeu a utilizar o Power Query para recuperar dados do HDInsight para o Excel. Da mesma forma, pode recuperar dados de HDInsight no banco de dados do Azure SQL. Também é possível carregar dados para o HDInsight. Para obter mais informações, consulte os artigos seguintes:

* [Visualize os dados do Apache Hive com o Microsoft Power BI no Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Visualizar dados de consulta interativa do Hive com o Power BI no Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Utilizar Apache Zeppelin para executar consultas do Apache Hive no Azure HDInsight](./../hdinsight-connect-hive-zeppelin.md).
* [Ligar o Excel ao HDInsight com o controlador Microsoft Hive ODBC](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Ligar ao Azure HDInsight e executar consultas do Apache Hive com o Data Lake Tools para Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Utilize a ferramenta do Azure HDInsight para Visual Studio Code](../hdinsight-for-vscode.md).
* [Carregar dados para o HDInsight](./../hdinsight-upload-data.md).

[image-hdi-powerquery-hdi-source]: ./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.selecthdisource.png
[image-hdi-powerquery-importdata]: ./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.importdata.png
[image-hdi-powerquery-imported-table]: ./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.importedtable.PNG

[powerquery-download]: https://go.microsoft.com/fwlink/?LinkID=286689
