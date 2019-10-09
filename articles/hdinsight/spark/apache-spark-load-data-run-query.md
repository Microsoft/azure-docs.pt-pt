---
title: 'Tutorial: Carregar dados e executar consultas no cluster Apache Spark no Azure HDInsight'
description: Tutorial-saiba como carregar dados e executar consultas interativas em clusters do Spark no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 10/03/2019
ms.openlocfilehash: 3d6b7cf67faa94d0947d16cc79d0d5b839de7acb
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72027785"
---
# <a name="tutorial-load-data-and-run-queries-on-an-apache-spark-cluster-in-azure-hdinsight"></a>Tutorial: Carregar dados e executar consultas em um cluster Apache Spark no Azure HDInsight

Neste tutorial, você aprenderá a criar um dataframe a partir de um arquivo CSV e a executar consultas interativas do Spark SQL em um cluster [Apache Spark](https://spark.apache.org/) no Azure HDInsight. No Spark, um pacote de dados é uma coleção distribuída de dados organizados em colunas com nomes. Do ponto de vista conceptual, o pacote de dados equivale a uma tabela numa base de dados relacional ou a um pacote de dados em R/Python.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Criar um pacote de dados a partir de um ficheiro CSV
> * Executar consultas no pacote de dados

## <a name="prerequisites"></a>Pré-requisitos

Um cluster do Apache Spark no HDInsight. Consulte [criar um cluster apache Spark](./apache-spark-jupyter-spark-sql-use-portal.md).

## <a name="create-a-jupyter-notebook"></a>Criar um bloco de notas do Jupyter

O Jupyter Notebook é um ambiente de bloco de notas interativo que suporta várias linguagens de programação. O bloco de notas permite-lhe interagir com os seus dados, combinar código com texto markdown e realizar visualizações simples.

1. Edite a URL `https://SPARKCLUSTER.azurehdinsight.net/jupyter` substituindo `SPARKCLUSTER` pelo nome do cluster do Spark. Em seguida, insira a URL editada em um navegador da Web. Se lhe for pedido, introduza as credenciais de início de sessão do cluster.

2. Na página da Web do Jupyter, selecione **novo** > **PySpark** para criar um bloco de anotações.

   ![Crie um Bloco de Notas Jupyter para executar a consulta interativa do Spark SQL](./media/apache-spark-load-data-run-query/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Create a Jupyter Notebook to run interactive Spark SQL query")

   Um novo bloco de anotações é criado e aberto com o nome sem título (`Untitled.ipynb`).

    > [!NOTE]  
    > Ao utilizar o kernel do PySpark para criar um bloco de notas, a sessão `spark` é criada automaticamente quando executa a primeira célula de código. Não precisa de criar explicitamente a sessão.

## <a name="create-a-dataframe-from-a-csv-file"></a>Criar um pacote de dados a partir de um ficheiro CSV

Os aplicativos podem criar quadros de molduras diretamente de arquivos ou pastas no armazenamento remoto, como o armazenamento do Azure ou o Azure Data Lake Storage; de uma tabela Hive; ou de outras fontes de dados com suporte no Spark, como Cosmos DB, BD SQL do Azure, DW e assim por diante. A seguinte captura de ecrã mostra um instantâneo do ficheiro HVAC.csv utilizado neste tutorial. O ficheiro CSV é fornecido com todos os clusters do Spark no HDInsight. Os dados ilustram as variações de temperatura de alguns edifícios.

![Instantâneo de dados para a consulta SQL interativa de Spark](./media/apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png "Instantâneo de dados para a consulta SQL interativa de Spark")

1. Cole o código a seguir em uma célula vazia do notebook Jupyter e pressione **Shift + Enter** para executar o código. O código importa os tipos necessários para este cenário:

    ```python
    from pyspark.sql import *
    from pyspark.sql.types import *
    ```

    Quando executa uma consulta interativa no Jupyter, a janela do browser ou a legenda do separador apresenta o estado **(Ocupado)** , juntamente com o título do bloco de notas. Também vê um círculo sólido junto ao texto do **PySpark** no canto superior direito. Após a conclusão da tarefa, este é alterado para um círculo vazio.

    ![Estado da consulta interativa do Spark SQL](./media/apache-spark-load-data-run-query/hdinsight-spark-interactive-spark-query-status.png "Status of interactive Spark SQL query")

2. Execute o seguinte código para criar um pacote de dados e uma tabela temporária (**hvac**) ao utilizar o seguinte código.

    ```python
    # Create a dataframe and table from sample data
    csvFile = spark.read.csv('/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv', header=True, inferSchema=True)
    csvFile.write.saveAsTable("hvac")
    ```

## <a name="run-queries-on-the-dataframe"></a>Executar consultas no pacote de dados

Após a criação da tabela, pode executar uma consulta interativa nos dados.

1. Execute o seguinte código numa célula vazia do bloco de notas:

    ```sql
    %%sql
    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```

   É apresentado o seguinte resultado em forma de tabela.

     ![Saída da tabela do resultado da consulta interativa do Spark](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result.png "Table output of interactive Spark query result")

2. Também pode ver os resultados noutras visualizações. Para ver um gráfico de área do mesmo resultado, selecione **Area (Área)** e, em seguida, defina outros valores, conforme mostrado.

    ![Gráfico de área do resultado da consulta interativa do Spark](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result-area-chart.png "Area graph of interactive Spark query result")

3. Na barra de menus do bloco de anotações, navegue até **arquivo** > **salvar e ponto de verificação**.

4. Se tenciona iniciar o [próximo tutorial](apache-spark-use-bi-tools.md) agora, deixe o bloco de notas aberto. Caso contrário, desligue o bloco de anotações para liberar os recursos de cluster: na barra de menus do bloco de anotações, navegue até **arquivo** >  **fechar e parar**.

## <a name="clean-up-resources"></a>Limpar recursos

Com o HDInsight, seus blocos de anotações de dados e Jupyter são armazenados no armazenamento do Azure ou Azure Data Lake Storage, para que você possa excluir um cluster com segurança quando ele não estiver em uso. Você também é cobrado por um cluster HDInsight, mesmo quando ele não está em uso. Como os encargos para o cluster são muitas vezes mais do que os encargos de armazenamento, ele faz sentido econômico excluir clusters quando eles não estiverem em uso. Se tenciona começar já a trabalhar no próximo tutorial, convém manter o cluster.

Abra o cluster no portal do Azure e, em seguida, selecione **Eliminar**.

![Eliminar o cluster do HDInsight](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "Eliminar o cluster do HDInsight")

Também pode selecionar o nome do grupo de recursos para abrir a página do grupo de recursos e, em seguida, selecionar **Eliminar grupo de recursos**. Ao eliminar o grupo de recursos, está a eliminar o cluster do Spark no HDInsight e a conta de armazenamento predefinida.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu a criar um dataframe a partir de um arquivo CSV e a executar consultas interativas do Spark SQL em um cluster Apache Spark no Azure HDInsight. Avance para o próximo artigo para ver como os dados registrados no Apache Spark podem ser obtidos em uma ferramenta de análise de BI, como Power BI.

> [!div class="nextstepaction"]
> [Analisar dados com ferramentas do BI](apache-spark-use-bi-tools.md)
