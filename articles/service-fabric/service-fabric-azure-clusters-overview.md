---
title: Criar clusters no Windows Server e Linux
description: Os clusters de tecido de serviço funcionam no Windows Server e linux. Pode implementar e hospedar aplicações de Tecido de Serviço em qualquer lugar que possa executar Windows Server ou Linux.
services: service-fabric
documentationcenter: .net
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 4aed4ab38db9f8d8b95647b6662245c93778afed
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520161"
---
# <a name="overview-of-service-fabric-clusters-on-azure"></a>Visão geral dos clusters de tecido de serviço em Azure
Um cluster de tecido de serviço é um conjunto de máquinas virtuais ou físicas ligadas à rede em que os seus microserviços são implantados e geridos. Uma máquina ou VM que faz parte de um cluster é chamada de nó de cluster. Os aglomerados podem escalar para milhares de nós. Se adicionar novos nós ao cluster, o Service Fabric reequilibra as réplicas de partição de serviço e as instâncias através do aumento do número de nós. O desempenho geral da aplicação melhora e a contenção para o acesso à memória diminui. Se os nós do cluster não estiverem a ser utilizados de forma eficiente, pode diminuir o número de nós no cluster. O Tecido de Serviço reequilibra novamente as réplicas de partição e instâncias através do número reduzido de nós para melhor utilizar o hardware em cada nó.

