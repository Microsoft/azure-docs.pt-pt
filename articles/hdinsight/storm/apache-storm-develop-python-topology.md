---
title: Apache Storm com os componentes do Python – Azure HDInsight
description: Saiba como criar uma topologia de Apache Storm que usa componentes do Python no Azure HDInsight
author: hrasheed-msft
ms.reviewer: jasonh
keywords: Python do Apache Storm
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/30/2018
ms.author: hrasheed
ms.openlocfilehash: a15506632e90edae235c3d1889603ca4997a3398
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/09/2019
ms.locfileid: "70813890"
---
# <a name="develop-apache-storm-topologies-using-python-on-hdinsight"></a>Desenvolver topologias de Apache Storm usando Python no HDInsight

Saiba como criar uma topologia de [Apache Storm](https://storm.apache.org/) que usa componentes do Python. O Apache Storm dá suporte a vários idiomas, até mesmo permitindo que você combine componentes de vários idiomas em uma topologia. A estrutura de [fluxo](https://storm.apache.org/releases/current/flux.html) (introduzida com o Storm 0.10.0) permite que você crie facilmente soluções que usam componentes do Python.

> [!IMPORTANT]  
> As informações neste documento foram testadas usando o Storm no HDInsight 3,6. 

O código deste projeto está disponível em [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount).

## <a name="prerequisites"></a>Pré-requisitos

* Python 2,7 ou superior

* Java JDK 1,8 ou superior

* [Apache Maven 3](https://maven.apache.org/download.cgi)

* Adicional Um ambiente de desenvolvimento Storm local. Um ambiente Storm local só será necessário se você quiser executar a topologia localmente. Para obter mais informações, consulte [Configurando um ambiente de desenvolvimento](http://storm.apache.org/releases/current/Setting-up-development-environment.html).

## <a name="storm-multi-language-support"></a>Suporte a vários idiomas do Storm

Apache Storm foi projetado para trabalhar com componentes escritos usando qualquer linguagem de programação. Os componentes devem entender como trabalhar com a [definição de thrift para Storm](https://github.com/apache/storm/blob/master/storm-core/src/storm.thrift). Para o Python, um módulo é fornecido como parte do projeto de Apache Storm que permite que você faça a interface facilmente com o Storm. Você pode encontrar este módulo em [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py).

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

A classe `FluxShellSpout` é usada para iniciar o `sentencespout.py` script que implementa o Spout.

O fluxo espera que os scripts Python estejam no `/resources` diretório dentro do arquivo JAR que contém a topologia. Portanto, este exemplo armazena os scripts do Python `/multilang/resources` no diretório. O `pom.xml` inclui este arquivo usando o seguinte XML:

```xml
<!-- include the Python components -->
<resource>
    <directory>${basedir}/multilang</directory>
    <filtering>false</filtering>
</resource>
```

Como mencionado anteriormente, há um `storm.py` arquivo que implementa a definição de thrift para Storm. A estrutura de fluxo `storm.py` inclui automaticamente quando o projeto é compilado, de modo que você não precisa se preocupar em incluí-lo.

## <a name="build-the-project"></a>Compilar o projeto

Na raiz do projeto, use o seguinte comando:

```bash
mvn clean compile package
```

Este comando cria um `target/WordCount-1.0-SNAPSHOT.jar` arquivo que contém a topologia compilada.

## <a name="run-the-topology-locally"></a>Executar a topologia localmente

Para executar a topologia localmente, use o seguinte comando:

```bash
storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -l -R /topology.yaml
```

> [!NOTE]  
> Esse comando requer um ambiente de desenvolvimento Storm local. Para obter mais informações, consulte [Configurando um ambiente de desenvolvimento](https://storm.apache.org/releases/current/Setting-up-development-environment.html)

Depois que a topologia é iniciada, ela emite informações para o console local semelhante ao seguinte texto:


    24302 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
    24302 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting the
    24302 [Thread-28] INFO  o.a.s.t.ShellBolt - ShellLog pid:2437, name:counter-bolt Emitting years:160
    24302 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=the, count=599}
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=seven, count=302}
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=dwarfs, count=143}
    24303 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
    24303 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting cow
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=four, count=160}


Para interromper a topologia, use __Ctrl + C__.

## <a name="run-the-storm-topology-on-hdinsight"></a>Executar a topologia do Storm no HDInsight

1. Use o comando a seguir para copiar `WordCount-1.0-SNAPSHOT.jar` o arquivo para o Storm no cluster HDInsight:

    ```bash
    scp target\WordCount-1.0-SNAPSHOT.jar sshuser@mycluster-ssh.azurehdinsight.net
    ```

    Substitua `sshuser` pelo usuário SSH para o cluster. Substituir `mycluster` pelo nome do cluster. Você pode ser solicitado a inserir a senha para o usuário SSH.

    Para obter mais informações sobre como usar SSH e SCP, consulte [usar SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Depois que o arquivo for carregado, conecte-se ao cluster usando SSH:

    ```bash
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. Na sessão SSH, use o seguinte comando para iniciar a topologia no cluster:

    ```bash
    storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -r -R /topology.yaml
    ```

3. Você pode usar a interface do usuário do Storm para exibir a topologia no cluster. A interface do usuário do Storm https://mycluster.azurehdinsight.net/stormui está localizada em. Substitua `mycluster` pelo nome do cluster.

> [!NOTE]  
> Depois de iniciada, uma topologia Storm é executada até ser interrompida. Para interromper a topologia, use um dos seguintes métodos:
>
> * O `storm kill TOPOLOGYNAME` comando da linha de comando
> * O botão de **eliminação** na interface do usuário do Storm.


## <a name="next-steps"></a>Passos Seguintes

Consulte os seguintes documentos para obter outras maneiras de usar o Python com o HDInsight:

* [Como usar UDF (funções definidas pelo usuário) do Python no Apache Pig e Apache Hive](../hadoop/python-udf-hdinsight.md)
