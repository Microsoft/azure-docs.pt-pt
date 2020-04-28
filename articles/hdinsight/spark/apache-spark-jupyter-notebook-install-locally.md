---
title: Instale a Jupyter localmente e ligue-se à Spark em Azure HDInsight
description: Aprenda a instalar o portátil Jupyter localmente no seu computador e conecte-o a um cluster Apache Spark.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/23/2020
ms.openlocfilehash: b2394c580b871105fee84d63c478c3c490b56a0b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82191928"
---
# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-on-hdinsight"></a>Instale o caderno Jupyter no seu computador e ligue-se ao Apache Spark no HDInsight

Neste artigo, você aprende a instalar o portátil Jupyter com os núcleos personalizados pySpark (para Python) e Apache Spark (para Scala) com magia Spark. Em seguida, ligue o caderno a um cluster HDInsight.

Existem quatro passos-chave envolvidos na instalação do Jupyter e na ligação à Apache Spark no HDInsight.

* Configure o cluster de faíscas.
* Instale o caderno Jupyter.
* Instale os núcleos PySpark e Spark com a magia Spark.
* Configure a magia spark para aceder ao cluster Spark no HDInsight.

Para obter mais informações sobre kernels personalizados e magia Spark, consulte [kernels disponíveis para cadernos Jupyter com clusters Apache Spark Linux no HDInsight](apache-spark-jupyter-notebook-kernels.md).

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Spark no HDInsight. Para obter instruções, veja [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md). O caderno local liga-se ao cluster HDInsight.

* Familiaridade com a utilização de Jupyter Notebooks com o Spark no HDInsight.

## <a name="install-jupyter-notebook-on-your-computer"></a>Instale o caderno Jupyter no seu computador

