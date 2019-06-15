---
title: Utilizar o Caffe para aprendizagem profunda distribuída no Azure HDInsight Spark
description: Utilizar o Caffe para aprendizagem profunda distribuída no Azure HDInsight Spark
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/17/2017
ms.openlocfilehash: d0d68263485c5ab6e57a349317b1975862470cc2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64721512"
---
# <a name="use-caffe-on-azure-hdinsight-spark-for-distributed-deep-learning"></a>Utilizar o Caffe para aprendizagem profunda distribuída no Azure HDInsight Spark


## <a name="introduction"></a>Introdução

Aprendizagem profunda está a afetar a tudo, desde cuidados de saúde para transporte para fabricação e muito mais. As empresas estão transformando para resolver problemas difíceis, como de aprendizagem profunda [classificação de imagens](https://blogs.microsoft.com/next/2015/12/10/microsoft-researchers-win-imagenet-computer-vision-challenge/), [reconhecimento de fala](https://googleresearch.blogspot.jp/2015/08/the-neural-networks-behind-google-voice.html), reconhecimento de objeto e tradução do computador. 

Existem [muitas estruturas populares](https://en.wikipedia.org/wiki/Comparison_of_deep_learning_software), incluindo [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/research/product/cognitive-toolkit/), [Tensorflow](https://www.tensorflow.org/), [Apache MXNet](https://mxnet.apache.org/), Theano, etc. [Caffe](https://caffe.berkeleyvision.org/) é uma das arquiteturas de rede neural de (imperativo) não simbólico mais famosas e amplamente usados em muitas áreas, incluindo a visão do computador. Além disso, [CaffeOnSpark](https://yahoohadoop.tumblr.com/post/139916563586/caffeonspark-open-sourced-for-distributed-deep) combina Caffe com o Apache Spark, caso em que aprendizagem profunda pode ser facilmente utilizado num cluster do Hadoop existente. Pode utilizar a aprendizagem profunda, juntamente com os pipelines de ETL do Spark, reduzindo a complexidade de sistema e latência para a aprendizagem automática da solução completa.

[HDInsight](https://azure.microsoft.com/services/hdinsight/) é uma cloud Apache Hadoop oferta que fornece clusters de análise de código-fonte aberto otimizadas para o Apache Spark, Apache Hive, Apache Hadoop, Apache HBase, Apache Storm, Apache Kafka e serviços de ML. HDInsight é apoiada por um SLA de 99,9%. Cada uma dessas tecnologias de macrodados e aplicações ISV é facilmente implementável como clusters geridos com segurança e monitorização para as empresas.

Este artigo demonstra como instalar o [Caffe no Spark](https://github.com/yahoo/CaffeOnSpark) para um cluster do HDInsight. Este artigo utiliza também a demonstração MNIST incorporada para mostrar como utilizar a aprendizagem profunda distribuído com o Spark do HDInsight nas CPUs.

Existem quatro passos para realizar a tarefa:

1. Instalar as dependências necessárias em todos os nós
2. Criar Caffe no Spark para HDInsight no nó principal
3. Distribuir as bibliotecas necessárias para todos os nós de trabalho
4. Compor um modelo do Caffe e executá-lo de maneira distribuída.

Uma vez que o HDInsight é uma solução de PaaS, oferece funcionalidades de plataforma excelente -, portanto, é fácil de realizar algumas tarefas. Um dos recursos utilizados nesta postagem no blog é chamado [ação de Script](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux), com a qual pode executar comandos da shell para personalizar nós de cluster (nó principal, nó de trabalho ou nó de extremidade).

## <a name="step-1--install-the-required-dependencies-on-all-the-nodes"></a>Passo 1:  Instalar as dependências necessárias em todos os nós

Para começar, terá de instalar as dependências. O site do Caffe e [CaffeOnSpark site](https://github.com/yahoo/CaffeOnSpark/wiki/GetStarted_yarn) oferece alguns wiki útil para instalar as dependências para o Spark no modo YARN. HDInsight também utiliza o Spark no modo YARN. No entanto, terá de adicionar algumas dependências mais para a plataforma do HDInsight. Para tal, utilize uma ação de script e executá-lo em todos os nós principais e nós de trabalho. Esta ação de script demora cerca de 20 minutos, como essas dependências também dependem de outros pacotes. Deve colocá-los em algum local que esteja acessível ao seu cluster do HDInsight, como uma localização do GitHub ou a conta de armazenamento de BLOBS predefinido.

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


Há duas etapas na ação de script. A primeira etapa é instalar todas as bibliotecas necessárias. Essas bibliotecas incluem as bibliotecas necessárias para compilar o Caffe (por exemplo, gflags, glog) e em execução Caffe (por exemplo, numpy). está a utilizar libatlas para otimização de CPU, mas pode seguir sempre o wiki CaffeOnSpark sobre como instalar outras bibliotecas de otimização, como MKL ou CUDA (para GPU).

O segundo passo consiste em transferir, compilar e instalar o protobuf 2.5.0 para Caffe durante o tempo de execução. O Protobuf 2.5.0 [é necessário](https://github.com/yahoo/CaffeOnSpark/issues/87), no entanto, esta versão não está disponível como um pacote em 16 de Ubuntu, por isso terá de compilá-lo do código-fonte. Também existem alguns recursos na Internet sobre como compilá-lo. Para obter mais informações, consulte [aqui](https://jugnu-life.blogspot.com/2013/09/install-protobuf-25-on-ubuntu.html).

Para começar, basta executar esta ação de script no cluster a todos os nós de trabalho e nós principais (para o HDInsight 3.5). Pode executar as ações de script num cluster existente, ou utilize ações de script durante a criação de cluster. Para obter mais informações sobre as ações de script, consulte a documentação [aqui](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).

![Ações de script para instalar dependências](./media/apache-spark-deep-learning-caffe/Script-Action-1.png)


## <a name="step-2-build-caffe-on-apache-spark-for-hdinsight-on-the-head-node"></a>Passo 2: Criar Caffe no Apache Spark para HDInsight no nó principal

A segunda etapa é criar Caffe num nó principal e, em seguida, distribuir as bibliotecas compiladas para todos os nós de trabalho. Neste passo, deve [ssh no seu nó principal](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix). Depois disso, tem de seguir a [processo de compilação CaffeOnSpark](https://github.com/yahoo/CaffeOnSpark/wiki/GetStarted_yarn). Segue-se o script, que pode usar para criar CaffeOnSpark com alguns passos adicionais. 

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

Terá de fazer mais do que o que diz a documentação de CaffeOnSpark. As alterações são:
- Alterar apenas a CPU e utilize libatlas para esta finalidade específica.
- Coloque os conjuntos de dados para o armazenamento de BLOBS, que é uma localização partilhada que está acessível para todos os nós de trabalho para utilização posterior.
- Colocar as bibliotecas do Caffe compiladas para o armazenamento de BLOBS e, depois, copie essas bibliotecas para todos os nós com ações de script para evitar o tempo de compilação adicionais.


### <a name="troubleshooting-an-ant-buildexception-has-occurred-exec-returned-2"></a>Resolução de problemas: Ocorreu um BuildException Ant: exec devolvido: 2

Quando o primeiro a tentar criar CaffeOnSpark, às vezes, que diz

    failed to execute goal org.apache.maven.plugins:maven-antrun-plugin:1.7:run (proto) on project caffe-distri: An Ant BuildException has occurred: exec returned: 2

Limpar o repositório de código por "tornar limpa" e, em seguida, execute "make criar" para resolver este problema, desde que terá as dependências corretas.

### <a name="troubleshooting-maven-repository-connection-time-out"></a>Resolução de problemas: Tempo limite de ligação do repositório maven

Por vezes, o maven dá um erro de tempo limite de ligação, semelhante ao seguinte fragmento:

    Retry:
    [INFO] Downloading: https://repo.maven.apache.org/maven2/com/twitter/chill_2.11/0.8.0/chill_2.11-0.8.0.jar
    Feb 01, 2017 5:14:49 AM org.apache.maven.wagon.providers.http.httpclient.impl.execchain.RetryExec execute
    INFO: I/O exception (java.net.SocketException) caught when processing request to {s}->https://repo.maven.apache.org:443: Connection timed out (Read failed)

Tem novamente após alguns minutos.


### <a name="troubleshooting-test-failure-for-caffe"></a>Resolução de problemas: Falha de teste para Caffe

Provavelmente ver uma falha de teste ao fazer a verificação final para CaffeOnSpark. Isso provavelmente está relacionado com codificação UTF-8, mas não deve afetar a utilização do Caffe

    Run completed in 32 seconds, 78 milliseconds.
    Total number of tests run: 7
    Suites: completed 5, aborted 0
    Tests: succeeded 6, failed 1, canceled 0, ignored 0, pending 0
    *** 1 TEST FAILED ***

## <a name="step-3-distribute-the-required-libraries-to-all-the-worker-nodes"></a>Passo 3: Distribuir as bibliotecas necessárias para todos os nós de trabalho

A próxima etapa é distribuir as bibliotecas (basicamente, as bibliotecas em CaffeOnSpark/caffe-público/distribuir/lib/e CaffeOnSpark/caffe-distri/distribuir/lib /) a todos os nós. No passo 2, coloca essas bibliotecas no armazenamento de BLOBS e, neste passo, vai utilizar as ações de script para copiá-lo a todos os nós principais e nós de trabalho.

Para tal, execute uma ação de script, conforme mostrado no seguinte fragmento:

    #!/bin/bash
    hadoop fs -get wasb:///CaffeOnSpark /home/changetoyourusername/

Certifique-se de que precisa ponto para a localização certa específicos ao seu cluster)

Porque o passo 2, colocá-lo no armazenamento de BLOBS, que é acessível a todos os nós, neste passo, basta copiá-lo a todos os nós.

## <a name="step-4-compose-a-caffe-model-and-run-it-in-a-distributed-manner"></a>Passo 4: Compor um modelo do Caffe e executá-lo de maneira distribuída

Caffe está instalado depois de executar os passos anteriores. A próxima etapa é escrever um modelo do Caffe. 

Caffe é "arquitetura expressiva", que onde para compor um modelo, basta definir o ficheiro de configuração, o uso e sem codificação em todos os (na maioria dos casos). Vamos então dar uma olhada aqui. 

O modelo que treinar é um modelo de exemplo para treinamento de MNIST. A base de dados MNIST de dígitos manuscritos tem um conjunto de treinamento de 60 000 exemplos e um conjunto de teste de 10 000 exemplos. É um subconjunto de um conjunto maior disponível a partir do NIST. Os dígitos foram normalizados de tamanho e centralizado numa imagem de tamanho fixo. CaffeOnSpark tem alguns scripts para transferir o conjunto de dados e convertê-lo no formato correto.

CaffeOnSpark fornece alguns exemplos de topologias de rede para treinamento de MNIST. Tem um bom design de dividir a arquitetura de rede (a topologia da rede) e a otimização. Neste caso, existem dois ficheiros necessários: 

o arquivo de "Solucionador" (${CAFFE_ON_SPARK}/data/lenet_memory_solver.prototxt) é usado para supervisionar a Otimização e geração de atualizações de parâmetro. Por exemplo, ele define se CPU ou de GPU é utilizado, o que é a dinâmica, o número de iterações é, etc. Também define a topologia de rede do neurônio deve o programa utilizar (que é o segundo arquivo que tem). Para obter mais informações sobre o solucionador, consulte [Caffe documentação](https://caffe.berkeleyvision.org/tutorial/solver.html).

Neste exemplo, como está usando a CPU, em vez de GPU, deve alterar a última linha para:

    # solver mode: CPU or GPU
    solver_mode: CPU

![Configuração do Caffe](./media/apache-spark-deep-learning-caffe/Caffe-1.png)

Pode alterar outras linhas, conforme necessário.

O segundo arquivo (${CAFFE_ON_SPARK}/data/lenet_memory_train_test.prototxt) define como a rede de neurônio é o aspeto e a entrada relevante e ficheiro de saída. terá também de atualizar o ficheiro para refletir a localização de dados de treinamento. Altere a parte seguinte lenet_memory_train_test.prototxt (precisa para apontar para a localização certa específica ao seu cluster):

- Altere o "file:/Users/mridul/bigml/demodl/mnist_train_lmdb" para "wasb: / / / projetos/machine_learning/image_dataset/mnist_train_lmdb"
- change "file:/Users/mridul/bigml/demodl/mnist_test_lmdb/" to "wasb:///projects/machine_learning/image_dataset/mnist_test_lmdb"

![Configuração do Caffe](./media/apache-spark-deep-learning-caffe/Caffe-2.png)

Para obter mais informações sobre como definir a rede, verifique o [Caffe documentação num conjunto de dados MNIST](https://caffe.berkeleyvision.org/gathered/examples/mnist.html)

Para efeitos deste artigo, utilize este exemplo MNIST. Execute os seguintes comandos do nó principal:

    spark-submit --master yarn --deploy-mode cluster --num-executors 8 --files ${CAFFE_ON_SPARK}/data/lenet_memory_solver.prototxt,${CAFFE_ON_SPARK}/data/lenet_memory_train_test.prototxt --conf spark.driver.extraLibraryPath="${LD_LIBRARY_PATH}" --conf spark.executorEnv.LD_LIBRARY_PATH="${LD_LIBRARY_PATH}" --class com.yahoo.ml.caffe.CaffeOnSpark ${CAFFE_ON_SPARK}/caffe-grid/target/caffe-grid-0.1-SNAPSHOT-jar-with-dependencies.jar -train -features accuracy,loss -label label -conf lenet_memory_solver.prototxt -devices 1 -connection ethernet -model wasb:///mnist.model -output wasb:///mnist_features_result

O comando anterior distribui os ficheiros necessários (lenet_memory_solver.prototxt e lenet_memory_train_test.prototxt) para cada contentor do YARN. O comando define também o caminho relevante de cada driver/executor do Spark para LD_LIBRARY_PATH. LD_LIBRARY_PATH é definido no trecho de código anterior e aponta para a localização que tenha CaffeOnSpark bibliotecas. 

## <a name="monitoring-and-troubleshooting"></a>Monitorização e resolução de problemas

Uma vez que estiver a utilizar o modo de cluster do YARN, caso em que será agendado com o controlador do Spark para um contentor arbitrário (e um nó de trabalho arbitrário) apenas deve conseguir ver na consola de saída de algo como:

    17/02/01 23:22:16 INFO Client: Application report for application_1485916338528_0015 (state: RUNNING)

Se deseja saber o que aconteceu, normalmente, terá de obter o Spark log do controlador, que tem mais informações. Neste caso, terá de aceder à IU do YARN para localizar os registos do YARN relevantes. Pode obter a IU do YARN por este URL: 

    https://yourclustername.azurehdinsight.net/yarnui
   
![IU DO YARN](./media/apache-spark-deep-learning-caffe/YARN-UI-1.png)

Pode dar uma olhada e quantos recursos são alocados para este aplicativo específico. Pode clicar na ligação "Scheduler" e, em seguida, verá que para esse aplicativo, há nove contentores em execução. Faça o YARN para fornecer oito executores e outro contentor destina-se o processo de driver. 

![Agendador YARN](./media/apache-spark-deep-learning-caffe/YARN-Scheduler.png)

Convém verificar os registos de controlador ou registos de contentor se existirem falhas. Para os registos de driver, pode clique o ID da aplicação na IU do YARN, em seguida, clique no botão "Registos". Os registos de driver são escritos em stderr.

![YARN UI 2](./media/apache-spark-deep-learning-caffe/YARN-UI-2.png)

Por exemplo, poderá ver algumas do erro abaixo dos registos de driver, indicando que alocar executores demasiados.

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

Às vezes, o problema pode acontecer em executores em vez de controladores. Neste caso, precisa verificar os registos de contentor. Pode sempre obter os registos de contentor e, em seguida, obter o contentor com falha. Por exemplo, poderá cumprir esta falha ao executar o Caffe.

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

Neste caso, terá de obter o ID de contentor com falhas (no caso acima, é container_1485916338528_0008_05_000005). Em seguida, tiver de executar 

    yarn logs -containerId container_1485916338528_0008_03_000005

no nó principal. Depois de verificar a falha de contentor, é provocado com o modo GPU (onde deve usar o modo de CPU em vez disso) no lenet_memory_solver.prototxt.

    17/02/01 07:10:48 INFO LMDB: Batch size:100
    WARNING: Logging before InitGoogleLogging() is written to STDERR
    F0201 07:10:48.309725 11624 common.cpp:79] Cannot use GPU in CPU-only Caffe: check mode.


## <a name="getting-results"></a>Obter resultados

Como aloca 8 executores, e a topologia de rede é simples, deve demorar apenas cerca de 30 minutos para o resultado da execução. Na linha de comando, pode ver que colocar o modelo para wasb:///mnist.model e colocar os resultados para uma pasta chamada wasb: / / / mnist_features_result.

Pode obter os resultados através da execução

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

O SampleID representa o ID do conjunto de dados MNIST e a etiqueta é o número que identifica o modelo.


## <a name="conclusion"></a>Conclusão

Nesta documentação, tentou instalar CaffeOnSpark com a execução de um exemplo simples. HDInsight é uma plataforma de computação distribuída completa gerida na cloud e é o melhor local para a execução de cargas de trabalho de análise avançada e de aprendizagem automática em grandes conjuntos de dados e para aprendizagem profunda distribuída, pode utilizar Caffe no Spark do HDInsight para executar a aprendizagem profunda tarefas.


## <a name="seealso"></a>Ver também
* [Descrição geral: Apache Spark no HDInsight do Azure](apache-spark-overview.md)

### <a name="scenarios"></a>Cenários
* [Apache Spark com Machine Learning: Utilizar o Spark no HDInsight para analisar a temperatura de construção com dados de AVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: Utilizar o Spark no HDInsight para prever resultados de inspeções alimentares](apache-spark-machine-learning-mllib-ipython.md)

### <a name="manage-resources"></a>Gerir recursos
* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)

