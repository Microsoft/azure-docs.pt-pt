---
title: Instale o Jupyter localmente e ligue-se à Spark em Azure HDInsight
description: Aprenda a instalar o Jupyter Notebook localmente no seu computador e conecte-o a um cluster Apache Spark.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020, devx-track-python
ms.date: 04/23/2020
ms.openlocfilehash: 6dc91dc07d11f195092343e657911a884d8bf475
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104866000"
---
# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-on-hdinsight"></a>Instale o Bloco de Notas Jupyter no seu computador e ligue-se ao Apache Spark em HDInsight

Neste artigo, você aprende a instalar Jupyter Notebook com os kernels PySpark (para Python) e Apache Spark (para Scala) com magia Spark. Em seguida, ligue o caderno a um cluster HDInsight.

Existem quatro passos-chave envolvidos na instalação do Jupyter e na ligação ao Apache Spark em HDInsight.

* Configurar o aglomerado de faíscas.
* Instale o Bloco de Notas do Jupyter.
* Instale os núcleos PySpark e Spark com a magia Spark.
* Configure a magia spark para aceder ao cluster Spark em HDInsight.

Para obter mais informações sobre núcleos personalizados e magia spark, consulte [Kernels disponível para Cadernos Jupyter com aglomerados Apache Spark Linux em HDInsight.](apache-spark-jupyter-notebook-kernels.md)

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Spark no HDInsight. Para obter instruções, veja [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md). O caderno local liga-se ao cluster HDInsight.

* Familiaridade com a utilização de Jupyter Notebooks com o Spark no HDInsight.

## <a name="install-jupyter-notebook-on-your-computer"></a>Instale o Bloco de Notas Jupyter no seu computador

