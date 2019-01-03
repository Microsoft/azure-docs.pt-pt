---
title: Ação de script - pacotes de instalar o Python com o Jupyter no Azure HDInsight
description: Instruções passo a passo sobre como utilizar a ação de script para configurar disponíveis da blocos de notas do Jupyter com clusters do Spark do HDInsight para utilizar pacotes externos python.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: f804cfd693a37099edc22e7f4861d6d7e1af0fc7
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/20/2018
ms.locfileid: "53651118"
---
# <a name="use-script-action-to-install-external-python-packages-for-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>Utilize a ação de Script para instalar pacotes de Python externos para blocos de notas do Jupyter nos clusters do Apache Spark no HDInsight
> [!div class="op_single_selector"]
> * [Magia de células](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Com a ação de Script](apache-spark-python-package-installation.md)

Saiba como utilizar as ações de Script para configurar uma [Apache Spark](https://spark.apache.org/) cluster no HDInsight (Linux) para utilizar externo, fornecidos pela Comunidade **python** pacotes que não são incluídos out of box, no cluster.

> [!NOTE]  
> Também pode configurar um bloco de notas do Jupyter com o `%%configure` mágica para utilizar pacotes externos. Para obter instruções, consulte [utilizar pacotes externos com blocos de notas do Jupyter nos clusters do Apache Spark no HDInsight](apache-spark-jupyter-notebook-use-external-packages.md).

Pode pesquisar o [índice de pacote](https://pypi.python.org/pypi) para obter a lista completa de pacotes que estão disponíveis. Também pode obter uma lista dos pacotes disponíveis a partir de outras origens. Por exemplo, pode instalar disponibilizados por meio de pacotes [uma falsificação conda](https://conda-forge.org/feedstocks/).

Neste artigo, irá aprender a instalar o [TensorFlow](https://www.tensorflow.org/) com a ação de Script no seu cluster do pacote e usá-lo por meio do bloco de notas do Jupyter como exemplo.

## <a name="prerequisites"></a>Pré-requisitos
Tem de ter o seguinte:

* Uma subscrição do Azure. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Um cluster do Apache Spark no HDInsight. Para obter instruções, veja [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).

   > [!NOTE]  
   > Se ainda não tiver um cluster do Spark no HDInsight Linux, pode executar ações de script durante a criação do cluster. Visite a documentação sobre [como utilizar as ações de script personalizado](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).
   
## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Suporte para o software de código aberto utilizada nos clusters do HDInsight

O serviço do Microsoft Azure HDInsight utiliza um ecossistema de tecnologias de open source formado em torno do Apache Hadoop. Microsoft Azure fornece um nível geral de suporte para tecnologias de código aberto. Para obter mais informações, consulte a **âmbito de suporte** secção a [site FAQ de suporte do Azure](https://azure.microsoft.com/support/faq/). O serviço HDInsight fornece um nível adicional de suporte para componentes internos.

Existem dois tipos de componentes de código-fonte aberto que estão disponíveis no serviço HDInsight:

* **Componentes internos** -esses componentes estão pré-instaladas em clusters do HDInsight e fornecer a funcionalidade principal do cluster. Por exemplo, Apache Hadoop YARN Resource Manager, a linguagem de consulta (HiveQL) Apache Hive e a biblioteca Mahout pertencem a essa categoria. Uma lista completa dos componentes de cluster está disponível no [quais são as novidades nas versões de cluster do Apache Hadoop fornecidas pelo HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning).
* **Componentes personalizados** -, como um utilizador do cluster, pode instalar ou utilizar na sua carga de trabalho qualquer componente disponível na Comunidade ou criado por si.

> [!WARNING]   
> Componentes fornecidos com o cluster do HDInsight são totalmente suportadas. Support da Microsoft ajuda a isolar e resolver problemas relacionados com esses componentes.
>
> Componentes personalizados recebem suporte comercialmente razoável para ajudá-lo a resolver o problema. Suporte da Microsoft poderá conseguir resolver o problema ou eles poderão pedir-lhe para interagir com os canais disponíveis para as tecnologias de código-fonte aberto, onde os conhecimentos aprofundados para essa tecnologia é encontrado. Por exemplo, há muitos sites de Comunidade que podem ser utilizados, como: [Fórum do MSDN para o HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight), [ https://stackoverflow.com ](https://stackoverflow.com). Também projetos Apache tem sites de projeto no [ https://apache.org ](https://apache.org), por exemplo: [Hadoop](https://hadoop.apache.org/).


## <a name="use-external-packages-with-jupyter-notebooks"></a>Utilizar pacotes externos com blocos de notas do Jupyter

1. No [Portal do Azure](https://portal.azure.com/), no startboard, clique no mosaico relativo ao cluster do Spark (se o tiver afixado ao startboard). Também pode navegar até ao cluster em **Procurar Tudo** > **Clusters do HDInsight**.   

2. No painel do cluster do Spark, clique em **ações de Script** no painel à esquerda. Utilize o tipo de script "Personalizado" e introduza um nome amigável para a ação de script. Execute o script no **nós principais e de trabalho** e deixar o campo de parâmetros em branco. O script de bash pode ser referenciado a partir de: https://hdiconfigactions.blob.core.windows.net/linuxtensorflow/tensorflowinstall.sh Visite a documentação sobre [como utilizar as ações de script personalizado](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).

   > [!NOTE]  
   > Existem dois python instalações no cluster. Spark irá utilizar a instalação do python Anaconda localizada em `/usr/bin/anaconda/bin` e será predefinido para o ambiente de Python 2.7. Para utilizar pacotes de Python 3.x e instalar no kernel do PySpark3, utilize o caminho para o `conda` executável para esse ambiente e use o `-n` parâmetro para especificar o ambiente. Por exemplo, o comando `/usr/bin/anaconda/envs/py35/bin/conda install -c conda-forge ggplot -n py35`, instala o `ggplot` pacote para o ambiente de Python 3.5 com o `conda-forge` canal.

3. Abra um bloco de notas do Jupyter do PySpark

    ![Criar um novo bloco de notas Jupyter](./media/apache-spark-python-package-installation/hdinsight-spark-create-notebook.png "Criar um novo bloco de notas Jupyter")

4. É criado e aberto um novo bloco de notas com o nome Untitled.pynb. Clique no nome do bloco de notas na parte superior e introduza um nome amigável.

    ![Forneça um nome para o bloco de notas](./media/apache-spark-python-package-installation/hdinsight-spark-name-notebook.png "Forneça um nome para o bloco de notas")

5. Irá agora `import tensorflow` e executar um exemplo do hello world. 

    Código para copiar:

        import tensorflow as tf
        hello = tf.constant('Hello, TensorFlow!')
        sess = tf.Session()
        print(sess.run(hello))

    O resultado tem o seguinte aspeto:
    
    ![Execução de código do TensorFlow](./media/apache-spark-python-package-installation/execution.png "TensorFlow executar código")

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
* [Utilizar pacotes externos com blocos de notas do Jupyter nos clusters do Apache Spark no HDInsight](apache-spark-jupyter-notebook-use-external-packages.md)
* [Utilizar o Plug-in das Ferramentas do HDInsight para o IntelliJ IDEA para criar e submeter aplicações do Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Utilizar o plug-in ferramentas do HDInsight para o IntelliJ IDEA para depurar aplicações do Apache Spark remotamente](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utilizar blocos de notas do Zeppelin do Apache com um cluster do Apache Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o bloco de notas do Jupyter no cluster do Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerir recursos
* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](apache-spark-job-debugging.md)
