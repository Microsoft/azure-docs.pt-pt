---
title: Ação de script para pacotes Python com Jupyter no Azure HDInsight
description: Instruções passo a passo sobre como usar a ação do script para configurar os cadernos Jupyter disponíveis com clusters HDInsight Spark para usar pacotes de pitão externos.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: 98326d23f5aca1264bc47168cc25b427c3db331d
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2020
ms.locfileid: "79135960"
---
# <a name="safely-manage-python-environment-on-azure-hdinsight-using-script-action"></a>Gerir com segurança o ambiente do Python no Azure HDInsight com a Ação de Script

> [!div class="op_single_selector"]
> * [Usando magia celular](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Usando a ação do script](apache-spark-python-package-installation.md)

O HDInsight tem duas instalações de Python incorporadas no cluster Spark, Anaconda Python 2.7 e Python 3.5. Em alguns casos, os clientes precisam personalizar o ambiente Python, como instalar pacotes externos python ou outra versão Python. Neste artigo, mostramos as melhores práticas de gerir com segurança os ambientes Python para um cluster [Apache Spark](https://spark.apache.org/) no HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Veja [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Um cluster do Apache Spark no HDInsight. Para obter instruções, veja [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).

   > [!NOTE]  
   > Se ainda não tiver um cluster Spark no HDInsight Linux, pode executar ações de script durante a criação de cluster. Visite a documentação sobre [como usar ações personalizadas](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)de script.

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Suporte para software de código aberto utilizado em clusters HDInsight

O serviço Microsoft Azure HDInsight utiliza um ecossistema de tecnologias de código aberto formadas em torno do Apache Hadoop. O Microsoft Azure fornece um nível geral de suporte para tecnologias de código aberto. Para mais informações, consulte o [site da Azure Support FAQ](https://azure.microsoft.com/support/faq/). O serviço HDInsight fornece um nível adicional de suporte para componentes incorporados.

Existem dois tipos de componentes de código aberto que estão disponíveis no serviço HDInsight:

* **Componentes incorporados** - Estes componentes são pré-instalados em clusters HDInsight e fornecem a funcionalidade central do cluster. Por exemplo, Apache Hadoop YARN Resource Manager, a linguagem de consulta da Colmeia Apache (HiveQL), e a biblioteca Mahout pertencem a esta categoria. Uma lista completa de componentes de cluster está disponível nas [versões do cluster Apache Hadoop fornecidas pela HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning).
* **Componentes personalizados** - Você, como utilizador do cluster, pode instalar ou utilizar na sua carga de trabalho qualquer componente disponível na comunidade ou criado por si.

> [!IMPORTANT]
> Os componentes fornecidos com o cluster HDInsight são totalmente suportados. O Microsoft Support ajuda a isolar e resolver problemas relacionados com estes componentes.
>
> Os componentes personalizados recebem suporte comercialmente razoável para ajudá-lo a resolver o problema. O suporte da Microsoft pode ser capaz de resolver o problema OU pode pedir-lhe para contratar canais disponíveis para as tecnologias de código aberto onde se encontra uma profunda experiência para essa tecnologia. Por exemplo, existem muitos sites comunitários que podem ser usados, como: [Fórum MSDN para HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight), [https://stackoverflow.com](https://stackoverflow.com). Também os projetos Apache têm sites de projetos em [https://apache.org, ](https://apache.org)por exemplo: [Hadoop](https://hadoop.apache.org/).

## <a name="understand-default-python-installation"></a>Compreender a instalação padrão python

O cluster HDInsight Spark é criado com a instalação Anaconda. Existem duas instalações Python no cluster, Anaconda Python 2.7 e Python 3.5. A tabela abaixo mostra as definições padrão de Python para Spark, Livy e Jupyter.

| |Python 2.7|Python 3.5|
|----|----|----|
|Caminho|/usr/bin/anaconda/bin|/usr/bin/anaconda/envs/py35/bin|
|Spark|Predefinido para 2.7|N/D|
|Livy|Predefinido para 2.7|N/D|
|Jupyter|Núcleo pySpark|Núcleo PySpark3|

## <a name="safely-install-external-python-packages"></a>Instale com segurança pacotes de Python externos

O cluster HDInsight depende do ambiente python incorporado, tanto Python 2.7 como Python 3.5. Instalar diretamente pacotes personalizados nesses ambientes incorporados por defeito pode causar alterações inesperadas na versão da biblioteca e quebrar ainda mais o cluster. Para instalar com segurança pacotes externos de Python para as suas aplicações Spark, siga abaixo dos passos.

1. Crie o ambiente virtual Python usando conda. Um ambiente virtual proporciona um espaço isolado para os seus projetos sem quebrar outros. Ao criar o ambiente virtual Python, pode especificar a versão python que pretende utilizar. Note que ainda precisa de criar um ambiente virtual, mesmo que queira utilizar Python 2.7 e 3.5. Isto é para garantir que o ambiente padrão do cluster não se quebre. Execute as ações de script no seu cluster para todos os nós com script abaixo para criar um ambiente virtual Python. 

    -   `--prefix` especifica um caminho onde vive um ambiente virtual conda. Há vários configs que precisam de ser alterados com base no caminho aqui especificado. Neste exemplo, usamos o py35new, uma vez que o cluster já tem um ambiente virtual existente chamado py35.
    -   `python=` especifica a versão Python para o ambiente virtual. Neste exemplo, utilizamos a versão 3.5, a mesma versão que o cluster construído numa. Também pode usar outras versões Python para criar o ambiente virtual.
    -   `anaconda` especifica a package_spec como anaconda para instalar pacotes Anaconda em ambiente virtual.
    
    ```bash
    sudo /usr/bin/anaconda/bin/conda create --prefix /usr/bin/anaconda/envs/py35new python=3.5 anaconda --yes 
    ```

2. Instale pacotes python externos no ambiente virtual criado, se necessário. Execute as ações de script no seu cluster para todos os nós com script abaixo para instalar pacotes python externos. Precisa de ter privilégio sudo aqui para escrever ficheiros para a pasta do ambiente virtual.

    Pode pesquisar o índice de [pacotes](https://pypi.python.org/pypi) para obter a lista completa de pacotes disponíveis. Também pode obter uma lista de pacotes disponíveis de outras fontes. Por exemplo, pode instalar pacotes disponibilizados através [da forja de conda](https://conda-forge.org/feedstocks/).

    Utilize abaixo o comando se quiser instalar uma biblioteca com a sua versão mais recente:
    
    - Utilize o canal de condomínio:

        -   `seaborn` é o nome do pacote que gostaria de instalar.
        -   `-n py35new` especificar o nome do ambiente virtual que acaba de ser criado. Certifique-se de que altera o nome correspondentemente com base na sua criação de ambiente virtual.

        ```bash
        sudo /usr/bin/anaconda/bin/conda install seaborn -n py35new --yes
        ```

    - Ou use repo PyPi, troque `seaborn` e `py35new` correspondentemente:
        ```bash
        sudo /usr/bin/anaconda/env/py35new/bin/pip install seaborn
        ```        

    Utilize abaixo o comando se quiser instalar uma biblioteca com uma versão específica:

    - Utilize o canal de condomínio:

        -   `numpy=1.16.1` é o nome e a versão do pacote que gostaria de instalar.
        -   `-n py35new` especificar o nome do ambiente virtual que acaba de ser criado. Certifique-se de que altera o nome correspondentemente com base na sua criação de ambiente virtual.

        ```bash
        sudo /usr/bin/anaconda/bin/conda install numpy=1.16.1 -n py35new --yes
        ```

    - Ou use repo PyPi, troque `numpy==1.16.1` e `py35new` correspondentemente:

        ```bash
        sudo /usr/bin/anaconda/env/py35new/bin/pip install numpy==1.16.1
        ```

    se não sabe o nome do ambiente virtual, pode sSH até ao nó da cabeça do cluster e executar `/usr/bin/anaconda/bin/conda info -e` para mostrar todos os ambientes virtuais.

3. Change Spark e Livy configs e apontam para o ambiente virtual criado.

    1. Abra ambari UI, vá à página Spark2, separador Configs.
    
        ![Change Spark e Livy config através de Ambari](./media/apache-spark-python-package-installation/ambari-spark-and-livy-config.png)
 
    2. Expanda advanced livy2-env, adicione declarações abaixo na parte inferior. Se instalou o ambiente virtual com um prefixo diferente, altere o caminho correspondentemente.

        ```
        export PYSPARK_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        export PYSPARK_DRIVER_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        ```

        ![Mude livy config através de Ambari](./media/apache-spark-python-package-installation/ambari-livy-config.png)

    3. Expanda a Advanced spark2-env, substitua a declaração de exportação PYSPARK_PYTHON existente no fundo. Se instalou o ambiente virtual com um prefixo diferente, altere o caminho correspondentemente.

        ```
        export PYSPARK_PYTHON=${PYSPARK_PYTHON:-/usr/bin/anaconda/envs/py35new/bin/python}
        ```

        ![Change Spark config através de Ambari](./media/apache-spark-python-package-installation/ambari-spark-config.png)

    4. Guarde as alterações e reinicie os serviços afetados. Estas alterações precisam de um reinício do serviço Spark2. A Ambari UI solicitará um lembrete de reinício necessário, clique em Reiniciar para reiniciar todos os serviços afetados.

        ![Change Spark config através de Ambari](./media/apache-spark-python-package-installation/ambari-restart-services.png)
 
4.  Se quiser usar o novo ambiente virtual criado em Jupyter. Tens de mudar os configs do Jupyter e reiniciar o Jupyter. Execute as ações de script em todos os nós cabeçalhos com a declaração abaixo para apontar Jupyter para o novo ambiente virtual criado. Certifique-se de modificar o caminho para o prefixo especificado para o seu ambiente virtual. Depois de executar esta ação de script, reinicie o serviço Jupyter através da Ambari UI para disponibilizar esta alteração.

    ```
    sudo sed -i '/python3_executable_path/c\ \"python3_executable_path\" : \"/usr/bin/anaconda/envs/py35new/bin/python3\"' /home/spark/.sparkmagic/config.json
    ```

    Você poderia confirmar o ambiente Python em Jupyter Notebook executando abaixo do código:

    ![Consulte a versão Python no Jupyter Notebook](./media/apache-spark-python-package-installation/check-python-version-in-jupyter.png)

## <a name="known-issue"></a>Problema conhecido

Existe um bug conhecido para a versão Anaconda 4.7.11, 4.7.12 e 4.8.0. Se vires as tuas ações de guião penduradas no `"Collecting package metadata (repodata.json): ...working..."` e a falhar com `"Python script has been killed due to timeout after waiting 3600 secs"`. Você pode baixar [este script](https://gregorysfixes.blob.core.windows.net/public/fix-conda.sh) e executá-lo como ações de script em todos os nós para corrigir o problema.

Para verificar a sua versão Anaconda, pode SSH no nó do cabeçalho do cluster e executa `/usr/bin/anaconda/bin/conda --v`.

## <a name="seealso"></a>Ver também

* [Descrição geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Cenários

* [Apache Spark com BI: Realizar análise interativa de dados utilizando spark in HDInsight com ferramentas BI](apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: Use Spark no HDInsight para analisar a temperatura do edifício utilizando dados de AVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: Use Spark no HDInsight para prever resultados da inspeção alimentar](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de log do site usando Apache Spark em HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicações

* [Criar uma aplicação autónoma com o Scala](apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster Apache Spark usando Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões

* [Utilize pacotes externos com cadernos Jupyter em clusters Apache Spark no HDInsight](apache-spark-jupyter-notebook-use-external-packages.md)
* [Utilizar o Plug-in das Ferramentas do HDInsight para o IntelliJ IDEA para criar e submeter aplicações do Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Utilize o Plugin de Ferramentas HDInsight para intelliJ IDEA para depurar aplicações Apache Spark remotamente](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Use os cadernos Apache Zeppelin com um cluster Apache Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o portátil Jupyter no cluster Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerir recursos

* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](apache-spark-job-debugging.md)
