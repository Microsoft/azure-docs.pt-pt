---
title: Kernels para o bloco de notas do Jupyter nos clusters do Spark no Azure HDInsight
description: Saiba mais sobre os kernels do PySpark, PySpark3 e Spark para o bloco de notas do Jupyter disponível com clusters do Spark no HDInsight do Azure.
keywords: Bloco de notas do jupyter no spark, spark do jupyter
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: hrasheed
ms.openlocfilehash: 92ffa02959f020789d14b3bea71763f3f5b9bb47
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58084103"
---
# <a name="kernels-for-jupyter-notebook-on-apache-spark-clusters-in-azure-hdinsight"></a>Kernels para o bloco de notas do Jupyter nos clusters do Apache Spark no Azure HDInsight 

Os clusters do HDInsight Spark fornecem kernels que pode utilizar com o bloco de notas do Jupyter no [Apache Spark](https://spark.apache.org/) para testar as suas aplicações. Um kernel é um programa que é executado e interpreta seu código. Os três kernels são:

- **PySpark** – para aplicações escritas em Python2.
- **PySpark3** – para aplicações escritas em Python3.
- **Spark** – para aplicações escritas no Scala.

Neste artigo, irá aprender a utilizar estas kernels e os benefícios de usá-los.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Spark no HDInsight. Para obter instruções, veja [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="create-a-jupyter-notebook-on-spark-hdinsight"></a>Criar um bloco de notas do Jupyter no HDInsight Spark

1. Partir do [portal do Azure](https://portal.azure.com/), abra o seu cluster.  Ver [listar e Mostrar clusters](../hdinsight-administer-use-portal-linux.md#showClusters) para obter instruções. O cluster é aberto num novo painel do portal.

2. Do **ligações rápidas** secção, clique em **dashboards de clusters** para abrir o **dashboards de clusters** painel.  Se não vir **ligações rápidas**, clique em **descrição geral** no menu à esquerda, no painel.

    ![Bloco de notas do Jupyter no Spark](./media/apache-spark-jupyter-notebook-kernels/hdinsight-jupyter-notebook-on-spark.png "bloco de notas do Jupyter no Spark") 

3. Clique em **bloco de notas do Jupyter**. Se lhe for solicitado, introduza as credenciais de administrador do cluster.
   
   > [!NOTE]  
   > Também pode aceder ao bloco de notas do Jupyter no cluster do Spark ao abrir o URL seguinte no seu browser. Substitua **CLUSTERNAME** pelo nome do cluster:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`


3. Clique em **New**e, em seguida, clique em **Pyspark**, **PySpark3**, ou **Spark** para criar um bloco de notas. Utilize o kernel do Spark para aplicativos de Scala, kernel do PySpark para aplicativos de Python2 e PySpark3 kernel para aplicações de Python3.
   
    ![Kernels para o bloco de notas do Jupyter no Spark](./media/apache-spark-jupyter-notebook-kernels/kernel-jupyter-notebook-on-spark.png "Kernels para o bloco de notas do Jupyter no Spark") 

4. Um bloco de notas abre-se com o kernel que selecionou.

## <a name="benefits-of-using-the-kernels"></a>Vantagens de utilizar os kernels

Aqui estão alguns benefícios de usar os kernels novo com o bloco de notas do Jupyter nos clusters do HDInsight Spark.

- **Configuração predefinida contextos**. Com o **PySpark**, **PySpark3**, ou o **Spark** kernels, não precisa de definir os contextos do Spark ou do Hive explicitamente antes de começar a trabalhar com as suas aplicações. Estas estão disponíveis por predefinição. Desses contextos são:
   
  * **sc** – para o contexto do Spark
  * **kontext sqlContext** – para o contexto do Hive
   
    Por isso, não precisa de instruções de como o seguinte para definir os contextos de execução:
   
         sc = SparkContext('yarn-client')
         sqlContext = HiveContext(sc)
   
    Em vez disso, pode usar diretamente os contextos predefinidos em seu aplicativo.

- **Magia de células**. O kernel do PySpark fornece alguns predefinidas "magia", que são comandos especiais que pode chamar com `%%` (por exemplo, `%%MAGIC` <args>). O comando mágico tem de ser a primeira palavra numa célula de código e permitir várias linhas de conteúdo. A palavra mágica deve ser a primeira palavra na célula. Adicionar qualquer coisa antes da mágica, até mesmo comentários, causa um erro.     Para obter mais informações sobre a magia, consulte [aqui](https://ipython.readthedocs.org/en/stable/interactive/magics.html).
   
    A tabela seguinte lista a magia de diferentes disponíveis por meio dos kernels.

   | Magic | Exemplo | Descrição |
   | --- | --- | --- |
   | ajuda |`%%help` |Gera uma tabela de todos os a magia disponíveis com o exemplo e uma descrição |
   | informações |`%%info` |Informações de sessão de saídas para o ponto de final atual do Livy |
   | configurar |`%%configure -f`<br>`{"executorMemory": "1000M"`,<br>`"executorCores": 4`} |Configura os parâmetros para a criação de uma sessão. O sinalizador force (-f) é obrigatório se a sessão já foi criada, que garante que a sessão é removida e recriada. Examinar [/sessions POST de Livy corpo do pedido](https://github.com/cloudera/livy#request-body) para obter uma lista de parâmetros válidos. Parâmetros devem ser passados como uma cadeia de caracteres do JSON e tem de ser na próxima linha posterior a mágica, conforme mostrado na coluna de exemplo. |
   | sql |`%%sql -o <variable name>`<br> `SHOW TABLES` |Executa uma consulta do Hive contra o kontext sqlContext. Se o `-o` parâmetro é transmitido, o resultado da consulta é mantido no % % contexto Python local como uma [Pandas](https://pandas.pydata.org/) dataframe. |
   | local |`%%local`<br>`a=1` |Todo o código nas linhas subsequentes é executado localmente. Código tem de ser código Python2 válido até mesmo, independentemente do kernel que estiver a utilizar. Assim, mesmo se tiver selecionado **PySpark3** ou **Spark** kernels ao criar o bloco de notas, se usar o `%%local` mágica numa célula, essa célula tem apenas de ter código de Python2 válido.... |
   | registos |`%%logs` |Devolve os registos para a sessão atual do Livy. |
   | delete |`%%delete -f -s <session number>` |Elimina uma sessão específica do ponto de extremidade Livy atual. Não é possível eliminar a sessão iniciada para o kernel propriamente dito. |
   | limpeza |`%%cleanup -f` |Elimina todas as sessões para o ponto de extremidade Livy atual, incluindo sessão este bloco de notas. O sinalizador -f força é obrigatório. |

   > [!NOTE]  
   > Além de magia adicionada pelo kernel do PySpark, também pode utilizar o [incorporada IPython magia](https://ipython.org/ipython-doc/3/interactive/magics.html#cell-magics), incluindo `%%sh`. Pode utilizar o `%%sh` mágica para executar scripts e o bloco de código no nó principal do cluster.

1. **Visualização de auto**. O **Pyspark** kernel automaticamente visualiza a saída das consultas do Hive e do SQL. Pode escolher entre vários tipos diferentes de visualizações, incluindo tabela, a circular, a linha, a área, a barra.

## <a name="parameters-supported-with-the-sql-magic"></a>Parâmetros suportados com o % % "sql Magic"
O `%%sql` mágica oferece suporte a parâmetros diferentes que pode utilizar para controlar o tipo de saída que recebe quando executar consultas. A tabela seguinte lista o resultado.

| Parâmetro | Exemplo | Descrição |
| --- | --- | --- |
| -o |`-o <VARIABLE NAME>` |Utilize este parâmetro para manter o resultado da consulta, na % % contexto de Python local, como um [Pandas](https://pandas.pydata.org/) dataframe. O nome da variável dataframe é o nome da variável que especificar. |
| -q |`-q` |Utilize esta opção para desativar as visualizações da célula. Se não quiser auto-visualize o conteúdo de uma célula e quiser apenas capturá-la como um pacote de dados, em seguida, utilize `-q -o <VARIABLE>`. Se pretender desativar visualizações sem capturar os resultados (por exemplo, para executar uma consulta SQL, como um `CREATE TABLE` instrução), utilize `-q` sem especificar um `-o` argumento. |
| -m |`-m <METHOD>` |Em que **método** está **tirar** ou **exemplo** (a predefinição é **tirar**). Se o método for **tirar**, o kernel escolhe elementos na parte superior do conjunto de dados de resultado especificado pelo MAXROWS (descrito mais adiante nesta tabela). Se o método for **amostra**, o kernel aleatoriamente amostras de elementos de conjunto de dados de acordo com a `-r` parâmetro, descrito a seguir nesta tabela. |
| -r |`-r <FRACTION>` |Aqui **FRAÇÃO** é um número de vírgula flutuante entre 0,0 e 1,0. Se o método de exemplo para a consulta SQL é `sample`, em seguida, o kernel exemplos aleatoriamente a fração especificada de elementos do conjunto de resultados para. Por exemplo, se executar uma consulta SQL com os argumentos `-m sample -r 0.01`, em seguida, 1% das linhas resultado aleatoriamente são objeto de amostragem. |
| -n |`-n <MAXROWS>` |**MAXROWS** é um valor inteiro. O kernel limita o número de linhas de saída a **MAXROWS**. Se **MAXROWS** é um número negativo, tal como **-1**, em seguida, não se limita o número de linhas no conjunto de resultados. |

**Exemplo:**

    %%sql -q -m sample -r 0.1 -n 500 -o query2
    SELECT * FROM hivesampletable

A instrução anterior faz o seguinte:

* Seleciona todos os registos da **hivesampletable**.
* Uma vez que utilizamos - q, desativa auto-visualização.
* Uma vez que utilizamos `-m sample -r 0.1 -n 500` amostras de 10% das linhas no hivesampletable aleatoriamente e limita o tamanho do resultado definido como 500 linhas.
* Por fim, porque usamos `-o query2` também salva a saída para um dataframe chamado **query2**.

## <a name="considerations-while-using-the-new-kernels"></a>Considerações ao utilizar os kernels novo

Qualquer que seja kernel utilizar, deixar os blocos de notas em execução consome os recursos do cluster.  Com estas kernels, uma vez que os contextos são a configuração predefinidos, simplesmente sair os blocos de notas não interrompe o contexto e, por conseguinte, os recursos do cluster continuam a ser utilizados. Uma boa prática é utilizar o **fechar e parar** opção do bloco de notas **ficheiro** menu quando terminar de usar o bloco de notas, o que interrompe o contexto e, em seguida, fecha o bloco de notas.     

## <a name="show-me-some-examples"></a>Mostre-me alguns exemplos

Quando abrir um bloco de notas do Jupyter, verá duas pastas disponíveis no nível de raiz.

* O **PySpark** pasta tem de blocos de notas de exemplo que utilizam a nova **Python** kernel.
* O **Scala** pasta tem de blocos de notas de exemplo que utilizam a nova **Spark** kernel.

Pode abrir o **00 - recursos de Kernel da mágica Spark [leia-ME primeiro]** bloco de notas do **PySpark** ou **Spark** pasta para saber mais sobre a magia de diferentes disponíveis. Também pode utilizar os outros exemplo blocos de notas disponíveis em duas pastas para saber como atingir diferentes cenários usando blocos de notas do Jupyter com clusters do HDInsight Spark.

## <a name="where-are-the-notebooks-stored"></a>Onde estão armazenados os blocos de notas?

Se o cluster utiliza armazenamento do Azure como a conta de armazenamento predefinida, blocos de notas do Jupyter são guardados para a conta de armazenamento sob a **/HdiNotebooks** pasta.  Blocos de notas, ficheiros de texto e pastas que criar a partir de dentro do Jupyter são acessíveis a partir da conta de armazenamento.  Por exemplo, se utilizar o Jupyter para criar uma pasta **myfolder** e um bloco de notas **myfolder/mynotebook.ipynb**, pode aceder ao bloco de anotações em `/HdiNotebooks/myfolder/mynotebook.ipynb` na conta de armazenamento.  O inverso também é verdade, ou seja, se carregar um bloco de notas diretamente à sua conta de armazenamento em `/HdiNotebooks/mynotebook1.ipynb`, o bloco de notas é também visível a partir do Jupyter.  Blocos de notas, permanecem na conta de armazenamento, mesmo depois do cluster é eliminado.

> [!NOTE]  
> Clusters do HDInsight com o armazenamento do Azure Data Lake como armazenamento predefinido não armazenar blocos de notas no armazenamento associado.

A forma como os blocos de notas são guardados para a conta de armazenamento é compatível com [Apache Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html). Deste modo, se SSH para o cluster que pode utilizar comandos de gestão de ficheiros conforme mostrado no seguinte fragmento:

    hdfs dfs -ls /HdiNotebooks                               # List everything at the root directory – everything in this directory is visible to Jupyter from the home page
    hdfs dfs –copyToLocal /HdiNotebooks                    # Download the contents of the HdiNotebooks folder
    hdfs dfs –copyFromLocal example.ipynb /HdiNotebooks   # Upload a notebook example.ipynb to the root folder so it’s visible from Jupyter

Independentemente se o cluster utiliza armazenamento do Azure ou o armazenamento do Azure Data Lake como a conta de armazenamento predefinida, os blocos de notas também são guardados no nó principal do cluster em `/var/lib/jupyter`.

## <a name="supported-browser"></a>Browser suportado

Blocos de notas do Jupyter nos clusters do Spark HDInsight são suportados apenas no Google Chrome.

## <a name="feedback"></a>Comentários
Os kernels novos são na fase de evolução e serão amadurecer ao longo do tempo. Isso pode significar que APIs poderia alterar como estes kernels madura. Agradecemos os comentários que precisa ao utilizar estes kernels de novo. Isto é útil na formatação a versão final destes kernels. Pode deixar os seus comentários/comentários sob o **comentários** seção na parte inferior deste artigo.

## <a name="seealso"></a>Ver também
* [Descrição geral: Apache Spark no HDInsight do Azure](apache-spark-overview.md)

### <a name="scenarios"></a>Cenários
* [Apache Spark com BI: Efetuar análise de dados interativa com o Spark no HDInsight com ferramentas de BI](apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: Utilizar o Spark no HDInsight para analisar a temperatura de construção com dados de AVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: Utilizar o Spark no HDInsight para prever resultados de inspeções alimentares](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de registos de Web site com o Apache Spark no HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicações
* [Criar uma aplicação autónoma com o Scala](apache-spark-create-standalone-application.md)
* [Executar tarefas remotamente num cluster do Apache Spark com o Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Utilizar o Plug-in das Ferramentas do HDInsight para o IntelliJ IDEA para criar e submeter aplicações do Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Utilizar o plug-in ferramentas do HDInsight para o IntelliJ IDEA para depurar aplicações do Apache Spark remotamente](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utilizar blocos de notas do Zeppelin do Apache com um cluster do Apache Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Utilizar pacotes externos com blocos de notas do Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerir recursos
* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](apache-spark-job-debugging.md)
