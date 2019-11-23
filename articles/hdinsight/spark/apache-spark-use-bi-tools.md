---
title: 'Tutorial: Analyze Azure HDInsight Apache Spark data with Power BI'
description: Tutorial - Use Microsoft Power BI to visualize Apache Spark data stored HDInsight clusters
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 10/03/2019
ms.openlocfilehash: 3fd1405d8421d71f52d9cd215dd055ce1595abd0
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327282"
---
# <a name="tutorial-analyze-apache-spark-data-using-power-bi-in-hdinsight"></a>Tutorial: Analyze Apache Spark data using Power BI in HDInsight

In this tutorial, you learn how to use [Microsoft Power BI](https://powerbi.microsoft.com/) to visualize data in an Apache Spark cluster in [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/).

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Utilizar o Power BI para ver dados do Spark

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Complete the article [Tutorial: Load data and run queries on an Apache Spark cluster in Azure HDInsight](./apache-spark-load-data-run-query.md).

* [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).

* Optional: [Power BI trial subscription](https://app.powerbi.com/signupredirect?pbi_source=web).

## <a name="verify-the-data"></a>Verificar os dados

The [Jupyter Notebook](https://jupyter.org/) that you created in the [previous tutorial](apache-spark-load-data-run-query.md) includes code to create an `hvac` table. This table is based on the CSV file available on all HDInsight Spark clusters at `\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv`. Utilize o seguinte procedimento para verificar os dados.

1. No bloco de notas do Jupyter, cole o seguinte código e prima **SHIFT + ENTER**. O código verifica a existência das tabelas.

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    O resultado tem o seguinte aspeto:

    ![Mostrar tabelas no Spark](./media/apache-spark-use-bi-tools/apache-spark-show-tables.png)

    Se tiver fechado o bloco de notas antes de iniciar este tutorial, `hvactemptable` é limpa, pelo que não é incluída na saída.  Só as tabelas do Hive que estejam armazenadas na metastore (indicadas com **False** (Falso), na coluna **isTemporary**) podem ser acedidas a partir das ferramentas de BI. Neste tutorial, vai ligar à tabela **hvac** que criou.

2. Cole o seguinte código numa célula vazia e prima **SHIFT + ENTER**. O código verifica os dados na tabela.

    ```PySpark
    %%sql
    SELECT * FROM hvac LIMIT 10
    ```

    O resultado tem o seguinte aspeto:

    ![Mostrar linhas da tabela hvac no Spark](./media/apache-spark-use-bi-tools/apache-spark-select-limit.png)

3. No menu **File (Ficheiro)** do bloco de notas, selecione **Close and Halt (Fechar e Parar)** . Encerre o bloco de notas para libertar os recursos.

## <a name="visualize-the-data"></a>Ver os dados

Nesta secção, vai utilizar o Power BI para criar visualizações, relatórios e dashboards a partir dos dados do cluster do Spark.

### <a name="create-a-report-in-power-bi-desktop"></a>Criar um relatório no Power BI Desktop

Os primeiros passos para começar a trabalhar com o Spark são ligar ao cluster no Power BI Desktop, carregar dados a partir do cluster e criar uma visualização básica com base nesses dados.

> [!NOTE]  
> O conector demonstrado neste artigo está atualmente em pré-visualização. Se tiver comentários, envie-os através dos sites [Comunidade do Power BI](https://community.powerbi.com/) ou [Power BI Ideas](https://ideas.powerbi.com/forums/265200-power-bi-ideas).

1. Abra o Power BI Desktop. Close the start-up splash screen if it opens.

2. From the **Home** tab, navigate to **Get Data** > **More..** .

    ![Get data into Power BI Desktop from HDInsight Apache Spark](./media/apache-spark-use-bi-tools/hdinsight-spark-power-bi-desktop-get-data.png "Get data into Power BI from Apache Spark BI")

3. Enter `Spark` in the search box, select **Azure HDInsight Spark**, and then select **Connect**.

    ![Get data into Power BI from Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "Get data into Power BI from Apache Spark BI")

4. Enter your cluster URL (in the form `mysparkcluster.azurehdinsight.net`) in the **Server** text box.

5. Under **Data connectivity mode**, select **DirectQuery**. Em seguida, selecione **OK**.

    Pode utilizar qualquer um dos modos de conectividade de dados com o Spark. Se utilizar o DirectQuery, as alterações são refletidas nos relatórios sem atualizar o conjunto de dados completo. Se importar os dados, tem de atualizar o conjunto de dados para ver as alterações. Para obter mais informações sobre como e quando utilizar o DirectQuery, veja [Utilizar o DirectQuery no Power BI](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/).

6. Enter the HDInsight login account information, then select **Connect**. O nome predefinido da conta é *admin*.

7. Select the `hvac` table, wait to see a preview of the data, and then select **Load**.

    ![Spark cluster user name and password](./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png "Spark cluster user name and password")

    O Power BI Desktop tem as informações de que precisa para se ligar ao cluster do Spark e carregar dados da tabela `hvac`. A tabela e as colunas são apresentadas no painel **Fields** (Campos).

8. Visualize a variância entre a temperatura de destino e a temperatura real de cada edifício:

    1. No painel **VISUALIZATIONS** (VISUALIZAÇÕES), selecione **Area Chart** (Gráfico de Área).

    2. Arraste o campo **BuildingID** para **Axis** (Eixo) e os campos **ActualTemp** e **TargetTemp** para **Value** (Valor).

        ![add value columns](./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "add value columns")

        O diagrama tem o seguinte aspeto:

        ![area graph sum](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph-sum.png "area graph sum")

        Por predefinição, a visualização mostra a soma de **ActualTemp** e **TargetTemp**. Select the down arrow next to **ActualTemp** and **TragetTemp** in the Visualizations pane, you can see **Sum** is selected.

    3. Select the down arrows next to **ActualTemp** and **TragetTemp** in the Visualizations pane, select **Average** to get an average of actual and target temperatures for each building.

        ![average of values](./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "average of values")

        A visualização de dados deverá ser semelhante à da captura de ecrã. Mova o cursor sobre a visualização para obter sugestões de contexto com dados relevantes.

        ![area graph](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "area graph")

9. Navigate to **File** > **Save**, enter the name `BuildingTemperature` for the file, then select **Save**.

### <a name="publish-the-report-to-the-power-bi-service-optional"></a>Publicar o relatório no serviço Power BI (opcional)

O serviço Power BI permite-lhe partilhar relatórios e dashboards em toda a sua organização. Nesta secção, vai publicar primeiro o conjunto de dados e o relatório. Em seguida, vai afixar o relatório a um dashboard. Dashboards are typically used to focus on a subset of data in a report. You have only one visualization in your report, but it's still useful to go through the steps.

1. Abra o Power BI Desktop.

1. No separador **Home** (Base), clique em **Publish** (Publicar).

    ![Publicar a partir do Power BI Desktop](./media/apache-spark-use-bi-tools/apache-spark-bi-publish.png "Publicar a partir do Power BI Desktop")

1. Selecione uma área de trabalho na qual publicar o conjunto de dados e o relatório e clique em **Select** (Selecionar). Na imagem seguinte, está selecionada a área de trabalho **My Workspace** predefinida.

    ![Select workspace to publish dataset and report to](./media/apache-spark-use-bi-tools/apache-spark-bi-select-workspace.png "Select workspace to publish dataset and report to") 

1. Após a publicação bem-sucedida, clique em **Open 'BuildingTemperature.pbix' in Power BI** (Abrir “BuildingTemperature.pbix” no Power BI).

    ![Publish success, click to enter credentials](./media/apache-spark-use-bi-tools/apache-spark-bi-publish-success.png "Publish success, click to enter credentials") 

1. No serviço Power BI, clique em **Enter credentials** (Introduzir credenciais).

    ![Enter credentials in Power BI service](./media/apache-spark-use-bi-tools/apache-spark-bi-enter-credentials.png "Enter credentials in Power BI service")

1. Clique em **Editar credenciais**.

    ![Edit credentials in Power BI service](./media/apache-spark-use-bi-tools/apache-spark-bi-edit-credentials.png "Edit credentials in Power BI service")

1. Introduza as informações de conta de início de sessão do HDInsight e clique em **Sign in** (Iniciar sessão). O nome predefinido da conta é *admin*.

    ![Sign in to Spark cluster](./media/apache-spark-use-bi-tools/apache-spark-bi-sign-in.png "Sign in to Spark cluster")

1. No painel do lado esquerdo, aceda a **Workspaces** (Áreas de Trabalho) > **My Workspace** (As Minhas Áreas de Trabalho) > **REPORTS** (RELATÓRIOS) e clique em **BuildingTemperature**.

    ![Report listed under reports in left pane](./media/apache-spark-use-bi-tools/apache-spark-bi-service-left-pane.png "Report listed under reports in left pane")

    Também deverá ver **BuildingTemperature** em **DATASETS** (CONJUNTOS DE DADOS), no painel do lado esquerdo.

    O elemento visual que criou no Power BI Desktop está agora disponível no serviço Power BI. 

1. Coloque o cursor sobre a visualização e clique no ícone de alfinete no canto superior direito.

    ![Report in the Power BI service](./media/apache-spark-use-bi-tools/apache-spark-bi-service-report.png "Report in the Power BI service")

1. Selecione "New dashboard" (“Novo dashboard”), introduza o nome `Building temperature` e clique em **Pin** (Afixar).

    ![Pin to new dashboard](./media/apache-spark-use-bi-tools/apache-spark-bi-pin-dashboard.png "Pin to new dashboard")

1. No relatório, clique em **Go to dashboard** (Aceder ao dashboard).

O elemento visual é afixado ao dashboard. Pode adicionar outros elementos visuais ao relatório e afixá-los ao mesmo dashboard. For more information about reports and dashboards, see [Reports in Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-reports/) and [Dashboards in Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).

## <a name="clean-up-resources"></a>Limpar recursos

Depois de concluir o tutorial, pode pretender eliminar o cluster. With HDInsight, your data is stored in Azure Storage, so you can safely delete a cluster when it isn't in use. You're also charged for an HDInsight cluster, even when it isn't in use. Since the charges for the cluster are many times more than the charges for storage, it makes economic sense to delete clusters when they aren't in use.

To delete a cluster, see [Delete an HDInsight cluster using your browser, PowerShell, or the Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Passos seguintes

In this tutorial, you learned how to use [Microsoft Power BI](https://powerbi.microsoft.com/) to visualize data in an Apache Spark cluster in [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/). Advance to the next article to see you can create a machine learning application.

> [!div class="nextstepaction"]
> [Create a machine learning application](./apache-spark-ipython-notebook-machine-learning.md)