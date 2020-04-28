---
title: Criar clusters no Windows Server e Linux
description: Os clusters de tecido de serviço funcionam no Windows Server e linux, o que significa que poderá implementar e hospedar aplicações de Tecido de Serviço em qualquer lugar onde possa executar o Windows Server ou o Linux.
services: service-fabric
documentationcenter: .net
author: dkkapur
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: dekapur
ms.openlocfilehash: b6942c2a0647401df0d88b83e1b144ca3207a6db
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75614677"
---
# <a name="overview-of-service-fabric-clusters-on-azure"></a>Visão geral dos clusters de tecido de serviço em Azure
Um cluster de tecido de serviço é um conjunto de máquinas virtuais ou físicas ligadas à rede, nas quais os seus microserviços são implantados e geridos. Uma máquina ou VM que faz parte de um cluster é chamado de nó de cluster. Os aglomerados podem escalar para milhares de nós. Se adicionar novos nós ao cluster, o Service Fabric reequilibra as réplicas e instâncias de partição de serviços em todo o número aumentado de nós. O desempenho global da aplicação melhora e a contenção para o acesso à memória diminui. Se os nós do cluster não estiverem a ser utilizados de forma eficiente, pode diminuir o número de nós no cluster. O Service Fabric reequilibra novamente as réplicas e instâncias da partição através do número reduzido de nós para fazer melhor uso do hardware em cada nó.

