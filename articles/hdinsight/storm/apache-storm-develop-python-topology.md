---
title: Tempestade Apache com componentes Python - Azure HDInsight
description: Saiba como criar uma topologia de tempestade Apache que usa componentes Python em Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/16/2019
ms.openlocfilehash: 20e4827b1a86bff338646ef71f0dd732255c09c9
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77460029"
---
# <a name="develop-apache-storm-topologies-using-python-on-hdinsight"></a>Desenvolver topoologias de tempestade Apache usando Python no HDInsight

Aprenda a criar uma topologia [de tempestade Apache](https://storm.apache.org/) que usa componentes Python. A Tempestade Apache suporta várias línguas, permitindo mesmo combinar componentes de várias línguas numa topologia. A estrutura [de Fluxo](https://storm.apache.org/releases/current/flux.html) (introduzida com a Tempestade 0.10.0) permite-lhe criar facilmente soluções que utilizem componentes Python.

> [!IMPORTANT]  
> A informação neste documento foi testada usando storm no HDInsight 3.6.

## <a name="prerequisites"></a>Pré-requisitos

* Um aglomerado de tempestade Apache no HDInsight. Consulte os [clusters De Apache Hadoop utilizando o portal Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) e selecione **Storm** for **Cluster type**.

* Um ambiente local de desenvolvimento de tempestades (Opcional). Um ambiente de tempestade local só é necessário se quiser gerir a topologia localmente. Para mais informações, consulte a [criação de um ambiente](https://storm.apache.org/releases/current/Setting-up-development-environment.html)de desenvolvimento.

* [Python 2.7 ou superior.](https://www.python.org/downloads/)

* [Java Developer Kit (JDK) versão 8](https://aka.ms/azure-jdks).

* [Apache Maven](https://maven.apache.org/download.cgi) devidamente [instalado](https://maven.apache.org/install.html) de acordo com Apache.  Maven é um sistema de construção de projetos para projetos Java.

## <a name="storm-multi-language-support"></a>Suporte multilingma tempestade

A Tempestade Apache foi projetada para trabalhar com componentes escritos usando qualquer linguagem de programação. Os componentes devem entender como trabalhar com a definição thrift para tempestade. Para python, um módulo é fornecido como parte do projeto Apache Storm que permite-lhe interagir facilmente com storm. Pode encontrar este módulo em [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py).

Storm é um processo de Java que funciona na Máquina Virtual de Java (JVM). Os componentes escritos noutras línguas são executados como subprocessos. A Tempestade comunica com estes subprocessos usando mensagens JSON enviadas por stdin/stdout. Mais detalhes sobre a comunicação entre componentes podem ser encontrados na documentação do [Protocolo Multi-lang.](https://storm.apache.org/releases/current/Multilang-protocol.html)

## <a name="python-with-the-flux-framework"></a>Python com a estrutura do Fluxo

A estrutura do Fluxo permite-lhe definir as topoologias da tempestade separadamente dos componentes. A estrutura do Fluxo usa o YAML para definir a topologia da tempestade. O seguinte texto é um exemplo de como fazer referência a um componente Python no documento YAML:

```yaml
# Spout definitions
spouts:
  - id: "sentence-spout"
    className: "org.apache.storm.flux.wrappers.spouts.FluxShellSpout"
    constructorArgs:
      # Command line
      - ["python", "sentencespout.py"]
      # Output field(s)
      - ["sentence"]
    # parallelism hint
    parallelism: 1
```

A classe `FluxShellSpout` é usada para iniciar o `sentencespout.py` script que implementa o bico.

O Flux espera que os scripts python estejam no `/resources` diretório dentro do ficheiro do frasco que contém a topologia. Portanto, este exemplo armazena os scripts Python no diretório `/multilang/resources`. O `pom.xml` inclui este ficheiro utilizando o seguinte XML:

```xml
<!-- include the Python components -->
<resource>
    <directory>${basedir}/multilang</directory>
    <filtering>false</filtering>
</resource>
```

Como mencionado anteriormente, há um ficheiro `storm.py` que implementa a definição thrift para tempestade. A estrutura do Flux inclui `storm.py` automaticamente quando o projeto é construído, para que não tenha que se preocupar em incluí-lo.

## <a name="build-the-project"></a>Compilar o projeto

1. Transfira o projeto a partir de [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount).

1. Abra um pedido de comando e navegue para a raiz do projeto: `hdinsight-python-storm-wordcount-master`. Introduza o seguinte comando:

    ```cmd
    mvn clean compile package
    ```

    Este comando cria um ficheiro `target/WordCount-1.0-SNAPSHOT.jar` que contém a topologia compilada.

## <a name="run-the-storm-topology-on-hdinsight"></a>Executar a topologia da tempestade no HDInsight

1. Utilize o [comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para copiar o ficheiro `WordCount-1.0-SNAPSHOT.jar` para o seu cluster Storm on HDInsight. Editar o comando abaixo substituindo CLUSTERNAME pelo nome do seu cluster e, em seguida, introduzir o comando:

    ```cmd
    scp target/WordCount-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. Uma vez que o ficheiro tenha sido carregado, ligue-se ao cluster utilizando SSH:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. A partir da sessão SSH, utilize o seguinte comando para iniciar a topologia no cluster:

    ```bash
    storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -r -R /topology.yaml
    ```

    Uma vez iniciado, uma topologia da tempestade corre até parar.

1. Use a Tempestade UI para ver a topologia no cluster. A Tempestade UI está localizada em `https://CLUSTERNAME.azurehdinsight.net/stormui`. Substitua `CLUSTERNAME` pelo nome do cluster.

1. Parem a topologia da tempestade. Utilize o seguinte comando para parar a topologia no cluster:

    ```bash
    storm kill wordcount
    ```

    Em alternativa, pode usar a Tempestade UI. Sob as ações de **Topologia** para a topologia, selecione **Kill**.

## <a name="run-the-topology-locally"></a>Executar a topologia localmente

Para executar a topologia localmente, use o seguinte comando:

```bash
storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -l -R /topology.yaml
```

> [!NOTE]  
> Este comando requer um ambiente local de desenvolvimento de tempestades. Para mais informações, consulte a [criação de um ambiente](https://storm.apache.org/releases/current/Setting-up-development-environment.html)de desenvolvimento.

Uma vez iniciada a topologia, emite informações para a consola local semelhantes ao seguinte texto:

```output
24302 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
24302 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting the
24302 [Thread-28] INFO  o.a.s.t.ShellBolt - ShellLog pid:2437, name:counter-bolt Emitting years:160
24302 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=the, count=599}
24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=seven, count=302}
24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=dwarfs, count=143}
24303 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
24303 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting cow
24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=four, count=160}
```

Para parar a topologia, utilize __Ctrl + C__.

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes documentos para outras formas de utilizar python com HDInsight: [Como utilizar funções definidas pelo utilizador Python (UDF) em Apache Pig e Apache Hive](../hadoop/python-udf-hdinsight.md).
