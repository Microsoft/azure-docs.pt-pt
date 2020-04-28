---
title: Componentes de alta disponibilidade no Azure HDInsight
description: Visão geral dos vários componentes de alta disponibilidade utilizados pelos clusters HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/11/2019
ms.openlocfilehash: 38fb45fd339b5e2c7cab6f66a1ed6c0df73fb29e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74069631"
---
# <a name="high-availability-services-supported-by-azure-hdinsight"></a>Serviços de alta disponibilidade suportados pelo Azure HDInsight

 De forma a proporcionar-lhe os melhores níveis de disponibilidade para os seus componentes de análise, o HDInsight foi desenvolvido com uma arquitetura única para garantir uma elevada disponibilidade (HA) de serviços críticos. Alguns componentes desta arquitetura foram desenvolvidos pela Microsoft para fornecer falhas automáticas. Outros componentes são componentes Apache padrão que são implantados para apoiar serviços específicos. Este artigo explica a arquitetura do modelo de serviço HA no HDInsight, como o HDInsight suporta falhas nos serviços de HA e as melhores práticas para recuperar de outras interrupções de serviço.

## <a name="high-availability-infrastructure"></a>Infraestrutura de alta disponibilidade

O HDInsight fornece infraestruturas personalizadas para garantir que quatro serviços primários são de alta disponibilidade com capacidades automáticas de failover:

- Servidor Apache Ambari
- Servidor de linha do tempo de aplicação para YARN Apache
- Servidor de histórico de emprego para mapa de hadoopReduzir
- Apache Livy

Esta infraestrutura consiste numa série de serviços e componentes de software, alguns dos quais são desenhados pela Microsoft. Os seguintes componentes são exclusivos da plataforma HDInsight:

- Controlador de falha de escravo
- Controlador mestre failover
- Serviço de alta disponibilidade de escravos
- Master high availability service

![infraestrutura de alta disponibilidade](./media/hdinsight-high-availability-components/high-availability-architecture.png)

Existem também outros serviços de alta disponibilidade, que são suportados por componentes de fiabilidade Apache de código aberto. Estes componentes também estão presentes em clusters HDInsight:

- Nó de nome do Sistema de Ficheiros Hadoop (HDFS)
- Gestor de Recursos yARN
- Mestre HBase

As seguintes secções fornecerão mais detalhes sobre como estes serviços funcionam em conjunto.

## <a name="hdinsight-high-availability-services"></a>Serviços de alta disponibilidade HDInsight

A Microsoft fornece suporte para os quatro serviços Apache na tabela seguinte nos clusters HDInsight. Para distingui-los de serviços de alta disponibilidade suportados por componentes da Apache, são chamados *serviços HDInsight HA*.

| Serviço | Nós de cluster | Tipos de cluster | Objetivo |
|---|---|---|---|
| Servidor Apache Ambari| Cabeçada ativa | Todos | Monitoriza e gere o cluster.|
| Servidor de linha do tempo de aplicação para YARN Apache | Cabeçada ativa | Todos exceto Kafka. | Mantém informações de depuração sobre trabalhos de ARN em funcionamento no cluster.|
| Servidor de histórico de emprego para mapa de hadoopReduzir | Cabeçada ativa | Todos exceto Kafka. | Mantém dados de depuração para mapReduce jobs.|
| Apache Livy | Cabeçada ativa | Spark | Permite uma interação fácil com um cluster Spark sobre uma interface REST |

>[!Note]
> Os clusters hDInsight Enterprise Security Package (ESP) atualmente apenas fornecem ao servidor Ambari alta disponibilidade.

### <a name="architecture"></a>Arquitetura

Cada cluster HDInsight tem dois headnodes em modos ativos e de espera, respectivamente. Os serviços HDInsight HA funcionam apenas com nódoos. Estes serviços devem estar sempre a funcionar no nódoado ativo e parados e colocados em modo de manutenção no nó de cabeça.

Para manter os estados corretos dos serviços ha e fornecer uma falha rápida, o HDInsight utiliza o Apache ZooKeeper, que é um serviço de coordenação para aplicações distribuídas, para realizar uma eleição de cabeçada ativa. O HDInsight também prevê alguns processos java de fundo, que coordenam o procedimento de failover para os serviços HDInsight HA. Estes serviços são os seguintes: o controlador mestre failover, o controlador de falhas de escravos, o *master-ha-service*, e o *slave-ha-service*.

