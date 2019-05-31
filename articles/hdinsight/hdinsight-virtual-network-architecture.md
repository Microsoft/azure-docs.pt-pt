---
title: Arquitetura de rede virtual do Azure HDInsight
description: Saiba os recursos disponíveis quando criar um cluster do HDInsight numa rede Virtual do Azure.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: hrasheed
ms.openlocfilehash: 41420497bffd0abdc598e4c86b2dbda1466b2ce1
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/28/2019
ms.locfileid: "66252856"
---
# <a name="azure-hdinsight-virtual-network-architecture"></a>Arquitetura de rede virtual do Azure HDInsight

Este artigo explica os recursos que estão presentes quando implementar um cluster do HDInsight numa rede Virtual do Azure personalizadas. Estas informações irão ajudá-lo a ligar a recursos no local seu cluster do HDInsight no Azure. Para obter mais informações sobre redes virtuais do Azure, consulte [o que é a rede Virtual do Azure?](../virtual-network/virtual-networks-overview.md).

## <a name="resource-types-in-azure-hdinsight-clusters"></a>Tipos de recursos em clusters do HDInsight do Azure

Os clusters de HDInsight do Azure têm diferentes tipos de máquinas virtuais ou nós. Cada tipo de nó desempenha um papel na operação do sistema. A tabela a seguir resume esses tipos de nó e as respetivas funções em cluster.

| Type | Descrição |
| --- | --- |
| Nó principal |  Para todos os tipos de cluster, exceto o Apache Storm, os nós principais alojam os processos de gerenciamento da execução da aplicação distribuída. O nó principal também é o nó que pode SSH no e executar aplicativos que são coordenados e, em seguida, executar entre os recursos do cluster. O número de nós principais é fixa em dois para que todos os tipos de cluster. |
| Nó do zooKeeper | Zookeeper coordena tarefas entre os nós que estão a fazer o processamento de dados. Também não eleição de coordenador do nó principal e mantém um registro de qual nó principal está a executar um serviço principal específico. O número de nós do ZooKeeper é fixa em três. |
| Nó de trabalho | Representa os nós que suportam a funcionalidade de processamento de dados. Nós de trabalho podem ser adicionadas ou removidas do cluster para dimensionar a capacidade de computação e gerir os custos. |
| Nó de extremidade do R Server | O nó de extremidade do R Server representa o nó pode encaminhar o SSH para e executar aplicativos que são coordenados e, em seguida, executar entre os recursos do cluster. Um nó de extremidade não participa na análise de dados dentro do cluster. Este nó também aloja R Studio Server, permitindo-lhe executar a aplicação de R a utilizar um browser. |
| Nó de região | Para o tipo de cluster do HBase, o nó de região (também referido como um nó de dados) é executado o servidor de região. Servidores de região servirem e gerir uma parte dos dados geridos pelo HBase. Nós de região podem ser adicionadas ou removidas do cluster para dimensionar a capacidade de computação e gerir os custos.|
| Nó nimbus | Para o tipo de cluster do Storm, o nó Nimbus fornece uma funcionalidade semelhante para o nó principal. O nó Nimbus atribui tarefas a outros nós no cluster através do Zookeeper, que coordena a execução de topologias do Storm. |
| Nó de supervisor | Para o tipo de cluster do Storm, o nó de supervisor executa as instruções fornecidas pelo nó Nimbus para executar o processamento pretendido. |

## <a name="basic-virtual-network-resources"></a>Recursos de rede virtual básico

O diagrama seguinte mostra a colocação de nós do HDInsight e os recursos de rede no Azure.

![Diagrama de entidades de HDInsight criados num VNET personalizado do Azure](./media/hdinsight-virtual-network-architecture/vnet-diagram.png)

Os recursos predefinidos presentes quando o HDInsight é implementado numa rede Virtual do Azure incluem os tipos de nós de cluster mencionados na tabela anterior, bem como dispositivos de rede que suportam a comunicação entre a rede virtual e fora de redes.

A tabela seguinte resume os nós de cluster de nove que são criados quando o HDInsight é implementado numa rede Virtual do Azure personalizadas.

| Tipo de recurso | Número presente | Detalhes |
| --- | --- | --- |
|Nó principal | dois |    |
|Nó do zookeeper | três | |
|Nó de trabalho | dois | Este número pode variar com base na configuração de cluster e dimensionamento. Um mínimo de três nós de trabalho é necessária para o Apache Kafka.  |
|Nó de gateway | dois | Os nós de gateway são máquinas virtuais do Azure, que são criadas no Azure, mas não são visíveis na sua subscrição. Contacte o suporte se precisar de reiniciar estes nós. |

Os seguintes recursos de rede presentes são criados automaticamente dentro da rede virtual utilizada com o HDInsight:

| Recursos de rede | Número presente | Detalhes |
| --- | --- | --- |
|Load balancer | três | |
|Interfaces de Rede | nove | Este valor se baseia num cluster normal, em que cada nó tem sua própria interface de rede. As interfaces de nove são para os dois nós principais, três nós zookeeper, dois nós de trabalho e dois nós de gateway mencionados na tabela anterior. |
|Endereços IP Públicos | dois |    |

## <a name="endpoints-for-connecting-to-hdinsight"></a>Pontos de extremidade para ligar ao HDInsight

Pode acessar o seu cluster do HDInsight de três formas:

- Um ponto final HTTPS fora da rede virtual em `CLUSTERNAME.azurehdinsight.net`.
- Um ponto de final SSH para ligar diretamente ao nó principal em `CLUSTERNAME-ssh.azurehdinsight.net`.
- Um ponto final HTTPS dentro da rede virtual `CLUSTERNAME-int.azurehdinsight.net`. Observe que o "-int" neste URL. Este ponto final será resolvido para um IP privado nessa rede virtual e não está acessível a partir da internet pública.

Cada um desses três pontos de extremidade são atribuídos um balanceador de carga.

Endereços IP públicos também são fornecidos para os dois pontos de extremidade que permitem a ligação a partir de fora da rede virtual.

1. Um IP público é atribuído ao balanceador de carga para o nome de domínio completamente qualificado (FQDN) para utilizar quando ligar ao cluster a partir da internet `CLUSTERNAME.azurehdinsight.net`.
1. O segundo endereço IP público é utilizado para o nome de domínio só de SSH `CLUSTERNAME-ssh.azurehdinsight.net`.

## <a name="next-steps"></a>Passos Seguintes

* [Proteger o tráfego de entrada para clusters do HDInsight numa rede virtual com o ponto final privado](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)
