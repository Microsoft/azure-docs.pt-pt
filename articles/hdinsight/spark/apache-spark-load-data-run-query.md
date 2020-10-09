---
title: 'Tutorial: Carregar dados & executar consultas com Apache Spark - Azure HDInsight'
description: Tutorial - Aprenda a carregar dados e executar consultas interativas em clusters Spark em Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,mvc
ms.date: 02/12/2020
ms.openlocfilehash: 5eb6788a558e4429296731f1693edd18bf92f98f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "77198893"
---
# <a name="tutorial-load-data-and-run-queries-on-an-apache-spark-cluster-in-azure-hdinsight"></a>Tutorial: Carregar dados e executar consultas num cluster do Apache Spark no Azure HDInsight

Neste tutorial, você aprende a criar um dataframe a partir de um ficheiro csv, e como executar consultas interativas spark SQL contra um cluster [Apache Spark](https://spark.apache.org/) em Azure HDInsight. No Spark, um pacote de dados é uma coleção distribuída de dados organizados em colunas com nomes. Do ponto de vista conceptual, o pacote de dados equivale a uma tabela numa base de dados relacional ou a um pacote de dados em R/Python.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Criar um pacote de dados a partir de um ficheiro CSV
> * Executar consultas no pacote de dados

## <a name="prerequisites"></a>Pré-requisitos

Um cluster do Apache Spark no HDInsight. Ver [Criar um aglomerado de faíscas Apache.](./apache-spark-jupyter-spark-sql-use-portal.md)

## <a name="create-a-jupyter-notebook"></a>Criar um bloco de notas do Jupyter

O Jupyter Notebook é um ambiente de bloco de notas interativo que suporta várias linguagens de programação. O bloco de notas permite-lhe interagir com os seus dados, combinar código com texto markdown e realizar visualizações simples.

1. Edite o URL `https://SPARKCLUSTER.azurehdinsight.net/jupyter` substituindo-o pelo `SPARKCLUSTER` nome do seu cluster Spark. Em seguida, insira o URL editado num navegador web. Se lhe for pedido, introduza as credenciais de início de sessão do cluster.

2. A partir da página web do Jupyter, selecione **New**  >  **PySpark** para criar um caderno.

   ![Crie um Caderno Jupyter para executar consulta interativa spark SQL](./media/apache-spark-load-data-run-query/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Crie um Caderno Jupyter para executar consulta interativa spark SQL")

   Um novo caderno é criado e aberto com o nome `Untitled.ipynb` Untitled.

    > [!NOTE]  
    > Ao utilizar o kernel do PySpark para criar um bloco de notas, a sessão `spark` é criada automaticamente quando executa a primeira célula de código. Não precisa de criar explicitamente a sessão.

## <a name="create-a-dataframe-from-a-csv-file"></a>Criar um pacote de dados a partir de um ficheiro CSV

As aplicações podem criar dataframes diretamente a partir de ficheiros ou pastas no armazenamento remoto, tais como Azure Storage ou Azure Data Lake Storage; de uma mesa de colmeia; ou de outras fontes de dados suportadas pela Spark, tais como Cosmos DB, Azure SQL DB, DW, e assim por diante. A seguinte captura de ecrã mostra um instantâneo do ficheiro HVAC.csv utilizado neste tutorial. O ficheiro CSV é fornecido com todos os clusters do Spark no HDInsight. Os dados ilustram as variações de temperatura de alguns edifícios.

![Instantâneo de dados para consulta interativa Spark SQL](./media/apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png "Instantâneo de dados para consulta interativa Spark SQL")

1. Cole o seguinte código numa célula vazia do caderno Jupyter e, em seguida, prima **SHIFT + ENTER** para executar o código. O código importa os tipos necessários para este cenário:

    ```python
    from pyspark.sql import *
    from pyspark.sql.types import *
    ```

    Quando executa uma consulta interativa no Jupyter, a janela do browser ou a legenda do separador apresenta o estado **(Ocupado)**, juntamente com o título do bloco de notas. Também vê um círculo sólido junto ao texto do **PySpark** no canto superior direito. Após a conclusão da tarefa, este é alterado para um círculo vazio.

    ![Estado da consulta interativa Spark SQL](./media/apache-spark-load-data-run-query/hdinsight-spark-interactive-spark-query-status.png "Estado da consulta interativa Spark SQL")

1. Reparem no id da sessão. Da imagem acima, o id da sessão é 0. Se desejar, pode recuperar os detalhes da sessão navegando até `https://CLUSTERNAME.azurehdinsight.net/livy/sessions/ID/statements` onde clustername é o nome do seu cluster Spark e ID é o seu número de identificação de sessão.

1. Execute o seguinte código para criar um pacote de dados e uma tabela temporária (**hvac**) ao utilizar o seguinte código.

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

     ![Saída da tabela do resultado da consulta interativa de faíscas](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result.png "Saída da tabela do resultado da consulta interativa de faíscas")

2. Também pode ver os resultados noutras visualizações. Para ver um gráfico de área do mesmo resultado, selecione **Area (Área)** e, em seguida, defina outros valores, conforme mostrado.

    ![Gráfico de área do resultado da consulta interativa de faíscas](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result-area-chart.png "Gráfico de área do resultado da consulta interativa de faíscas")

3. A partir da barra de menu de portátil, navegue para **Guardar**  >  **ficheiros e checkpoint.**

4. Se tenciona iniciar o [próximo tutorial](apache-spark-use-bi-tools.md) agora, deixe o bloco de notas aberto. Caso contrário, desligue o caderno para libertar os recursos do cluster: da barra de menu do portátil, navegue para **File**  >   **Close e Halt**.

## <a name="clean-up-resources"></a>Limpar os recursos

Com o HDInsight, os seus dados e os seus cadernos Jupyter são armazenados no Azure Storage ou no Azure Data Lake Storage, para que possa eliminar com segurança um cluster quando este não estiver a ser utilizado. Também é cobrado por um cluster HDInsight, mesmo quando não está a ser utilizado. Uma vez que as taxas para o cluster são muitas vezes mais do que os encargos de armazenamento, faz sentido económico apagar clusters quando não estão a ser utilizados. Se tenciona começar já a trabalhar no próximo tutorial, convém manter o cluster.

Abra o cluster no portal do Azure e, em seguida, selecione **Eliminar**.

![Excluir o cluster HDInsight](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "Excluir o cluster HDInsight")

Também pode selecionar o nome do grupo de recursos para abrir a página do grupo de recursos e, em seguida, selecionar **Eliminar grupo de recursos**. Ao eliminar o grupo de recursos, está a eliminar o cluster do Spark no HDInsight e a conta de armazenamento predefinida.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu a criar um dataframe a partir de um ficheiro csv, e como executar consultas interativas spark SQL contra um cluster Apache Spark em Azure HDInsight. Avance para o próximo artigo para ver como os dados registados no Apache Spark podem ser puxados para uma ferramenta de análise de BI, como o Power BI.

> [!div class="nextstepaction"]
> [Analisar dados com ferramentas do BI](apache-spark-use-bi-tools.md)
