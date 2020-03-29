---
title: Ligue Excel a Apache Hadoop com power query - Azure HDInsight
description: Saiba como aproveitar os componentes da inteligência empresarial e use power query para Excel aceda aos dados armazenados em Hadoop no HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: e643c7fe7b18eed30843e7cab3977036435d2112
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435793"
---
# <a name="connect-excel-to-apache-hadoop-by-using-power-query"></a>Ligue Excel a Apache Hadoop usando power query

Uma das principais características da solução de big-data da Microsoft é a integração de componentes de inteligência empresarial da Microsoft (BI) com clusters Apache Hadoop no Azure HDInsight. Um exemplo primário é a capacidade de ligar o Excel à conta de Armazenamento Azure que contém os dados associados ao seu cluster Hadoop utilizando a Consulta de Energia do Microsoft para o add-in excel. Este artigo acompanha-o através de como configurar e usar power query para consultar dados associados a um cluster Hadoop gerido com hDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* Um aglomerado Apache Hadoop no HDInsight. Ver [Começar com HDInsight no Linux](./apache-hadoop-linux-tutorial-get-started.md).
* Uma estação de trabalho que está a executar o Windows 10, 7, Windows Server 2008 R2 ou um sistema operativo posterior.
* Office 2016, Office 2013 Professional Plus, Office 365 ProPlus, Excel 2013 Autónomo, ou Office 2010 Professional Plus.

## <a name="install-microsoft-power-query"></a>Instalar consulta de energia do Microsoft

A Power Query pode importar dados que foram produzidos ou que foram gerados por um trabalho hadoop em execução num cluster HDInsight.

No Excel 2016, a Power Query foi integrada na fita Data sob a secção Get & Transform. Para versões mais antigas do Excel, baixe o Microsoft Power Query para excel a partir do [Microsoft Download Center](https://go.microsoft.com/fwlink/?LinkID=286689) e instale-o.

## <a name="import-hdinsight-data-into-excel"></a>Importar dados hDInsight para Excel

O add-in Power Query para o Excel facilita a importação de dados do seu cluster HDInsight para o Excel, onde ferramentas bi como PowerPivot e Power Map podem ser usadas para inspecionar, analisar e apresentar os dados.

1. Lançar Excel.

1. Crie um novo livro em branco.

1. Execute os seguintes passos com base na versão Excel:

   * Excel 2016

     * Selecione > **dados** > **obtenha dados** > **do Azure** > **from Azure HDInsight (HDFS)**.

       ![O HDI. PowerQuery.SelectHdiSource.2016](./media/apache-hadoop-connect-excel-power-query/powerquery-selecthdisource-excel2016.png)

   * Excel 2013/2010

     * Selecione **Power Query** > **From Azure** > **From Microsoft Azure HDInsight**.

       ![O HDI. PowerQuery.SelectHdiSource](./media/apache-hadoop-connect-excel-power-query/powerquery-selecthdisource.png)

       **Nota:** Se não vir o menu **Power Query,** vá a**Add-ins**de**Opções** > de **Ficheiros** > e selecione **Add-ins COM** a partir da caixa drop-down **Gerir** na parte inferior da página. Selecione o botão **Go...** e verifique se a caixa para o suplemento de Excel foi verificada.

       **Nota:** A Power Query também permite importar dados do HDFS selecionando **de Outras Fontes**.

1. A partir do diálogo **Azure HDInsight (HDFS),** no nome da conta ou caixa de texto **URL,** introduza o nome da conta de armazenamento Azure Blob associada ao seu cluster. Em seguida, selecione **OK**. Esta conta pode ser a conta de armazenamento por defeito ou uma conta de armazenamento ligada.  O formato é `https://StorageAccountName.blob.core.windows.net/`.

1. Para **a Chave de Conta,** introduza a chave para a conta de armazenamento Blob e, em seguida, selecione **Connect**. (Só precisa de introduzir as informações da conta na primeira vez que acede a esta loja.)

1. No painel do **Navigator** à esquerda do Editor de Consulta, clique duas vezes no nome do recipiente de armazenamento Blob associado ao seu cluster. Por predefinição, o nome do recipiente é o mesmo nome que o nome do cluster.

1. Localize **HiveSampleData.txt** na coluna **Nome** (o caminho da pasta é **.. /hive/armazém/hivesampletable/**), e, em seguida, **selecione Binary** à esquerda de HiveSampleData.txt. HiveSampleData.txt vem com todo o cluster. Opcionalmente, pode usar o seu próprio ficheiro.

    ![Dados de importação de energia Do HDI Excel](./media/apache-hadoop-connect-excel-power-query/powerquery-importdata.png)

1. Se quiser, pode mudar o nome das colunas. Quando estiver pronto, selecione **Close & Load**.  Os dados foram carregados para o seu livro:

    ![Tabela importada de consulta de energia HDI Excel](./media/apache-hadoop-connect-excel-power-query/powerquery-importedtable.png)

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a usar a Power Query para recuperar dados do HDInsight para o Excel. Da mesma forma, pode recuperar dados do HDInsight para a Base de Dados Azure SQL. Também é possível fazer o upload de dados para o HDInsight. Para saber mais, consulte os seguintes artigos:

* [Visualizar os dados da Hive Apache com o Microsoft Power BI no Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Visualizar dados de Colmeia de Consulta Interativa com Power BI em Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Use Apache Zeppelin para executar consultas apache hive em Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Ligue o Excel ao HDInsight com o condutor microsoft Hive ODBC](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Ligue-se ao Azure HDInsight e execute consultas apache hive usando ferramentas de data lake para estúdio visual](apache-hadoop-visual-studio-tools-get-started.md).
* [Utilize a ferramenta Azure HDInsight para o Código](../hdinsight-for-vscode.md)do Estúdio Visual .
* [Faça upload de dados para HDInsight](./../hdinsight-upload-data.md).
