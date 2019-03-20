---
title: Instalar o Jupyter localmente e ligar ao Spark no Azure HDInsight
description: Saiba como instalar o bloco de notas do Jupyter localmente no seu computador e ligá-la a um cluster do Apache Spark.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/05/2019
ms.author: hrasheed
ms.openlocfilehash: 7b20f0ec4669b485f87d050fcf597244fb898c85
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58091261"
---
# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-on-hdinsight"></a>Instalar o bloco de notas do Jupyter no seu computador e ligar ao Apache Spark no HDInsight

Neste artigo, saiba como instalar o bloco de notas do Jupyter com o PySpark personalizada (para Python) e os kernels do Apache Spark (para Scala) com a mágica do Spark e ligar o bloco de notas a um cluster do HDInsight. Pode haver vários motivos para instalar o Jupyter no computador local e pode haver alguns desafios também. Para saber mais sobre isso, consulte a secção [por que motivo deve posso instalar o Jupyter no meu computador](#why-should-i-install-jupyter-on-my-computer) no final deste artigo.

Existem quatro principais etapas envolvidas na instalação Jupyter e ligar ao Apache Spark no HDInsight.

* Configure o cluster do Spark.
* Instale o bloco de notas do Jupyter.
* Instale os kernels PySpark e do Spark com a mágica do Spark.
* Configure a mágica do Spark para aceder ao cluster do Spark no HDInsight.

Para obter mais informações sobre os kernels personalizados e a mágica de Spark disponível para blocos de notas do Jupyter com clusters do HDInsight, consulte [Kernels disponíveis para blocos de notas do Jupyter com o Apache Spark Linux clusters no HDInsight](apache-spark-jupyter-notebook-kernels.md).

> [!IMPORTANT]  
> Os passos no artigo só funcionam a versão 2.1.0 do Spark.

## <a name="prerequisites"></a>Pré-requisitos
Os pré-requisitos listados aqui não são para instalar o Jupyter. Estes destinam-se o bloco de notas do Jupyter para um cluster do HDInsight depois de instalar o bloco de notas.

* Uma subscrição do Azure. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Um cluster do Apache Spark (vidor 2.1.0 ou inferior) no HDInsight. Para obter instruções, veja [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).



## <a name="install-jupyter-notebook-on-your-computer"></a>Instalar o bloco de notas do Jupyter no seu computador

Tem de instalar o Python antes de instalar o Jupyter notebooks. Python e o Jupyter estão disponíveis como parte dos [distribuição Anaconda](https://www.anaconda.com/download/). Quando instalar o Anaconda, instala uma distribuição do Python. Depois de instalar o Anaconda, adicionar a instalação de Jupyter ao executar comandos apropriados.

1. Transfira o [instalador de Anaconda](https://www.anaconda.com/download/) por sua plataforma e a execução do programa de configuração. Ao executar o Assistente de configuração, certifique-se de que selecionar a opção para adicionar Anaconda a sua variável de caminho.

2. Execute o seguinte comando para instalar o Jupyter.

        conda install jupyter

    Para obter mais informações sobre como instalar o Jupyter, consulte [instalar o Jupyter usando Anaconda](https://jupyter.readthedocs.io/en/latest/install.html).

## <a name="install-the-kernels-and-spark-magic"></a>Instalar os kernels e mágica de Spark

Para obter instruções sobre como instalar a mágica de Spark, os kernels PySpark e do Spark, siga as instruções de instalação no [sparkmagic documentação](https://github.com/jupyter-incubator/sparkmagic#installation) no GitHub. A primeira etapa na documentação de mágica do Spark pede-lhe para instalar a mágica do Spark. Substitua essa primeira etapa na ligação com os seguintes comandos, dependendo da versão do cluster que irá ligar ao HDInsight. Depois disso, siga os passos restantes na documentação de mágico do Spark. Se pretende instalar os kernels diferentes, tem de efetuar o passo 3 na seção de instruções de instalação mágica de Spark.

* Para clusters v3.5 e v3.6, instalar sparkmagic 0.11.2 executando `pip install sparkmagic==0.11.2`

* Para clusters v3.4, instalar sparkmagic 0.2.3 executando `pip install sparkmagic==0.2.3`

## <a name="configure-spark-magic-to-connect-to-hdinsight-spark-cluster"></a>Configurar a mágica do Spark para ligar ao cluster do HDInsight Spark

Nesta secção, vai configurar a mágica do Spark que instalou anteriormente para ligar a um cluster do Apache Spark que deve já ter criado no Azure HDInsight.

1. Inicie o shell de Python com o seguinte comando:

    ```
    python
    ```

2. As informações de configuração do Jupyter são normalmente armazenadas no diretório de raiz de utilizadores. Introduza o seguinte comando para identificar o diretório de raiz e crie uma pasta chamada **.sparkmagic**.  O caminho completo será ter saído.

    ```python
    import os
    path = os.path.expanduser('~') + "\\.sparkmagic"
    os.makedirs(path)
    print(path)
    exit()
    ```

3. Dentro da pasta `.sparkmagic`, crie um ficheiro chamado **config** e adicione o fragmento JSON seguinte no interior do mesmo.  

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
4. Efetue as seguintes edições para o ficheiro:

    |Valor de modelo | Novo valor |
    |---|---|
    |{USERNAME}|Início de sessão do cluster, predefinido é Admin.|
    |{CLUSTERDNSNAME}|Nome do cluster|
    |{BASE64ENCODEDPASSWORD}|Um base64 codificado palavra-passe para a sua palavra-passe real.  Pode gerar uma palavra-passe de base64 em [ https://www.url-encode-decode.com/base64-encode-decode/ ](https://www.url-encode-decode.com/base64-encode-decode/).|
    |`"livy_server_heartbeat_timeout_seconds": 60`|Se utilizar `sparkmagic 0.11.23` (clusters v3.5 e v3.6).  Se utilizar `sparkmagic 0.2.3` (clusters v3.4), substitua `"should_heartbeat": true`.|

    Pode ver um ficheiro de exemplo completo em [JSON de exemplo](https://github.com/jupyter-incubator/sparkmagic/blob/master/sparkmagic/example_config.json).

   > [!TIP]  
   > Heartbeats são enviados para se certificar de que existem sessões não podem fugir. Quando um computador entra em suspensão ou é encerrado, o heartbeat não é enviado, resultando na sessão que está a ser limpos. Para clusters v3.4, se pretender desativar este comportamento, pode definir a configuração do Livy `livy.server.interactive.heartbeat.timeout` para `0` da IU do Ambari. Para clusters v3.5, se não definir a configuração de 3,5 acima, a sessão não será eliminada.

5. Inicie o Jupyter. Utilize o seguinte comando no prompt de comando.

        jupyter notebook

6. Certifique-se de que pode utilizar a mágica de Spark disponível com os kernels. Realize os seguintes passos.

    a. Crie um novo bloco de notas. No canto direito, selecione **New**. Deverá ver o kernel de predefinição **Python 2** ou **Python 3** e os kernels instalou. Os valores reais podem variar consoante as opções de instalação.  Selecione **PySpark**.

    ![Kernels no bloco de notas do Jupyter](./media/apache-spark-jupyter-notebook-install-locally/jupyter-kernels.png "Kernels no bloco de notas do Jupyter")

    > [!IMPORTANT]  
    > Depois de selecionar **New** rever seu shell para quaisquer erros.  Se vir o erro `TypeError: __init__() got an unexpected keyword argument 'io_loop'` poderá estar a ocorrer um problema conhecido com determinadas versões do Tornado.  Se assim for, pare o kernel e, em seguida, mudar sua instalação Tornado com o seguinte comando: `pip install tornado==4.5.3`.

    b. Execute o seguinte fragmento de código.

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 5
    ```  

    Se com êxito, pode recuperar o resultado, a ligação ao cluster do HDInsight é testada.

    Se quiser atualizar a configuração do bloco de notas para ligar a um cluster diferente, atualize o JSON com o novo conjunto de valores, conforme mostrado no passo 3, acima.

## <a name="why-should-i-install-jupyter-on-my-computer"></a>Por que motivo posso instalar o Jupyter no meu computador?

Pode haver vários motivos por que razão poderá querer instalar o Jupyter no seu computador e, em seguida, ligá-la para um cluster do Apache Spark no HDInsight.

* Apesar dos blocos de notas do Jupyter já estão disponíveis no cluster do Spark no HDInsight do Azure, instalar o Jupyter no seu computador fornece-lhe a opção para criar localmente seus blocos de notas, testar a aplicação relativamente um cluster em execução e, em seguida, carregue o blocos de notas para o cluster. Para carregar os blocos de notas para o cluster, pode carregá-los usando o bloco de notas do Jupyter que está em execução ou o cluster, ou salvá-los para a pasta de /HdiNotebooks na conta de armazenamento associada ao cluster. Para obter mais informações sobre como os blocos de anotações são armazenados no cluster, consulte [onde são blocos de notas do Jupyter armazenados](apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)?
* Com blocos de notas disponíveis localmente, pode ligar a clusters do Spark diferentes com base nas necessidades da sua aplicação.
* Pode utilizar o GitHub para implementar um sistema de controle de origem e tem controle de versão para os blocos de notas. Também pode ter um ambiente de colaboração em que vários utilizadores podem trabalhar com o mesmo bloco de notas.
* Pode trabalhar com blocos de notas localmente sem mesmo ter um cluster de cópia de segurança. Só precisa de um cluster para testar seus blocos de notas em relação a, não para gerir manualmente os seus blocos de notas ou um ambiente de desenvolvimento.
* Talvez seja mais fácil de configurar o seu próprio ambiente de desenvolvimento local que vale configurar a instalação do Jupyter no cluster.  Pode tirar partido de todo o software que tiver instalado localmente sem configurar um ou mais clusters remotos.

> [!WARNING]  
> Com o Jupyter instalado no seu computador local, vários usuários podem executar o mesmo bloco de notas no mesmo cluster do Spark ao mesmo tempo. Em tal situação, são criadas várias sessões do Livy. Se se deparar com um problema e quiser efetuar a depuração, é uma tarefa complexa para controlar qual sessão Livy pertence a que utilizador.  

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
* [Kernels disponíveis para o bloco de notas do Jupyter no cluster do Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Utilizar pacotes externos com blocos de notas do Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)

### <a name="manage-resources"></a>Gerir recursos
* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](apache-spark-job-debugging.md)
