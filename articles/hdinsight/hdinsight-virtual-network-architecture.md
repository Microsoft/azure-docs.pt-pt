---
title: Arquitetura de rede virtual Azure HDInsight
description: Aprenda os recursos disponíveis quando criar um cluster HDInsight numa Rede Virtual Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: b3f622b360f565ef5b16d5376cb1aa2498655017
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79272152"
---
# <a name="azure-hdinsight-virtual-network-architecture"></a>Arquitetura de rede virtual Azure HDInsight

Este artigo explica os recursos que estão presentes quando implementa um cluster HDInsight numa Rede Virtual Azure personalizada. Esta informação irá ajudá-lo a ligar recursos no local ao seu cluster HDInsight em Azure. Para obter mais informações sobre redes virtuais Azure, consulte o que é a [Rede Virtual Azure?](../virtual-network/virtual-networks-overview.md)

## <a name="resource-types-in-azure-hdinsight-clusters"></a>Tipos de recursos em clusters Azure HDInsight

Os clusters Azure HDInsight têm diferentes tipos de máquinas virtuais, ou nós. Cada tipo de nó desempenha um papel no funcionamento do sistema. A tabela que se segue resume estes tipos de nós e os seus papéis no cluster.

| Tipo | Descrição |
| --- | --- |
| Nó principal |  Para todos os tipos de cluster, exceto a Tempestade Apache, os nós da cabeça acolhem os processos que gerem a execução da aplicação distribuída. O nó de cabeça é também o nó em que você pode SSH e executar aplicações que são então coordenadas para correr através dos recursos do cluster. O número de nós de cabeça é fixado a dois para todos os tipos de cluster. |
| Nó ZooKeeper | Zookeeper coordena tarefas entre os nós que estão a fazer o processamento de dados. Também é líder da eleição do nó de cabeça, e mantém-se a ver com qual nó de cabeça está a executar um serviço principal específico. O número de nós do ZooKeeper é fixado às três. |
| Nó operário | Representa os nódosos que suportam a funcionalidade de processamento de dados. Os nós dos trabalhadores podem ser adicionados ou removidos do cluster para escalar a capacidade de computação e gerir os custos. |
| Nó de borda do servidor R | O nó de borda r Server representa o nó em que pode sSH e executa aplicações que são então coordenadas para correr através dos recursos do cluster. Um nó de borda não participa na análise de dados dentro do cluster. Este nó também acolhe r Studio Server, permitindo-lhe executar a aplicação R usando um browser. |
| Nó da região | Para o tipo de cluster HBase, o nó da região (também referido como nó de dados) executa o Servidor da Região. Os Servidores da Região servem e gerem uma parte dos dados geridos pela HBase. Os nós da região podem ser adicionados ou removidos do cluster para escalar a capacidade de computação e gerir os custos.|
| Nó nimbus | Para o tipo de aglomerado de tempestade, o nó Nimbus fornece uma funcionalidade semelhante ao nó de cabeça. O nó Nimbus atribui tarefas a outros nós num aglomerado através do Zookeeper, que coordena o funcionamento das topoologias da tempestade. |
| Nó de supervisor | Para o tipo de aglomerado de tempestade, o nó de supervisor executa as instruções fornecidas pelo nó Nimbus para realizar o processamento desejado. |

## <a name="resource-naming-conventions"></a>Convenções de nomeação de recursos

Utilize nomes de domínio totalmente qualificados (FQDNs) ao abordar os nós do seu cluster. Você pode obter as FQDNs para vários tipos de nó no seu cluster usando a [Ambari API](hdinsight-hadoop-manage-ambari-rest-api.md). 

Estas FQDNs serão do formulário `<node-type-prefix><instance-number>-<abbreviated-clustername>.<unique-identifier>.cx.internal.cloudapp.net`.

O `<node-type-prefix>` será *hn* para os nódosos, *wn* para nós operários e *zn* para nós de zookeeper.

Se precisar apenas do nome de anfitrião, utilize apenas a primeira parte do FQDN: `<node-type-prefix><instance-number>-<abbreviated-clustername>`

## <a name="basic-virtual-network-resources"></a>Recursos básicos da rede virtual

O diagrama seguinte mostra a colocação de nódoshos HDInsight e recursos de rede em Azure.

![Diagrama de entidades HDInsight criadas em VNET personalizado Azure](./media/hdinsight-virtual-network-architecture/hdinsight-vnet-diagram.png)

Os recursos padrão presentes quando o HDInsight é implantado numa Rede Virtual Azure incluem os tipos de nó de cluster mencionados na tabela anterior, bem como dispositivos de rede que suportam a comunicação entre a rede virtual e as redes externas.

A tabela seguinte resume os nove nós de cluster que são criados quando o HDInsight é implantado numa rede virtual Azure personalizada.

| Tipo de recurso | Número presente | Detalhes |
| --- | --- | --- |
|Nó principal | dois |    |
|Nó do zookeeper | três | |
|Nó operário | dois | Este número pode variar em função da configuração do cluster e da escala. Um mínimo de três nódosos operários é necessário para Apache Kafka.  |
|Nó de gateway | dois | Os nós gateway são máquinas virtuais Azure que são criadas no Azure, mas não são visíveis na sua subscrição. Suporte de contato se precisar reiniciar estes nódosos. |

Os seguintes recursos de rede presentes são automaticamente criados dentro da rede virtual utilizada com o HDInsight:

| Recurso de rede | Número presente | Detalhes |
| --- | --- | --- |
|Load balancer | três | |
|Interfaces de Rede | nove | Este valor baseia-se num cluster normal, onde cada nó tem a sua própria interface de rede. As nove interfaces são para os dois nós de cabeça, três nós de zookeeper, dois nós operários, e dois nós de gateway mencionados na tabela anterior. |
|Endereços IP Públicos | dois |    |

## <a name="endpoints-for-connecting-to-hdinsight"></a>Pontos finais para a ligação ao HDInsight

Pode aceder ao seu cluster HDInsight de três maneiras:

- Um ponto final HTTPS fora da rede virtual em `CLUSTERNAME.azurehdinsight.net`.
- Um ponto final SSH para ligar diretamente ao nódoo em `CLUSTERNAME-ssh.azurehdinsight.net`.
- Um ponto final HTTPS dentro da rede virtual `CLUSTERNAME-int.azurehdinsight.net`. Note o "int" neste URL. Este ponto final irá resolver para um IP privado nessa rede virtual e não é acessível a partir da internet pública.

Estes três pontos finais são atribuídos a cada um um equilibrador de carga.

Os endereços IP públicos também são fornecidos aos dois pontos finais que permitem a ligação de fora da rede virtual.

1. Um IP público é atribuído ao equilibrista de carga para o nome de domínio totalmente qualificado (FQDN) a utilizar quando se conecta ao cluster a partir da internet `CLUSTERNAME.azurehdinsight.net`.
1. O segundo endereço IP público é utilizado para o nome de domínio ssh apenas `CLUSTERNAME-ssh.azurehdinsight.net`.

## <a name="next-steps"></a>Passos Seguintes

- [Tráfego de entrada seguro para clusters HDInsight em uma rede virtual com ponto final privado](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)