Um tipo de nó define o tamanho, número e propriedades para um conjunto de nós (máquinas virtuais) no cluster. Cada tipo de nó pode então ser aumentado ou reduzido verticalmente de forma independente, pode ter conjuntos diferentes de portas abertas e ter métricas de capacidade diferente. Os tipos de nó são utilizados para definir funções para um conjunto de nós de cluster, como o "front-end" ou o "back-end". O cluster pode ter mais do que um tipo de nó, mas o tipo de nó primário tem de ter, pelo menos, cinco VMs para clusters de produção (ou, pelo menos, três VMs para clusters de teste). Os [serviços do sistema do Service Fabric](service-fabric-technical-overview.md#system-services) são colocados em nós do tipo de nó primário. 

## <a name="cluster-components-and-resources"></a>Componentes e recursos de cluster
Um cluster de tecido de serviço em Azure é um recurso Azure que utiliza e interage com outros recursos Azure:
* VMs e cartões de rede virtuais
* conjuntos de dimensionamento de máquinas virtuais
* redes virtuais
* Balanceadores de carga
* contas de armazenamento
* endereços IP públicos

![Cluster de tecido de serviço][Image]

### <a name="virtual-machine"></a>Máquina virtual
Uma [máquina virtual](/azure/virtual-machines/) que faz parte de um cluster é chamada de nó embora, tecnicamente, um nó de cluster é um processo de tempo de execução de Tecido de Serviço. É atribuído um nome de nó (uma cadeia) a cada nó. Os nós têm características, tais como propriedades de [colocação.](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints) Cada máquina ou VM tem um serviço de arranque automático, *FabricHost.exe,* que começa a funcionar na hora de arranque e depois inicia dois executáveis, *Fabric.exe* e *FabricGateway.exe,* que compõem o nó. Uma implantação de produção é um nó por máquina física ou virtual. Para cenários de teste, pode hospedar vários nós numa única máquina ou VM executando várias instâncias de *Fabric.exe* e *FabricGateway.exe*.

Cada VM está associado a um cartão de interface de rede virtual (NIC) e cada NIC é atribuído um endereço IP privado.  Um VM é atribuído a uma rede virtual e equilibrista local através do NIC.

Todos os VMs num cluster são colocados numa rede virtual.  Todos os nós do mesmo conjunto de nó/escala são colocados na mesma sub-rede na rede virtual.  Estes nós têm apenas endereços IP privados e não são diretamente endereçados fora da rede virtual.  Os clientes podem aceder a serviços nos nódosos através do equilibrador de carga Azure.

### <a name="scale-setnode-type"></a>Tipo de conjunto/nó de escala
Quando se cria um cluster, define-se um ou mais tipos de nó.  Os nós, ou VMs, num tipo de nó têm o mesmo tamanho e características tais como número de CPUs, memória, número de discos e Disco I/O.  Por exemplo, um tipo de nó pode ser para pequenos VMs frontais com portas abertas à internet, enquanto outro tipo de nó pode ser para grandes VMs de back-end que processam dados. Nos aglomerados Azure, cada tipo de nó é mapeado para um conjunto de escala de [máquina virtual](/azure/virtual-machine-scale-sets/).

Pode utilizar conjuntos de escala para implantar e gerir uma coleção de máquinas virtuais como conjunto. Cada tipo de nó que define num cluster Azure Service Fabric configura um conjunto de escala separado. O tempo de execução do Tecido de Serviço é as botas presas a cada máquina virtual no conjunto de escala utilizando extensões Azure VM. Pode escalar de forma independente cada nó para cima ou para baixo, alterar o OS SKU em cada nó de cluster, ter diferentes conjuntos de portas abertas e usar métricas de capacidade diferentes. Um conjunto de escala tem cinco [domínios](service-fabric-cluster-resource-manager-cluster-description.md#upgrade-domains) de upgrade e cinco [domínios](service-fabric-cluster-resource-manager-cluster-description.md#fault-domains) de falha e pode ter até 100 VMs.  Cria-se aglomerados de mais de 100 nós criando vários conjuntos/tipos de nó.

> [!IMPORTANT]
> Escolher o número de tipos de nós para o seu cluster e as propriedades de cada tipo de nó (tamanho, primário, virado para a internet, número de VMs, etc.) é uma tarefa importante.  Para mais informações, leia [as considerações de planeamento](service-fabric-cluster-capacity.md)da capacidade do cluster.

Para mais informações, leia os tipos de [nó do tecido de serviço e os conjuntos](service-fabric-cluster-nodetypes.md)de escala de máquina virtual .

### <a name="azure-load-balancer"></a>Azure Load Balancer
As instâncias vM juntam-se atrás de um equilibrador de [carga Azure,](/azure/load-balancer/load-balancer-overview)que está associado a um [endereço IP público](/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses) e etiqueta DNS.  Quando fornete * &lt;&gt;* um cluster com nome de cluster , o nome DNS, * &lt;o nome&gt;de cluster .&lt; localização&gt;.cloudapp.azure.com* é a etiqueta DNS associada ao equilibrista de carga em frente ao conjunto de escala.

Os VMs num cluster têm apenas [endereços IP privados](/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses).  O tráfego de gestão e o tráfego de serviços são encaminhados através do equilibrador de carga virado para o público.  O tráfego de rede é encaminhado para estas máquinas através de regras NAT (os clientes ligam-se a nós/instâncias específicos) ou regras de equilíbrio de carga (o tráfego vai para os VMs round robin).  Um equilibrador de carga tem um IP público associado com um nome DNS no formato: * &lt;clustername&gt;.&lt; localização&gt;.cloudapp.azure.com*.  Um IP público é outro recurso azure no grupo de recursos.  Se definir vários tipos de nó num cluster, é criado um equilibrista de carga para cada conjunto de tipo/escala do nó. Ou, pode configurar um único equilibrador de carga para vários tipos de nó.  O tipo principal do nó tem o * &lt;nome&gt;de cluster da etiqueta DNS .&lt; localização&gt;.cloudapp.azure.com,* outros tipos de nó têm o * &lt;&gt;-&lt;&gt;nódetipo de clustername de etiqueta DNS .&lt; localização&gt;.cloudapp.azure.com*.

### <a name="storage-accounts"></a>Contas de armazenamento
Cada tipo de nó de cluster é suportado por uma conta de [armazenamento Azure](/azure/storage/common/storage-introduction) e discos geridos.

## <a name="cluster-security"></a>Segurança do cluster
Um cluster de tecido de serviço é um recurso que possui.  É da sua responsabilidade proteger os seus clusters para ajudar a evitar que utilizadores não autorizados se conectem a eles. Um cluster seguro é especialmente importante quando se está a executar cargas de trabalho de produção no cluster. 

### <a name="node-to-node-security"></a>Segurança nó-ao-nó
A segurança nó-ao-nó assegura a comunicação entre os VMs ou os computadores num cluster. Este cenário de segurança garante que apenas os computadores autorizados a aderir ao cluster podem participar no alojamento de aplicações e serviços no cluster. Service Fabric utiliza certificados X.509 para garantir um cluster e fornecer funcionalidades de segurança da aplicação.  É necessário um certificado de cluster para proteger o tráfego do cluster e fornecer a autenticação do cluster e do servidor.  Os certificados auto-assinados podem ser utilizados para agrupamentos de ensaio, mas deve ser utilizado um certificado de uma autoridade de certificados fidedignos para assegurar agrupamentos de produção.

Para mais informações, leia [a segurança nó-ao-nó](service-fabric-cluster-security.md#node-to-node-security)

### <a name="client-to-node-security"></a>Segurança cliente-a-nó
A segurança cliente-a-nó autentica clientes e ajuda a garantir a comunicação entre um cliente e nós individuais no cluster. Este tipo de segurança ajuda a garantir que apenas os utilizadores autorizados podem aceder ao cluster e às aplicações que são implementadas no cluster. Os clientes são identificados de forma única através das suas credenciais de segurança de certificadox.509. Qualquer número de certificados de cliente opcionais pode ser usado para autenticar clientes administrativos ou utilizadores com o cluster.

Além dos certificados de cliente, o Azure Ative Directory também pode ser configurado para autenticar clientes com o cluster.

Para mais informações, leia [a segurança cliente-a-nó](service-fabric-cluster-security.md#client-to-node-security)

### <a name="role-based-access-control"></a>Controlo de Acesso Baseado em Funções
O Controlo de Acesso baseado em funções (RBAC) permite-lhe atribuir controlos de acesso finos aos recursos do Azure.  Pode atribuir diferentes regras de acesso a subscrições, grupos de recursos e recursos.  As regras rBAC são herdadas ao longo da hierarquia de recursos, a menos que sejam ultrapassadas a um nível mais baixo.  Pode atribuir qualquer utilizador ou grupo de utilizadores no seu AAD com regras RBAC para que utilizadores e grupos designados possam modificar o seu cluster.  Para mais informações, leia a visão geral do [Azure RBAC.](/azure/role-based-access-control/overview)

O Service Fabric também suporta o controlo de acesso para limitar o acesso a determinadas operações de cluster para diferentes grupos de utilizadores. Isto ajuda a tornar o cluster mais seguro. São suportados dois tipos de controlo de acesso para clientes que se ligam a um cluster: função de administrador e função de utilizador.  

Para mais informações, leia o Controlo de Acesso baseado em Funções de Tecido de [Serviço (RBAC)](service-fabric-cluster-security.md#role-based-access-control-rbac).

### <a name="network-security-groups"></a>Grupos de segurança de rede 
Os grupos de segurança da rede (NSGs) controlam o tráfego de entrada e saída de uma sub-rede, VM ou NIC específico.  Por padrão, quando vários VMs são colocados na mesma rede virtual podem comunicar uns com os outros através de qualquer porta.  Se pretender restringir as comunicações entre as máquinas, pode definir NSGs para segmentar a rede ou isolar VMs uns dos outros.  Se tiver vários tipos de nó num cluster, pode aplicar NSGs em subredes para evitar que máquinas pertencentes a diferentes tipos de nós se comuniquem entre si.  

Para mais informações, leia sobre [grupos de segurança](/azure/virtual-network/security-overview)

## <a name="scaling"></a>Dimensionamento

A aplicação exige mudanças ao longo do tempo. Pode ser necessário aumentar os recursos de cluster para satisfazer o aumento da carga de trabalho das aplicações ou o tráfego de rede ou diminuir os recursos de cluster quando a procura diminui. Depois de criar um cluster de Tecido de Serviço, pode escalar o cluster horizontalmente (alterar o número de nós) ou verticalmente (alterar os recursos dos nós). Pode escalar o cluster a qualquer momento, mesmo quando as cargas de trabalho estão a decorrer no cluster. À medida que o cluster escala, as suas aplicações também escalam automaticamente.

Para mais informações, leia os [clusters Scaling Azure.](service-fabric-cluster-scaling.md)

## <a name="upgrading"></a>Upgrade
Um cluster Azure Service Fabric é um recurso que possui, mas é parcialmente gerido pela Microsoft. A Microsoft é responsável por corrigir o SISTEMA subjacente e realizar atualizações de tempo de execução do Service Fabric no seu cluster. Pode configurar o seu cluster para receber atualizações automáticas de tempo de execução, quando a Microsoft lançar uma nova versão, ou optar por selecionar uma versão de tempo de execução suportada que deseja. Além de atualizações de tempo de execução, também pode atualizar a configuração do cluster, como certificados ou portas de aplicação.

Para mais informações, leia [a atualização](service-fabric-cluster-upgrade.md)dos clusters .

## <a name="supported-operating-systems"></a>Sistemas operativos suportados
É capaz de criar clusters em máquinas virtuais que executam estes sistemas operativos:

| Sistema operativo | Versão de Tecido de Serviço Suportado mais antiga |
| --- | --- |
| Windows Server 2012 R2 | Todas as versões |
| Windows Server 2016 | Todas as versões |
| Windows Server 1709 | 6.0 |
| Windows Server 1803 | 6.4 |
| Windows Server 1809 | 6.4.654.9590 |
| Windows Server 2019 | 6.4.654.9590 |
| Linux Ubuntu 16.04 | 6.0 |

Para mais informações consulte [Versões de Cluster Suportadas em Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-versions#supported-operating-systems)

> [!NOTE]
> Se decidir implementar o Tecido de Serviço no Windows Server 1709, por favor note que (1) não se trata de um ramo de manutenção a longo prazo, pelo que poderá ter de mover versões no futuro, e (2) se implementar contentores, os contentores construídos no Windows Server 2016 não funcionam no Windows Server 1709, e vice-versa (terá de reconstruí-los para os implementar).
>


## <a name="next-steps"></a>Passos seguintes
Leia mais sobre [a segurança,](service-fabric-cluster-security.md) [escalamento](service-fabric-cluster-scaling.md)e [modernização](service-fabric-cluster-upgrade.md) dos clusters Azure.

Saiba mais sobre as opções de suporte do [Tecido de Serviço.](service-fabric-support.md)

[Image]: media/service-fabric-azure-clusters-overview/Cluster.PNG
