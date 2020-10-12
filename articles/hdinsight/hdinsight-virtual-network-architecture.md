---
title: Arquitetura de rede virtual Azure HDInsight
description: Aprenda os recursos disponíveis quando criar um cluster HDInsight numa Rede Virtual Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: ad0e0250b32f2bdef4944e6e148be3215f3822f7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "81390213"
---
# <a name="azure-hdinsight-virtual-network-architecture"></a>Arquitetura de rede virtual Azure HDInsight

Este artigo explica os recursos que estão presentes quando coloca um cluster HDInsight numa Rede Virtual Azure personalizada. Estas informações irão ajudá-lo a conectar recursos no local ao seu cluster HDInsight em Azure. Para obter mais informações sobre redes virtuais Azure, veja [o que é a Rede Virtual Azure?](../virtual-network/virtual-networks-overview.md)

## <a name="resource-types-in-azure-hdinsight-clusters"></a>Tipos de recursos em clusters Azure HDInsight

Os clusters Azure HDInsight têm diferentes tipos de máquinas virtuais, ou nós. Cada tipo de nó desempenha um papel no funcionamento do sistema. A tabela seguinte resume estes tipos de nó e as suas funções no cluster.

| Tipo | Descrição |
| --- | --- |
| Nó principal |  Para todos os tipos de cluster, exceto Apache Storm, os nós de cabeça acolhem os processos que gerem a execução da aplicação distribuída. O nó da cabeça é também o nó em que se pode entrar e executar aplicações que são então coordenadas para correr através dos recursos do cluster. O número de nós de cabeça é fixado em dois para todos os tipos de cluster. |
| Nó do ZooKeeper | Zookeeper coordena tarefas entre os nós que estão fazendo o processamento de dados. Também faz a eleição do líder do nó de cabeça, e mantém o rasto de qual nó de cabeça está executando um serviço de mestre específico. O número de nós do ZooKeeper é fixado em três. |
| Nó do trabalhador | Representa os nós que suportam a funcionalidade de processamento de dados. Os nós dos trabalhadores podem ser adicionados ou removidos do cluster para escalar a capacidade de computação e gerir os custos. |
| Nó de borda do servidor R | O nó de borda do R Server representa o nó que pode SSH e executa aplicações que são então coordenadas para executar os recursos do cluster. Um nó de borda não participa na análise de dados dentro do cluster. Este nó também acolhe O Servidor R Studio, permitindo-lhe executar a aplicação R usando um browser. |
| Nó regional | Para o tipo de cluster HBase, o nó da região (também designado por nó de dados) executa o Servidor regional. Os Servidores regionais servem e gerem uma parte dos dados geridos pela HBase. Os nós da região podem ser adicionados ou removidos do cluster para dimensionar a capacidade de computação e gerir os custos.|
| Nó nimbus | Para o tipo de cluster Storm, o nó Nimbus fornece funcionalidade semelhante ao nó de cabeça. O nó Nimbus atribui tarefas a outros nós num aglomerado através do Zookeeper, que coordena o funcionamento das topologias da tempestade. |
| Nó de supervisor | Para o tipo de aglomerado de tempestade, o nó de supervisor executa as instruções fornecidas pelo nó Nimbus para fazer o processamento. |

## <a name="resource-naming-conventions"></a>Convenções de nomeação de recursos

Utilize nomes de domínio totalmente qualificados (FQDNs) ao endereçar nós no seu cluster. Você pode obter os FQDNs para vários tipos de nós no seu cluster usando a [AMBAri API](hdinsight-hadoop-manage-ambari-rest-api.md).

Estes FQDNs serão do `<node-type-prefix><instance-number>-<abbreviated-clustername>.<unique-identifier>.cx.internal.cloudapp.net` formulário.

O `<node-type-prefix>` será *hn* para headnodes, *wn* para nós operários e *zn* para os nós zookeeper.

Se precisar apenas do nome de anfitrião, use apenas a primeira parte do FQDN: `<node-type-prefix><instance-number>-<abbreviated-clustername>`

## <a name="basic-virtual-network-resources"></a>Recursos básicos da rede virtual

O diagrama seguinte mostra a colocação de nós HDInsight e recursos de rede em Azure.

![Diagrama de entidades HDInsight criadas em Azure custom VNET](./media/hdinsight-virtual-network-architecture/hdinsight-vnet-diagram.png)

Os recursos predefinidos numa Rede Virtual Azure incluem os tipos de nó de cluster mencionados na tabela anterior. E dispositivos de rede que suportam a comunicação entre a rede virtual e as redes externas.

A tabela seguinte resume os nove nós de cluster criados quando o HDInsight é implantado numa Rede Virtual Azure personalizada.

| Tipo de recurso | Número presente | Detalhes |
| --- | --- | --- |
|Nó principal | dois |    |
|Nó do zookeeper | três | |
|Nó do trabalhador | dois | Este número pode variar em função da configuração e escala do cluster. Um mínimo de três nós operários é necessário para Apache Kafka.  |
|Nó de gateway | dois | Os nós gateway são máquinas virtuais Azure que são criadas no Azure, mas não são visíveis na sua subscrição. Contacte o suporte se precisar de reiniciar estes nós. |

Os seguintes recursos de rede presentes são automaticamente criados dentro da rede virtual utilizada com HDInsight:

| Recurso de rede | Número presente | Detalhes |
| --- | --- | --- |
|Balanceador de carga | três | |
|Interfaces de Rede | Nove | Este valor baseia-se num cluster normal, onde cada nó tem a sua própria interface de rede. As nove interfaces são para: dois nós de cabeça, três nóns de zookeeper, dois nós operários, e dois nós de gateway mencionados na tabela anterior. |
|Endereços IP Públicos | dois |    |

## <a name="endpoints-for-connecting-to-hdinsight"></a>Pontos finais para ligação ao HDInsight

Pode aceder ao seu cluster HDInsight de três formas:

- Um ponto final HTTPS fora da rede virtual em `CLUSTERNAME.azurehdinsight.net` .
- Um ponto final SSH para ligar diretamente ao headnode em `CLUSTERNAME-ssh.azurehdinsight.net` .
- Um ponto final HTTPS dentro da rede `CLUSTERNAME-int.azurehdinsight.net` virtual. Note o " `-int` " neste URL. Este ponto final irá resolver-se para um IP privado naquela rede virtual e não é acessível a partir da internet pública.

Estes três pontos finais são atribuídos a cada um um equilibrador de carga.

Os endereços IP públicos também são fornecidos aos dois pontos finais que permitem a ligação a partir de fora da rede virtual.

1. Um IP público é atribuído ao equilibrador de carga para o nome de domínio totalmente qualificado (FQDN) para utilizar ao ligar ao cluster a partir da internet `CLUSTERNAME.azurehdinsight.net` .
1. O segundo endereço IP público é utilizado para o único nome de domínio SSH `CLUSTERNAME-ssh.azurehdinsight.net` .

## <a name="next-steps"></a>Passos seguintes

- [Tráfego de entrada segura para clusters HDInsight em uma rede virtual com ponto final privado](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)
