---
title: 'Tutorial: Analisar dados do Apache Spark com o Power BI no Azure HDInsight '
description: Utilize o Microsoft Power BI para visualizar os dados do Apache Spark armazenados clusters do HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 05/07/2018
ms.openlocfilehash: ed7beeadc0a550a28d1f936702aabeb45823b677
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127213"
---
# <a name="tutorial-analyze-apache-spark-data-using-power-bi-in-hdinsight"></a>Tutorial: Analisar dados do Apache Spark no HDInsight com o Power BI 

Aprenda a usar [Microsoft Power BI](https://powerbi.microsoft.com/) para visualizar os dados num [Apache Spark](https://spark.apache.org/) cluster no [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/).

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Utilizar o Power BI para ver dados do Spark

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* **Concluir o artigo [Tutorial: Carregar dados e executar consultas num cluster do Apache Spark no Azure HDInsight](./apache-spark-load-data-run-query.md)**.
* **Power BI**: [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/) e [subscrição de avaliação do Power BI](https://app.powerbi.com/signupredirect?pbi_source=web) (opcional).


## <a name="verify-the-data"></a>Verificar os dados

O [bloco de notas do Jupyter](https://jupyter.org/) que criou no [tutorial anterior](apache-spark-load-data-run-query.md) inclui código para criar um `hvac` tabela. Esta tabela baseia-se no ficheiro CSV disponível em todos os clusters do Spark do HDInsight, em **\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv**. Utilize o seguinte procedimento para verificar os dados.

1. No bloco de notas do Jupyter, cole o seguinte código e prima **SHIFT + ENTER**. O código verifica a existência das tabelas.

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    O resultado tem o seguinte aspeto:

    ![Mostrar tabelas no Spark](./media/apache-spark-use-bi-tools/show-tables.png)

    Se tiver fechado o bloco de notas antes de iniciar este tutorial, `hvactemptable` é limpa, pelo que não é incluída na saída.  Só as tabelas do Hive que estejam armazenadas na metastore (indicadas com **False** (Falso), na coluna **isTemporary**) podem ser acedidas a partir das ferramentas de BI. Neste tutorial, vai ligar à tabela **hvac** que criou.

2. Cole o seguinte código numa célula vazia e prima **SHIFT + ENTER**. O código verifica os dados na tabela.

    ```PySpark
    %%sql
    SELECT * FROM hvac LIMIT 10
    ```

    O resultado tem o seguinte aspeto:

    ![Mostrar linhas da tabela hvac no Spark](./media/apache-spark-use-bi-tools/select-limit.png)

3. A partir do menu **Ficheiro** do bloco de notas, clique em **Fechar e Parar**. Encerre o bloco de notas para libertar os recursos. 

## <a name="visualize-the-data"></a>Ver os dados

Nesta secção, vai utilizar o Power BI para criar visualizações, relatórios e dashboards a partir dos dados do cluster do Spark. 

### <a name="create-a-report-in-power-bi-desktop"></a>Criar um relatório no Power BI Desktop
Os primeiros passos para começar a trabalhar com o Spark são ligar ao cluster no Power BI Desktop, carregar dados a partir do cluster e criar uma visualização básica com base nesses dados.

> [!NOTE]  
> O conector demonstrado neste artigo está atualmente em pré-visualização. Se tiver comentários, envie-os através dos sites [Comunidade do Power BI](https://community.powerbi.com/) ou [Power BI Ideas](https://ideas.powerbi.com/forums/265200-power-bi-ideas).

1. Abra o [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).
1. No separador **Home** (Base), clique em **Get Data** (Obter Dados) e em **More** (Mais).

    ![Obter dados no Power BI Desktop a partir do HDInsight Apache Spark](./media/apache-spark-use-bi-tools/hdinsight-spark-power-bi-desktop-get-data.png "Obter dados no Power BI Desktop a partir do HDInsight Apache Spark")


2. Introduza `Spark` na caixa de pesquisa, selecione **do Azure HDInsight Spark**e, em seguida, clique em **Connect**.

    ![Obter dados no Power BI a partir do Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "Obter dados no Power BI a partir do Apache Spark BI")

3. Introduza o URL do cluster (no formato `mysparkcluster.azurehdinsight.net`), selecione **DirectQuery** e clique em **OK**.

    Pode utilizar qualquer um dos modos de conectividade de dados com o Spark. Se utilizar o DirectQuery, as alterações são refletidas nos relatórios sem atualizar o conjunto de dados completo. Se importar os dados, tem de atualizar o conjunto de dados para ver as alterações. Para obter mais informações sobre como e quando utilizar o DirectQuery, veja [Utilizar o DirectQuery no Power BI](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/). 

4. Introduza as informações de conta de início de sessão do HDInsight e clique em **Connect** (Ligar). O nome predefinido da conta é *admin*.

5. Selecione a tabela `hvac`, espere para ver uma pré-visualização dos dados e clique em **Load** (Carregar).

    ![Nome de utilizador e palavra-passe do cluster do Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png "Nome de utilizador e palavra-passe do cluster do Spark")

    O Power BI Desktop tem as informações de que precisa para se ligar ao cluster do Spark e carregar dados da tabela `hvac`. A tabela e as colunas são apresentadas no painel **Fields** (Campos).  Veja a captura de ecrã abaixo:

6. Visualize a variância entre a temperatura de destino e a temperatura real de cada edifício: 

    1. No painel **VISUALIZATIONS** (VISUALIZAÇÕES), selecione **Area Chart** (Gráfico de Área). 
    2. Arraste o campo **BuildingID** para **Axis** (Eixo) e os campos **ActualTemp** e **TargetTemp** para **Value** (Valor).

        ![Utilizar o Apache Spark BI para criar visualizações de dados do Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "Utilizar o Apache Spark BI para criar visualizações de dados do Spark")

        O diagrama tem o seguinte aspeto:

        ![Utilizar o Apache Spark BI para criar visualizações de dados do Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph-sum.png "Utilizar o Apache Spark BI para criar visualizações de dados do Spark")

        Por predefinição, a visualização mostra a soma de **ActualTemp** e **TargetTemp**. Clique na seta para baixo junto a **ActualTemp** e **TragetTemp**, no painel Visualizations, e pode ver que **Sum** (Soma) está selecionado.

    3. Clique nas setas para baixo junto a **ActualTemp** e a **TragetTemp**, no painel Visualizations, selecione **Average** (Média) para obter uma média das temperaturas reais e pretendidas para cada edifício.

        ![Utilizar o Apache Spark BI para criar visualizações de dados do Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "Utilizar o Apache Spark BI para criar visualizações de dados do Spark")

        A visualização de dados deverá ser semelhante à da captura de ecrã. Mova o cursor sobre a visualização para obter sugestões de contexto com dados relevantes.

        ![Utilizar o Apache Spark BI para criar visualizações de dados do Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "Utilizar o Apache Spark BI para criar visualizações de dados do Spark")

7. Clique em **File** (Ficheiro), em seguida, em **Save** (Guardar) e introduza o nome para o ficheiro `BuildingTemperature.pbix`. 

### <a name="publish-the-report-to-the-power-bi-service-optional"></a>Publicar o relatório no serviço Power BI (opcional)

O serviço Power BI permite-lhe partilhar relatórios e dashboards em toda a sua organização. Nesta secção, vai publicar primeiro o conjunto de dados e o relatório. Em seguida, vai afixar o relatório a um dashboard. Normalmente, os dashboards são utilizados para se concentrarem num subconjunto de dados num relatório; tem apenas uma visualização no seu relatório, mas, ainda assim, é útil seguir os passos.

1. Abra o Power BI Desktop.
2. No separador **Home** (Base), clique em **Publish** (Publicar).

    ![Publicar a partir do Power BI Desktop](./media/apache-spark-use-bi-tools/apache-spark-bi-publish.png "Publicar a partir do Power BI Desktop")

2. Selecione uma área de trabalho na qual publicar o conjunto de dados e o relatório e clique em **Select** (Selecionar). Na imagem seguinte, está selecionada a área de trabalho **My Workspace** predefinida.

    ![Selecionar a área de trabalho na qual publicar o conjunto de dados e o relatório](./media/apache-spark-use-bi-tools/apache-spark-bi-select-workspace.png "Selecionar a área de trabalho na qual publicar o conjunto de dados e o relatório") 

3. Após a publicação bem-sucedida, clique em **Open 'BuildingTemperature.pbix' in Power BI** (Abrir “BuildingTemperature.pbix” no Power BI).

    ![Publicar com êxito, clique para introduzir credenciais](./media/apache-spark-use-bi-tools/apache-spark-bi-publish-success.png "Publicar com êxito, clique para introduzir credenciais") 

4. No serviço Power BI, clique em **Enter credentials** (Introduzir credenciais).

    ![Introduzir as credenciais no serviço Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-enter-credentials.png "Introduzir as credenciais no serviço Power BI")

5. Clique em **Editar credenciais**.

    ![Editar credenciais no serviço Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-edit-credentials.png "Editar credenciais no serviço Power BI")

6. Introduza as informações de conta de início de sessão do HDInsight e clique em **Sign in** (Iniciar sessão). O nome predefinido da conta é *admin*.

    ![Iniciar sessão no cluster do Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-sign-in.png "Iniciar sessão no cluster do Spark")

7. No painel do lado esquerdo, aceda a **Workspaces** (Áreas de Trabalho) > **My Workspace** (As Minhas Áreas de Trabalho) > **REPORTS** (RELATÓRIOS) e clique em **BuildingTemperature**.

    ![Relatório mostrado em “relatórios” no painel do lado esquerdo](./media/apache-spark-use-bi-tools/apache-spark-bi-service-left-pane.png "Relatório mostrado em “relatórios” no painel do lado esquerdo")

    Também deverá ver **BuildingTemperature** em **DATASETS** (CONJUNTOS DE DADOS), no painel do lado esquerdo.

    O elemento visual que criou no Power BI Desktop está agora disponível no serviço Power BI. 

8. Coloque o cursor sobre a visualização e clique no ícone de alfinete no canto superior direito.

    ![Relatório no serviço Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-service-report.png "Relatório no serviço Power BI")

9. Selecione "New dashboard" (“Novo dashboard”), introduza o nome `Building temperature` e clique em **Pin** (Afixar).

    ![Afixar ao dashboard novo](./media/apache-spark-use-bi-tools/apache-spark-bi-pin-dashboard.png "Afixar ao dashboard novo")

10. No relatório, clique em **Go to dashboard** (Aceder ao dashboard). 

O elemento visual é afixado ao dashboard. Pode adicionar outros elementos visuais ao relatório e afixá-los ao mesmo dashboard. Para obter mais informações sobre relatórios e dashboards, veja [relatórios no Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-reports/) e [Dashboards no Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).

<!--
## <a name="tableau"></a>Use Tableau Desktop 

> [!NOTE]
> This section is applicable only for Spark 1.5.2 clusters created in Azure HDInsight.
>
>

1. Install [Tableau Desktop](https://www.tableau.com/products/desktop) on the computer where you are running this Apache Spark BI tutorial.

2. Make sure that computer also has Microsoft Spark ODBC driver installed. You can install the driver from [here](https://go.microsoft.com/fwlink/?LinkId=616229).

1. Launch Tableau Desktop. In the left pane, from the list of server to connect to, click **Spark SQL**. If Spark SQL is not listed by default in the left pane, you can find it by click **More Servers**.
2. In the Spark SQL connection dialog box, provide the values as shown in the screenshot, and then click **OK**.

    ![Connect to a cluster for Apache Spark BI](./media/apache-spark-use-bi-tools/connect-to-tableau-apache-spark-bi.png "Connect to a cluster for Apache Spark BI")

    The authentication drop-down lists **Microsoft Azure HDInsight Service** as an option, only if you installed the [Microsoft Spark ODBC Driver](https://go.microsoft.com/fwlink/?LinkId=616229) on the computer.
3. On the next screen, from the **Schema** drop-down, click the **Find** icon, and then click **default**.

    ![Find schema for Apache Spark BI](./media/apache-spark-use-bi-tools/tableau-find-schema-apache-spark-bi.png "Find schema for Apache Spark BI")
4. For the **Table** field, click the **Find** icon again to list all the Hive tables available in the cluster. You should see the **hvac** table you created earlier using the notebook.

    ![Find table for Apache Spark BI](./media/apache-spark-use-bi-tools/tableau-find-table-apache-spark-bi.png "Find table for Apache Spark BI")
5. Drag and drop the table to the top box on the right. Tableau imports the data and displays the schema as highlighted by the red box.

    ![Add tables to Tableau for Apache Spark BI](./media/apache-spark-use-bi-tools/tableau-add-table-apache-spark-bi.png "Add tables to Tableau for Apache Spark BI")
6. Click the **Sheet1** tab at the bottom left. Make a visualization that shows the average target and actual temperatures for all buildings for each date. Drag **Date** and **Building ID** to **Columns** and **Actual Temp**/**Target Temp** to **Rows**. Under **Marks**, select **Area** to use an area map for Spark data visualization.

     ![Add fields for Spark data visualization](./media/apache-spark-use-bi-tools/spark-data-visualization-add-fields.png "Add fields for Spark data visualization")
7. By default, the temperature fields are shown as aggregate. If you want to show the average temperatures instead, you can do so from the drop-down, as shown in the following screenshot:

    ![Take average of temperature for Spark data visualization](./media/apache-spark-use-bi-tools/spark-data-visualization-average-temperature.png "Take average of temperature for Spark data visualization")

8. You can also super-impose one temperature map over the other to get a better feel of difference between target and actual temperatures. Move the mouse to the corner of the lower area map until you see the handle shape highlighted in a red circle. Drag the map to the other map on the top and release the mouse when you see the shape highlighted in red rectangle.

    ![Merge maps for Spark data visualization](./media/apache-spark-use-bi-tools/spark-data-visualization-merge-maps.png "Merge maps for Spark data visualization")

     Your data visualization should change as shown in the screenshot:

    ![Tableau output for Spark data visualization](./media/apache-spark-use-bi-tools/spark-data-visualization-tableau-output.png "Tableau output for Spark data visualization")
9. Click **Save** to save the worksheet. You can create dashboards and add one or more sheets to it.
-->

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

- Visualize dados do Apache Spark com o Power BI.

Avance para o próximo artigo para ver como os dados que registou no Spark podem ser extraídos para uma ferramenta de análise de BI como o Power BI. 
> [!div class="nextstepaction"]
> [Executar uma tarefa de transmissão em fluxo do Apache Spark](apache-spark-eventhub-streaming.md)

