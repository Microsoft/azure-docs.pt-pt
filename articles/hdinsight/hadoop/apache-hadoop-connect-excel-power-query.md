---
title: Conecte Excel a Apache Hadoop com Consulta de Potência - Azure HDInsight
description: Aprenda a tirar partido dos componentes de inteligência empresarial e use a Power Query para o Excel aceder aos dados armazenados em Hadoop em HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: bcca511bcb61ceab8395ee56b377efa99f095586
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98946706"
---
# <a name="connect-excel-to-apache-hadoop-by-using-power-query"></a>Conecte o Excel a Apache Hadoop utilizando a Consulta de Energia

Uma das principais características da solução de big data da Microsoft é a integração de componentes de inteligência empresarial (BI) da Microsoft com clusters Apache Hadoop no Azure HDInsight. Um exemplo primordial é a capacidade de ligar o Excel à conta de Armazenamento Azure que contém os dados associados ao seu cluster Hadoop utilizando a consulta de energia da Microsoft para o add-in do Excel. Este artigo acompanha-o como configurar e usar a Consulta de Energia para consultar dados associados a um cluster Hadoop gerido com HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* Um aglomerado Apache Hadoop em HDInsight. Ver [Começar com HDInsight no Linux](./apache-hadoop-linux-tutorial-get-started.md).
* Uma estação de trabalho que está a executar o Windows 10, 7, Windows Server 2008 R2 ou um sistema operativo posterior.
* Aplicativos Microsoft 365 para empresas, Office 2016, Office 2013 Professional Plus, Excel 2013 Standalone ou Office 2010 Professional Plus.

## <a name="install-microsoft-power-query"></a>Instalar consulta de energia da Microsoft

A Power Query pode importar dados que foram produzidos ou que foram gerados por um trabalho hadoop em execução num cluster HDInsight.

No Excel 2016, a Power Query foi integrada na fita de dados sob a secção Get & Transform. Para versões excel mais antigas, baixe a Microsoft Power Query para Excel a partir do [Microsoft Download Center](https://go.microsoft.com/fwlink/?LinkID=286689) e instale-a.

## <a name="import-hdinsight-data-into-excel"></a>Importar dados de HDInsight no Excel

O add-in power-in para o Excel facilita a importação de dados do seu cluster HDInsight para o Excel, onde ferramentas bi como PowerPivot e Power Map podem ser usadas para inspecionar, analisar e apresentar os dados.

1. Lançamento Excel.

1. Crie um novo livro em branco.

1. Execute os seguintes passos com base na versão Excel:

   * Excel 2016

     * Selecione **> dados**  >  **obtenha dados**  >  **do Azure Azure**  >  **HDInsight (HDFS)**.

       ![O HDI. PowerQuery.SelectHdiSource.2016](./media/apache-hadoop-connect-excel-power-query/powerquery-selecthdisource-excel2016.png)

   * Excel 2013/2010

     * **Selecione a consulta** de energia a partir de  >  **Azure** da Microsoft  >  **Azure HDInsight**.

       ![O HDI. PowerQuery.SelectHdiSource](./media/apache-hadoop-connect-excel-power-query/powerquery-selecthdisource.png)

       **Nota:** Se não vir o menu **'Consulta de Energia',** aceda aos   >  Add-ins **de Opções**  >  **de Ficheiros** e selecione **Add-ins COM** da caixa De **gestão** de down-down na parte inferior da página. Selecione o botão **Go...** e verifique se a caixa para a consulta de alimentação para o add-in do Excel foi verificada.

       **Nota:** A Consulta de Energia também permite importar dados de HDFS selecionando **De Outras Fontes.**

1. A partir do diálogo **Azure HDInsight (HDFS),** no nome da conta ou caixa de texto **URL,** insira o nome da conta de armazenamento Azure Blob associada ao seu cluster. Em seguida, selecione **OK**. Esta conta pode ser a conta de armazenamento padrão ou uma conta de armazenamento ligada.  O formato é `https://StorageAccountName.blob.core.windows.net/`.

1. Para **a Tecla de conta,** introduza a chave para a conta de armazenamento Blob e, em seguida, selecione **Connect**. (Só precisa de introduzir a informação da conta na primeira vez que aceda a esta loja.)

1. No painel **Navigator** à esquerda do Editor de Consulta, clique duas vezes no nome do recipiente de armazenamento Blob associado ao seu cluster. Por predefinição, o nome do recipiente é o mesmo nome do nome do cluster.

1. Localizar **HiveSampleData.txt** na coluna **Nome** (o caminho da pasta é **.. /colmeia/armazém/hivesmpletable/**), e, em seguida, selecione **Binário** à esquerda de HiveSampleData.txt. HiveSampleData.txt vem com todo o aglomerado. Opcionalmente, pode usar o seu próprio ficheiro.

    ![Dados de importação de consulta de energia HDI Excel](./media/apache-hadoop-connect-excel-power-query/powerquery-importdata.png)

1. Se quiser, pode mudar o nome dos nomes das colunas. Quando estiver pronto, selecione **Fechar & Carregar**.  Os dados foram carregados no seu livro:

    ![Tabela importada de consulta de energia HDI Excel](./media/apache-hadoop-connect-excel-power-query/powerquery-importedtable.png)

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a usar a Power Query para recuperar dados de HDInsight para Excel. Da mesma forma, pode obter dados de HDInsight para Azure SQL Database. Também é possível enviar dados para o HDInsight. Para saber mais, leia os artigos seguintes:

* [Visualizar os dados da Apache Hive com o Microsoft Power BI em Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Visualizar dados de hive de consulta interativa com Power BI em Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Use Apache Zeppelin para executar consultas apache hive em Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Ligue o Excel ao HDInsight com o controlador ODBC da Microsoft Hive](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Conecte-se ao Azure HDInsight e execute consultas apache hive usando ferramentas data lake para estúdio visual.](apache-hadoop-visual-studio-tools-get-started.md)
* [Utilize a ferramenta Azure HDInsight para código de estúdio visual](../hdinsight-for-vscode.md).
* [Faça upload de dados para HDInsight](./../hdinsight-upload-data.md).
