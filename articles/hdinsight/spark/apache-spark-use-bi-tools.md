---
title: 'Tutorial: analisar dados de Apache Spark do Azure HDInsight com Power BI'
description: Tutorial-usar o Microsoft Power BI para visualizar Apache Spark os clusters HDInsight armazenados
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
# <a name="tutorial-analyze-apache-spark-data-using-power-bi-in-hdinsight"></a>Tutorial: analisar dados de Apache Spark usando Power BI no HDInsight

Neste tutorial, você aprenderá a usar o [Microsoft Power bi](https://powerbi.microsoft.com/) para visualizar dados em um cluster apache Spark no [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/).

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Utilizar o Power BI para ver dados do Spark

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Conclua o [tutorial de artigo: carregar dados e executar consultas em um cluster apache Spark no Azure HDInsight](./apache-spark-load-data-run-query.md).

* [Power bi desktop](https://powerbi.microsoft.com/en-us/desktop/).

* Opcional: [assinatura de avaliação do Power bi](https://app.powerbi.com/signupredirect?pbi_source=web).

## <a name="verify-the-data"></a>Verificar os dados

O [Jupyter Notebook](https://jupyter.org/) que você criou no [tutorial anterior](apache-spark-load-data-run-query.md) inclui código para criar uma tabela `hvac`. Esta tabela se baseia no arquivo CSV disponível em todos os clusters HDInsight Spark em `\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv`. Utilize o seguinte procedimento para verificar os dados.

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

1. Abra o Power BI Desktop. Feche a tela inicial de inicialização se ela for aberta.

2. Na guia **início** , navegue até **obter dados** > **mais..** .

    ![Obter dados em Power BI Desktop do HDInsight Apache Spark](./media/apache-spark-use-bi-tools/hdinsight-spark-power-bi-desktop-get-data.png "Obter dados em Power BI de Apache Spark BI")

3. Insira `Spark` na caixa de pesquisa, selecione **Azure HDInsight Spark**e, em seguida, selecione **conectar**.

    ![Obter dados em Power BI de Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "Obter dados em Power BI de Apache Spark BI")

4. Insira a URL do cluster (no formato `mysparkcluster.azurehdinsight.net`) na caixa de texto **servidor** .

5. Em **modo de conectividade de dados**, selecione **DirectQuery**. Em seguida, selecione **OK**.

    Pode utilizar qualquer um dos modos de conectividade de dados com o Spark. Se utilizar o DirectQuery, as alterações são refletidas nos relatórios sem atualizar o conjunto de dados completo. Se importar os dados, tem de atualizar o conjunto de dados para ver as alterações. Para obter mais informações sobre como e quando utilizar o DirectQuery, veja [Utilizar o DirectQuery no Power BI](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/).

6. Insira as informações da conta de logon do HDInsight e, em seguida, selecione **conectar**. O nome predefinido da conta é *admin*.

7. Selecione a tabela `hvac`, aguarde para ver uma visualização dos dados e, em seguida, selecione **carregar**.

    ![Nome de usuário e senha do cluster Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png "Nome de usuário e senha do cluster Spark")

    O Power BI Desktop tem as informações de que precisa para se ligar ao cluster do Spark e carregar dados da tabela `hvac`. A tabela e as colunas são apresentadas no painel **Fields** (Campos).

8. Visualize a variância entre a temperatura de destino e a temperatura real de cada edifício:

    1. No painel **VISUALIZATIONS** (VISUALIZAÇÕES), selecione **Area Chart** (Gráfico de Área).

    2. Arraste o campo **BuildingID** para **Axis** (Eixo) e os campos **ActualTemp** e **TargetTemp** para **Value** (Valor).

        ![Adicionar colunas de valor](./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "Adicionar colunas de valor")

        O diagrama tem o seguinte aspeto:

        ![soma do gráfico de área](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph-sum.png "soma do gráfico de área")

        Por predefinição, a visualização mostra a soma de **ActualTemp** e **TargetTemp**. Selecione a seta para baixo ao lado de **ActualTemp** e **TragetTemp** no painel visualizações, você pode ver **sum** está selecionado.

    3. Selecione as setas para baixo ao lado de **ActualTemp** e **TragetTemp** no painel visualizações, selecione **média** para obter uma média das temperaturas reais e de destino para cada edifício.

        ![média de valores](./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "média de valores")

        A visualização de dados deverá ser semelhante à da captura de ecrã. Mova o cursor sobre a visualização para obter sugestões de contexto com dados relevantes.

        ![gráfico de área](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "gráfico de área")

9. Navegue até **arquivo** > **salvar**, insira o nome `BuildingTemperature` para o arquivo e, em seguida, selecione **salvar**.

### <a name="publish-the-report-to-the-power-bi-service-optional"></a>Publicar o relatório no serviço Power BI (opcional)

O serviço Power BI permite-lhe partilhar relatórios e dashboards em toda a sua organização. Nesta secção, vai publicar primeiro o conjunto de dados e o relatório. Em seguida, vai afixar o relatório a um dashboard. Os painéis normalmente são usados para se concentrar em um subconjunto de dados em um relatório. Você tem apenas uma visualização em seu relatório, mas ainda é útil percorrer as etapas.

1. Abra o Power BI Desktop.

1. No separador **Home** (Base), clique em **Publish** (Publicar).

    ![Publicar a partir do Power BI Desktop](./media/apache-spark-use-bi-tools/apache-spark-bi-publish.png "Publicar a partir do Power BI Desktop")

1. Selecione uma área de trabalho na qual publicar o conjunto de dados e o relatório e clique em **Select** (Selecionar). Na imagem seguinte, está selecionada a área de trabalho **My Workspace** predefinida.

    ![Selecione o espaço de trabalho para publicar o conjunto de relatórios e o relatório](./media/apache-spark-use-bi-tools/apache-spark-bi-select-workspace.png "Selecione o espaço de trabalho para publicar o conjunto de relatórios e o relatório") 

1. Após a publicação bem-sucedida, clique em **Open 'BuildingTemperature.pbix' in Power BI** (Abrir “BuildingTemperature.pbix” no Power BI).

    ![Publicação bem-sucedida, clique para inserir as credenciais](./media/apache-spark-use-bi-tools/apache-spark-bi-publish-success.png "Publicação bem-sucedida, clique para inserir as credenciais") 

1. No serviço Power BI, clique em **Enter credentials** (Introduzir credenciais).

    ![Insira as credenciais no serviço do Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-enter-credentials.png "Insira as credenciais no serviço do Power BI")

1. Clique em **Editar credenciais**.

    ![Editar credenciais no serviço do Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-edit-credentials.png "Editar credenciais no serviço do Power BI")

1. Introduza as informações de conta de início de sessão do HDInsight e clique em **Sign in** (Iniciar sessão). O nome predefinido da conta é *admin*.

    ![Entrar no cluster Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-sign-in.png "Entrar no cluster Spark")

1. No painel do lado esquerdo, aceda a **Workspaces** (Áreas de Trabalho) > **My Workspace** (As Minhas Áreas de Trabalho) > **REPORTS** (RELATÓRIOS) e clique em **BuildingTemperature**.

    ![Relatório listado em relatórios no painel esquerdo](./media/apache-spark-use-bi-tools/apache-spark-bi-service-left-pane.png "Relatório listado em relatórios no painel esquerdo")

    Também deverá ver **BuildingTemperature** em **DATASETS** (CONJUNTOS DE DADOS), no painel do lado esquerdo.

    O elemento visual que criou no Power BI Desktop está agora disponível no serviço Power BI. 

1. Coloque o cursor sobre a visualização e clique no ícone de alfinete no canto superior direito.

    ![Relatório no serviço do Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-service-report.png "Relatório no serviço do Power BI")

1. Selecione "New dashboard" (“Novo dashboard”), introduza o nome `Building temperature` e clique em **Pin** (Afixar).

    ![Fixar no novo painel](./media/apache-spark-use-bi-tools/apache-spark-bi-pin-dashboard.png "Fixar no novo painel")

1. No relatório, clique em **Go to dashboard** (Aceder ao dashboard).

O elemento visual é afixado ao dashboard. Pode adicionar outros elementos visuais ao relatório e afixá-los ao mesmo dashboard. Para obter mais informações sobre relatórios e painéis, consulte [relatórios em Power bi](https://powerbi.microsoft.com/documentation/powerbi-service-reports/) e [painéis em Power bi](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).

## <a name="clean-up-resources"></a>Limpar recursos

Depois de concluir o tutorial, pode pretender eliminar o cluster. Com o HDInsight, seus dados são armazenados no armazenamento do Azure, para que você possa excluir um cluster com segurança quando ele não estiver em uso. Você também é cobrado por um cluster HDInsight, mesmo quando ele não está em uso. Como os encargos para o cluster são muitas vezes mais do que os encargos de armazenamento, ele faz sentido econômico excluir clusters quando eles não estiverem em uso.

Para excluir um cluster, consulte [excluir um cluster HDInsight usando seu navegador, o PowerShell ou o CLI do Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu a usar o [Microsoft Power bi](https://powerbi.microsoft.com/) para visualizar dados em um cluster apache Spark no [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/). Avance para o próximo artigo para ver você pode criar um aplicativo de aprendizado de máquina.

> [!div class="nextstepaction"]
> [Criar um aplicativo de Machine Learning](./apache-spark-ipython-notebook-machine-learning.md)