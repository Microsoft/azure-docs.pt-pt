---
title: 'Tutorial: Analisar dados do Apache Spark com o Power BI no Azure HDInsight '
description: Tutorial - utilize o Microsoft Power BI para visualizar os dados do Apache Spark armazenados clusters do HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 05/16/2019
ms.openlocfilehash: d5296fe19cef9e8881d39bd9e59eb4c40d049959
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296195"
---
# <a name="tutorial-analyze-apache-spark-data-using-power-bi-in-hdinsight"></a>Tutorial: Analisar dados do Apache Spark no HDInsight com o Power BI

Neste tutorial, irá aprender a utilizar [Microsoft Power BI](https://powerbi.microsoft.com/) para visualizar os dados num cluster do Apache Spark no [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/).

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Utilizar o Power BI para ver dados do Spark

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Concluir o artigo [Tutorial: Carregar dados e executar consultas num cluster do Apache Spark no Azure HDInsight](./apache-spark-load-data-run-query.md).

* [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).

* Opcional: [Subscrição de avaliação de BI de energia](https://app.powerbi.com/signupredirect?pbi_source=web).

## <a name="verify-the-data"></a>Verificar os dados

O [bloco de notas do Jupyter](https://jupyter.org/) que criou no [tutorial anterior](apache-spark-load-data-run-query.md) inclui código para criar um `hvac` tabela. Esta tabela baseia-se no ficheiro CSV disponível em todos os clusters do HDInsight Spark em `\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv`. Utilize o seguinte procedimento para verificar os dados.

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

3. No menu **File (Ficheiro)** do bloco de notas, selecione **Close and Halt (Fechar e Parar)** . Encerre o bloco de notas para libertar os recursos.

## <a name="visualize-the-data"></a>Ver os dados

Nesta secção, vai utilizar o Power BI para criar visualizações, relatórios e dashboards a partir dos dados do cluster do Spark.

### <a name="create-a-report-in-power-bi-desktop"></a>Criar um relatório no Power BI Desktop

Os primeiros passos para começar a trabalhar com o Spark são ligar ao cluster no Power BI Desktop, carregar dados a partir do cluster e criar uma visualização básica com base nesses dados.

> [!NOTE]  
> O conector demonstrado neste artigo está atualmente em pré-visualização. Se tiver comentários, envie-os através dos sites [Comunidade do Power BI](https://community.powerbi.com/) ou [Power BI Ideas](https://ideas.powerbi.com/forums/265200-power-bi-ideas).

1. Abra o Power BI Desktop. Feche a tela inicial de arranque, se ele for aberta.

2. Partir do **home page** separador, navegue até **obter dados** > **mais....** .

    ![Obter dados no Power BI Desktop a partir do HDInsight Apache Spark](./media/apache-spark-use-bi-tools/hdinsight-spark-power-bi-desktop-get-data.png "Obter dados no Power BI Desktop a partir do HDInsight Apache Spark")

3. Introduza `Spark` na caixa de pesquisa, selecione **do Azure HDInsight Spark**e, em seguida, selecione **Connect**.

    ![Obter dados no Power BI a partir do Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "Obter dados no Power BI a partir do Apache Spark BI")

4. Introduza o URL do cluster (sob a forma `mysparkcluster.azurehdinsight.net`) no **Server** caixa de texto.

5. Sob **modo de conectividade de dados**, selecione **DirectQuery**. Em seguida, selecione **OK**.

    Pode utilizar qualquer um dos modos de conectividade de dados com o Spark. Se utilizar o DirectQuery, as alterações são refletidas nos relatórios sem atualizar o conjunto de dados completo. Se importar os dados, tem de atualizar o conjunto de dados para ver as alterações. Para obter mais informações sobre como e quando utilizar o DirectQuery, veja [Utilizar o DirectQuery no Power BI](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/).

6. Introduza as informações de conta de início de sessão do HDInsight, em seguida, selecione **Connect**. O nome predefinido da conta é *admin*.

7. Selecione o `hvac` de tabela, aguarde para ver uma pré-visualização dos dados e, em seguida, selecione **carga**.

    ![Nome de utilizador e palavra-passe do cluster do Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png "Nome de utilizador e palavra-passe do cluster do Spark")

    O Power BI Desktop tem as informações de que precisa para se ligar ao cluster do Spark e carregar dados da tabela `hvac`. A tabela e as colunas são apresentadas no painel **Fields** (Campos).

8. Visualize a variância entre a temperatura de destino e a temperatura real de cada edifício:

    1. No painel **VISUALIZATIONS** (VISUALIZAÇÕES), selecione **Area Chart** (Gráfico de Área).

    2. Arraste o campo **BuildingID** para **Axis** (Eixo) e os campos **ActualTemp** e **TargetTemp** para **Value** (Valor).

        ![Utilizar o Apache Spark BI para criar visualizações de dados do Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "Utilizar o Apache Spark BI para criar visualizações de dados do Spark")

        O diagrama tem o seguinte aspeto:

        ![Utilizar o Apache Spark BI para criar visualizações de dados do Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph-sum.png "Utilizar o Apache Spark BI para criar visualizações de dados do Spark")

        Por predefinição, a visualização mostra a soma de **ActualTemp** e **TargetTemp**. Selecione a seta para baixo junto a **ActualTemp** e **TragetTemp** no painel visualizações, pode ver **soma** está selecionada.

    3. Selecione as setas para baixo junto a **ActualTemp** e **TragetTemp** no painel visualizações, selecione **média** para obter uma média de real e temperaturas de destino para cada a criar.

        ![Utilizar o Apache Spark BI para criar visualizações de dados do Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "Utilizar o Apache Spark BI para criar visualizações de dados do Spark")

        A visualização de dados deverá ser semelhante à da captura de ecrã. Mova o cursor sobre a visualização para obter sugestões de contexto com dados relevantes.

        ![Utilizar o Apache Spark BI para criar visualizações de dados do Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "Utilizar o Apache Spark BI para criar visualizações de dados do Spark")

9. Navegue para **arquivo** > **guardar**, introduza o nome `BuildingTemperature` para o ficheiro, em seguida, selecione **guardar**.

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

## <a name="clean-up-resources"></a>Limpar recursos

Depois de concluir o tutorial, pode pretender eliminar o cluster. Com o HDInsight, os dados são armazenados no Storage do Azure, pelo que pode eliminar um cluster em segurança quando este não está a ser utilizado. Também lhe é cobrado o valor de um cluster do HDInsight mesmo quando não o está a utilizar. Uma vez que os custos do cluster são muito superiores aos custos do armazenamento, faz sentido do ponto de vista económico eliminar os clusters quando não estiverem a ser utilizados.

Para eliminar um cluster, veja [eliminar um cluster do HDInsight com o seu browser, o PowerShell ou a CLI do Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu a usar [Microsoft Power BI](https://powerbi.microsoft.com/) para visualizar os dados num cluster do Apache Spark no [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/). Avance para o próximo artigo para ver como os dados que registou no Spark podem ser extraídos para uma ferramenta de análise de BI como o Power BI.

> [!div class="nextstepaction"]
> [Executar uma tarefa de transmissão em fluxo do Apache Spark](apache-spark-eventhub-streaming.md)