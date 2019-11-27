---
title: Ação de script para pacotes do Python com Jupyter no Azure HDInsight
description: Instruções passo a passo sobre como usar a ação de script para configurar os blocos de anotações do Jupyter disponíveis com clusters do HDInsight Spark para usar pacotes python externos.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: a8654f6c9c6c6d020872d2c89e0dd141db4e0451
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74215589"
---
# <a name="safely-manage-python-environment-on-azure-hdinsight-using-script-action"></a>Gerir com segurança o ambiente do Python no Azure HDInsight com a Ação de Script

> [!div class="op_single_selector"]
> * [Usando a mágica da célula](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Usando a ação de script](apache-spark-python-package-installation.md)

O HDInsight tem duas instalações internas do Python no cluster do Spark, Anaconda Python 2,7 e Python 3,5. Em alguns casos, os clientes precisam personalizar o ambiente do Python, como instalar pacotes python externos ou outra versão do Python. Neste artigo, mostramos a prática recomendada de gerenciar com segurança ambientes de Python para um cluster [Apache Spark](https://spark.apache.org/) no HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Um cluster do Apache Spark no HDInsight. Para obter instruções, veja [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).

   > [!NOTE]  
   > Se você ainda não tiver um cluster Spark no HDInsight Linux, poderá executar ações de script durante a criação do cluster. Visite a documentação sobre [como usar ações de script personalizado](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Suporte para software livre usado em clusters HDInsight

O serviço de Microsoft Azure HDInsight usa um ecossistema de tecnologias de software livre, formadas sobre Apache Hadoop. O Microsoft Azure fornece um nível geral de suporte para tecnologias de software livre. Para obter mais informações, consulte [site de perguntas frequentes do suporte do Azure](https://azure.microsoft.com/support/faq/). O serviço HDInsight fornece um nível adicional de suporte para componentes internos.

Há dois tipos de componentes de código-fonte aberto que estão disponíveis no serviço HDInsight:

* **Componentes internos** -esses componentes são pré-instalados em clusters HDInsight e fornecem a funcionalidade básica do cluster. Por exemplo, Apache Hadoop o YARN Resource Manager, a linguagem de consulta Apache Hive (HiveQL) e a biblioteca Mahout pertencem a essa categoria. Uma lista completa de componentes de cluster está disponível em [novidades nas versões de cluster apache Hadoop fornecidas pelo HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning).
* **Componentes personalizados** – você, como um usuário do cluster, pode instalar ou usar em sua carga de trabalho qualquer componente disponível na Comunidade ou criado por você.

> [!IMPORTANT]
> Há suporte total para os componentes fornecidos com o cluster HDInsight. Suporte da Microsoft ajuda a isolar e resolver problemas relacionados a esses componentes.
>
> Os componentes personalizados recebem suporte comercialmente razoável para ajudá-lo a solucionar o problema. O suporte da Microsoft pode ser capaz de resolver o problema ou pode pedir que você envolva canais disponíveis para as tecnologias de software livre em que a profunda experiência para essa tecnologia é encontrada. Por exemplo, há muitos sites de comunidade que podem ser usados, como o [Fórum do MSDN para HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight), [https://stackoverflow.com](https://stackoverflow.com). Além disso, os projetos do Apache têm sites de projeto em [https://apache.org](https://apache.org), por exemplo: [Hadoop](https://hadoop.apache.org/).

## <a name="understand-default-python-installation"></a>Entender a instalação padrão do Python

O cluster HDInsight Spark é criado com a instalação do Anaconda. Há duas instalações do Python no cluster, Anaconda Python 2,7 e Python 3,5. A tabela a seguir mostra as configurações padrão do Python para Spark, Livy e Jupyter.

| |Python 2.7|Python 3,5|
|----|----|----|
|Caminho|/usr/bin/anaconda/bin|/usr/bin/anaconda/envs/py35/bin|
|Spark|Padrão definido como 2,7|N/A|
|Livy|Padrão definido como 2,7|N/A|
|Jupyter|Kernel PySpark|Kernel PySpark3|

## <a name="safely-install-external-python-packages"></a>Instalar com segurança pacotes do Python externos

O cluster HDInsight depende do ambiente interno do Python, Python 2,7 e Python 3,5. A instalação direta de pacotes personalizados nesses ambientes internos padrão pode causar alterações inesperadas na versão da biblioteca e interromper ainda mais o cluster. Para instalar com segurança pacotes do Python externos personalizados para seus aplicativos Spark, siga as etapas abaixo.

1. Crie um ambiente virtual Python usando o Conda. Um ambiente virtual fornece um espaço isolado para seus projetos sem quebrar outros. Ao criar o ambiente virtual do Python, você pode especificar a versão do Python que deseja usar. Observe que você ainda precisa criar um ambiente virtual, embora queira usar python 2,7 e 3,5. Isso é para garantir que o ambiente padrão do cluster não esteja sendo quebrado. Execute ações de script no cluster para todos os nós com o script abaixo para criar um ambiente virtual do Python. 

    -   `--prefix` especifica um caminho onde reside um ambiente virtual Conda. Há várias configurações que precisam ser alteradas com base no caminho especificado aqui. Neste exemplo, usamos o py35new, pois o cluster já tem um ambiente virtual existente chamado py35.
    -   `python=` especifica a versão do Python para o ambiente virtual. Neste exemplo, usamos a versão 3,5, a mesma versão do cluster criado em um. Você também pode usar outras versões do Python para criar o ambiente virtual.
    -   `anaconda` especifica o package_spec como Anaconda para instalar os pacotes do Anaconda no ambiente virtual.
    
    ```bash
    sudo /usr/bin/anaconda/bin/conda create --prefix /usr/bin/anaconda/envs/py35new python=3.5 anaconda --yes 
    ```

2. Instale pacotes python externos no ambiente virtual criado, se necessário. Execute ações de script no cluster para todos os nós com o script abaixo para instalar pacotes python externos. Você precisa ter privilégio sudo aqui para gravar arquivos na pasta do ambiente virtual.

    Você pode pesquisar o [índice do pacote](https://pypi.python.org/pypi) para obter a lista completa de pacotes que estão disponíveis. Você também pode obter uma lista de pacotes disponíveis de outras fontes. Por exemplo, você pode instalar pacotes disponibilizados por meio [de Conda](https://conda-forge.org/feedstocks/).

    -   `seaborn` é o nome do pacote que você deseja instalar.
    -   `-n py35new` especifique o nome do ambiente virtual que acabou de ser criado. Certifique-se de alterar o nome de forma correspondente com base em sua criação de ambiente virtual.

    ```bash
    sudo /usr/bin/anaconda/bin/conda install seaborn -n py35new --yes
    ```

    Se você não souber o nome do ambiente virtual, poderá fazer SSH no nó de cabeçalho do cluster e executar `/usr/bin/anaconda/bin/conda info -e` para mostrar todos os ambientes virtuais.

3. Altere as configurações do Spark e do Livy e aponte para o ambiente virtual criado.

    1. Abra a interface do usuário do Ambari, vá para a página Spark2, guia Configurações.
    
        ![Alterar a configuração do Spark e do Livy por meio do Ambari](./media/apache-spark-python-package-installation/ambari-spark-and-livy-config.png)
 
    2. Expanda Advanced livy2-env, adicione as instruções abaixo na parte inferior. Se você instalou o ambiente virtual com um prefixo diferente, altere o caminho de forma correspondente.

        ```
        export PYSPARK_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        export PYSPARK_DRIVER_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        ```

        ![Alterar Livy config por meio de Ambari](./media/apache-spark-python-package-installation/ambari-livy-config.png)

    3. Expanda spark2-env avançado, substitua a instrução de PYSPARK_PYTHON de exportação existente na parte inferior. Se você instalou o ambiente virtual com um prefixo diferente, altere o caminho de forma correspondente.

        ```
        export PYSPARK_PYTHON=${PYSPARK_PYTHON:-/usr/bin/anaconda/envs/py35new/bin/python}
        ```

        ![Alterar a configuração do Spark por meio de Ambari](./media/apache-spark-python-package-installation/ambari-spark-config.png)

    4. Salve as alterações e reinicie os serviços afetados. Essas alterações precisam de uma reinicialização do serviço Spark2. A interface do usuário do Ambari solicitará um lembrete de reinicialização necessário, clique em reiniciar para reiniciar todos os serviços afetados.

        ![Alterar a configuração do Spark por meio de Ambari](./media/apache-spark-python-package-installation/ambari-restart-services.png)
 
4.  Se você quiser usar o novo ambiente virtual criado em Jupyter. Você precisa alterar as configurações do Jupyter e reiniciar o Jupyter. Execute ações de script em todos os nós de cabeçalho com a instrução abaixo para apontar Jupyter para o novo ambiente virtual criado. Certifique-se de modificar o caminho para o prefixo que você especificou para o seu ambiente virtual. Depois de executar essa ação de script, reinicie o serviço Jupyter por meio da interface do usuário do Ambari para tornar essa alteração disponível.

    ```
    sudo sed -i '/python3_executable_path/c\ \"python3_executable_path\" : \"/usr/bin/anaconda/envs/py35new/bin/python3\"' /home/spark/.sparkmagic/config.json
    ```

    Você poderia confirmar duas vezes o ambiente do Python em Jupyter Notebook executando o código abaixo:

    ![Verificar a versão do Python no Jupyter Notebook](./media/apache-spark-python-package-installation/check-python-version-in-jupyter.png)

## <a name="known-issue"></a>Problema conhecido

Há um bug conhecido para Anaconda versão 4.7.11 e 4.7.12. Se você vir suas ações de script travando em `"Collecting package metadata (repodata.json): ...working..."` e falhando com `"Python script has been killed due to timeout after waiting 3600 secs"`. Você pode baixar [esse script](https://gregorysfixes.blob.core.windows.net/public/fix-conda.sh) e executá-lo como ações de script em todos os nós para corrigir o problema.

Para verificar sua versão do Anaconda, você pode executar SSH no nó de cabeçalho do cluster e executá `/usr/bin/anaconda/bin/conda --v`.

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
