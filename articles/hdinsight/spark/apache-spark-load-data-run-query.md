---
title: 'Tutorial: Carregar dados e executar consultas num cluster do Apache Spark no Azure HDInsight '
description: Saiba como carregar dados e executar consultas interativas em clusters do Spark no Azure HDInsight.
services: azure-hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.author: hrasheed
ms.date: 11/06/2018
ms.openlocfilehash: ddcde2956da774e687c1e587649e65b79003bf3a
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57448991"
---
# <a name="tutorial-load-data-and-run-queries-on-an-apache-spark-cluster-in-azure-hdinsight"></a>Tutorial: Carregar dados e executar consultas num cluster do Apache Spark no Azure HDInsight

Neste tutorial, saiba como criar um pacote de dados de um ficheiro csv e como executar consultas interativas do Spark SQL em relação a uma [Apache Spark](https://spark.apache.org/) cluster no Azure HDInsight. No Spark, um pacote de dados é uma coleção distribuída de dados organizados em colunas com nomes. Do ponto de vista conceptual, o pacote de dados equivale a uma tabela numa base de dados relacional ou a um pacote de dados em R/Python.
 
Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Criar um pacote de dados a partir de um ficheiro CSV
> * Executar consultas no pacote de dados

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Conclua [Criar um cluster do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="create-a-dataframe-from-a-csv-file"></a>Criar um pacote de dados a partir de um ficheiro CSV

As aplicações podem criar pacotes de dados diretamente a partir de ficheiros ou pastas para o armazenamento remoto, como o Armazenamento do Azure ou o Azure Data Lake Storage; ou de uma tabela do Hive; ou de outras origens de dados suportadas pelo Spark, como o Cosmos DB, a DB SQL do Azure, DW, etc. A seguinte captura de ecrã mostra um instantâneo do ficheiro HVAC.csv utilizado neste tutorial. O ficheiro CSV é fornecido com todos os clusters do Spark no HDInsight. Os dados ilustram as variações de temperatura de alguns edifícios.
    
![Instantâneo de dados para a consulta SQL interativa de Spark](./media/apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png "Instantâneo de dados para a consulta SQL interativa de Spark")


1. Abra o bloco de notas Jupyter Notebook que criou na secção de pré-requisitos.
2. Cole o seguinte código numa célula vazia do bloco de notas e, em seguida, prima **SHIFT + ENTER** para o executar. O código importa os tipos necessários para este cenário:

    ```python
    from pyspark.sql import *
    from pyspark.sql.types import *
    ```

    Quando executa uma consulta interativa no Jupyter, a janela do browser ou a legenda do separador apresenta o estado **(Ocupado)**, juntamente com o título do bloco de notas. Também vê um círculo sólido junto ao texto do **PySpark** no canto superior direito. Após a conclusão da tarefa, este é alterado para um círculo vazio.

    ![Estado da consulta interativa do Spark SQL](./media/apache-spark-load-data-run-query/hdinsight-spark-interactive-spark-query-status.png "Status of interactive Spark SQL query")

3. Execute o seguinte código para criar um pacote de dados e uma tabela temporária (**hvac**) ao utilizar o seguinte código. 

    ```python
    # Create a dataframe and table from sample data
    csvFile = spark.read.csv('/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv', header=True, inferSchema=True)
    csvFile.write.saveAsTable("hvac")
    ```

    > [!NOTE]  
    > Ao utilizar o kernel do PySpark para criar um bloco de notas, a sessão `spark` é criada automaticamente quando executa a primeira célula de código. Não precisa de criar explicitamente a sessão.


## <a name="run-queries-on-the-dataframe"></a>Executar consultas no pacote de dados

Após a criação da tabela, pode executar uma consulta interativa nos dados.

1. Execute o seguinte código numa célula vazia do bloco de notas:

    ```sql
    %%sql
    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```

   É apresentado o seguinte resultado em forma de tabela.

     ![Saída da tabela do resultado da consulta interativa do Spark](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result.png "Table output of interactive Spark query result")

3. Também pode ver os resultados noutras visualizações. Para ver um gráfico de área do mesmo resultado, selecione **Area (Área)** e, em seguida, defina outros valores, conforme mostrado.

    ![Gráfico de área do resultado da consulta interativa do Spark](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result-area-chart.png "Area graph of interactive Spark query result")

10. No menu **File (Ficheiro)** do bloco de notas, selecione **Save and Checkpoint (Guardar e Ponto de Verificação)**. 

11. Se tenciona iniciar o [próximo tutorial](apache-spark-use-bi-tools.md) agora, deixe o bloco de notas aberto. Se não estiver, encerrar o bloco de notas para libertar os recursos de cluster: do **arquivo** menu no bloco de notas, selecione **fechar e parar**.

## <a name="clean-up-resources"></a>Limpar recursos

Com o HDInsight, seus dados e os blocos de notas do Jupyter são armazenados no armazenamento do Azure ou o armazenamento do Azure Data Lake, pelo que pode eliminar em segurança um cluster quando não está em utilização. Também lhe é cobrado o valor de um cluster do HDInsight mesmo quando não o está a utilizar. Uma vez que os custos do cluster são muito superiores aos custos do armazenamento, faz sentido do ponto de vista económico eliminar os clusters quando não estiverem a ser utilizados. Se tenciona começar já a trabalhar no próximo tutorial, convém manter o cluster.

Abra o cluster no portal do Azure e, em seguida, selecione **Eliminar**.

![Eliminar o cluster do HDInsight](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "Eliminar o cluster do HDInsight")

Também pode selecionar o nome do grupo de recursos para abrir a página do grupo de recursos e, em seguida, selecionar **Eliminar grupo de recursos**. Ao eliminar o grupo de recursos, está a eliminar o cluster do Spark no HDInsight e a conta de armazenamento predefinida.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

* Crie um dataframe do Apache Spark.
* Execute a consulta SQL de Spark no pacote de dados.

Avance para o artigo seguinte para ver como os dados que registou no Apache Spark podem ser extraídos para uma ferramenta de análise de BI como o Power BI. 
> [!div class="nextstepaction"]
> [Analisar dados com ferramentas do BI](apache-spark-use-bi-tools.md)

