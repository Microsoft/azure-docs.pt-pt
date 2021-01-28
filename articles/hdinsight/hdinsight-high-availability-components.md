---
title: Componentes de alta disponibilidade em Azure HDInsight
description: Visão geral dos vários componentes de alta disponibilidade utilizados pelos clusters HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: 336fe91174a8fc6d73d6e45c5fd1e2bf244eda52
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945310"
---
# <a name="high-availability-services-supported-by-azure-hdinsight"></a>Serviços de alta disponibilidade suportados pela Azure HDInsight

De forma a proporcionar-lhe os níveis ideais de disponibilidade para os seus componentes de análise, o HDInsight foi desenvolvido com uma arquitetura única para garantir alta disponibilidade (HA) de serviços críticos. Alguns componentes desta arquitetura foram desenvolvidos pela Microsoft para fornecer failover automático. Outros componentes são componentes Apache standard que são implantados para suportar serviços específicos. Este artigo explica a arquitetura do modelo de serviço HA em HDInsight, como a HDInsight suporta o failover para os serviços HA, e as melhores práticas para recuperar de outras interrupções de serviço.

> [!NOTE]
> Este artigo contém referências ao termo *escravo*, um termo que a Microsoft já não utiliza. Quando o termo for removido do software, vamos removê-lo deste artigo.

## <a name="high-availability-infrastructure"></a>Infraestrutura de alta disponibilidade

O HDInsight fornece infraestruturas personalizadas para garantir que quatro serviços primários são de alta disponibilidade com capacidades automáticas de failover:

- Servidor Apache Ambari
- Servidor de linha do tempo de aplicação para Apache YARN
- Servidor de Histórico de Trabalho para Hadoop MapReduce
- Apache Livy

Esta infraestrutura é constituída por uma série de serviços e componentes de software, alguns dos quais são projetados pela Microsoft. Os seguintes componentes são exclusivos da plataforma HDInsight:

- Controlador de falha de escravo
- Controlador de falha mestre
- Serviço de alta disponibilidade de escravos
- Serviço de alta disponibilidade master

![alta infraestrutura de disponibilidade](./media/hdinsight-high-availability-components/high-availability-architecture.png)

Existem também outros serviços de alta disponibilidade, que são suportados por componentes de fiabilidade Apache de código aberto. Estes componentes também estão presentes em clusters HDInsight:

- Nome do Sistema de Ficheiros Hadoop (HDFS)
- Fion ResourceManager
- HBase Master

As seguintes secções fornecerão mais detalhes sobre como estes serviços funcionam em conjunto.

## <a name="hdinsight-high-availability-services"></a>Serviços de alta disponibilidade HDInsight

A Microsoft fornece suporte para os quatro serviços Apache na tabela seguinte em clusters HDInsight. Para distingui-los dos serviços de alta disponibilidade suportados por componentes da Apache, são chamados *serviços HDInsight HA*.

| Serviço | Nós do cluster | Tipos de cluster | Objetivo |
|---|---|---|---|
| Servidor Apache Ambari| Cabeçano ativo | Todos | Monitoriza e gere o cluster.|
| Servidor de linha do tempo de aplicação para Apache YARN | Cabeçano ativo | Todos, exceto Kafka. | Mantém informações depurativas sobre os empregos de YARN a funcionar no cluster.|
| Servidor de Histórico de Trabalho para Hadoop MapReduce | Cabeçano ativo | Todos, exceto Kafka. | Mantém a depuragem de dados para trabalhos mapReduce.|
| Apache Livy | Cabeçano ativo | Spark | Permite uma interação fácil com um cluster Spark sobre uma interface REST |

>[!Note]
> Os clusters HDInsight Enterprise Security Package (ESP) atualmente apenas fornecem ao servidor Ambari alta disponibilidade. O Servidor de Linha do Tempo de Aplicação, o Job History Server e o Livy estão todos a funcionar apenas em headnode0 e não falham em fazer frente a frente quando o Ambari falha. A base de dados da linha de tempo da aplicação também está no headnode0 e não no servidor Ambari SQL.

### <a name="architecture"></a>Arquitetura

Cada cluster HDInsight tem dois headnodes em modos ativos e de espera, respectivamente. Os serviços HDInsight HA funcionam apenas com headnodes. Estes serviços devem estar sempre a funcionar no headnode ativo, e parados e colocados em modo de manutenção no headnode de espera.