Um tipo de nó define o tamanho, número e propriedades para um conjunto de nós (máquinas virtuais) no cluster. Cada tipo de nó pode então ser aumentado ou reduzido verticalmente de forma independente, pode ter conjuntos diferentes de portas abertas e ter métricas de capacidade diferente. Os tipos de nó são utilizados para definir funções para um conjunto de nós de cluster, como o "front-end" ou o "back-end". O cluster pode ter mais do que um tipo de nó, mas o tipo de nó primário tem de ter, pelo menos, cinco VMs para clusters de produção (ou, pelo menos, três VMs para clusters de teste). Os [serviços do sistema do Service Fabric](service-fabric-technical-overview.md#system-services) são colocados em nós do tipo de nó primário. 

## <a name="cluster-components-and-resources"></a>Componentes e recursos do cluster
Um cluster de tecido de serviço em Azure é um recurso Azure que utiliza e interage com outros recursos Azure:
* VMs e cartões de rede virtuais
* conjuntos de dimensionamento de máquinas virtuais
* redes virtuais
* Balanceadores de carga
* contas de armazenamento
* endereços IP públicos

![Cluster de Tecido de Serviço][Image]

### <a name="virtual-machine"></a>Máquina virtual
Uma [máquina virtual](../virtual-machines/index.yml) que faz parte de um cluster é chamada de nó, no entanto, tecnicamente, um nó de cluster é um processo de tempo de execução do Tecido de Serviço. É atribuído um nome de nó (uma cadeia) a cada nó. Os nós têm características, tais como [propriedades de colocação.](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints) Cada máquina ou VM tem um serviço de arranque automático, *FabricHost.exe,* que começa a funcionar na hora do arranque e depois inicia dois executáveis, *Fabric.exe* e *FabricGateway.exe*, que compõem o nó. Uma implantação de produção é um nó por máquina física ou virtual. Para os cenários de teste, pode hospedar vários nós numa única máquina ou VM executando várias instâncias de *Fabric.exe* e *FabricGateway.exe*.

Cada VM está associado a um cartão de interface de rede virtual (NIC) e cada NIC é atribuído um endereço IP privado.  Um VM é atribuído a uma rede virtual e equilibrador local através do NIC.

Todos os VMs de um cluster são colocados numa rede virtual.  Todos os nós do mesmo tipo/escala do nó são colocados na mesma sub-rede na rede virtual.  Estes nós têm apenas endereços IP privados e não são diretamente endereçados fora da rede virtual.  Os clientes podem aceder aos serviços nos nós através do equilibrador de carga Azure.

### <a name="scale-setnode-type"></a>Conjunto de escala/tipo de nó
Quando se cria um cluster, define-se um ou mais tipos de nós.  Os nós, ou VMs, num tipo de nó têm o mesmo tamanho e características tais como o número de CPUs, memória, número de discos e E/O do disco.  Por exemplo, um tipo de nó pode ser para pequenos VMs frontais com portas abertas para a internet, enquanto outro tipo de nó pode ser para grandes VMs de back-end que processam dados. Nos agrupamentos Azure, cada tipo de nó é mapeado para um [conjunto de escala de máquina virtual](../virtual-machine-scale-sets/index.yml).

Pode utilizar conjuntos de escala para implantar e gerir uma coleção de máquinas virtuais como conjunto. Cada nó que define num cluster de tecido de serviço Azure configura um conjunto de escala separada. O tempo de funcionamento do Tecido de Serviço é colocado em cada máquina virtual no conjunto de escala utilizando extensões Azure VM. Pode escalar independentemente cada tipo de nó para cima ou para baixo, alterar o SISTEMA SKU funcionando em cada nó de cluster, ter diferentes conjuntos de portas abertas e usar diferentes métricas de capacidade. Um conjunto de escala tem cinco [domínios de atualização](service-fabric-cluster-resource-manager-cluster-description.md#upgrade-domains) e cinco [domínios de avaria](service-fabric-cluster-resource-manager-cluster-description.md#fault-domains) e pode ter até 100 VMs.  Cria clusters de mais de 100 nós criando conjuntos/tipos de nó de escala múltipla.

> [!IMPORTANT]
> Escolher o número de tipos de nós para o seu cluster e as propriedades de cada um dos tipos de nó (tamanho, primário, virado para a internet, número de VMs, etc.) é uma tarefa importante.  Para mais informações, leia [as considerações de planeamento da capacidade do cluster.](service-fabric-cluster-capacity.md)

Para obter mais informações, leia [os tipos de nó de nó de tecido de serviço e conjuntos de balança de máquinas virtuais](service-fabric-cluster-nodetypes.md).

### <a name="azure-load-balancer"></a>Balanceador de Carga do Azure
As instâncias VM são unidas por trás de um [equilibrador de carga Azure](../load-balancer/load-balancer-overview.md), que está associado a um [endereço IP público](../virtual-network/public-ip-addresses.md) e etiqueta DNS.  Quando fornece um cluster com *&lt; nome &gt; de cluster,* o nome DNS, *&lt; nome de &gt; cluster. &lt; localização &gt; .cloudapp.azure.com* é a etiqueta DNS associada ao equilibrador de carga em frente ao conjunto de escala.

Os VMs num cluster têm [apenas endereços IP privados](../virtual-network/private-ip-addresses.md).  O tráfego de tráfego de gestão e o tráfego de serviços são encaminhados através do público virado para o balancer de carga.  O tráfego de rede é encaminhado para estas máquinas através das regras NAT (os clientes ligam-se a nós/instâncias específicos) ou regras de equilíbrio de carga (o tráfego vai para o robin redondo dos VMs).  Um equilibrador de carga tem um IP público associado com um nome DNS no formato: *&lt; clustername &gt; . &lt; localização &gt; .cloudapp.azure.com*.  Um IP público é outro recurso Azure no grupo de recursos.  Se definir vários tipos de nós num cluster, é criado um equilibrador de carga para cada conjunto de tipo/escala de nó. Ou, pode configurar um único balançador de carga para vários tipos de nós.  O nó primário tem o nome de cluster de etiqueta DNS *&lt; &gt; . &lt; localização &gt; .cloudapp.azure.com*, outros tipos de nós têm o nó de conjunto de rótulo DNS *&lt; &gt; - &lt; &gt; . &lt; localização &gt; .cloudapp.azure.com*.

### <a name="storage-accounts"></a>Contas de armazenamento
Cada tipo de nó de cluster é suportado por uma [conta de armazenamento Azure](../storage/common/storage-introduction.md) e discos geridos.

## <a name="cluster-security"></a>Segurança do cluster
Um cluster de tecido de serviço é um recurso que possui.  É da sua responsabilidade proteger os seus clusters para ajudar a evitar que utilizadores não autorizados se conectem a eles. Um cluster seguro é especialmente importante quando se está a executar cargas de trabalho de produção no cluster. 

### <a name="node-to-node-security"></a>Segurança nó-a-nó
A segurança nó-a-nó assegura a comunicação entre os VMs ou os computadores num cluster. Este cenário de segurança garante que apenas os computadores autorizados a aderir ao cluster podem participar no alojamento de aplicações e serviços no cluster. O Service Fabric utiliza certificados X.509 para garantir um cluster e fornecer funcionalidades de segurança da aplicação.  É necessário um certificado de cluster para garantir o tráfego de clusters e fornecer a autenticação de cluster e servidor.  Os certificados auto-assinados podem ser utilizados para agrupamentos de ensaio, mas deve ser utilizado um certificado de uma autoridade de certificados fidedignos para assegurar aglomerados de produção.

Para mais informações, leia [a segurança nó-a-nó](service-fabric-cluster-security.md#node-to-node-security)

### <a name="client-to-node-security"></a>Segurança cliente-nó
A segurança entre clientes e nó autentica os clientes e ajuda a garantir a comunicação entre um cliente e os nós individuais no cluster. Este tipo de segurança ajuda a garantir que apenas os utilizadores autorizados possam aceder ao cluster e às aplicações que são implementadas no cluster. Os clientes são identificados exclusivamente através das suas credenciais de segurança de certificadoS X.509. Qualquer número de certificados de cliente opcionais pode ser usado para autenticar a administração ou clientes utilizadores com o cluster.

Além dos certificados de cliente, o Azure Ative Directory também pode ser configurado para autenticar clientes com o cluster.

Para mais informações, leia [a segurança do Cliente-a-nó](service-fabric-cluster-security.md#client-to-node-security)

### <a name="role-based-access-control"></a>Controlo de acesso baseado em funções
O controlo de acesso baseado em funções Azure (Azure RBAC) permite-lhe atribuir controlos de acesso finos nos recursos Azure.  Pode atribuir diferentes regras de acesso a subscrições, grupos de recursos e recursos.  As regras do RBAC são herdadas ao longo da hierarquia de recursos, a menos que sejam ultrapassadas a um nível mais baixo.  Pode atribuir qualquer utilizador ou grupo de utilizadores no seu AAD com as regras do Azure RBAC para que os utilizadores e grupos designados possam modificar o seu cluster.  Para mais informações, leia a visão geral do [Azure RBAC.](../role-based-access-control/overview.md)

O Service Fabric também suporta o controlo de acesso para limitar o acesso a certas operações de cluster para diferentes grupos de utilizadores. Isto ajuda a tornar o cluster mais seguro. Dois tipos de controlo de acesso são suportados para clientes que se conectam a um cluster: função de administrador e papel do Utilizador.  

Para obter mais informações, leia [o controlo de acesso baseado em funções do Service Fabric.](service-fabric-cluster-security.md#service-fabric-role-based-access-control)

### <a name="network-security-groups"></a>Grupos de segurança de rede 
Os grupos de segurança da rede (NSGs) controlam o tráfego de entrada e saída de uma sub-rede, VM ou NIC específico.  Por predefinição, quando vários VMs são colocados na mesma rede virtual, podem comunicar entre si através de qualquer porta.  Se quiser limitar as comunicações entre as máquinas, pode definir NSGs para segmentar a rede ou isolar VMs uns dos outros.  Se tiver vários tipos de nós num cluster, pode aplicar NSGs em sub-redes para evitar que máquinas pertencentes a diferentes tipos de nós se comuniquem entre si.  

Para mais informações, leia sobre [grupos de segurança](../virtual-network/network-security-groups-overview.md)

## <a name="scaling"></a>Dimensionamento

A aplicação exige mudanças ao longo do tempo. Poderá ser necessário aumentar os recursos de cluster para responder ao aumento da carga de trabalho de aplicação ou ao tráfego de redes ou diminuir os recursos de cluster quando a procura diminui. Depois de criar um cluster de Tecido de Serviço, pode escalar o cluster horizontalmente (alterar o número de nós) ou verticalmente (alterar os recursos dos nós). Pode escalar o cluster a qualquer momento, mesmo quando as cargas de trabalho estão a funcionar no cluster. À medida que o cluster escala, as suas aplicações também escalam automaticamente.

Para mais informações, leia [os clusters Scaling Azure.](service-fabric-cluster-scaling.md)

## <a name="upgrading"></a>Atualizar
Um cluster Azure Service Fabric é um recurso que possui, mas é parcialmente gerido pela Microsoft. A Microsoft é responsável por corrigir o sistema operativo subjacente e executar atualizações de tempo de execução do Tecido de Serviço no seu cluster. Pode configurar o seu cluster para receber atualizações automáticas de tempo de execução, quando a Microsoft lançar uma nova versão, ou optar por selecionar uma versão de tempo de execução suportada que pretenda. Além das atualizações de tempo de execução, também pode atualizar a configuração do cluster, como certificados ou portas de aplicação.

Para mais informações, leia [a atualização dos agrupamentos.](service-fabric-cluster-upgrade.md)

## <a name="supported-operating-systems"></a>Sistemas operativos suportados
Consulte [versões suportadas no Azure](./service-fabric-versions.md) para obter informações adicionais


## <a name="next-steps"></a>Passos seguintes
Leia mais sobre [a fixação,](service-fabric-cluster-security.md) [dimensionamento](service-fabric-cluster-scaling.md)e [modernização dos](service-fabric-cluster-upgrade.md) agrupamentos Azure.

Saiba mais sobre [as opções de suporte do Tecido de Serviço.](service-fabric-support.md)

[Image]: media/service-fabric-azure-clusters-overview/Cluster.PNG