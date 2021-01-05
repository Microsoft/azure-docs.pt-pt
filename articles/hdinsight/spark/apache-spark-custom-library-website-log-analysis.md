---
title: Analise os registos do site com bibliotecas Python em Spark - Azure
description: Este caderno demonstra como analisar dados de registo usando uma biblioteca personalizada com Spark on Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 12/27/2019
ms.openlocfilehash: 61ec2db1799919eb395996b56d08b77e3be7ff5a
ms.sourcegitcommit: 28c93f364c51774e8fbde9afb5aa62f1299e649e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/30/2020
ms.locfileid: "97822374"
---
# <a name="analyze-website-logs-using-a-custom-python-library-with-apache-spark-cluster-on-hdinsight"></a>Analise os registos do site usando uma biblioteca de Python personalizada com aglomerado de faíscas Apache em HDInsight

Este caderno demonstra como analisar dados de registo usando uma biblioteca personalizada com Apache Spark em HDInsight. A biblioteca personalizada que usamos é uma biblioteca Python chamada **iislogparser.py.**

## <a name="prerequisites"></a>Pré-requisitos

Um cluster do Apache Spark no HDInsight. Para obter instruções, veja [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="save-raw-data-as-an-rdd"></a>Guardar dados brutos como RDD

Nesta secção, usamos o [Jupyter](https://jupyter.org) Notebook associado a um cluster Apache Spark em HDInsight para executar trabalhos que processam os seus dados de amostras brutas e guardá-lo como uma tabela de Colmeia. Os dados da amostra são um ficheiro .csv (hvac.csv) disponível em todos os clusters por padrão.

Assim que os seus dados são guardados como uma tabela de Colmeia Apache, na secção seguinte ligaremos à tabela Hive usando ferramentas BI como Power BI e Tableau.

1. A partir de um navegador web, navegue `https://CLUSTERNAME.azurehdinsight.net/jupyter` para, onde `CLUSTERNAME` está o nome do seu cluster.

1. Crie um novo bloco de notas. Selecione **Novo**, e depois **PySpark**.

    ![Criar um novo Caderno Apache Jupyter](./media/apache-spark-custom-library-website-log-analysis/hdinsight-create-jupyter-notebook.png "Criar um novo Caderno Jupyter")

1. É criado e aberto um novo bloco de notas com o nome Untitled.pynb. Selecione o nome do portátil na parte superior e introduza um nome amigável.

    ![Fornecer um nome para o bloco de notas](./media/apache-spark-custom-library-website-log-analysis/hdinsight-name-jupyter-notebook.png "Fornecer um nome para o bloco de notas")

1. Como criou um caderno usando o núcleo PySpark, não precisa de criar nenhum contexto explicitamente. Os contextos do Spark e do Hive serão criados automaticamente quando executa a primeira célula do código. Pode começar por importar os tipos necessários para este cenário. Cole o seguinte corte numa célula vazia e, em seguida, prima **Shift + Enter**.

    ```pyspark
    from pyspark.sql import Row
    from pyspark.sql.types import *
    ```

1. Crie um RDD utilizando os dados de registo de amostra já disponíveis no cluster. Pode aceder aos dados na conta de armazenamento predefinida associada ao cluster em `\HdiSamples\HdiSamples\WebsiteLogSampleData\SampleLog\909f2b.log` . Execute o seguinte código:

    ```pyspark
    logs = sc.textFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log')
    ```

1. Recupere um conjunto de registo de amostra para verificar se o passo anterior foi concluído com sucesso.

    ```pyspark
    logs.take(5)
    ```

    Deve ver uma saída semelhante ao seguinte texto:

    ```output
    [u'#Software: Microsoft Internet Information Services 8.0',
    u'#Fields: date time s-sitename cs-method cs-uri-stem cs-uri-query s-port cs-username c-ip cs(User-Agent) cs(Cookie) cs(Referer) cs-host sc-status sc-substatus sc-win32-status sc-bytes cs-bytes time-taken',
    u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46',
    u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32',
    u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step4.png X-ARR-LOG-ID=4bea5b3d-8ac9-46c9-9b8c-ec3e9500cbea 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 72177 871 47']
    ```

## <a name="analyze-log-data-using-a-custom-python-library"></a>Analise os dados de registo usando uma biblioteca pitão personalizada

1. Na saída acima, as primeiras linhas de casal incluem a informação do cabeçalho e cada linha restante corresponde ao esquema descrito no cabeçalho. Analisar estes troncos pode ser complicado. Assim, usamos uma biblioteca piton personalizada **(iislogparser.py)** que torna a análise desses troncos muito mais fácil. Por predefinição, esta biblioteca está incluída com o seu cluster Spark em HDInsight em `/HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py` .

    No entanto, esta biblioteca não está no `PYTHONPATH` então não podemos usá-la usando uma declaração de importação `import iislogparser` como. Para usar esta biblioteca, devemos distribuí-la a todos os nós operários. Executar o seguinte corte.

    ```pyspark
    sc.addPyFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py')
    ```

1. `iislogparser` fornece uma função `parse_log_line` que retorna `None` se uma linha de log for uma linha de cabeçalho, e devolve uma instância da `LogLine` classe se encontrar uma linha de log. Utilize a `LogLine` classe para extrair apenas as linhas de registo do RDD:

    ```pyspark
    def parse_line(l):
        import iislogparser
        return iislogparser.parse_log_line(l)
    logLines = logs.map(parse_line).filter(lambda p: p is not None).cache()
    ```

1. Recupere algumas linhas de madeira extraídas para verificar se o passo foi concluído com sucesso.

    ```pyspark
    logLines.take(2)
    ```

   A saída deve ser semelhante ao seguinte texto:

    ```output
    [2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46,
    2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32]
    ```

1. A `LogLine` classe, por sua vez, tem alguns métodos úteis, como `is_error()` , que devolve se uma entrada de registo tem um código de erro. Utilize esta classe para calcular o número de erros nas linhas de registo extraídas e, em seguida, registar todos os erros num ficheiro diferente.

    ```pyspark
    errors = logLines.filter(lambda p: p.is_error())
    numLines = logLines.count()
    numErrors = errors.count()
    print 'There are', numErrors, 'errors and', numLines, 'log entries'
    errors.map(lambda p: str(p)).saveAsTextFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b-2.log')
    ```

    A saída deve `There are 30 errors and 646 log entries` indicar.

1. Também pode usar **Matplotlib** para construir uma visualização dos dados. Por exemplo, se quiser isolar a causa dos pedidos que duram durante muito tempo, talvez queira encontrar os ficheiros que demoram mais tempo a servir em média. O snippet abaixo recupera os 25 melhores recursos que demoraram mais tempo a servir um pedido.

    ```pyspark
    def avgTimeTakenByKey(rdd):
        return rdd.combineByKey(lambda line: (line.time_taken, 1),
                                lambda x, line: (x[0] + line.time_taken, x[1] + 1),
                                lambda x, y: (x[0] + y[0], x[1] + y[1]))\
                    .map(lambda x: (x[0], float(x[1][0]) / float(x[1][1])))

    avgTimeTakenByKey(logLines.map(lambda p: (p.cs_uri_stem, p))).top(25, lambda x: x[1])
    ```

   Deve ver uma saída como o seguinte texto:

    ```output
    [(u'/blogposts/mvc4/step13.png', 197.5),
    (u'/blogposts/mvc2/step10.jpg', 179.5),
    (u'/blogposts/extractusercontrol/step5.png', 170.0),
    (u'/blogposts/mvc4/step8.png', 159.0),
    (u'/blogposts/mvcrouting/step22.jpg', 155.0),
    (u'/blogposts/mvcrouting/step3.jpg', 152.0),
    (u'/blogposts/linqsproc1/step16.jpg', 138.75),
    (u'/blogposts/linqsproc1/step26.jpg', 137.33333333333334),
    (u'/blogposts/vs2008javascript/step10.jpg', 127.0),
    (u'/blogposts/nested/step2.jpg', 126.0),
    (u'/blogposts/adminpack/step1.png', 124.0),
    (u'/BlogPosts/datalistpaging/step2.png', 118.0),
    (u'/blogposts/mvc4/step35.png', 117.0),
    (u'/blogposts/mvcrouting/step2.jpg', 116.5),
    (u'/blogposts/aboutme/basketball.jpg', 109.0),
    (u'/blogposts/anonymoustypes/step11.jpg', 109.0),
    (u'/blogposts/mvc4/step12.png', 106.0),
    (u'/blogposts/linq8/step0.jpg', 105.5),
    (u'/blogposts/mvc2/step18.jpg', 104.0),
    (u'/blogposts/mvc2/step11.jpg', 104.0),
    (u'/blogposts/mvcrouting/step1.jpg', 104.0),
    (u'/blogposts/extractusercontrol/step1.png', 103.0),
    (u'/blogposts/sqlvideos/sqlvideos.jpg', 102.0),
    (u'/blogposts/mvcrouting/step21.jpg', 101.0),
    (u'/blogposts/mvc4/step1.png', 98.0)]
    ```

1. Pode também apresentar esta informação sob a forma de enredo. Como um primeiro passo para criar um enredo, vamos primeiro criar uma tabela temporária **AverageTime**. A mesa agru está a agrum os registos a tempo de ver se havia picos de latência incomuns em algum momento específico.

    ```pyspark
    avgTimeTakenByMinute = avgTimeTakenByKey(logLines.map(lambda p: (p.datetime.minute, p))).sortByKey()
    schema = StructType([StructField('Minutes', IntegerType(), True),
                        StructField('Time', FloatType(), True)])

    avgTimeTakenByMinuteDF = sqlContext.createDataFrame(avgTimeTakenByMinute, schema)
    avgTimeTakenByMinuteDF.registerTempTable('AverageTime')
    ```

1. Em seguida, pode executar a seguinte consulta SQL para obter todos os registos na tabela **AverageTime.**

    ```pyspark
    %%sql -o averagetime
    SELECT * FROM AverageTime
    ```

   A `%%sql` magia seguida garante que a saída da consulta é `-o averagetime` persistiu localmente no servidor Jupyter (tipicamente o headnode do cluster). A saída é persistiu como um dataframe [de dados pandas](https://pandas.pydata.org/) com o nome especificado **tempo médio**.

   Deve ver uma saída como a seguinte imagem:

   ![hdinsight jupyter sql saída de consulta](./media/apache-spark-custom-library-website-log-analysis/hdinsight-jupyter-sql-qyery-output.png "Saída de consulta SQL")

   Para obter mais informações sobre a `%%sql` magia, consulte [Parâmetros suportados com a magia %%sql](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

1. Agora pode usar Matplotlib, uma biblioteca usada para construir a visualização de dados, para criar um enredo. Como o enredo deve ser criado a partir do dataframe de **tempo médio** persistido localmente, o corte de código deve começar com a `%%local` magia. Isto garante que o código é executado localmente no servidor Jupyter.

    ```pyspark
    %%local
    %matplotlib inline
    import matplotlib.pyplot as plt

    plt.plot(averagetime['Minutes'], averagetime['Time'], marker='o', linestyle='--')
    plt.xlabel('Time (min)')
    plt.ylabel('Average time taken for request (ms)')
    ```

   Deve ver uma saída como a seguinte imagem:

   ![apache spark web log plano de análise](./media/apache-spark-custom-library-website-log-analysis/hdinsight-apache-spark-web-log-analysis-plot.png "Saída de Matplotlib")

1. Depois de terminar a execução da aplicação, deve desligar o caderno para libertar os recursos. Para tal, no menu **File** (Ficheiro) do bloco de notas, selecione **Close and Halt** (Fechar e Parar). Esta ação vai encerrar e fechar o caderno.

## <a name="next-steps"></a>Passos seguintes

Explore os seguintes artigos:

* [Descrição geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)
* [Use pacotes externos com cadernos Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)