Instale python antes de instalar os cadernos Jupyter. A distribuição da [Anaconda](https://www.anaconda.com/download/) vai instalar ambos, Python, e Jupyter Notebook.

Descarregue o [instalador Anaconda](https://www.anaconda.com/download/) para a sua plataforma e execute a configuração. Durante a execução do assistente de configuração, certifique-se de que seleciona a opção de adicionar Anaconda à sua variável PATH.  Consulte também, [Instalação de Jupyter utilizando Anaconda](https://jupyter.readthedocs.io/en/latest/install.html).

## <a name="install-spark-magic"></a>Instale magia spark

1. Introduza um dos comandos abaixo para instalar a magia Spark. Ver também, [documentação de magia de faísca.](https://github.com/jupyter-incubator/sparkmagic#installation)

    |Versão cluster | Instalar comando |
    |---|---|
    |v3.6 e v3.5 |`pip install sparkmagic==0.13.1`|
    |v3.4|`pip install sparkmagic==0.2.3`|

1. Certifique-se de que `ipywidgets` está corretamente instalado com o seguinte comando:

    ```cmd
    jupyter nbextension enable --py --sys-prefix widgetsnbextension
    ```

## <a name="install-pyspark-and-spark-kernels"></a>Instale núcleos PySpark e Spark

1. Identifique `sparkmagic` onde está instalado entrando no seguinte comando:

    ```cmd
    pip show sparkmagic
    ```

    Em seguida, mude o seu diretório de trabalho para o **local** identificado com o comando acima indicado.

1. A partir do seu novo diretório de trabalho, insira um ou mais comandos abaixo para instalar o núcleo(s) procurado):

    |Kernel | Comando |
    |---|---|
    |Spark|`jupyter-kernelspec install sparkmagic/kernels/sparkkernel`|
    |Faísca|`jupyter-kernelspec install sparkmagic/kernels/sparkrkernel`|
    |PySpark|`jupyter-kernelspec install sparkmagic/kernels/pysparkkernel`|
    |PySpark3|`jupyter-kernelspec install sparkmagic/kernels/pyspark3kernel`|

1. Opcional. Introduza o comando abaixo para ativar a extensão do servidor:

    ```cmd
    jupyter serverextension enable --py sparkmagic
    ```

## <a name="configure-spark-magic-to-connect-to-hdinsight-spark-cluster"></a>Configure magia spark para ligar ao cluster HDInsight Spark

Nesta secção, configura a magia Spark que instalou anteriormente para se ligar a um cluster Apache Spark.

1. Inicie a concha Python com o seguinte comando:

    ```cmd
    python
    ```

2. A informação de configuração jupyter é normalmente armazenada no diretório doméstico dos utilizadores. Insira o seguinte comando para identificar o diretório inicial e crie uma pasta chamada ** \.sparkmagic**.  O caminho completo será desviado.

    ```python
    import os
    path = os.path.expanduser('~') + "\\.sparkmagic"
    os.makedirs(path)
    print(path)
    exit()
    ```

3. Dentro da `.sparkmagic`pasta, crie um ficheiro chamado **config.json** e adicione o seguinte corte JSON no seu interior.  

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

      "custom_headers" : {
        "X-Requested-By": "livy"
      },

      "heartbeat_refresh_seconds": 5,
      "livy_server_heartbeat_timeout_seconds": 60,
      "heartbeat_retry_seconds": 1
    }
    ```

4. Faça as seguintes edições no ficheiro:

    |Valor do modelo | Valor novo |
    |---|---|
    |{NOME DE UTILIZADOR}|Login de cluster, predefinido é `admin`.|
    |{CLUSTERDNSNAME}|Nome do cluster|
    |{BASE64ENCODEDPassword}|Uma senha codificada base64 para a sua senha real.  Pode gerar uma senha base64 em [https://www.url-encode-decode.com/base64-encode-decode/](https://www.url-encode-decode.com/base64-encode-decode/).|
    |`"livy_server_heartbeat_timeout_seconds": 60`|Mantenha se `sparkmagic 0.12.7` utilizar (aglomerados v3.5 e v3.6).  Se `sparkmagic 0.2.3` utilizar (clusters v3.4), substitua por `"should_heartbeat": true`.|

    Pode ver um ficheiro completo de exemplo na [amostra config.json](https://github.com/jupyter-incubator/sparkmagic/blob/master/sparkmagic/example_config.json).

   > [!TIP]  
   > São enviados batimentos cardíacos para garantir que as sessões não sejam vazadas. Quando um computador dorme ou é desligado, o batimento cardíaco não é enviado, resultando na limpeza da sessão. Para os clusters v3.4, se desejar desativar este comportamento, pode definir o config `livy.server.interactive.heartbeat.timeout` Livy para `0` a partir da UI Ambari. Para os clusters v3.5, se não definir a configuração 3.5 acima, a sessão não será eliminada.

5. Começa o Jupyter. Utilize o seguinte comando a partir do pedido de comando.

    ```cmd
    jupyter notebook
    ```

6. Verifique se pode utilizar a magia Spark disponível com os núcleos. Siga os seguintes passos.

    a. Crie um novo bloco de notas. A partir do canto direito, selecione **New**. Deve ver o kernel padrão **Python 2** ou **Python 3** e os núcleos instalados. Os valores reais podem variar dependendo das suas escolhas de instalação.  Selecione **PySpark**.

    ![Núcleos disponíveis em caderno Jupyter](./media/apache-spark-jupyter-notebook-install-locally/jupyter-kernels-notebook.png "Kernels no caderno jupyter")

    > [!IMPORTANT]  
    > Depois de selecionar **New** reveja a sua concha para eventuais erros.  Se vir o `TypeError: __init__() got an unexpected keyword argument 'io_loop'` erro, pode estar a ter um problema conhecido com certas versões do Tornado.  Em caso afirmativo, pare o núcleo e desapare `pip install tornado==4.5.3`a instalação do Tornado com o seguinte comando: .

    b. Executar o seguinte código snippet.

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 5
    ```  

    Se conseguir recuperar com sucesso a saída, a sua ligação ao cluster HDInsight é testada.

    Se pretender atualizar a configuração do portátil para se ligar a um cluster diferente, atualize o config.json com o novo conjunto de valores, como mostra o Passo 3, acima.

## <a name="why-should-i-install-jupyter-on-my-computer"></a>Por que haveria de instalar o Jupyter no meu computador?

Razões para instalar o Jupyter no seu computador e, em seguida, ligá-lo a um cluster Apache Spark no HDInsight:

* Fornece-lhe a opção de criar os seus cadernos localmente, testar a sua aplicação contra um cluster em execução e, em seguida, fazer upload dos cadernos para o cluster. Para fazer o upload dos cadernos para o cluster, pode carregá-los utilizando o caderno `/HdiNotebooks` Jupyter que está em execução ou o cluster, ou guardá-los para a pasta na conta de armazenamento associada ao cluster. Para obter mais informações sobre como os cadernos são armazenados no cluster, veja [onde estão armazenados os cadernos Jupyter?](apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)
* Com os cadernos disponíveis localmente, pode ligar-se a diferentes clusters Spark com base no seu requisito de aplicação.
* Pode utilizar o GitHub para implementar um sistema de controlo de fontes e ter controlo de versão para os cadernos. Também pode ter um ambiente colaborativo onde vários utilizadores podem trabalhar com o mesmo caderno.
* Você pode trabalhar com cadernos localmente sem sequer ter um aglomerado. Basta um cluster para testar os seus cadernos, não para gerir manualmente os seus cadernos ou um ambiente de desenvolvimento.
* Pode ser mais fácil configurar o seu próprio ambiente de desenvolvimento local do que configurar a instalação jupyter no cluster.  Pode aproveitar todo o software que instalou localmente sem configurar um ou mais clusters remotos.

> [!WARNING]  
> Com a Jupyter instalada no seu computador local, vários utilizadores podem executar o mesmo bloco de notas no mesmo cluster Spark ao mesmo tempo. Em tal situação, são criadas várias sessões livy. Se você encontrar um problema e quiser desbugicar isso, será uma tarefa complexa para rastrear qual a sessão da Livy pertence a que utilizador.  

## <a name="next-steps"></a>Passos seguintes

* [Descrição geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)
* [Kernels para o caderno Jupyter em Apache Spark](apache-spark-jupyter-notebook-kernels.md)
* [Use pacotes externos com cadernos Jupyter em Apache Spark](apache-spark-jupyter-notebook-use-external-packages.md)
