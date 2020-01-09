---
title: Apache Storm com os componentes do Python – Azure HDInsight
description: Saiba como criar uma topologia de Apache Storm que usa componentes do Python no Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/16/2019
ms.openlocfilehash: ba632a98c21926ec28606def128cc068abf47f53
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646630"
---
# <a name="develop-apache-storm-topologies-using-python-on-hdinsight"></a>Desenvolver topologias de Apache Storm usando Python no HDInsight

Saiba como criar uma topologia de [Apache Storm](https://storm.apache.org/) que usa componentes do Python. O Apache Storm dá suporte a vários idiomas, até mesmo permitindo que você combine componentes de vários idiomas em uma topologia. A estrutura de [fluxo](https://storm.apache.org/releases/current/flux.html) (introduzida com o Storm 0.10.0) permite que você crie facilmente soluções que usam componentes do Python.

> [!IMPORTANT]  
> As informações neste documento foram testadas usando o Storm no HDInsight 3,6.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster Apache Storm no HDInsight. Consulte [criar Apache Hadoop clusters usando o portal do Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) e selecione **Storm** para o **tipo de cluster**.

* Um ambiente de desenvolvimento Storm local (opcional). Um ambiente Storm local só será necessário se você quiser executar a topologia localmente. Para obter mais informações, consulte [Configurando um ambiente de desenvolvimento](http://storm.apache.org/releases/current/Setting-up-development-environment.html).

* [Python 2,7 ou superior](https://www.python.org/downloads/).

* [Java Developer Kit (JDK) versão 8](https://aka.ms/azure-jdks).

* O [Apache Maven](https://maven.apache.org/download.cgi) foi [instalado](https://maven.apache.org/install.html) corretamente de acordo com o Apache.  O Maven é um sistema de compilação de projeto para projetos Java.

## <a name="storm-multi-language-support"></a>Suporte a vários idiomas do Storm

Apache Storm foi projetado para trabalhar com componentes escritos usando qualquer linguagem de programação. Os componentes devem entender como trabalhar com a definição de thrift para Storm. Para o Python, um módulo é fornecido como parte do projeto de Apache Storm que permite que você faça a interface facilmente com o Storm. Você pode encontrar esse módulo em [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py).

O Storm é um processo Java executado no Máquina Virtual Java (JVM). Os componentes escritos em outras linguagens são executados como subprocessos. O Storm se comunica com esses subprocessos usando mensagens JSON enviadas por STDIN/STDOUT. Mais detalhes sobre a comunicação entre componentes podem ser encontrados na documentação do [protocolo multi-Lang](https://storm.apache.org/documentation/Multilang-protocol.html) .

## <a name="python-with-the-flux-framework"></a>Python com a estrutura de fluxo

A estrutura de fluxo permite definir topologias Storm separadamente dos componentes. A estrutura de fluxo usa YAML para definir a topologia Storm. O texto a seguir é um exemplo de como fazer referência a um componente do Python no documento YAML:

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

A classe `FluxShellSpout` é usada para iniciar o script de `sentencespout.py` que implementa o Spout.

O fluxo espera que os scripts Python estejam no diretório `/resources` dentro do arquivo JAR que contém a topologia. Portanto, este exemplo armazena os scripts Python no diretório `/multilang/resources`. O `pom.xml` inclui esse arquivo usando o seguinte XML:

```xml
<!-- include the Python components -->
<resource>
    <directory>${basedir}/multilang</directory>
    <filtering>false</filtering>
</resource>
```

Como mencionado anteriormente, há um arquivo `storm.py` que implementa a definição de thrift para Storm. A estrutura de fluxo inclui `storm.py` automaticamente quando o projeto é criado, de modo que você não precisa se preocupar em incluí-lo.

## <a name="build-the-project"></a>Compilar o projeto

1. Transfira o projeto a partir de [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount).

1. Abra um prompt de comando e navegue até a raiz do projeto: `hdinsight-python-storm-wordcount-master`. Introduza o seguinte comando:

    ```cmd
    mvn clean compile package
    ```

    Este comando cria um arquivo de `target/WordCount-1.0-SNAPSHOT.jar` que contém a topologia compilada.

## <a name="run-the-storm-topology-on-hdinsight"></a>Executar a topologia do Storm no HDInsight

1. Use o [comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para copiar o arquivo de `WordCount-1.0-SNAPSHOT.jar` para o Storm no cluster HDInsight. Edite o comando a seguir substituindo CLUSTERname pelo nome do cluster e, em seguida, digite o comando:

    ```cmd
    scp target/WordCount-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. Depois que o arquivo for carregado, conecte-se ao cluster usando SSH:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Na sessão SSH, use o seguinte comando para iniciar a topologia no cluster:

    ```bash
    storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -r -R /topology.yaml
    ```

    Depois de iniciada, uma topologia Storm é executada até ser interrompida.

1. Use a interface do usuário do Storm para exibir a topologia no cluster. A interface do usuário do Storm está localizada em `https://CLUSTERNAME.azurehdinsight.net/stormui`. Substitua `CLUSTERNAME` pelo nome do cluster.

1. Pare a topologia do Storm. Use o seguinte comando para interromper a topologia no cluster:

    ```bash
    storm kill wordcount
    ```

    Como alternativa, você pode usar a interface do usuário do Storm. Em **ações de topologia** para a topologia, selecione **Kill**.

## <a name="run-the-topology-locally"></a>Executar a topologia localmente

Para executar a topologia localmente, use o seguinte comando:

```bash
storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -l -R /topology.yaml
```

> [!NOTE]  
> Esse comando requer um ambiente de desenvolvimento Storm local. Para obter mais informações, consulte [Configurando um ambiente de desenvolvimento](https://storm.apache.org/releases/current/Setting-up-development-environment.html).

Depois que a topologia é iniciada, ela emite informações para o console local semelhante ao seguinte texto:

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

Para interromper a topologia, use __Ctrl + C__.

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes documentos para obter outras maneiras de usar o Python com [o HDInsight: como usar UDF (funções definidas pelo usuário) do Python no Apache Pig e Apache Hive](../hadoop/python-udf-hdinsight.md).
