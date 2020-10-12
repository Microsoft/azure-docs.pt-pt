---
title: Tempestade Apache com componentes Python - Azure HDInsight
description: Saiba como criar uma topologia da Tempestade Apache que usa componentes Python em Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017, devx-track-python
ms.date: 12/16/2019
ms.openlocfilehash: 1f062a8f358ac311b53e657fb5714583458bd9b5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87872587"
---
# <a name="develop-apache-storm-topologies-using-python-on-hdinsight"></a>Desenvolver topologias de tempestade Apache usando Python em HDInsight

Aprenda a criar uma topologia [da Tempestade Apache](https://storm.apache.org/) que usa componentes Python. A Tempestade Apache suporta várias línguas, permitindo mesmo combinar componentes de várias línguas numa topologia. O [quadro flux](https://storm.apache.org/releases/current/flux.html) (introduzido com a Tempestade 0.10.0) permite-lhe criar facilmente soluções que utilizem componentes Python.

> [!IMPORTANT]  
> A informação neste documento foi testada com storm on HDInsight 3.6.

## <a name="prerequisites"></a>Pré-requisitos

* Um aglomerado de tempestade Apache em HDInsight. Consulte [os clusters Apache Hadoop utilizando o portal Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) e selecione **Storm** for **Cluster type**.

* Um ambiente local de desenvolvimento de tempestades (opcional). Um ambiente local de tempestade só é necessário se você quiser executar a topologia localmente. Para obter mais informações, consulte [configurar um ambiente de desenvolvimento.](https://storm.apache.org/releases/current/Setting-up-development-environment.html)

* [Python 2.7 ou superior](https://www.python.org/downloads/).

* [Java Developer Kit (JDK) versão 8](https://aka.ms/azure-jdks).

* [Apache Maven](https://maven.apache.org/download.cgi) devidamente [instalado de](https://maven.apache.org/install.html) acordo com Apache.  Maven é um sistema de construção de projetos para projetos java.

## <a name="storm-multi-language-support"></a>Apoio multi-linguístico da tempestade

A Tempestade Apache foi projetada para trabalhar com componentes escritos usando qualquer linguagem de programação. Os componentes devem entender como trabalhar com a definição thrift para storm. Para python, um módulo é fornecido como parte do projeto Apache Storm que lhe permite interagir facilmente com Storm. Pode encontrar este módulo em [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py) .

Tempestade é um processo java que funciona na Máquina Virtual Java (JVM). Os componentes escritos noutras línguas são executados como subprocessos. A Tempestade comunica com estes subprocessos usando mensagens JSON enviadas através de stdin/stdout. Mais detalhes sobre a comunicação entre componentes podem ser encontrados na documentação do [Protocolo Multi-Lang.](https://storm.apache.org/releases/current/Multilang-protocol.html)

## <a name="python-with-the-flux-framework"></a>Python com a estrutura do fluxo

A estrutura flux permite definir as topologias da tempestade separadamente dos componentes. A estrutura do fluxo usa YAML para definir a topologia da tempestade. O texto a seguir é um exemplo de como fazer referência a um componente Python no documento YAML:

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

A aula `FluxShellSpout` é usada para iniciar o script que implementa o `sentencespout.py` bico.

O Flux espera que os scripts Python estejam no `/resources` diretório dentro do ficheiro do frasco que contém a topologia. Então este exemplo armazena os scripts Python no `/multilang/resources` diretório. `pom.xml`O ficheiro inclui este ficheiro utilizando o seguinte XML:

```xml
<!-- include the Python components -->
<resource>
    <directory>${basedir}/multilang</directory>
    <filtering>false</filtering>
</resource>
```

Como mencionado anteriormente, há um `storm.py` ficheiro que implementa a definição de Thrift para storm. A estrutura do Fluxo inclui `storm.py` automaticamente quando o projeto é construído, para que não tenha que se preocupar em incluí-lo.

## <a name="build-the-project"></a>Compilar o projeto

1. Descarregue o projeto a partir de [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount) .

1. Abra um pedido de comando e navegue até à raiz do projeto: `hdinsight-python-storm-wordcount-master` . Introduza o seguinte comando:

    ```cmd
    mvn clean compile package
    ```

    Este comando cria um `target/WordCount-1.0-SNAPSHOT.jar` ficheiro que contém a topologia compilada.

## <a name="run-the-storm-topology-on-hdinsight"></a>Executar a topologia da tempestade em HDInsight

1. Use [o comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para copiar o `WordCount-1.0-SNAPSHOT.jar` ficheiro para o seu storm on HDInsight cluster. Edite o comando abaixo substituindo o CLUSTERNAME pelo nome do seu cluster e, em seguida, insira o comando:

    ```cmd
    scp target/WordCount-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. Uma vez que o ficheiro tenha sido carregado, ligue-se ao cluster utilizando sSH:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. A partir da sessão SSH, use o seguinte comando para iniciar a topologia no cluster:

    ```bash
    storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -r -R /topology.yaml
    ```

    Uma vez iniciado, uma topologia da tempestade corre até parar.

1. Use a UI da tempestade para ver a topologia no aglomerado. A UI da tempestade está localizada em `https://CLUSTERNAME.azurehdinsight.net/stormui` . `CLUSTERNAME`Substitua-o pelo seu nome de agrupamento.

1. Pare a topologia da tempestade. Utilize o seguinte comando para parar a topologia do cluster:

    ```bash
    storm kill wordcount
    ```

    Em alternativa, pode utilizar a UI storm. Sob **as ações de Topologia** para a topologia, selecione **Kill**.

## <a name="run-the-topology-locally"></a>Executar a topologia localmente

Para executar a topologia localmente, use o seguinte comando:

```bash
storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -l -R /topology.yaml
```

> [!NOTE]  
> Este comando requer um ambiente local de desenvolvimento da tempestade. Para obter mais informações, consulte [configurar um ambiente de desenvolvimento.](https://storm.apache.org/releases/current/Setting-up-development-environment.html)

Assim que a topologia começa, emite informações para a consola local semelhantes ao seguinte texto:

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

Consulte os seguintes documentos para outras formas de utilizar Python com HDInsight: [Como utilizar funções definidas do utilizador Python (UDF) em Apache Pig e Apache Hive](../hadoop/python-udf-hdinsight.md).