Para manter os estados corretos dos serviços ha e fornecer uma rápida falha, o HDInsight utiliza o Apache ZooKeeper, que é um serviço de coordenação para aplicações distribuídas, para realizar uma eleição de headnode ativa. O HDInsight também prevê alguns processos java de fundo, que coordenam o procedimento de failover para os serviços HDInsight HA. Estes serviços são os seguintes: o controlador mestre de failover, o controlador de failover escravo, o *master-ha-service*, e o *slave-ha-service*.

### <a name="apache-zookeeper"></a>Apache ZooKeeper

Apache ZooKeeper é um serviço de coordenação de alto desempenho para aplicações distribuídas. Na produção, o ZooKeeper geralmente funciona em modo replicado onde um grupo replicado de servidores ZooKeeper formam um quórum. Cada cluster HDInsight tem três nós ZooKeeper que permitem que três servidores ZooKeeper formem um quórum. HDInsight tem dois quórums ZooKeeper em paralelo entre si. Um quórum decide o headnode ativo num cluster no qual os serviços HDInsight HA devem funcionar. Outro quórum é utilizado para coordenar os serviços ha fornecidos pela Apache, conforme detalhado em secções posteriores.

### <a name="slave-failover-controller"></a>Controlador de falha de escravo

O controlador de falha de escravos funciona em todos os nós de um aglomerado HDInsight. Este controlador é responsável por iniciar o agente Ambari e *o serviço de escravos* em cada nó. Consulta periodicamente o primeiro quórum do ZooKeeper sobre o headnode ativo. Quando os headnodes ativos e de espera mudam, o controlador de failover escravo executa o seguinte:

1. Atualiza o ficheiro de configuração do anfitrião.
1. Reinicia o agente Ambari.

O *serviço slave-ha* é responsável por parar os serviços HDInsight HA (exceto o servidor Ambari) no headnode de espera.

### <a name="master-failover-controller"></a>Controlador de falha mestre

Um controlador de falha mestre corre em ambos os cabeçanhos. Ambos os controladores mestres de failover comunicam com o primeiro quórum zookeeper para nomear o headnode em que estão a correr como o headnode ativo.

Por exemplo, se o controlador de failover principal no headnode 0 ganhar a eleição, as seguintes alterações ocorrem:

1. Headnode 0 torna-se ativo.
1. O controlador principal de failover inicia o servidor Ambari e o *master-ha-service* no headnode 0.
1. O outro controlador de falha mestre para o servidor Ambari e o *master-ha-service* no headnode 1.

O serviço master-ha funciona apenas no headnode ativo, para os serviços HDInsight HA (exceto o servidor Ambari) em headnode de espera e inicia-os em headnode ativo.

### <a name="the-failover-process"></a>O processo de failover

![processo de failover](./media/hdinsight-high-availability-components/failover-steps.png)

Um monitor de saúde corre em cada cabeçada juntamente com o controlador mestre de failover para enviar notificações de batimentocardíaco para o quórum zookeeper. O headnode é considerado um serviço de HA neste cenário. O monitor de saúde verifica se cada serviço de alta disponibilidade é saudável e se está pronto para participar na eleição para a liderança. Se sim, este cabeçanode vai competir na eleição. Se não, sairá da eleição até que se prepare novamente.

Se o headnode de standby alguma vez alcançar a liderança e se tornar ativo (como no caso de uma falha com o nó ativo anterior), o seu controlador principal de failover iniciará todos os serviços HDInsight HA nele. O controlador principal de failover também irá parar estes serviços no outro headnode.

Para falhas de serviço HDInsight HA, como um serviço que está em baixo ou não é saudável, o controlador principal de failover deve reiniciar ou parar automaticamente os serviços de acordo com o estado do headnode. Os utilizadores não devem iniciar manualmente os serviços HDInsight HA em ambos os nós da cabeça. Em vez disso, permita que o bloqueio automático ou manual ajude a recuperar o serviço.

### <a name="inadvertent-manual-intervention"></a>Intervenção manual inadvertida

Os serviços HDInsight HA só devem funcionar no headnode ativo e serão automaticamente reiniciados quando necessário. Uma vez que os serviços individuais de HA não têm o seu próprio monitor de saúde, o failover não pode ser desencadeado ao nível do serviço individual. A falha é assegurada ao nível do nó e não ao nível de serviço.

