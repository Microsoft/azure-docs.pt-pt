---
title: O Apache Storm com componentes de Python - Azure HDInsight
description: Saiba como criar uma topologia de Apache Storm utiliza componentes de Python.
author: hrasheed-msft
ms.reviewer: jasonh
keywords: python do Apache storm
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/30/2018
ms.author: hrasheed
ms.openlocfilehash: a5cbd54dd07143688b676c063133bb1a73bed01a
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64694394"
---
# <a name="develop-apache-storm-topologies-using-python-on-hdinsight"></a>Desenvolver topologias do Apache Storm com Python no HDInsight

Saiba como criar uma [Apache Storm](https://storm.apache.org/) topologia que utiliza componentes de Python. O Apache Storm suporta vários idiomas, até mesmo permitindo-lhe combinar componentes de várias linguagens numa topologia. O [fluxo](https://storm.apache.org/releases/current/flux.html) framework (introduzida com o Storm 0.10.0) permite-lhe facilmente criar soluções que utilizam componentes de Python.

> [!IMPORTANT]  
> As informações neste documento foi testadas com o Storm no HDInsight 3.6. O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

O código para este projeto está disponível em [ https://github.com/Azure-Samples/hdinsight-python-storm-wordcount ](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount).

## <a name="prerequisites"></a>Pré-requisitos

* Python 2.7 ou superior

* Java JDK 1.8 ou superior

* [Apache Maven 3](https://maven.apache.org/download.cgi)

* (Opcional) Um ambiente de desenvolvimento local do Storm. Um ambiente local do Storm só é necessário se pretender executar localmente a topologia. Para obter mais informações, consulte [como configurar um ambiente de desenvolvimento](https://storm.apache.org/releases/1.1.2/Setting-up-development-environment.html).

## <a name="storm-multi-language-support"></a>Suporte a vários idiomas Storm

O Apache Storm foi concebido para trabalhar com componentes escritos usando qualquer linguagem de programação. Os componentes tem de compreender como trabalhar com o [definição de Thrift para o Storm](https://github.com/apache/storm/blob/master/storm-core/src/storm.thrift). Para o Python, um módulo é fornecido como parte do projeto Apache Storm, que permite-lhe interagir facilmente com o Storm. Pode encontrar este módulo na [ https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py ](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py).

O Storm é um processo de Java que é executado na máquina Virtual de Java (JVM). Componentes escritos em outras linguagens são executados como subprocessos por. O Storm comunica com estes subprocessos por usando mensagens JSON enviadas através de stdin/stdout. Obter mais detalhes sobre a comunicação entre os componentes podem ser encontradas no [protocolo várias lang](https://storm.apache.org/documentation/Multilang-protocol.html) documentação.

## <a name="python-with-the-flux-framework"></a>Python com o framework de fluxo

A estrutura de fluxo permite-lhe definir topologias do Storm separadamente a partir dos componentes. A estrutura de fluxo usa YAML para definir a topologia do Storm. O texto seguinte é um exemplo de como fazer referência a um componente de Python no documento YAML:

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

A classe `FluxShellSpout` é utilizada para iniciar o `sentencespout.py` script que implementa o spout.

Fluxo espera que os scripts de Python no `/resources` diretório dentro do ficheiro jar que contém a topologia. Portanto, neste exemplo armazena os scripts de Python no `/multilang/resources` diretório. O `pom.xml` inclui este ficheiro com o seguinte XML:

```xml
<!-- include the Python components -->
<resource>
    <directory>${basedir}/multilang</directory>
    <filtering>false</filtering>
</resource>
```

Como mencionado anteriormente, há um `storm.py` ficheiro que implementa a definição de Thrift para o Storm. A estrutura de fluxo inclui `storm.py` automaticamente quando o projeto for criado, para que não tenha de se preocupar sobre incluí-lo.

## <a name="build-the-project"></a>Compilar o projeto

A partir da raiz do projeto, utilize o seguinte comando:

```bash
mvn clean compile package
```

Este comando cria um `target/WordCount-1.0-SNAPSHOT.jar` ficheiro que contém a topologia compilada.

## <a name="run-the-topology-locally"></a>Executar localmente a topologia

Para executar a topologia localmente, utilize o seguinte comando:

```bash
storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -l -R /topology.yaml
```

> [!NOTE]  
> Este comando necessita de um ambiente de desenvolvimento local do Storm. Para obter mais informações, consulte [configurar um ambiente de desenvolvimento](https://storm.apache.org/releases/current/Setting-up-development-environment.html)

Uma vez iniciada a topologia, ele emite informações para a consola local semelhante ao seguinte texto:


    24302 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
    24302 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting the
    24302 [Thread-28] INFO  o.a.s.t.ShellBolt - ShellLog pid:2437, name:counter-bolt Emitting years:160
    24302 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=the, count=599}
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=seven, count=302}
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=dwarfs, count=143}
    24303 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
    24303 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting cow
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=four, count=160}


Para parar a topologia, utilize __Ctrl + C__.

## <a name="run-the-storm-topology-on-hdinsight"></a>Execute a topologia do Storm no HDInsight

1. Utilize o comando seguinte para copiar o `WordCount-1.0-SNAPSHOT.jar` ficheiro para o Storm no cluster do HDInsight:

    ```bash
    scp target\WordCount-1.0-SNAPSHOT.jar sshuser@mycluster-ssh.azurehdinsight.net
    ```

    Substitua `sshuser` com o utilizador SSH para o seu cluster. Substitua `mycluster` com o nome do cluster. Poderá ser-lhe pedido para introduzir a palavra-passe do utilizador SSH.

    Para obter mais informações sobre como utilizar SSH e SCP, consulte [utilizar o SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Depois do ficheiro foi carregado, ligue-se ao cluster através de SSH:

    ```bash
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. A partir da sessão SSH, utilize o seguinte comando para iniciar a topologia no cluster:

    ```bash
    storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -r -R /topology.yaml
    ```

3. Pode utilizar a IU do Storm para ver a topologia no cluster. A IU do Storm está localizado em https://mycluster.azurehdinsight.net/stormui. Substitua `mycluster` com o nome do cluster.

> [!NOTE]  
> Depois de iniciada, uma topologia do Storm é executado até serem parados. Para parar a topologia, utilize um dos seguintes métodos:
>
> * O `storm kill TOPOLOGYNAME` comando na linha de comando
> * O **Kill** botão na IU do Storm.


## <a name="next-steps"></a>Passos Seguintes

Consulte os seguintes documentos para outras formas de utilizar o Python com HDInsight:

* [Como utilizar o Python definidas pelo utilizador funções (UDF) no Apache Pig e Apache Hive](../hadoop/python-udf-hdinsight.md)
