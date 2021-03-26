---
title: Ação de script para pacotes Python com Jupyter em Azure HDInsight
description: Instruções passo a passo sobre como usar a ação do script para configurar cadernos Jupyter disponíveis com clusters HDInsight Spark para usar pacotes de python externos.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020, devx-track-python
ms.date: 04/29/2020
ms.openlocfilehash: c3f912b4f4c2e78c44425f489927cee185b3d312
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104868720"
---
# <a name="safely-manage-python-environment-on-azure-hdinsight-using-script-action"></a>Gerir com segurança o ambiente do Python no Azure HDInsight com a Ação de Script

A HDInsight tem duas instalações python incorporadas no cluster Spark, Anaconda Python 2.7 e Python 3.5. Os clientes podem precisar de personalizar o ambiente Python como instalar pacotes Python externos. Aqui, mostramos a melhor prática de gerir com segurança ambientes Python para aglomerados Apache Spark em HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

Um cluster do Apache Spark no HDInsight. Para obter instruções, veja [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md). Se ainda não tiver um cluster Spark no HDInsight, pode executar ações de script durante a criação de clusters. Visite a documentação sobre [como usar ações de script personalizadas.](../hdinsight-hadoop-customize-cluster-linux.md)

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Suporte para software de código aberto utilizado em clusters HDInsight