Instale python antes de instalar os Cadernos Jupyter. A [distribuição da Anaconda](https://www.anaconda.com/download/) vai instalar ambos, Python e Jupyter Notebook.

Descarregue o [instalador Daaconda](https://www.anaconda.com/download/) para a sua plataforma e execute a configuração. Durante a execução do assistente de configuração, certifique-se de que seleciona a opção de adicionar Anaconda à variável PATH.  Consulte também, [instalar o Jupyter usando a Anaconda.](https://jupyter.readthedocs.io/en/latest/install.html)

## <a name="install-spark-magic"></a>Instalar magia de faísca

1. Introduza o comando `pip install sparkmagic==0.13.1` para instalar a magia Spark para os clusters HDInsight versão 3.6 e 4.0. Consulte também [documentação sparkmagic.](https://github.com/jupyter-incubator/sparkmagic#installation)

1. Certifique-se de que `ipywidgets` está corretamente instalado executando o seguinte comando:

    ```cmd
    jupyter nbextension enable --py --sys-prefix widgetsnbextension
    ```

## <a name="install-pyspark-and-spark-kernels"></a>Instalar núcleos de PySpark e Faísca

1. Identificar onde `sparkmagic` está instalado introduzindo o seguinte comando:

    ```cmd
    pip show sparkmagic
    ```

    Em seguida, mude o seu diretório de trabalho para o **local** identificado com o comando acima.

1. A partir do seu novo diretório de trabalho, insira um ou mais dos comandos abaixo para instalar o(s) ou núcleos desejados):

    |Kernel | Comando |
    |---|---|
    |Spark|`jupyter-kernelspec install sparkmagic/kernels/sparkkernel`|
    |SparkR|`jupyter-kernelspec install sparkmagic/kernels/sparkrkernel`|
    |PySpark|`jupyter-kernelspec install sparkmagic/kernels/pysparkkernel`|
    |PySpark3|`jupyter-kernelspec install sparkmagic/kernels/pyspark3kernel`|

1. Opcional. Introduza o comando abaixo para ativar a extensão do servidor:

    ```cmd
    jupyter serverextension enable --py sparkmagic
    ```

## <a name="configure-spark-magic-to-connect-to-hdinsight-spark-cluster"></a>Configure a magia da faísca para ligar ao cluster HDInsight Spark

Nesta secção, configura a magia spark que instalou anteriormente para se ligar a um aglomerado de Faíscas Apache.

1. Inicie a casca python com o seguinte comando:

    ```cmd
    python
    ```

2. A informação de configuração jupyter é normalmente armazenada no diretório de utilizadores. Insira o seguinte comando para identificar o diretório doméstico e crie uma pasta chamada **\. sparkmagic**.  O caminho completo será desviado.

    ```python
    import os
    path = os.path.expanduser('~') + "\\.sparkmagic"
    os.makedirs(path)
    print(path)
    exit()
    ```

3. Dentro da `.sparkmagic` pasta, crie um ficheiro chamado **config.js** e adicione o seguinte corte JSON no seu interior.  

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

4. Faça as seguintes edições para o ficheiro:

    |Valor do modelo | Valor novo |
    |---|---|
    |{USERNAME}|Login de cluster, predefinição é `admin` .|
    |{CLUSTERDNSNAME}|Nome do cluster|
    |{BASE64ENCODEDPASSWORD}|Uma senha codificada base64 para a sua senha real.  Pode gerar uma palavra-passe base64 em [https://www.url-encode-decode.com/base64-encode-decode/](https://www.url-encode-decode.com/base64-encode-decode/) .|
    |`"livy_server_heartbeat_timeout_seconds": 60`|Mantenha-se em utilização `sparkmagic 0.12.7` (agrupamentos v3.5 e v3.6).  Se utilizar `sparkmagic 0.2.3` (agrupamentos v3.4), substitua-o por `"should_heartbeat": true` .|

    Pode ver um ficheiro de exemplo completo na [amostra config.jsem](https://github.com/jupyter-incubator/sparkmagic/blob/master/sparkmagic/example_config.json).

   > [!TIP]  
   > Os batimentos cardíacos são enviados para garantir que as sessões não são vazadas. Quando um computador dorme ou é desligado, o batimento cardíaco não é enviado, resultando na limpeza da sessão. Para clusters v3.4, se desejar desativar este comportamento, pode definir o config Livy `livy.server.interactive.heartbeat.timeout` para a partir da `0` UI Ambari. Para os clusters v3.5, se não definir a configuração 3.5 acima, a sessão não será eliminada.

5. Começa o Jupyter. Utilize o seguinte comando a partir da pronta de comando.

    ```cmd
    jupyter notebook
    ```

6. Verifique se pode utilizar a magia Spark disponível com os núcleos. Siga os seguintes passos.

    a. Crie um novo bloco de notas. Do canto direito, selecione **New**. Deve ver o núcleo padrão **Python 2** ou **Python 3** e os núcleos que instalou. Os valores reais podem variar dependendo das suas opções de instalação.  Selecione **PySpark**.

    :::image type="content" source="./media/apache-spark-jupyter-notebook-install-locally/jupyter-kernels-notebook.png " alt-text="Kernels disponíveis no Caderno Jupyter" border="true":::

    > [!IMPORTANT]  
    > Depois de selecionar **New** review your shell for any errors.  Se vires o `TypeError: __init__() got an unexpected keyword argument 'io_loop'` erro, podes estar a passar por um problema conhecido com certas versões do Tornado.  Em caso afirmativo, pare o núcleo e, em seguida, desclasse a sua instalação tornado com o seguinte comando: `pip install tornado==4.5.3` .

    b. Executar o seguinte corte de código.

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 5
    ```  

    Se conseguir recuperar a saída com sucesso, a sua ligação ao cluster HDInsight é testada.

    Se pretender atualizar a configuração do portátil para ligar a um cluster diferente, atualize o config.jscom o novo conjunto de valores, como mostra o Passo 3, acima.

## <a name="why-should-i-install-jupyter-on-my-computer"></a>Porque haveria de instalar o Jupyter no meu computador?

Razões para instalar o Jupyter no seu computador e, em seguida, conectá-lo a um cluster Apache Spark em HDInsight:

* Fornece-lhe a opção de criar os seus cadernos localmente, testar a sua aplicação contra um cluster de execução e, em seguida, fazer o upload dos cadernos para o cluster. Para fazer o upload dos cadernos para o cluster, pode carregá-los utilizando o Bloco de Notas Jupyter que está em execução ou o cluster, ou guardá-los para a `/HdiNotebooks` pasta na conta de armazenamento associada ao cluster. Para obter mais informações sobre como os cadernos são armazenados no cluster, veja [onde estão armazenados os Cadernos Jupyter?](apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)
* Com os cadernos disponíveis localmente, pode ligar-se a diferentes clusters Spark com base no seu requisito de aplicação.
* Pode utilizar o GitHub para implementar um sistema de controlo de fontes e ter controlo de versão para os cadernos. Também pode ter um ambiente colaborativo onde vários utilizadores podem trabalhar com o mesmo caderno.
* Você pode trabalhar com cadernos localmente sem sequer ter um aglomerado. Só precisa de um cluster para testar os seus cadernos, não para gerir manualmente os seus cadernos ou um ambiente de desenvolvimento.
* Pode ser mais fácil configurar o seu próprio ambiente de desenvolvimento local do que configurar a instalação jupyter no cluster.  Pode tirar partido de todo o software que instalou localmente sem configurar um ou mais clusters remotos.

> [!WARNING]  
> Com o Jupyter instalado no seu computador local, vários utilizadores podem executar o mesmo portátil no mesmo cluster Spark ao mesmo tempo. Em tal situação, várias sessões livy são criadas. Se tiver um problema e quiser depurar isso, será uma tarefa complexa para rastrear a sessão da Livy a que utilizador pertence.  

## <a name="next-steps"></a>Passos seguintes

* [Descrição geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)
* [Kernels para Jupyter Notebook em Apache Spark](apache-spark-jupyter-notebook-kernels.md)
* [Use pacotes externos com cadernos Jupyter em Apache Spark](apache-spark-jupyter-notebook-use-external-packages.md)
