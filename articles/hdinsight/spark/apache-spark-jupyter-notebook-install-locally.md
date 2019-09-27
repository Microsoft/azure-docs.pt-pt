---
title: Instalar o Jupyter localmente e conectar-se ao Spark no Azure HDInsight
description: Saiba como instalar o Jupyter Notebook localmente em seu computador e conectá-lo a um cluster Apache Spark.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/06/2019
ms.openlocfilehash: 46164cfc0c2baff919808a831a67180b65a23ff7
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71337658"
---
# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-on-hdinsight"></a>Instalar o Jupyter Notebook no computador e conectar-se ao Apache Spark no HDInsight

Neste artigo, você aprenderá a instalar o Jupyter notebook, com os kernels personalizados do PySpark (para Python) e Apache Spark (para escalabilidade) com o Spark Magic e conectar o bloco de anotações a um cluster HDInsight. Pode haver vários motivos para instalar o Jupyter em seu computador local, e também pode haver alguns desafios. Para obter mais informações sobre isso, consulte a seção [por que devo instalar o Jupyter no meu computador](#why-should-i-install-jupyter-on-my-computer) no final deste artigo.

Há quatro etapas principais envolvidas na instalação do Jupyter e na conexão com o Apache Spark no HDInsight.

* Configure o cluster Spark.
* Instale o Jupyter notebook.
* Instale os kernels PySpark e Spark com a mágica do Spark.
* Configure a mágica do Spark para acessar o cluster Spark no HDInsight.

Para obter mais informações sobre os kernels personalizados e o Spark Magic disponíveis para notebooks Jupyter com o cluster HDInsight, consulte [kernels disponíveis para notebooks Jupyter com clusters Apache Spark Linux no hdinsight](apache-spark-jupyter-notebook-kernels.md).

## <a name="prerequisites"></a>Pré-requisitos

Os pré-requisitos listados aqui não são para instalar o Jupyter. Esses são para conectar o notebook Jupyter a um cluster HDInsight depois que o notebook é instalado.

* Um cluster do Apache Spark no HDInsight. Para obter instruções, veja [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="install-jupyter-notebook-on-your-computer"></a>Instalar o Jupyter Notebook no seu computador

Você deve instalar o Python antes de instalar o Jupyter notebooks. A [distribuição Anaconda](https://www.anaconda.com/download/) instalará o, o Python e o Jupyter notebook.

Baixe o [instalador do Anaconda](https://www.anaconda.com/download/) para sua plataforma e execute a instalação. Ao executar o assistente de instalação, certifique-se de selecionar a opção para adicionar Anaconda à variável de caminho.  Consulte também, [instalando o Jupyter usando o Anaconda](https://jupyter.readthedocs.io/en/latest/install.html).

## <a name="install-spark-magic"></a>Instalar a mágica do Spark

1. Insira um dos comandos abaixo para instalar o Spark Magic. Consulte também a [documentação do sparkmagic](https://github.com/jupyter-incubator/sparkmagic#installation).

    |Versão do cluster | Comando de instalação |
    |---|---|
    |v 3.6 e v 3.5 |`pip install sparkmagic==0.12.7`|
    |v3.4|`pip install sparkmagic==0.2.3`|

1. Verifique `ipywidgets` se o está instalado corretamente executando o seguinte comando:

    ```cmd
    jupyter nbextension enable --py --sys-prefix widgetsnbextension
    ```

## <a name="install-pyspark-and-spark-kernels"></a>Instalar kernels PySpark e Spark

1. Identifique onde `sparkmagic` o é instalado digitando o seguinte comando:

    ```cmd
    pip show sparkmagic
    ```

    Em seguida, altere seu diretório de trabalho para o local identificado com o comando acima.

1. No seu novo diretório de trabalho, insira um ou mais dos comandos abaixo para instalar os kernels desejados:

    |Kernel | Comando |
    |---|---|
    |Spark|`jupyter-kernelspec install sparkmagic/kernels/sparkkernel`|
    |SparkR|`jupyter-kernelspec install sparkmagic/kernels/sparkrkernel`|
    |PySpark|`jupyter-kernelspec install sparkmagic/kernels/pysparkkernel`|
    |PySpark3|`jupyter-kernelspec install sparkmagic/kernels/pyspark3kernel`|

1. Opcional. Digite o comando a seguir para habilitar a extensão do servidor:

    ```cmd
    jupyter serverextension enable --py sparkmagic
    ```

## <a name="configure-spark-magic-to-connect-to-hdinsight-spark-cluster"></a>Configurar a mágica do Spark para se conectar ao cluster HDInsight Spark

Nesta seção, você configura a mágica do Spark que você instalou anteriormente para se conectar a um cluster Apache Spark.

1. Inicie o Shell do Python com o seguinte comando:

    ```cmd
    python
    ```

2. As informações de configuração do Jupyter normalmente são armazenadas no diretório base dos usuários. Digite o seguinte comando para identificar o diretório base e crie uma pasta chamada **. sparkmagic**.  O caminho completo será disparado.

    ```python
    import os
    path = os.path.expanduser('~') + "\\.sparkmagic"
    os.makedirs(path)
    print(path)
    exit()
    ```

3. Dentro da pasta `.sparkmagic`, crie um arquivo chamado **config. JSON** e adicione o trecho JSON a seguir dentro dele.  

    ```json
    {
      "kernel_python_credentials" : {
        "username": "{USERNAME}",
        "base64_password": "{BASE64ENCODEDPASSWORD}",
        "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
      },

      "kernel_scala_credentials" : {
        "username": "{USERNAME}",
        "base64_password": "{BASE64ENCODEDPASSWORD}",
        "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
      },

      "heartbeat_refresh_seconds": 5,
      "livy_server_heartbeat_timeout_seconds": 60,
      "heartbeat_retry_seconds": 1
    }
    ```

4. Faça as seguintes edições no arquivo:

    |Valor do modelo | Novo valor |
    |---|---|
    |USU|Logon do cluster, o `admin`padrão é.|
    |{CLUSTERDNSNAME}|Nome do cluster|
    |{BASE64ENCODEDPASSWORD}|Uma senha codificada em base64 para sua senha real.  Você pode gerar uma senha base64 em [https://www.url-encode-decode.com/base64-encode-decode/](https://www.url-encode-decode.com/base64-encode-decode/).|
    |`"livy_server_heartbeat_timeout_seconds": 60`|Mantenha se estiver `sparkmagic 0.12.7` usando (clusters v 3.5 e v 3.6).  Se estiver `sparkmagic 0.2.3` usando (clusters v 3.4), substitua `"should_heartbeat": true`por.|

    Você pode ver um arquivo de exemplo completo em [Sample config. JSON](https://github.com/jupyter-incubator/sparkmagic/blob/master/sparkmagic/example_config.json).

   > [!TIP]  
   > As pulsações são enviadas para garantir que as sessões não sejam vazadas. Quando um computador entra em suspensão ou é desligado, a pulsação não é enviada, resultando na limpeza da sessão. Para clusters v 3.4, se você quiser desabilitar esse comportamento, poderá definir a configuração `livy.server.interactive.heartbeat.timeout` Livy como `0` da interface do usuário do amAmbari. Para clusters v 3.5, se você não definir a configuração 3,5 acima, a sessão não será excluída.

5. Inicie o Jupyter. Use o comando a seguir no prompt de comando.

    ```cmd
    jupyter notebook
    ```

6. Verifique se você pode usar a mágica do Spark disponível com os kernels. Realize os seguintes passos.

    a. Crie um novo bloco de notas. No canto direito, selecione **novo**. Você deve ver o kernel padrão **Python 2** ou **Python 3** e os kernels que você instalou. Os valores reais podem variar dependendo das suas opções de instalação.  Selecione **PySpark**.

    ![Kernels disponíveis em](./media/apache-spark-jupyter-notebook-install-locally/jupyter-kernels-notebook.png "kernels do notebook Jupyter no Jupyter Notebook")

    > [!IMPORTANT]  
    > Depois de selecionar **nova** revisão, seu shell para quaisquer erros.  Se você vir o erro `TypeError: __init__() got an unexpected keyword argument 'io_loop'` , talvez esteja ocorrendo um problema conhecido com determinadas versões do tornado.  Nesse caso, pare o kernel e, em seguida, faça o downgrade da instalação do `pip install tornado==4.5.3`tornado com o seguinte comando:.

    b. Execute o trecho de código a seguir.

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 5
    ```  

    Se você puder recuperar a saída com êxito, a conexão com o cluster HDInsight será testada.

    Se você quiser atualizar a configuração do bloco de anotações para se conectar a um cluster diferente, atualize o config. JSON com o novo conjunto de valores, conforme mostrado na etapa 3, acima.

## <a name="why-should-i-install-jupyter-on-my-computer"></a>Por que devo instalar o Jupyter no meu computador?

Pode haver várias razões pelas quais você talvez queira instalar o Jupyter em seu computador e, em seguida, conectá-lo a um cluster Apache Spark no HDInsight.

* Embora os blocos de anotações do Jupyter já estejam disponíveis no cluster do Spark no Azure HDInsight, a instalação do Jupyter em seu computador fornece a opção de criar blocos de anotações localmente, testar seu aplicativo em um cluster em execução e, em seguida, carregar o blocos de anotações para o cluster. Para carregar os blocos de anotações no cluster, você pode carregá-los usando o notebook Jupyter que está executando o ou o cluster, ou salvá-los na pasta/HdiNotebooks na conta de armazenamento associada ao cluster. Para obter mais informações sobre como os blocos de anotações são armazenados no cluster, consulte [onde estão armazenados os blocos de anotações do Jupyter](apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)?
* Com os blocos de anotações disponíveis localmente, você pode se conectar a diferentes clusters do Spark com base em seu requisito de aplicativo.
* Você pode usar o GitHub para implementar um sistema de controle do código-fonte e ter controle de versão para os notebooks. Você também pode ter um ambiente de colaboração em que vários usuários possam trabalhar com o mesmo bloco de anotações.
* Você pode trabalhar com blocos de anotações localmente sem até mesmo ter um cluster ativo. Você só precisa de um cluster para testar seus blocos de anotações, não para gerenciar manualmente seus notebooks ou um ambiente de desenvolvimento.
* Pode ser mais fácil configurar seu próprio ambiente de desenvolvimento local do que configurar a instalação do Jupyter no cluster.  Você pode aproveitar todos os softwares que você instalou localmente sem configurar um ou mais clusters remotos.

> [!WARNING]  
> Com o Jupyter instalado no computador local, vários usuários podem executar o mesmo bloco de anotações no mesmo cluster do Spark ao mesmo tempo. Nessa situação, várias sessões Livy são criadas. Se você encontrar um problema e desejar depurá-lo, será uma tarefa complexa controlar qual sessão Livy pertence a qual usuário.  

## <a name="next-steps"></a>Passos seguintes

* [Sobre Apache Spark no Azure HDInsight](apache-spark-overview.md)
* [Apache Spark com BI: Executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: Usar o Spark no HDInsight para analisar a temperatura de edifício usando dados de HVAC](apache-spark-ipython-notebook-machine-learning.md)