O serviço Microsoft Azure HDInsight utiliza um ambiente de tecnologias de código aberto formadas em torno de Apache Hadoop. O Microsoft Azure fornece um nível geral de suporte para tecnologias de código aberto. Para mais informações, consulte [o site da Azure Support FAQ.](https://azure.microsoft.com/support/faq/) O serviço HDInsight fornece um nível adicional de suporte para componentes incorporados.

Existem dois tipos de componentes de código aberto que estão disponíveis no serviço HDInsight:

|Componente |Descrição |
|---|---|
|Incorporado|Estes componentes são pré-instalados em clusters HDInsight e fornecem a funcionalidade central do cluster. Por exemplo, Apache Hadoop YARN Resource Manager, a língua de consulta da Colmeia Apache (HiveQL), e a biblioteca Mahout pertencem a esta categoria. Uma lista completa de componentes de cluster está disponível nas [novidades das versões do cluster Apache Hadoop fornecidas pela HDInsight.](../hdinsight-component-versioning.md)|
|Personalizado|Você, como utilizador do cluster, pode instalar ou utilizar na sua carga de trabalho qualquer componente disponível na comunidade ou criado por si.|

> [!IMPORTANT]
> Os componentes fornecidos com o cluster HDInsight são totalmente suportados. O Microsoft Support ajuda a isolar e resolver problemas relacionados com estes componentes.
>
> Os componentes personalizados recebem suporte comercialmente razoável para ajudá-lo a resolver o problema. O suporte da Microsoft pode ser capaz de resolver o problema OU eles podem pedir-lhe para envolver canais disponíveis para as tecnologias de código aberto onde se encontra uma experiência profunda para essa tecnologia. Por exemplo, existem muitos sites comunitários que podem ser usados, como: [Microsoft Q&Uma página de perguntas para HDInsight](/answers/topics/azure-hdinsight.html), `https://stackoverflow.com` . Também os projetos Apache têm sites de projeto em `https://apache.org` .

## <a name="understand-default-python-installation"></a>Compreender a instalação padrão python

O cluster HDInsight Spark é criado com a instalação da Anaconda. Existem duas instalações Python no aglomerado, Anaconda Python 2.7 e Python 3.5. A tabela abaixo mostra as definições padrão de Python para Spark, Livy e Jupyter.

|Definição |Python 2.7|Python 3.5|
|----|----|----|
|Caminho|/usr/bin/anaconda/bin|/usr/bin/anaconda/envs/py35/bin|
|Versão spark|Padrão definido para 2.7|Pode alterar config para 3.5|
|Versão livy|Padrão definido para 2.7|Pode alterar config para 3.5|
|Jupyter|Kernel PySpark|Núcleo PySpark3|

## <a name="safely-install-external-python-packages"></a>Instalar com segurança pacotes de Python externos

O cluster HDInsight depende do ambiente python incorporado, tanto python 2.7 como Python 3.5. Instalar diretamente pacotes personalizados nesses ambientes incorporados padrão pode causar alterações inesperadas na versão da biblioteca. E quebre ainda mais o aglomerado. Para instalar com segurança pacotes pitões externos personalizados para as suas aplicações Spark, siga os passos abaixo.

1. Crie o ambiente virtual Python usando conda. Um ambiente virtual proporciona um espaço isolado para os seus projetos sem quebrar outros. Ao criar o ambiente virtual Python, pode especificar a versão python que pretende utilizar. Você ainda precisa criar ambiente virtual, mesmo que você gostaria de usar Python 2.7 e 3.5. Este requisito é garantir que o ambiente padrão do cluster não se quebre. Execute as ações de script no seu cluster para todos os nós com script abaixo para criar um ambiente virtual Python.

    -   `--prefix` especifica um caminho onde um ambiente virtual conda vive. Há vários configs que precisam de ser alterados mais com base no caminho especificado aqui. Neste exemplo, usamos o py35new, já que o cluster já tem um ambiente virtual existente chamado py35.
    -   `python=` especifica a versão Python para o ambiente virtual. Neste exemplo, usamos a versão 3.5, a mesma versão do cluster construído numa só. Também pode usar outras versões Python para criar o ambiente virtual.
    -   `anaconda` especifica a package_spec como anaconda para instalar pacotes Anaconda no ambiente virtual.
    
    ```bash
    sudo /usr/bin/anaconda/bin/conda create --prefix /usr/bin/anaconda/envs/py35new python=3.5 anaconda --yes
    ```

2. Instale pacotes Python externos no ambiente virtual criado, se necessário. Execute as ações de script no seu cluster para todos os nós com script abaixo para instalar pacotes python externos. Precisa de ter aqui privilégios para escrever ficheiros para a pasta do ambiente virtual.

    Procure o [índice de pacotes](https://pypi.python.org/pypi) para obter a lista completa de pacotes disponíveis. Também pode obter uma lista de pacotes disponíveis de outras fontes. Por exemplo, pode instalar pacotes disponibilizados através [da conda-forge](https://conda-forge.org/feedstocks/).

    Use abaixo o comando se quiser instalar uma biblioteca com a sua versão mais recente:

    - Use o canal conda:

        -   `seaborn` é o nome do pacote que gostaria de instalar.
        -   `-n py35new` especificar o nome do ambiente virtual que acaba de ser criado. Certifique-se de alterar o nome correspondentemente com base na sua criação de ambiente virtual.

        ```bash
        sudo /usr/bin/anaconda/bin/conda install seaborn -n py35new --yes
        ```

    - Ou use o repo PyPi, mude `seaborn` e `py35new` correspondentemente:
        ```bash
        sudo /usr/bin/anaconda/envs/py35new/bin/pip install seaborn
        ```

    Use abaixo o comando se quiser instalar uma biblioteca com uma versão específica:

    - Use o canal conda:

        -   `numpy=1.16.1` é o nome e versão do pacote que gostaria de instalar.
        -   `-n py35new` especificar o nome do ambiente virtual que acaba de ser criado. Certifique-se de alterar o nome correspondentemente com base na sua criação de ambiente virtual.

        ```bash
        sudo /usr/bin/anaconda/bin/conda install numpy=1.16.1 -n py35new --yes
        ```

    - Ou use o repo PyPi, mude `numpy==1.16.1` e `py35new` correspondentemente:

        ```bash
        sudo /usr/bin/anaconda/envs/py35new/bin/pip install numpy==1.16.1
        ```

    se não sabe o nome do ambiente virtual, pode SSH para o nó de cabeça do cluster e correr `/usr/bin/anaconda/bin/conda info -e` para mostrar todos os ambientes virtuais.

3. Change Spark e Livy configs e apontar para o ambiente virtual criado.

    1. Abra a UI Ambari, vá à página Spark2, separador Configs.

        :::image type="content" source="./media/apache-spark-python-package-installation/ambari-spark-and-livy-config.png" alt-text="Change Spark e Livy config através de Ambari" border="true":::

    2. Expanda o livy2-env avançado, adicione abaixo as declarações no fundo. Se instalou o ambiente virtual com um prefixo diferente, altere o caminho correspondentemente.

        ```bash
        export PYSPARK_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        export PYSPARK_DRIVER_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        ```

        :::image type="content" source="./media/apache-spark-python-package-installation/ambari-livy-config.png" alt-text="Mude livy config através de Ambari" border="true":::

    3. Expandir a spark2-env avançada, substituir a declaração de PYSPARK_PYTHON de exportação existente no fundo. Se instalou o ambiente virtual com um prefixo diferente, altere o caminho correspondentemente.

        ```bash
        export PYSPARK_PYTHON=${PYSPARK_PYTHON:-/usr/bin/anaconda/envs/py35new/bin/python}
        ```

        :::image type="content" source="./media/apache-spark-python-package-installation/ambari-spark-config.png" alt-text="Change Spark config através de Ambari" border="true":::

    4. Guarde as alterações e reinicie os serviços afetados. Estas alterações precisam de um reinício do serviço Spark2. A Ambari UI irá solicitar um lembrete de reinício necessário, clique em Reiniciar para reiniciar todos os serviços afetados.

        :::image type="content" source="./media/apache-spark-python-package-installation/ambari-restart-services.png" alt-text="Reiniciar serviços" border="true":::

    5. Desaperte duas propriedades na sessão Spark para garantir que o trabalho aponta para a configuração atualizada da faísca: `spark.yarn.appMasterEnv.PYSPARK_PYTHON` e `spark.yarn.appMasterEnv.PYSPARK_DRIVER_PYTHON` . 

        Utilizando o terminal ou um caderno, utilize a `spark.conf.set` função.

        ```spark
        spark.conf.set("spark.yarn.appMasterEnv.PYSPARK_PYTHON", "/usr/bin/anaconda/envs/py35/bin/python")
        spark.conf.set("spark.yarn.appMasterEnv.PYSPARK_DRIVER_PYTHON", "/usr/bin/anaconda/envs/py35/bin/python")
        ```

        Se estiver a utilizar livy, adicione as seguintes propriedades ao organismo de pedido:

        ```
        “conf” : {
        “spark.yarn.appMasterEnv.PYSPARK_PYTHON”:”/usr/bin/anaconda/envs/py35/bin/python”,
        “spark.yarn.appMasterEnv.PYSPARK_DRIVER_PYTHON”:”/usr/bin/anaconda/envs/py35/bin/python”
        }
        ```

4. Se quiser usar o novo ambiente virtual criado no Jupyter. Mude jupyter configs e reinicie Jupyter. Executar ações de script em todos os nós de cabeçalho com declaração abaixo para apontar Jupyter para o novo ambiente virtual criado. Certifique-se de modificar o caminho para o prefixo especificado para o seu ambiente virtual. Depois de executar esta ação de script, reinicie o serviço Jupyter através da UI Ambari para disponibilizar esta mudança.

    ```bash
    sudo sed -i '/python3_executable_path/c\ \"python3_executable_path\" : \"/usr/bin/anaconda/envs/py35new/bin/python3\"' /home/spark/.sparkmagic/config.json
    ```

    Pode confirmar duas vezes o ambiente python no Jupyter Notebook, correndo abaixo do código:

    :::image type="content" source="./media/apache-spark-python-package-installation/check-python-version-in-jupyter.png" alt-text="Verifique a versão Python no Caderno Jupyter" border="true":::

## <a name="known-issue"></a>Problema conhecido

Há um bug conhecido para a versão Anaconda, `4.7.11` `4.7.12` e `4.8.0` . Se vires que as tuas ações de guião param de responder `"Collecting package metadata (repodata.json): ...working..."` e falhar com `"Python script has been killed due to timeout after waiting 3600 secs"` . Você pode baixar [este script](https://gregorysfixes.blob.core.windows.net/public/fix-conda.sh) e executá-lo como ações de script em todos os nós para corrigir o problema.

Para verificar a sua versão Anaconda, pode SSH para o nó do cabeçalho do cluster e correr `/usr/bin/anaconda/bin/conda --v` .

## <a name="next-steps"></a>Passos seguintes

* [Descrição geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)
* [Pacotes externos com cadernos Jupyter em Apache Spark](apache-spark-jupyter-notebook-use-external-packages.md)
* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](apache-spark-job-debugging.md)