### <a name="apache-zookeeper"></a>Apache ZooKeeper

Apache ZooKeeper é um serviço de coordenação de alto desempenho para aplicações distribuídas. Na produção, o ZooKeeper geralmente funciona em modo replicado onde um grupo replicado de servidores ZooKeeper forma um quórum. Cada cluster HDInsight tem três nós zooKeeper que permitem que três servidores ZooKeeper formem um quórum. O HDInsight tem dois quórums ZooKeeper em paralelo entre si. Um quórum decide o headnode ativo num cluster em que os serviços HDInsight HA devem ser executados. Outro quórum é usado para coordenar os serviços ha fornecidos pela Apache, conforme detalhado em secções posteriores.

### <a name="slave-failover-controller"></a>Controlador de falha de escravo

O controlador de failover de escravo corre em cada nó de um aglomerado HDInsight. Este controlador é responsável por iniciar o agente Ambari e *o serviço slave-ha-ha* em cada nó. Questiona periodicamente o primeiro quórum zookeeper sobre o nódoo ativo. Quando os cabeçanos ativos e de espera mudam, o controlador de failover de escravo executa o seguinte:

1. Atualiza o ficheiro de configuração do anfitrião.
1. Reinicia o agente Ambari.

O *serviço slave-ha é* responsável por parar os serviços HDInsight HA (exceto o servidor Ambari) no nó de cabeça.

### <a name="master-failover-controller"></a>Controlador mestre failover

Um controlador mestre falha em ambos os nódosos. Ambos os controladores de failover do mestre comunicam com o primeiro quórum ZooKeeper para nomear o nódeo que eles estão correndo como o nó ativo.

Por exemplo, se o controlador de falhas principal no headnode 0 ganhar a eleição, as seguintes alterações ocorrem:

1. Headnode 0 torna-se ativo.
1. O controlador de failover master inicia o servidor Ambari e o *master-ha-service* no headnode 0.
1. O outro controlador de failover principal para o servidor Ambari e o *master-ha-service* no headnode 1.

O master-ha-service funciona apenas no nódeo ativo, para os serviços HDInsight HA (exceto o servidor Ambari) no headnode de espera e inicia-os no nódeactivo ativo.

### <a name="the-failover-process"></a>O processo de failover

![processo failover](./media/hdinsight-high-availability-components/failover-steps.png)

Um monitor de saúde funciona em cada nódoo, juntamente com o controlador de falhas mestre para enviar notificações de hearbeat para o quórum zookeeper. O cabeçada é considerado um serviço ha neste cenário. O monitor de saúde verifica se cada serviço de alta disponibilidade é saudável e se está pronto para participar na eleição para a liderança. Se sim, este cabeçada vai competir na eleição. Se não, deixará a eleição até que fique pronta novamente.

Se o headnode de espera alguma vez atingir a liderança e se tornar ativo (como no caso de uma falha com o nó ativo anterior), o seu controlador de failover principal iniciará todos os serviços HDInsight HA nele. O controlador de failover mestre também irá parar estes serviços no outro nó de cabeça.

Para falhas de serviço HDInsight HA, tais como um serviço em baixo ou pouco saudável, o controlador de failover principal deve reiniciar ou parar automaticamente os serviços de acordo com o estado do cabeçano. Os utilizadores não devem iniciar manualmente os serviços HDInsight HA em ambos os nós da cabeça. Em vez disso, permita a falha automática ou manual para ajudar o serviço a recuperar.

### <a name="inadvertent-manual-intervention"></a>Intervenção manual inadvertida

Os serviços HDInsight HA só devem funcionar no nódoado ativo e serão automaticamente reiniciados quando necessário. Como os serviços de HA individuais não têm o seu próprio monitor de saúde, a falha não pode ser desencadeada ao nível do serviço individual. A failover é assegurada ao nível do nó e não ao nível do serviço.

### <a name="some-known-issues"></a>Algumas questões conhecidas

