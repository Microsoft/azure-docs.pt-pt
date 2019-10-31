---
title: Kernels for Jupyter notebook em clusters Spark no Azure HDInsight
description: Saiba mais sobre os kernels PySpark, PySpark3 e Spark para o notebook Jupyter disponível com clusters Spark no Azure HDInsight.
keywords: Notebook jupyter no Spark, jupyter Spark
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/27/2019
ms.openlocfilehash: 41cb27096782f525a531f38efda539c065fa4c72
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73163624"
---
# <a name="kernels-for-jupyter-notebook-on-apache-spark-clusters-in-azure-hdinsight"></a>Kernels for Jupyter notebook em clusters de Apache Spark no Azure HDInsight

Os clusters do HDInsight Spark fornecem kernels que você pode usar com o notebook Jupyter no [Apache Spark](https://spark.apache.org/) para testar seus aplicativos. Um kernel é um programa que executa e interpreta seu código. Os três kernels são:

- **PySpark** -para aplicativos escritos em Python2.
- **PySpark3** -para aplicativos escritos em Python3.
- **Spark** -para aplicativos escritos em escalabilidade.

Neste artigo, você aprenderá a usar esses kernels e os benefícios de usá-los.

## <a name="prerequisites"></a>Pré-requisitos

Um cluster Apache Spark no HDInsight. Para obter instruções, veja [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="create-a-jupyter-notebook-on-spark-hdinsight"></a>Criar um notebook Jupyter no Spark HDInsight

1. No [portal do Azure](https://portal.azure.com/), selecione o cluster Spark.  Consulte [listar e mostrar clusters](../hdinsight-administer-use-portal-linux.md#showClusters) para obter as instruções. A exibição **visão geral** é aberta.

2. No modo de exibição **visão geral** , na caixa **painéis de cluster** , selecione **Notebook Jupyter**. Se lhe for solicitado, introduza as credenciais de administrador do cluster.

    ![Jupyter notebook em Apache Spark](./media/apache-spark-jupyter-notebook-kernels/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Jupyter Notebook no Spark")
  
   > [!NOTE]  
   > Você também pode acessar o notebook Jupyter no cluster do Spark abrindo a seguinte URL em seu navegador. Substitua **CLUSTERNAME** pelo nome do cluster:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

3. Selecione **novo**e, em seguida, selecione **Pyspark**, **PySpark3**ou **Spark** para criar um bloco de anotações. Use o kernel do Spark para aplicativos escalares, kernel PySpark para aplicativos Python2 e kernel PySpark3 para aplicativos Python3.

    ![Kernels for Jupyter Notebook no Spark](./media/apache-spark-jupyter-notebook-kernels/kernel-jupyter-notebook-on-spark.png "Kernels for Jupyter Notebook no Spark")

4. Um notebook é aberto com o kernel selecionado.

## <a name="benefits-of-using-the-kernels"></a>Benefícios de usar os kernels

Aqui estão alguns benefícios de usar os novos kernels com o notebook Jupyter em clusters HDInsight do Spark.

- **Contextos predefinidos**. Com os kernels **PySpark**, **PySpark3**ou **Spark** , você não precisa definir os contextos Spark ou Hive explicitamente antes de começar a trabalhar com seus aplicativos. Eles estão disponíveis por padrão. Esses contextos são:

  - **SC** -para contexto do Spark
  - **sqlContext** -para contexto do hive

    Portanto, você não precisa executar instruções como as seguintes para definir os contextos:

         sc = SparkContext('yarn-client')
         sqlContext = HiveContext(sc)

    Em vez disso, você pode usar diretamente os contextos predefinidos em seu aplicativo.

- **Mágicas de célula**. O kernel PySpark fornece algumas "mágicas" predefinidas, que são comandos especiais que você pode chamar com `%%` (por exemplo, `%%MAGIC` `<args>`). O comando mágico deve ser a primeira palavra em uma célula de código e permitir várias linhas de conteúdo. A palavra mágica deve ser a primeira palavra na célula. Adicionar qualquer coisa antes da mágica, até mesmo comentários, causa um erro.     Para obter mais informações sobre as mágicas, consulte [aqui](https://ipython.readthedocs.org/en/stable/interactive/magics.html).

    A tabela a seguir lista as diferentes mágicas disponíveis por meio dos kernels.

   | Mágica | Exemplo | Descrição |
   | --- | --- | --- |
   | Ajuda |`%%help` |Gera uma tabela de todas as mágicas disponíveis com o exemplo e a descrição |
   | detalhes |`%%info` |Gera informações de sessão para o ponto de extremidade Livy atual |
   | Configurar |`%%configure -f`<br>`{"executorMemory": "1000M"`,<br>`"executorCores": 4`} |Configura os parâmetros para a criação de uma sessão. O sinalizador Force (-f) será obrigatório se uma sessão já tiver sido criada, o que garante que a sessão seja descartada e recriada. Examine o [corpo da solicitação post/Sessions do Livy](https://github.com/cloudera/livy#request-body) para obter uma lista de parâmetros válidos. Os parâmetros devem ser passados como uma cadeia de caracteres JSON e devem estar na próxima linha após a mágica, conforme mostrado na coluna de exemplo. |
   | SQL |`%%sql -o <variable name>`<br> `SHOW TABLES` |Executa uma consulta de Hive em relação ao sqlContext. Se o parâmetro `-o` for passado, o resultado da consulta será persistido no contexto de%% local do Python como um dataframe do [pandas](https://pandas.pydata.org/) . |
   | local |`%%local`<br>`a=1` |Todo o código nas linhas subsequentes é executado localmente. O código deve ser um código Python2 válido, independentemente do kernel que você está usando. Portanto, mesmo que você tenha selecionado os kernels **PySpark3** ou **Spark** ao criar o notebook, se você usar a `%%local` mágica em uma célula, essa célula deverá ter apenas um código Python2 válido. |
   | registos |`%%logs` |Gera os logs para a sessão Livy atual. |
   | delete |`%%delete -f -s <session number>` |Exclui uma sessão específica do ponto de extremidade Livy atual. Não é possível excluir a sessão iniciada para o próprio kernel. |
   | limpeza |`%%cleanup -f` |Exclui todas as sessões do ponto de extremidade Livy atual, incluindo a sessão do bloco de anotações. O sinalizador de força-f é obrigatório. |

   > [!NOTE]  
   > Além das mágicas adicionadas pelo kernel PySpark, você também pode usar as [mágicas ipython internas](https://ipython.org/ipython-doc/3/interactive/magics.html#cell-magics), incluindo `%%sh`. Você pode usar a `%%sh` mágica para executar scripts e blocos de código no cluster cabeçalho.

- **Visualização automática**. O kernel Pyspark visualiza automaticamente a saída de consultas do hive e do SQL. Você pode escolher entre vários tipos diferentes de visualizações, incluindo tabela, pizza, linha, área, barra.

## <a name="parameters-supported-with-the-sql-magic"></a>Parâmetros com suporte com a mágica%% SQL

A `%%sql` mágica dá suporte a parâmetros diferentes que você pode usar para controlar o tipo de saída que você recebe ao executar consultas. A tabela a seguir lista a saída.

| Parâmetro | Exemplo | Descrição |
| --- | --- | --- |
| -o |`-o <VARIABLE NAME>` |Use esse parâmetro para persistir o resultado da consulta, no contexto do Python local%%, como um dataframe do [pandas](https://pandas.pydata.org/) . O nome da variável dataframe é o nome da variável que você especificar. |
| -q |`-q` |Use isso para desativar as visualizações da célula. Se você não quiser autovisualizar o conteúdo de uma célula e apenas desejar capturá-la como um dataframe, use `-q -o <VARIABLE>`. Se você quiser desativar as visualizações sem capturar os resultados (por exemplo, para executar uma consulta SQL, como uma instrução `CREATE TABLE`), use `-q` sem especificar um argumento `-o`. |
| -m |`-m <METHOD>` |Onde o **método** é **Take** ou **Sample** (o padrão é **Take**). Se o método for **necessário**, o kernel selecionará os elementos da parte superior do conjunto de dados de resultado especificado por MAXROWS (descrito mais adiante nesta tabela). Se o método for de **exemplo**, o kernel exemplifica aleatoriamente os elementos do conjunto de dados de acordo com `-r` parâmetro, descrito a seguir nesta tabela. |
| -r |`-r <FRACTION>` |Aqui, **fração** é um número de ponto flutuante entre 0,0 e 1,0. Se o método de exemplo para a consulta SQL for `sample`, o kernel exemplificará aleatoriamente a fração especificada dos elementos do conjunto de resultados para você. Por exemplo, se você executar uma consulta SQL com os argumentos `-m sample -r 0.01`, 1% das linhas de resultado serão amostradas aleatoriamente. |
| -n |`-n <MAXROWS>` |**MAXROWS** é um valor inteiro. O kernel limita o número de linhas de saída para **MAXROWS**. Se **MAXROWS** for um número negativo, como **-1**, o número de linhas no conjunto de resultados não será limitado. |

**Exemplo:**

    %%sql -q -m sample -r 0.1 -n 500 -o query2
    SELECT * FROM hivesampletable

A instrução acima faz o seguinte:

- Seleciona todos os registros de **hivesampletable**.
- Como usamos-q, ela desativa a visualização autovisual.
- Como usamos `-m sample -r 0.1 -n 500` ele obtém aleatoriamente 10% das linhas no hivesampletable e limita o tamanho do conjunto de resultados a 500 linhas.
- Por fim, como usamos `-o query2` ele também salva a saída em um dataframe chamado **query2**.

## <a name="considerations-while-using-the-new-kernels"></a>Considerações ao usar os novos kernels

Seja qual for o kernel usado, deixar os notebooks em execução consome os recursos do cluster.  Com esses kernels, como os contextos são predefinidos, simplesmente sair dos Notebooks não interrompe o contexto e, portanto, os recursos do cluster continuam em uso. Uma prática recomendada é usar a opção **fechar e parar** no menu **arquivo** do bloco de anotações quando terminar de usar o bloco de anotações, o que interrompe o contexto e, em seguida, sai do bloco de anotações.

## <a name="where-are-the-notebooks-stored"></a>Onde os blocos de anotações são armazenados?

Se o cluster usar o armazenamento do Azure como a conta de armazenamento padrão, os blocos de anotações do Jupyter serão salvos na conta de armazenamento na pasta **/HdiNotebooks** .  Os blocos de anotações, os arquivos de texto e as pastas que você cria de dentro do Jupyter podem ser acessados da conta de armazenamento.  Por exemplo, se você usar Jupyter para criar uma pasta **MyFolder** e um notebook **MyFolder/mynotebook. ipynb**, poderá acessar esse notebook em `/HdiNotebooks/myfolder/mynotebook.ipynb` na conta de armazenamento.  O inverso também é verdadeiro, ou seja, se você carregar um bloco de anotações diretamente em sua conta de armazenamento em `/HdiNotebooks/mynotebook1.ipynb`, o notebook também estará visível em Jupyter.  Os blocos de anotações permanecem na conta de armazenamento mesmo depois que o cluster é excluído.

> [!NOTE]  
> Os clusters HDInsight com Azure Data Lake Storage como o armazenamento padrão não armazenam blocos de anotações no armazenamento associado.

A maneira como os blocos de anotações são salvos na conta de armazenamento é compatível com o [HDFS Apache Hadoop](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html). Portanto, se você estiver usando o SSH no cluster, poderá usar comandos de gerenciamento de arquivos, conforme mostrado no trecho a seguir:

    hdfs dfs -ls /HdiNotebooks                            # List everything at the root directory – everything in this directory is visible to Jupyter from the home page
    hdfs dfs –copyToLocal /HdiNotebooks                   # Download the contents of the HdiNotebooks folder
    hdfs dfs –copyFromLocal example.ipynb /HdiNotebooks   # Upload a notebook example.ipynb to the root folder so it’s visible from Jupyter

Independentemente de o cluster usar o armazenamento do Azure ou Azure Data Lake Storage como a conta de armazenamento padrão, os blocos de anotações também são salvos no cabeçalho do cluster em `/var/lib/jupyter`.

## <a name="supported-browser"></a>Browser suportado

Os notebooks Jupyter em clusters HDInsight Spark têm suporte apenas no Google Chrome.

## <a name="feedback"></a>Comentários

Os novos kernels estão em fase de evolução e serão amadureces ao longo do tempo. Isso também pode significar que as APIs poderiam mudar à medida que esses kernels amadureceram. Agradecemos os comentários que você tem ao usar esses novos kernels. Isso é útil para formatar a versão final desses kernels. Você pode deixar comentários/comentários na seção de **comentários** na parte inferior deste artigo.

## <a name="seealso"></a>Ver também

- [Descrição geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Cenários

- [Apache Spark com BI: executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](apache-spark-use-bi-tools.md)
- [Apache Spark com Machine Learning: Use o Spark no HDInsight para analisar a temperatura de edifício usando dados HVAC](apache-spark-ipython-notebook-machine-learning.md)
- [Apache Spark com Machine Learning: Use o Spark no HDInsight para prever os resultados da inspeção de alimentos](apache-spark-machine-learning-mllib-ipython.md)
- [Análise de log do site usando Apache Spark no HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicações

- [Criar uma aplicação autónoma com o Scala](apache-spark-create-standalone-application.md)
- [Executar trabalhos remotamente em um cluster Apache Spark usando o Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões

- [Utilizar o Plug-in das Ferramentas do HDInsight para o IntelliJ IDEA para criar e submeter aplicações do Spark Scala](apache-spark-intellij-tool-plugin.md)
- [Usar o plug-in de ferramentas do HDInsight para IntelliJ IDEA para depurar aplicativos Apache Spark remotamente](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
- [Usar notebooks do Apache Zeppelin com um cluster Apache Spark no HDInsight](apache-spark-zeppelin-notebook.md)
- [Utilizar pacotes externos com blocos de notas do Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
- [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerir recursos

- [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
- [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](apache-spark-job-debugging.md)