### <a name="some-known-issues"></a>Algumas questões conhecidas

- Quando iniciar manualmente um serviço de HA no headnode de espera, não para até que a próxima falha aconteça. Quando os serviços ha estão em funcionamento em ambos os headnodes, alguns problemas potenciais incluem: Ambari UI é inacessível, Ambari lança erros, YARN, Spark e Oozie empregos podem ficar presos.

- Quando um serviço HA no headnode ativo parar, não reiniciará até que a próxima falha aconteça ou o controlador de failover principal/master-ha-service reinicia. Quando um ou mais serviços ha param no headnode ativo, especialmente quando o servidor Ambari para, a UI Ambari está inacessível, outros problemas potenciais incluem falhas de empregos de YARN, Spark e Oozie.

## <a name="apache-high-availability-services"></a>Apache serviços de alta disponibilidade

O Apache fornece alta disponibilidade para HDFS NameNode, YARN ResourceManager e HBase Master, que também estão disponíveis em clusters HDInsight. Ao contrário dos serviços HDInsight HA, são suportados em clusters ESP. Os serviços Apache HA comunicam com o segundo quórum ZooKeeper (descrito na secção acima) para eleger estados ativos/de prontidão e realizar a falha automática. As seguintes secções detalham como estes serviços funcionam.

### <a name="hadoop-distributed-file-system-hdfs-namenode"></a>Sistema de ficheiros distribuídos hadoop (HDFS) NomeNode

Os clusters HDInsight baseados em Apache Hadoop 2.0 ou superior fornecem ao NameNode alta disponibilidade. Existem dois NomeNodes em execução nos headnodes, que são configurados para falha automática. Os NomesNodes usam o *Controlador ZKFailover* para comunicar com o Zookeeper para eleger para o estado ativo/de espera. O *ZKFailoverController* funciona em ambos os headnodes, e funciona da mesma forma que o controlador principal de failover acima.

O segundo quórum zookeeper é independente do primeiro quórum, por isso o Nome ativoNode pode não funcionar no headnode ativo. Quando o Nome ativoNode está morto ou insalubre, o nome de esperaNode ganha a eleição e torna-se ativo.

### <a name="yarn-resourcemanager"></a>Fion ResourceManager

Os clusters HDInsight baseados em Apache Hadoop 2.4 ou superior, suportam a alta disponibilidade do YARN ResourceManager. Existem dois ResourceManagers, rm1 e rm2, a correr em headnode 0 e headnode 1, respectivamente. Tal como o NameNode, o YARN ResourceManager também está configurado para falha automática. Outro ResourceManager é automaticamente eleito para estar ativo quando o atual Homem-Fonte de Recursos ativo cai ou não responde.

O YARN ResourceManager usa o seu *ActiveStandbyElector* incorporado como detetor de falhas e eleitor líder. Ao contrário do HDFS NameNode, o YARN ResourceManager não precisa de um daemon ZKFC separado. O Gerente de Recursos Ativos escreve os seus estados no Apache Zookeeper.

A alta disponibilidade do Yarn ResourceManager é independente do NameNode e de outros serviços HDInsight HA. O ResourceManager ativo não pode funcionar no headnode ativo ou no headnode onde o Nome Activonode está em execução. Para obter mais informações sobre a alta disponibilidade do YARN ResourceManager, consulte [ResourceManager High Availability](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/ResourceManagerHA.html).

### <a name="hbase-master"></a>HBase Master

Os clusters HDInsight HBase suportam a alta disponibilidade do HBase Master. Ao contrário de outros serviços ha, que funcionam em headnodes, o HBase Masters funciona nos três nós zookeeper, onde um deles é o mestre ativo e os outros dois estão em espera. Tal como o NameNode, o HBase Master coordena com o Apache Zookeeper para a eleição de líder e faz falhas automáticas quando o atual mestre ativo tem problemas. Há apenas um HBase Master ativo a qualquer momento.

## <a name="next-steps"></a>Próximos passos

- [Disponibilidade e fiabilidade dos clusters Apache Hadoop em HDInsight](./hdinsight-business-continuity.md)
- [Arquitetura de rede virtual Azure HDInsight](hdinsight-virtual-network-architecture.md)