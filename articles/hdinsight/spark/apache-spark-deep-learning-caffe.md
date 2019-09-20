---
title: Usar Caffe em Azure HDInsight Spark para aprendizado profundo distribuído
description: Use Caffe em Apache Spark para aprendizado profundo distribuído no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/17/2017
ms.openlocfilehash: e0490913029efc17d12139378369646c286a276c
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71145714"
---
# <a name="use-caffe-on-azure-hdinsight-spark-for-distributed-deep-learning"></a>Usar Caffe em Azure HDInsight Spark para aprendizado profundo distribuído

## <a name="introduction"></a>Introdução

O aprendizado profundo está afetando tudo, desde a saúde até o transporte até a fabricação e muito mais. As empresas estão recorrendo ao aprendizado profundo para resolver problemas graves, como [classificação de imagens](https://blogs.microsoft.com/next/2015/12/10/microsoft-researchers-win-imagenet-computer-vision-challenge/), [reconhecimento de fala](https://googleresearch.blogspot.jp/2015/08/the-neural-networks-behind-google-voice.html), reconhecimento de objetos e tradução automática.

Há [muitas estruturas populares](https://en.wikipedia.org/wiki/Comparison_of_deep_learning_software), incluindo [Microsoft cognitive Toolkit](https://www.microsoft.com/en-us/research/product/cognitive-toolkit/), [Tensorflow](https://www.tensorflow.org/), [Apache MXNet](https://mxnet.apache.org/), Theano, etc. O [Caffe](https://caffe.berkeleyvision.org/) é uma das estruturas de rede neural mais famosas (imperativa) não simbólicas e amplamente usada em muitas áreas, incluindo a visão computacional. Além disso, o [CaffeOnSpark](https://yahoohadoop.tumblr.com/post/139916563586/caffeonspark-open-sourced-for-distributed-deep) combina Caffe com Apache Spark, caso em que o aprendizado profundo pode ser facilmente usado em um cluster Hadoop existente. Você pode usar o aprendizado profundo junto com pipelines de ETL do Spark, reduzindo a complexidade do sistema e a latência para aprendizado de soluções completo.

O [HDInsight](https://azure.microsoft.com/services/hdinsight/) é uma oferta de Apache Hadoop de nuvem que fornece clusters de análise de software livre otimizados para os serviços Apache Spark, Apache Hive, Apache Hadoop, Apache HBase, Apache Storm, Apache Kafka e ml. O HDInsight é apoiado por um SLA de 99,9%. Cada uma dessas tecnologias de Big Data e de aplicativos ISV é facilmente implantável como clusters gerenciados com segurança e monitoramento para empresas.

Este artigo demonstra como instalar o [Caffe no Spark](https://github.com/yahoo/CaffeOnSpark) para um cluster HDInsight. Este artigo também usa a demonstração interna do MNIST para mostrar como usar o aprendizado profundo distribuído usando o HDInsight Spark em CPUs.

Há quatro etapas para realizar a tarefa:

1. Instalar as dependências necessárias em todos os nós
2. Criar Caffe em Spark para HDInsight no nó de cabeçalho
3. Distribua as bibliotecas necessárias para todos os nós de trabalho
4. Compor um modelo Caffe e executá-lo de maneira distribuída.

Como o HDInsight é uma solução PaaS, ele oferece ótimos recursos de plataforma, portanto, é fácil executar algumas tarefas. Um dos recursos usados nesta postagem de blog é chamado de [ação de script](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux), com a qual você pode executar comandos do Shell para personalizar nós de cluster (nó de cabeçalho, nó de trabalho ou nó de borda).

## <a name="step-1--install-the-required-dependencies-on-all-the-nodes"></a>Passo 1:  Instalar as dependências necessárias em todos os nós

Para começar, você precisa instalar as dependências. O site do Caffe e o [site do CaffeOnSpark](https://github.com/yahoo/CaffeOnSpark/wiki/GetStarted_yarn) oferecem um wiki útil para instalar as dependências do Spark no modo yarn. O HDInsight também usa o Spark no modo YARN. No entanto, você precisa adicionar mais algumas dependências para a plataforma HDInsight. Para fazer isso, use uma ação de script e execute-a em todos os nós de cabeçalho e nós de trabalho. Essa ação de script leva cerca de 20 minutos, pois essas dependências também dependem de outros pacotes. Você deve colocá-lo em algum local que seja acessível ao seu cluster HDInsight, como um local do GitHub ou a conta de armazenamento de BLOBs padrão.

    #!/bin/bash
    #Please be aware that installing the below will add additional 20 mins to cluster creation because of the dependencies
    #installing all dependencies, including the ones mentioned in https://caffe.berkeleyvision.org/install_apt.html, as well a few packages that are not included in HDInsight, such as gflags, glog, lmdb, numpy
    #It seems numpy will only needed during compilation time, but for safety purpose you install them on all the nodes

    sudo apt-get install -y libprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev libhdf5-serial-dev protobuf-compiler maven libatlas-base-dev libgflags-dev libgoogle-glog-dev liblmdb-dev build-essential  libboost-all-dev python-numpy python-scipy python-matplotlib ipython ipython-notebook python-pandas python-sympy python-nose

    #install protobuf
    wget https://github.com/google/protobuf/releases/download/v2.5.0/protobuf-2.5.0.tar.gz
    sudo tar xzvf protobuf-2.5.0.tar.gz -C /tmp/
    cd /tmp/protobuf-2.5.0/
    sudo ./configure
    sudo make
    sudo make check
    sudo make install
    sudo ldconfig
    echo "protobuf installation done"

Há duas etapas na ação de script. A primeira etapa é instalar todas as bibliotecas necessárias. Essas bibliotecas incluem as bibliotecas necessárias para a compilação de Caffe (como GFlags, Glog) e a execução de Caffe (como numpy). Você está usando o libatlas para otimização da CPU, mas sempre pode seguir o wiki do CaffeOnSpark sobre a instalação de outras bibliotecas de otimização, como MKL ou CUDA (para GPU).

A segunda etapa é baixar, compilar e instalar protobuf 2.5.0 para Caffe durante o tempo de execução. Protobuf 2.5.0 [é necessário](https://github.com/yahoo/CaffeOnSpark/issues/87), no entanto, essa versão não está disponível como um pacote no Ubuntu 16, portanto, você precisa compilá-la a partir do código-fonte. Também há alguns recursos na Internet sobre como compilá-los. Para obter mais informações, consulte [aqui](https://jugnu-life.blogspot.com/2013/09/install-protobuf-25-on-ubuntu.html).

Para começar, você pode apenas executar essa ação de script no cluster para todos os nós de trabalho e nós de cabeçalho (para o HDInsight 3,5). Você pode executar as ações de script em um cluster existente ou usar ações de script durante a criação do cluster. Para obter mais informações sobre as ações de script, consulte a documentação [aqui](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).

![Ações de script para instalar dependências](./media/apache-spark-deep-learning-caffe/submit-script-action.png)

## <a name="step-2-build-caffe-on-apache-spark-for-hdinsight-on-the-head-node"></a>Passo 2: Criar Caffe no Apache Spark para o HDInsight no nó principal

A segunda etapa é criar Caffe no cabeçalho e, em seguida, distribuir as bibliotecas compiladas para todos os nós de trabalho. Nesta etapa, você deve [SSH em seu cabeçalho](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix). Depois disso, você deve seguir o [processo de compilação CaffeOnSpark](https://github.com/yahoo/CaffeOnSpark/wiki/GetStarted_yarn). Veja abaixo o script que você pode usar para criar CaffeOnSpark com algumas etapas adicionais.

    #!/bin/bash
    git clone https://github.com/yahoo/CaffeOnSpark.git --recursive
    export CAFFE_ON_SPARK=$(pwd)/CaffeOnSpark

    pushd ${CAFFE_ON_SPARK}/caffe-public/
    cp Makefile.config.example Makefile.config
    echo "INCLUDE_DIRS += ${JAVA_HOME}/include" >> Makefile.config
    #Below configurations might need to be updated based on actual cases. For example, if you are using GPU, or using a different BLAS library, you may want to update those settings accordingly.
    echo "CPU_ONLY := 1" >> Makefile.config
    echo "BLAS := atlas" >> Makefile.config
    echo "INCLUDE_DIRS += /usr/include/hdf5/serial/" >> Makefile.config
    echo "LIBRARY_DIRS += /usr/lib/x86_64-linux-gnu/hdf5/serial/" >> Makefile.config
    popd

    #compile CaffeOnSpark
    pushd ${CAFFE_ON_SPARK}
    #always clean up the environment before building (especially when rebuiding), or there will be errors such as "failed to execute goal org.apache.maven.plugins:maven-antrun-plugin:1.7:run (proto) on project caffe-distri: An Ant BuildException has occurred: exec returned: 2"
    make clean 
    #the build step usually takes 20~30 mins, since it has a lot maven dependencies
    make build 
    popd
    export LD_LIBRARY_PATH=${CAFFE_ON_SPARK}/caffe-public/distribute/lib:${CAFFE_ON_SPARK}/caffe-distri/distribute/lib

    hadoop fs -mkdir -p wasb:///projects/machine_learning/image_dataset

    ${CAFFE_ON_SPARK}/scripts/setup-mnist.sh
    hadoop fs -put -f ${CAFFE_ON_SPARK}/data/mnist_*_lmdb wasb:///projects/machine_learning/image_dataset/

    ${CAFFE_ON_SPARK}/scripts/setup-cifar10.sh
    hadoop fs -put -f ${CAFFE_ON_SPARK}/data/cifar10_*_lmdb wasb:///projects/machine_learning/image_dataset/

    #put the already compiled CaffeOnSpark libraries to wasb storage, then read back to each node using script actions. This is because CaffeOnSpark requires all the nodes have the libarries
    hadoop fs -mkdir -p /CaffeOnSpark/caffe-public/distribute/lib/
    hadoop fs -mkdir -p /CaffeOnSpark/caffe-distri/distribute/lib/
    hadoop fs -put CaffeOnSpark/caffe-distri/distribute/lib/* /CaffeOnSpark/caffe-distri/distribute/lib/
    hadoop fs -put CaffeOnSpark/caffe-public/distribute/lib/* /CaffeOnSpark/caffe-public/distribute/lib/

Talvez seja necessário fazer mais do que o que a documentação do CaffeOnSpark diz. As alterações são:
- Altere para CPU apenas e use libatlas para essa finalidade específica.
- Coloque os conjuntos de valores no armazenamento de BLOBs, que é um local compartilhado que pode ser acessado por todos os nós de trabalho para uso posterior.
- Coloque as bibliotecas Caffe compiladas no armazenamento de BLOBs e, posteriormente, copie essas bibliotecas para todos os nós usando as ações de script para evitar o tempo de compilação adicional.

### <a name="troubleshooting-an-ant-buildexception-has-occurred-exec-returned-2"></a>Solução Ocorreu um Ant BuildException: exec retornado: 2

Ao tentar criar o CaffeOnSpark pela primeira vez, às vezes ele diz

    failed to execute goal org.apache.maven.plugins:maven-antrun-plugin:1.7:run (proto) on project caffe-distri: An Ant BuildException has occurred: exec returned: 2

Limpe o repositório de código "Make Clean" e execute "make build" para resolver esse problema, desde que você tenha as dependências corretas.

### <a name="troubleshooting-maven-repository-connection-time-out"></a>Solução Tempo limite de conexão do repositório Maven

Às vezes, o Maven fornece um erro de tempo limite de conexão, semelhante ao seguinte trecho:

    Retry:
    [INFO] Downloading: https://repo.maven.apache.org/maven2/com/twitter/chill_2.11/0.8.0/chill_2.11-0.8.0.jar
    Feb 01, 2017 5:14:49 AM org.apache.maven.wagon.providers.http.httpclient.impl.execchain.RetryExec execute
    INFO: I/O exception (java.net.SocketException) caught when processing request to {s}->https://repo.maven.apache.org:443: Connection timed out (Read failed)

Você deve tentar novamente após alguns minutos.

### <a name="troubleshooting-test-failure-for-caffe"></a>Solução Falha de teste para Caffe

Provavelmente, você verá uma falha de teste ao fazer a verificação final para CaffeOnSpark. Isso é provavelmente relacionado à codificação UTF-8, mas não deve afetar o uso de Caffe

    Run completed in 32 seconds, 78 milliseconds.
    Total number of tests run: 7
    Suites: completed 5, aborted 0
    Tests: succeeded 6, failed 1, canceled 0, ignored 0, pending 0
    *** 1 TEST FAILED ***

## <a name="step-3-distribute-the-required-libraries-to-all-the-worker-nodes"></a>Passo 3: Distribua as bibliotecas necessárias para todos os nós de trabalho

A próxima etapa é distribuir as bibliotecas (basicamente, as bibliotecas em CaffeOnSpark/Caffe-Public/distribute/lib/e CaffeOnSpark/Caffe-distri/distribute/lib/) para todos os nós. Na etapa 2, você coloca essas bibliotecas no armazenamento de BLOBs e, nesta etapa, você usa as ações de script para copiá-las para todos os nós de cabeçalho e nós de trabalho.

Para fazer isso, execute uma ação de script, conforme mostrado no trecho a seguir:

    #!/bin/bash
    hadoop fs -get wasb:///CaffeOnSpark /home/changetoyourusername/

Certifique-se de que você precisa apontar para o local certo específico para seu cluster)

Como na etapa 2, você o coloca no armazenamento de BLOBs, que pode ser acessado por todos os nós, nesta etapa, basta copiá-lo para todos os nós.

## <a name="step-4-compose-a-caffe-model-and-run-it-in-a-distributed-manner"></a>Passo 4: Compor um modelo Caffe e executá-lo de maneira distribuída

O Caffe é instalado após a execução das etapas anteriores. A próxima etapa é escrever um modelo Caffe. 

Caffe está usando uma "arquitetura expressiva", em que, para compor um modelo, você só precisa definir um arquivo de configuração e sem codificar (na maioria dos casos). Então vamos dar uma olhada. 

O modelo que você treina é um modelo de exemplo para treinamento de MNIST. O banco de dados MNIST de dígitos manuscritos tem um conjunto de treinamento de 60.000 exemplos e um conjunto de teste de 10.000 exemplos. É um subconjunto de um conjunto maior disponível do NIST. Os dígitos têm tamanho-normalizado e centralizado em uma imagem de tamanho fixo. CaffeOnSpark tem alguns scripts para baixar o conjunto de os e convertê-lo no formato correto.

O CaffeOnSpark fornece algumas topologias de rede de exemplo para treinamento de MNIST. Ele tem um bom design para dividir a arquitetura de rede (a topologia da rede) e a otimização. Nesse caso, há dois arquivos necessários:

o arquivo "Solver" ($ {CAFFE_ON_SPARK}/data/lenet_memory_solver.prototxt) é usado para supervisionar a otimização e gerar atualizações de parâmetros. Por exemplo, ele define se a CPU ou a GPU é usada, qual é o impulso, quantas iterações são, etc. Ele também define qual topologia de rede de neurônio deve ser usada pelo programa (que é o segundo arquivo de que você precisa). Para obter mais informações sobre o Solver, consulte a [documentação do Caffe](https://caffe.berkeleyvision.org/tutorial/solver.html).

Para este exemplo, como você está usando CPU em vez de GPU, você deve alterar a última linha para:

    # solver mode: CPU or GPU
    solver_mode: CPU

![Exemplo de configuração do HDInsight Caffe](./media/apache-spark-deep-learning-caffe/caffe-configuration1.png
)

Você pode alterar outras linhas conforme necessário.

O segundo arquivo ($ {CAFFE_ON_SPARK}/data/lenet_memory_train_test.prototxt) define como a rede de neurônios se parece e o arquivo de entrada e saída relevante. Você também precisa atualizar o arquivo para refletir o local dos dados de treinamento. Altere a seguinte parte em lenet_memory_train_test. prototxt (você precisa apontar para o local certo específico para seu cluster):

- Altere "file:/Users/mridul/bigml/demodl/mnist_train_lmdb" para "wasb:///projects/machine_learning/image_dataset/mnist_train_lmdb"
- Altere "file:/Users/mridul/bigml/demodl/mnist_test_lmdb/" para "wasb:///projects/machine_learning/image_dataset/mnist_test_lmdb"

![Exemplo de configuração do HDInsight Caffe novamente](./media/apache-spark-deep-learning-caffe/caffe-configuration2.png)

Para obter mais informações sobre como definir a rede, verifique a [documentação do Caffe no conjunto](https://caffe.berkeleyvision.org/gathered/examples/mnist.html) de dados do MNIST

Para fins deste artigo, você usa este exemplo de MNIST. Execute os seguintes comandos do nó principal:

    spark-submit --master yarn --deploy-mode cluster --num-executors 8 --files ${CAFFE_ON_SPARK}/data/lenet_memory_solver.prototxt,${CAFFE_ON_SPARK}/data/lenet_memory_train_test.prototxt --conf spark.driver.extraLibraryPath="${LD_LIBRARY_PATH}" --conf spark.executorEnv.LD_LIBRARY_PATH="${LD_LIBRARY_PATH}" --class com.yahoo.ml.caffe.CaffeOnSpark ${CAFFE_ON_SPARK}/caffe-grid/target/caffe-grid-0.1-SNAPSHOT-jar-with-dependencies.jar -train -features accuracy,loss -label label -conf lenet_memory_solver.prototxt -devices 1 -connection ethernet -model wasb:///mnist.model -output wasb:///mnist_features_result

O comando anterior distribui os arquivos necessários (lenet_memory_solver. prototxt e lenet_memory_train_test. prototxt) para cada contêiner YARN. O comando também define o caminho relevante de cada driver/executor do Spark para LD_LIBRARY_PATH. LD_LIBRARY_PATH é definido no trecho de código anterior e aponta para o local que tem bibliotecas CaffeOnSpark. 

## <a name="monitoring-and-troubleshooting"></a>Monitoramento e solução de problemas

Como você está usando o modo de cluster YARN, nesse caso, o driver do Spark será agendado para um contêiner arbitrário (e um nó de trabalho arbitrário) que você deve ver apenas no console de saída de algo como:

    17/02/01 23:22:16 INFO Client: Application report for application_1485916338528_0015 (state: RUNNING)

Se você quiser saber o que aconteceu, normalmente precisará obter o log do driver do Spark, que tem mais informações. Nesse caso, você precisa ir para a interface do usuário do amYARN para localizar os logs relevantes do YARN. Você pode obter a interface do usuário do amYARN por esta URL:

    https://yourclustername.azurehdinsight.net/yarnui

![exibição do navegador do Agendador do Apache yarn](./media/apache-spark-deep-learning-caffe/apache-yarn-window-1.png)

Você pode dar uma olhada em quantos recursos são alocados para esse aplicativo específico. Você pode clicar no link "scheduler" e, em seguida, verá que, para esse aplicativo, há nove contêineres em execução. Você pede ao YARN para fornecer oito executores e outro contêiner é para o processo de driver. 

![Exibição do Agendador do HDI Apache YARN](./media/apache-spark-deep-learning-caffe/apache-yarn-scheduler.png)

Talvez você queira verificar os logs de driver ou os logs de contêiner se houver falhas. Para logs de driver, você pode clicar na ID do aplicativo na interface do usuário do amYARN e clicar no botão "logs". Os logs de driver são gravados em stderr.

![exibição do navegador da janela do Apache yarn](./media/apache-spark-deep-learning-caffe/apache-yarn-window-2.png)

Por exemplo, você pode ver um pouco do erro abaixo dos logs de driver, indicando que você aloca muitos executores.

    17/02/01 07:26:06 ERROR ApplicationMaster: User class threw exception: java.lang.IllegalStateException: Insufficient training data. Please adjust hyperparameters or increase dataset.
    java.lang.IllegalStateException: Insufficient training data. Please adjust hyperparameters or increase dataset.
        at com.yahoo.ml.caffe.CaffeOnSpark.trainWithValidation(CaffeOnSpark.scala:261)
        at com.yahoo.ml.caffe.CaffeOnSpark$.main(CaffeOnSpark.scala:42)
        at com.yahoo.ml.caffe.CaffeOnSpark.main(CaffeOnSpark.scala)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:498)
        at org.apache.spark.deploy.yarn.ApplicationMaster$$anon$2.run(ApplicationMaster.scala:627)

Às vezes, o problema pode acontecer em executores em vez de drivers. Nesse caso, você precisa verificar os logs de contêiner. Você sempre pode obter os logs de contêiner e, em seguida, obter o contêiner com falha. Por exemplo, você pode atender a essa falha ao executar Caffe.

    17/02/01 07:12:05 WARN YarnAllocator: Container marked as failed: container_1485916338528_0008_05_000005 on host: 10.0.0.14. Exit status: 134. Diagnostics: Exception from container-launch.
    Container id: container_1485916338528_0008_05_000005
    Exit code: 134
    Exception message: /bin/bash: line 1: 12230 Aborted                 (core dumped) LD_LIBRARY_PATH=/usr/hdp/current/hadoop-client/lib/native:/usr/hdp/current/hadoop-client/lib/native/Linux-amd64-64:/home/xiaoyzhu/CaffeOnSpark/caffe-public/distribute/lib:/home/xiaoyzhu/CaffeOnSpark/caffe-distri/distribute/lib /usr/lib/jvm/java-8-openjdk-amd64/bin/java -server -Xmx4608m '-Dhdp.version=' '-Detwlogger.component=sparkexecutor' '-DlogFilter.filename=SparkLogFilters.xml' '-DpatternGroup.filename=SparkPatternGroups.xml' '-Dlog4jspark.root.logger=INFO,console,RFA,ETW,Anonymizer' '-Dlog4jspark.log.dir=/var/log/sparkapp/${user.name}' '-Dlog4jspark.log.file=sparkexecutor.log' '-Dlog4j.configuration=file:/usr/hdp/current/spark2-client/conf/log4j.properties' '-Djavax.xml.parsers.SAXParserFactory=com.sun.org.apache.xerces.internal.jaxp.SAXParserFactoryImpl' -Djava.io.tmpdir=/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/tmp '-Dspark.driver.port=43942' '-Dspark.history.ui.port=18080' '-Dspark.ui.port=0' -Dspark.yarn.app.container.log.dir=/mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005 -XX:OnOutOfMemoryError='kill %p' org.apache.spark.executor.CoarseGrainedExecutorBackend --driver-url spark://CoarseGrainedScheduler@10.0.0.13:43942 --executor-id 4 --hostname 10.0.0.14 --cores 3 --app-id application_1485916338528_0008 --user-class-path file:/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/__app__.jar > /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stdout 2> /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stderr

    Stack trace: ExitCodeException exitCode=134: /bin/bash: line 1: 12230 Aborted                 (core dumped) LD_LIBRARY_PATH=/usr/hdp/current/hadoop-client/lib/native:/usr/hdp/current/hadoop-client/lib/native/Linux-amd64-64:/home/xiaoyzhu/CaffeOnSpark/caffe-public/distribute/lib:/home/xiaoyzhu/CaffeOnSpark/caffe-distri/distribute/lib /usr/lib/jvm/java-8-openjdk-amd64/bin/java -server -Xmx4608m '-Dhdp.version=' '-Detwlogger.component=sparkexecutor' '-DlogFilter.filename=SparkLogFilters.xml' '-DpatternGroup.filename=SparkPatternGroups.xml' '-Dlog4jspark.root.logger=INFO,console,RFA,ETW,Anonymizer' '-Dlog4jspark.log.dir=/var/log/sparkapp/${user.name}' '-Dlog4jspark.log.file=sparkexecutor.log' '-Dlog4j.configuration=file:/usr/hdp/current/spark2-client/conf/log4j.properties' '-Djavax.xml.parsers.SAXParserFactory=com.sun.org.apache.xerces.internal.jaxp.SAXParserFactoryImpl' -Djava.io.tmpdir=/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/tmp '-Dspark.driver.port=43942' '-Dspark.history.ui.port=18080' '-Dspark.ui.port=0' -Dspark.yarn.app.container.log.dir=/mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005 -XX:OnOutOfMemoryError='kill %p' org.apache.spark.executor.CoarseGrainedExecutorBackend --driver-url spark://CoarseGrainedScheduler@10.0.0.13:43942 --executor-id 4 --hostname 10.0.0.14 --cores 3 --app-id application_1485916338528_0008 --user-class-path file:/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/__app__.jar > /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stdout 2> /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stderr

        at org.apache.hadoop.util.Shell.runCommand(Shell.java:933)
        at org.apache.hadoop.util.Shell.run(Shell.java:844)
        at org.apache.hadoop.util.Shell$ShellCommandExecutor.execute(Shell.java:1123)
        at org.apache.hadoop.yarn.server.nodemanager.DefaultContainerExecutor.launchContainer(DefaultContainerExecutor.java:225)
        at org.apache.hadoop.yarn.server.nodemanager.containermanager.launcher.ContainerLaunch.call(ContainerLaunch.java:317)
        at org.apache.hadoop.yarn.server.nodemanager.containermanager.launcher.ContainerLaunch.call(ContainerLaunch.java:83)
        at java.util.concurrent.FutureTask.run(FutureTask.java:266)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1142)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:617)
        at java.lang.Thread.run(Thread.java:745)


    Container exited with a non-zero exit code 134

Nesse caso, você precisa obter a ID do contêiner com falha (no caso acima, é container_1485916338528_0008_05_000005). Em seguida, você precisa executar 

    yarn logs -containerId container_1485916338528_0008_03_000005

do cabeçalho. Depois de verificar a falha do contêiner, ela é causada pelo uso do modo de GPU (em que você deve usar o modo de CPU) em lenet_memory_solver. prototxt.

    17/02/01 07:10:48 INFO LMDB: Batch size:100
    WARNING: Logging before InitGoogleLogging() is written to STDERR
    F0201 07:10:48.309725 11624 common.cpp:79] Cannot use GPU in CPU-only Caffe: check mode.

## <a name="getting-results"></a>Obtendo resultados

Como você está alocando oito executores e a topologia de rede é simples, só deve levar cerca de 30 minutos para executar o resultado. Na linha de comando, você pode ver que você coloca o modelo em wasb:///mnist.model e coloca os resultados em uma pasta chamada wasb:///mnist_features_result.

Você pode obter os resultados executando

    hadoop fs -cat hdfs:///mnist_features_result/*

e o resultado é semelhante a:

    {"SampleID":"00009597","accuracy":[1.0],"loss":[0.028171852],"label":[2.0]}
    {"SampleID":"00009598","accuracy":[1.0],"loss":[0.028171852],"label":[6.0]}
    {"SampleID":"00009599","accuracy":[1.0],"loss":[0.028171852],"label":[1.0]}
    {"SampleID":"00009600","accuracy":[0.97],"loss":[0.0677709],"label":[5.0]}
    {"SampleID":"00009601","accuracy":[0.97],"loss":[0.0677709],"label":[0.0]}
    {"SampleID":"00009602","accuracy":[0.97],"loss":[0.0677709],"label":[1.0]}
    {"SampleID":"00009603","accuracy":[0.97],"loss":[0.0677709],"label":[2.0]}
    {"SampleID":"00009604","accuracy":[0.97],"loss":[0.0677709],"label":[3.0]}
    {"SampleID":"00009605","accuracy":[0.97],"loss":[0.0677709],"label":[4.0]}

A Amostraid representa a ID no conjunto de MNIST e o rótulo é o número que o modelo identifica.

## <a name="conclusion"></a>Conclusão

Nesta documentação, você tentou instalar o CaffeOnSpark com a execução de um exemplo simples. O HDInsight é uma plataforma de computação distribuída de nuvem gerenciada completa e é o melhor lugar para executar cargas de trabalho de análise avançada e aprendizado de máquina em um conjunto de dados grande e para aprendizado de profundo distribuído, você pode usar o Caffe no HDInsight Spark para realizar aprendizado profundo tarefa.

## <a name="seealso"></a>Ver também

* [Sobre Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Cenários

* [Apache Spark com Machine Learning: Usar o Spark no HDInsight para analisar a temperatura de edifício usando dados de HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: Usar o Spark no HDInsight para prever os resultados da inspeção de alimentos](apache-spark-machine-learning-mllib-ipython.md)

### <a name="manage-resources"></a>Gerir recursos

* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
