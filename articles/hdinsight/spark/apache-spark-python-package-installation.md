---
title: Ação de script para pacotes do Python com Jupyter no Azure HDInsight
description: Instruções passo a passo sobre como usar a ação de script para configurar os blocos de anotações do Jupyter disponíveis com clusters do HDInsight Spark para usar pacotes python externos.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/22/2019
ms.openlocfilehash: f80486758152c002762bbddd6ae97a2ce9468ccf
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2019
ms.locfileid: "73241507"
---
# <a name="script-action-to-install-external-python-packages-for-jupyter-notebooks-in-apache-spark-on-hdinsight"></a>Ação de script para instalar pacotes python externos para notebooks Jupyter no Apache Spark no HDInsight

> [!div class="op_single_selector"]
> * [Usando a mágica da célula](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Usando a ação de script](apache-spark-python-package-installation.md)

Saiba como usar ações de script para configurar um cluster de [Apache Spark](https://spark.apache.org/) no HDInsight para usar pacotes **Python** externos e contribuídos pela Comunidade que não estão incluídos de forma integrada no cluster.

> [!NOTE]  
> Você também pode configurar um notebook Jupyter usando `%%configure` mágica para usar pacotes externos. Para obter instruções, consulte [usar pacotes externos com notebooks Jupyter em clusters Apache Spark no HDInsight](apache-spark-jupyter-notebook-use-external-packages.md).

Você pode pesquisar o [índice do pacote](https://pypi.python.org/pypi) para obter a lista completa de pacotes que estão disponíveis. Você também pode obter uma lista de pacotes disponíveis de outras fontes. Por exemplo, você pode instalar pacotes disponibilizados por meio [de Conda](https://conda-forge.org/feedstocks/).

Neste artigo, você aprende a instalar o pacote [TensorFlow](https://www.tensorflow.org/) usando a ação de script em seu cluster e usá-lo por meio do notebook Jupyter como exemplo.

## <a name="prerequisites"></a>Pré-requisitos
Você deve ter o seguinte:

* Uma subscrição do Azure. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Um cluster do Apache Spark no HDInsight. Para obter instruções, veja [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).

   > [!NOTE]  
   > Se você ainda não tiver um cluster Spark no HDInsight Linux, poderá executar ações de script durante a criação do cluster. Visite a documentação sobre [como usar ações de script personalizado](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).
   
## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Suporte para software livre usado em clusters HDInsight

O serviço de Microsoft Azure HDInsight usa um ecossistema de tecnologias de software livre, formadas sobre Apache Hadoop. O Microsoft Azure fornece um nível geral de suporte para tecnologias de software livre. Para obter mais informações, consulte a seção **escopo de suporte** do [site de perguntas frequentes do suporte do Azure](https://azure.microsoft.com/support/faq/). O serviço HDInsight fornece um nível adicional de suporte para componentes internos.

Há dois tipos de componentes de código-fonte aberto que estão disponíveis no serviço HDInsight:

* **Componentes internos** -esses componentes são pré-instalados em clusters HDInsight e fornecem a funcionalidade básica do cluster. Por exemplo, Apache Hadoop ResourceManager YARN, a linguagem de consulta de Apache Hive (HiveQL) e a biblioteca Mahout pertencem a essa categoria. Uma lista completa de componentes de cluster está disponível em [novidades nas versões de cluster apache Hadoop fornecidas pelo HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning).
* **Componentes personalizados** – você, como um usuário do cluster, pode instalar ou usar em sua carga de trabalho qualquer componente disponível na Comunidade ou criado por você.

> [!IMPORTANT]   
> Há suporte total para os componentes fornecidos com o cluster HDInsight. Suporte da Microsoft ajuda a isolar e resolver problemas relacionados a esses componentes.
>
> Os componentes personalizados recebem suporte comercialmente razoável para ajudá-lo a solucionar o problema. O suporte da Microsoft pode ser capaz de resolver o problema ou pode pedir que você envolva canais disponíveis para as tecnologias de software livre em que a profunda experiência para essa tecnologia é encontrada. Por exemplo, há muitos sites de comunidade que podem ser usados, como o [Fórum do MSDN para HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight), [https://stackoverflow.com](https://stackoverflow.com). Além disso, os projetos do Apache têm sites de projeto em [https://apache.org](https://apache.org), por exemplo: [Hadoop](https://hadoop.apache.org/).


## <a name="use-external-packages-with-jupyter-notebooks"></a>Utilizar pacotes externos com blocos de notas do Jupyter

1. No [portal do Azure](https://portal.azure.com/), navegue até o cluster.  

2. Com o cluster selecionado, no painel esquerdo, em **configurações**, selecione **ações de script**.

3. Selecione **+ Enviar novo**.

4. Insira os seguintes valores para a janela **Enviar ação de script** :  


    |Parâmetro | Valor |
    |---|---|
    |Tipo de script | Selecione **-personalizado** na lista suspensa.|
    |Nome |Insira `tensorflow` na caixa de texto.|
    |URI do script bash |Insira `https://hdiconfigactions.blob.core.windows.net/linuxtensorflow/tensorflowinstall.sh` na caixa de texto. |
    |Tipo (s) de nó | Marque as caixas de seleção **cabeçalho**e **trabalhador** . |

    `tensorflowinstall.sh` contém os seguintes comandos:

    ```bash
    #!/usr/bin/env bash
    /usr/bin/anaconda/bin/conda install --yes tensorflow
    ```

5. Selecione **Criar**.  Visite a documentação sobre [como usar ações de script personalizado](../hdinsight-hadoop-customize-cluster-linux.md).

6. Aguarde a conclusão do script.  O painel **ações de script** irá declarar **novas ações de script podem ser enviadas depois que a operação de cluster atual for concluída** enquanto o script estiver em execução.  Uma barra de progresso pode ser exibida na janela de **operações de segundo plano** da interface do usuário do Ambari.

7. Abra um notebook PySpark Jupyter.  Consulte [criar um notebook Jupyter no Spark HDInsight](./apache-spark-jupyter-notebook-kernels.md#create-a-jupyter-notebook-on-spark-hdinsight) para obter as etapas.

    ![Criar um novo notebook Jupyter](./media/apache-spark-python-package-installation/hdinsight-spark-create-notebook.png "Criar um novo bloco de notas do Jupyter")

8. Agora, você `import tensorflow` e executará um exemplo do Hello World. Introduza o seguinte código:

    ```
    import tensorflow as tf
    hello = tf.constant('Hello, TensorFlow!')
    sess = tf.Session()
    print(sess.run(hello))
    ```

    O resultado é semelhante a este:
    
    ![Execução de código TensorFlow](./media/apache-spark-python-package-installation/tensorflow-execution.png "Executar código TensorFlow")

> [!NOTE]  
> Há duas instalações do Python no cluster. O Spark usará a instalação do Python Anaconda localizada em `/usr/bin/anaconda/bin` e usará como padrão o ambiente Python 2,7. Para usar o Python 3. x e instalar pacotes no kernel do PySpark3, use o caminho para o `conda` executável desse ambiente e use o parâmetro `-n` para especificar o ambiente. Por exemplo, o comando `/usr/bin/anaconda/envs/py35/bin/conda install -c conda-forge ggplot -n py35`, instala o pacote de `ggplot` no ambiente Python 3,5 usando o canal de `conda-forge`.

## <a name="seealso"></a>Ver também
* [Descrição geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Cenários
* [Apache Spark com BI: executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: Use o Spark no HDInsight para analisar a temperatura de edifício usando dados HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: Use o Spark no HDInsight para prever os resultados da inspeção de alimentos](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de log do site usando Apache Spark no HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicações
* [Criar uma aplicação autónoma com o Scala](apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster Apache Spark usando o Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Usar pacotes externos com blocos de anotações do Jupyter em clusters Apache Spark no HDInsight](apache-spark-jupyter-notebook-use-external-packages.md)
* [Utilizar o Plug-in das Ferramentas do HDInsight para o IntelliJ IDEA para criar e submeter aplicações do Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Usar o plug-in de ferramentas do HDInsight para IntelliJ IDEA para depurar aplicativos Apache Spark remotamente](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usar notebooks do Apache Zeppelin com um cluster Apache Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o Jupyter Notebook no cluster Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerir recursos
* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](apache-spark-job-debugging.md)