- Ao iniciar manualmente um serviço HA no nó de espera, não vai parar até que a próxima falha aconteça. Quando os serviços de HA estão em execução em ambos os headnodes, alguns problemas potenciais incluem: Ambari UI é inacessível, Ambari lança erros, yARN, Spark e oozie empregos podem ficar presos.

- Quando um serviço HA no nódoano ativo para, não recomeçará até que aconteça a próxima falha ou se o controlador/master-ha-service reiniciar. Quando um ou mais serviços ha param no nódeo ativo, especialmente quando o servidor Ambari para, a Ambari UI está inacessível, outros problemas potenciais incluem falhas de emprego de YARN, Spark e Oozie.

## <a name="apache-high-availability-services"></a>Serviços de alta disponibilidade Apache

A Apache fornece uma elevada disponibilidade para HDFS NameNode, YARN ResourceManager e HBase Master, que também estão disponíveis em clusters HDInsight. Ao contrário dos serviços HDInsight HA, são suportados em clusters ESP. Os serviços Apache HA comunicam com o segundo quórum ZooKeeper (descrito na secção acima) para eleger estados ativos/de espera e realizar falhas automáticas. As seguintes secções detalham como estes serviços funcionam.

### <a name="hadoop-distributed-file-system-hdfs-namenode"></a>Hadoop Sistema de Ficheiros Distribuídos (HDFS) NameNode

Os clusters HDInsight baseados em Apache Hadoop 2.0 ou superior proporcionam ao NameNode alta disponibilidade. Existem dois NameNodes em execução nos nódosos, que são configurados para falha automática. Os NameNodes usam o *ZKFailoverController* para comunicar com o Zookeeper para eleger para o estado ativo/standby. O *ZKFailoverController* funciona em ambos os cabeçanos e funciona da mesma forma que o controlador de failover principal acima.

O segundo quórum zookeeper é independente do primeiro quórum, por isso o NameNode ativo pode não funcionar no nódeo ativo. Quando o NameNode ativo está morto ou insalubre, o nome de espera Node ganha a eleição e torna-se ativo.

### <a name="yarn-resourcemanager"></a>Gestor de Recursos yARN

Os clusters HDInsight baseados em Apache Hadoop 2.4 ou superior, suportam a alta disponibilidade do YARN ResourceManager. Existem dois ResourceManagers, rm1 e rm2, correndo no headnode 0 e no headnode 1, respectivamente. Tal como nameNode, o YARN ResourceManager também está configurado para falha automática. Outro Gestor de Recursos é automaticamente eleito para estar ativo quando o atual Gestor de Recursos ativo descer ou não responder.

O YARN ResourceManager utiliza o seu *ActiveStandbyElector* incorporado como detetor de falhas e eleitor líder. Ao contrário do Nome HDFS Node, o YARN ResourceManager não precisa de um daemon ZKFC separado. O Gestor de Recursos ativo escreve os seus estados no Apache Zookeeper.

A elevada disponibilidade do YARN ResourceManager é independente do NameNode e de outros serviços HDInsight HA. O Gestor de Recursos ativo não pode funcionar no nódoado ativo ou no headnode onde o NameNode ativo está em funcionamento. Para obter mais informações sobre a alta disponibilidade do YARN ResourceManager, consulte a [Alta Disponibilidade do Gestor de Recursos](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/ResourceManagerHA.html).

### <a name="hbase-master"></a>Mestre HBase

Os clusters HDInsight HBase suportam a alta disponibilidade do HBase Master. Ao contrário de outros serviços de HA, que funcionam em nódosos, o HBase Masters corre nos três nós do Zookeeper, onde um deles é o mestre ativo e os outros dois estão de prontidão. Tal como nameNode, o HBase Master coordena com o Apache Zookeeper para a eleição do líder e faz falhas automáticas quando o atual mestre ativo tem problemas. Há apenas um HBase Master ativo a qualquer momento.

## <a name="next-steps"></a>Passos seguintes

- [Disponibilidade e fiabilidade dos clusters Apache Hadoop no HDInsight](hdinsight-high-availability-linux.md)
- [Arquitetura de rede virtual Azure HDInsight](hdinsight-virtual-network-architecture.md)
