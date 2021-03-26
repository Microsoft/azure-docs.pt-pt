---
title: 'Tutorial: Analise os dados do Apache Spark Azure HDInsight com o Power BI'
description: Tutorial - Use o Microsoft Power BI para visualizar os dados do Apache Spark armazenados em clusters HDInsight
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,mvc,seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: a688f47e11e76f573ba2b99816e1fb402f4c50d2
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104870556"
---
# <a name="tutorial-analyze-apache-spark-data-using-power-bi-in-hdinsight"></a>Tutorial: Analise os dados do Apache Spark utilizando o Power BI em HDInsight

Neste tutorial, aprende-se a usar o Microsoft Power BI para visualizar dados num cluster Apache Spark em Azure HDInsight.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Utilizar o Power BI para ver dados do Spark

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Concluir o artigo [Tutorial: Load data and run queries on an Apache Spark cluster in Azure HDInsight](./apache-spark-load-data-run-query.md) (Tutorial: Carregar dados e executar consultas num cluster do Apache Spark no Azure HDInsight).

* [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).

* Opcional: [Assinatura de teste de Power BI](https://app.powerbi.com/signupredirect?pbi_source=web).

## <a name="verify-the-data"></a>Verificar os dados

O [Caderno Jupyter](https://jupyter.org/) que criou no [tutorial anterior](apache-spark-load-data-run-query.md) inclui código para criar uma `hvac` tabela. Esta tabela baseia-se no ficheiro CSV disponível em todos os clusters HDInsight Spark em `\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv` . Utilize o seguinte procedimento para verificar os dados.

1. A partir do Caderno Jupyter, cole o seguinte código e, em seguida, prima **SHIFT + ENTER**. O código verifica a existência das tabelas.

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    O resultado tem o seguinte aspeto:

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-show-tables.png" alt-text="Mostrar tabelas no Spark" border="true":::

    Se tiver fechado o bloco de notas antes de iniciar este tutorial, `hvactemptable` é limpa, pelo que não é incluída na saída.  Só as tabelas do Hive que estejam armazenadas na metastore (indicadas com **False** (Falso), na coluna **isTemporary**) podem ser acedidas a partir das ferramentas de BI. Neste tutorial, vai ligar à tabela **hvac** que criou.

2. Cole o seguinte código numa célula vazia e prima **SHIFT + ENTER**. O código verifica os dados na tabela.

    ```PySpark
    %%sql
    SELECT * FROM hvac LIMIT 10
    ```

    O resultado tem o seguinte aspeto:

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-select-limit.png" alt-text="Mostrar linhas da tabela hvac no Spark" border="true":::

3. No menu **File (Ficheiro)** do bloco de notas, selecione **Close and Halt (Fechar e Parar)**. Encerre o bloco de notas para libertar os recursos.

## <a name="visualize-the-data"></a>Ver os dados

Nesta secção, vai utilizar o Power BI para criar visualizações, relatórios e dashboards a partir dos dados do cluster do Spark.

### <a name="create-a-report-in-power-bi-desktop"></a>Criar um relatório no Power BI Desktop

Os primeiros passos para começar a trabalhar com o Spark são ligar ao cluster no Power BI Desktop, carregar dados a partir do cluster e criar uma visualização básica com base nesses dados.

1. Abra o Power BI Desktop. Feche o ecrã de respingo de arranque se abrir.

2. A partir do separador **Casa,** navegue para **Obter Mais**  >  **Dados..**.

    :::image type="content" source="./media/apache-spark-use-bi-tools/hdinsight-spark-power-bi-desktop-get-data.png " alt-text="Obtenha dados no Power BI Desktop a partir de HDInsight Apache Spark" border="true":::er="true"::

3. Introduza `Spark` na caixa de pesquisa, selecione **Azure HDInsight Spark** e, em seguida, selecione **Connect**.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png " alt-text="Obtenha dados em Power BI de Apache Spark BI" border="true":::er="true":::

4. Introduza o URL do cluster (no `mysparkcluster.azurehdinsight.net` formulário) na caixa de texto do **Servidor.**

5. No **modo de conectividade de dados,** selecione **DirectQuery**. Em seguida, selecione **OK**.

    Pode utilizar qualquer um dos modos de conectividade de dados com o Spark. Se utilizar o DirectQuery, as alterações são refletidas nos relatórios sem atualizar o conjunto de dados completo. Se importar os dados, tem de atualizar o conjunto de dados para ver as alterações. Para obter mais informações sobre como e quando utilizar o DirectQuery, veja [Utilizar o DirectQuery no Power BI](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/).

6. Introduza as informações da conta de login HDInsight e, em seguida, selecione **Connect**. O nome predefinido da conta é *admin*.

7. Selecione a `hvac` tabela, aguarde para ver uma pré-visualização dos dados e, em seguida, selecione **Carregar**.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png " alt-text="Nome de utilizador do cluster de faíscas e palavra-passe" border="true":::d" border="true":::

    O Power BI Desktop tem as informações de que precisa para se ligar ao cluster do Spark e carregar dados da tabela `hvac`. A tabela e as colunas são apresentadas no painel **Fields** (Campos).

8. Visualize a variância entre a temperatura de destino e a temperatura real de cada edifício:

    1. No painel **VISUALIZATIONS** (VISUALIZAÇÕES), selecione **Area Chart** (Gráfico de Área).

    2. Arraste o campo **BuildingID** para **Axis** (Eixo) e os campos **ActualTemp** e **TargetTemp** para **Value** (Valor).

        :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png " alt-text="adicionar colunas de valor" border="true":::t="adicionar colunas de valor" border="true"::

        O diagrama tem o seguinte aspeto:

        :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph-sum.png " alt-text="Gráfico de área soma" border="true":::lt-text="soma de gráfico de área" border="true"::

        Por predefinição, a visualização mostra a soma de **ActualTemp** e **TargetTemp**. Selecione a seta para baixo ao lado **do ActualTemp** e **tragetTemp** no painel visualizações, pode ver **sum** selecionada.

    3. Selecione as setas para baixo ao lado **do ActualTemp** e **tragetTemp** no painel visualizações, selecione **Average** para obter uma média de temperaturas reais e alvo para cada edifício.

        :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png " alt-text="média dos valores" border="true":::t="média dos valores" border="true":::

        A visualização de dados deverá ser semelhante à da captura de ecrã. Mova o cursor sobre a visualização para obter sugestões de contexto com dados relevantes.

        :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png " alt-text="gráfico de área" border="true":::.png " alt-text="area graph" border="true"::

9. Navegue para **Guardar**  >  **ficheiros,** insira o nome `BuildingTemperature` do ficheiro e, em seguida, selecione **Guardar**.

### <a name="publish-the-report-to-the-power-bi-service-optional"></a>Publicar o relatório no serviço Power BI (opcional)

O serviço Power BI permite-lhe partilhar relatórios e dashboards em toda a sua organização. Nesta secção, vai publicar primeiro o conjunto de dados e o relatório. Em seguida, vai afixar o relatório a um dashboard. Os dashboards são normalmente usados para se concentrarem num subconjunto de dados num relatório. Tem apenas uma visualização no seu relatório, mas ainda é útil passar pelos degraus.

1. Abra o Power BI Desktop.

1. A partir do separador **Casa,** **selecione Publicar**.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-publish.png " alt-text="Publicar a partir do Power BI Desktop" border="true"::: Desktop" border="true":::

1. Selecione um espaço de trabalho para publicar o seu conjunto de dados e reporte-o e, em seguida, **selecione Select**. Na imagem seguinte, está selecionada a área de trabalho **My Workspace** predefinida.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-select-workspace.png " alt-text="Selecione espaço de trabalho para publicar conjunto de dados e reporte à" border="true":::ue":::

1. Depois de ter sido bem sucedida a publicação, selecione **Open 'BuildingTemperature.pbix' em Power BI**.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-publish-success.png " alt-text="Publique o sucesso, clique para introduzir credenciais" border="true":::er="true":::

1. No serviço Power BI, selecione **Introduzir credenciais**.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-enter-credentials.png " alt-text="Introduza credenciais no serviço Power BI" border="true":::" border="true":::

1. Selecione **credenciais de edição**.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-edit-credentials.png " alt-text="Editar credenciais no serviço Power BI" border="true":::e" border="true":::

1. Introduza as informações da conta de login HDInsight e, em seguida, **selecione Iniciar sessão .** O nome predefinido da conta é *admin*.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-sign-in.png " alt-text="Inscreva-se no cluster Spark" border="true"::: "Cluster de faíscas" border="true":::

1. No painel esquerdo, vá a **Workspaces**  >  **My Workspace**  >  **REPORTS,** em seguida, selecione **BuildingTemperature**.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-service-left-pane.png " alt-text="Relatório listado em relatórios em" border="true":::ordem do painel esquerdo="true":::

    Também deverá ver **BuildingTemperature** em **DATASETS** (CONJUNTOS DE DADOS), no painel do lado esquerdo.

    O elemento visual que criou no Power BI Desktop está agora disponível no serviço Power BI.

1. Passe o cursor sobre a visualização e, em seguida, selecione o ícone do pino no canto superior direito.

    Relatório no serviço :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-service-report.png " alt-text="power bi&quot;" border="true":::border="true":::

1. Selecione "Novo painel de instrumentos", introduza o nome `Building temperature` e, em seguida, selecione **Pin**.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-pin-dashboard.png " alt-text="Pin to new dashboard" border="true"::: to new dashboard" border="true"::

1. No relatório, selecione Ir para o painel de **instrumentos**.

O elemento visual é afixado ao dashboard. Pode adicionar outros elementos visuais ao relatório e afixá-los ao mesmo dashboard. Para obter mais informações sobre relatórios e dashboards, consulte [Relatórios em Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-reports/) e [Dashboards em Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).

## <a name="clean-up-resources"></a>Limpar os recursos

Depois de concluir o tutorial, pode pretender eliminar o cluster. Com o HDInsight, os seus dados são armazenados no Azure Storage, para que possa eliminar com segurança um cluster quando este não estiver a ser utilizado. Também é cobrado por um cluster HDInsight, mesmo quando não está a ser utilizado. Uma vez que as taxas para o cluster são muitas vezes mais do que os encargos de armazenamento, faz sentido económico apagar clusters quando não estão a ser utilizados.

Para eliminar um cluster, consulte [Eliminar um cluster HDInsight utilizando o seu navegador, PowerShell ou o Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a usar o Microsoft Power BI para visualizar dados num cluster Apache Spark em Azure HDInsight. Avance para o próximo artigo para ver se pode criar uma aplicação de machine learning.

> [!div class="nextstepaction"]
> [Criar uma aplicação de aprendizagem automática](./apache-spark-ipython-notebook-machine-learning.md)