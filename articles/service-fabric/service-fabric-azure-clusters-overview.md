---
title: Criar clusters de Service Fabric do Azure no Windows Server e no Linux | Microsoft Docs
description: Service Fabric clusters são executados no Windows Server e no Linux, o que significa que você poderá implantar e hospedar Service Fabric aplicativos em qualquer lugar em que possa executar o Windows Server ou o Linux.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/01/2019
ms.author: dekapur
ms.openlocfilehash: edb6a84762ce65e65ff33492f3a7bcebbce60777
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390384"
---
# <a name="overview-of-service-fabric-clusters-on-azure"></a>Visão geral de clusters de Service Fabric no Azure
Um Cluster Service Fabric é um conjunto de máquinas físicas ou virtuais conectadas à rede em que seus microserviços são implantados e gerenciados. Uma máquina ou VM que faz parte de um cluster é chamada de nó de cluster. Os clusters podem ser dimensionados para milhares de nós. Se você adicionar novos nós ao cluster, Service Fabric reequilibrará as réplicas e instâncias de partição de serviço em um número maior de nós. O desempenho geral do aplicativo melhora e a contenção de acesso à memória diminui. Se os nós no cluster não estiverem sendo usados com eficiência, você poderá diminuir o número de nós no cluster. Service Fabric novamente reequilibra as réplicas e instâncias de partição em todo o número de nós reduzido para fazer melhor uso do hardware em cada nó.

Um tipo de nó define o tamanho, o número e as propriedades de um conjunto de nós (máquinas virtuais) no cluster. Cada tipo de nó pode então ser aumentado ou reduzido verticalmente de forma independente, pode ter conjuntos diferentes de portas abertas e ter métricas de capacidade diferente. Os tipos de nó são utilizados para definir funções para um conjunto de nós de cluster, como o "front-end" ou o "back-end". O cluster pode ter mais do que um tipo de nó, mas o tipo de nó primário tem de ter, pelo menos, cinco VMs para clusters de produção (ou, pelo menos, três VMs para clusters de teste). Os [serviços do sistema do Service Fabric](service-fabric-technical-overview.md#system-services) são colocados em nós do tipo de nó primário. 

## <a name="cluster-components-and-resources"></a>Componentes e recursos do cluster
Um Cluster Service Fabric no Azure é um recurso do Azure que usa e interage com outros recursos do Azure:
* VMs e placas de rede virtual
* conjuntos de dimensionamento de máquinas virtuais
* redes virtuais
* balanceadores de carga
* contas de armazenamento
* endereços IP públicos

![Cluster do Service Fabric][Image]

### <a name="virtual-machine"></a>Máquina virtual
Uma [máquina virtual](/azure/virtual-machines/) que faz parte de um cluster é chamada de nó, tecnicamente, um nó de cluster é um processo de tempo de execução Service Fabric. É atribuído um nome de nó (uma cadeia) a cada nó. Os nós têm características, como [Propriedades de posicionamento](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints). Cada máquina ou VM tem um serviço de início automático, *FabricHost. exe*, que começa a ser executado no momento da inicialização e, em seguida, inicia dois executáveis, *Fabric. exe* e *FabricGateway. exe*, que compõem o nó. Uma implantação de produção é um nó por máquina virtual ou física. Para cenários de teste, você pode hospedar vários nós em um único computador ou VM executando várias instâncias de *Fabric. exe* e *FabricGateway. exe*.

Cada VM é associada a uma NIC (placa de interface de rede) virtual e cada NIC recebe um endereço IP privado.  Uma VM é atribuída a uma rede virtual e a um balanceador local por meio da NIC.

Todas as VMs em um cluster são colocadas em uma rede virtual.  Todos os nós no mesmo tipo de nó/conjunto de dimensionamento são colocados na mesma sub-rede na rede virtual.  Esses nós têm apenas endereços IP privados e não são diretamente endereçáveis fora da rede virtual.  Os clientes podem acessar os serviços nos nós por meio do Azure Load Balancer.

### <a name="scale-setnode-type"></a>Conjunto de dimensionamento/tipo de nó
Ao criar um cluster, você define um ou mais tipos de nó.  Os nós, ou VMs, em um tipo de nó têm o mesmo tamanho e características, como o número de CPUs, memória, número de discos e e/s de disco.  Por exemplo, um tipo de nó poderia ser para VMs pequenas e de front-end com portas abertas para a Internet, enquanto outro tipo de nó poderia ser para VMs grandes e de back-end que processam dados. Em clusters do Azure, cada tipo de nó é mapeado para um [conjunto de dimensionamento de máquinas virtuais](/azure/virtual-machine-scale-sets/).

Você pode usar conjuntos de dimensionamento para implantar e gerenciar uma coleção de máquinas virtuais como um conjunto. Cada tipo de nó que você define em um cluster do Azure Service Fabric define um conjunto de dimensionamento separado. O tempo de execução de Service Fabric é inicializado em cada máquina virtual no conjunto de dimensionamento usando extensões de VM do Azure. Você pode dimensionar de forma independente cada tipo de nó para cima ou para baixo, alterar a SKU do sistema operacional em execução em cada nó de cluster, ter diferentes conjuntos de portas abertas e usar métricas de capacidade diferentes. Um conjunto de dimensionamento tem cinco [domínios de atualização](service-fabric-cluster-resource-manager-cluster-description.md#upgrade-domains) e cinco [domínios de falha](service-fabric-cluster-resource-manager-cluster-description.md#fault-domains) e pode ter até 100 VMS.  Crie clusters de mais de 100 nós criando vários conjuntos de dimensionamento/tipos de nós.

> [!IMPORTANT]
> Escolher o número de tipos de nó para o cluster e as propriedades de cada tipo de nó (tamanho, primário, voltado para a Internet, número de VMs etc.) é uma tarefa importante.  Para obter mais informações, leia [Considerações sobre planejamento da capacidade do cluster](service-fabric-cluster-capacity.md).

Para obter mais informações, leia [Service Fabric tipos de nó e conjuntos de dimensionamento de máquinas virtuais](service-fabric-cluster-nodetypes.md).

### <a name="azure-load-balancer"></a>Balanceador de Carga do Azure
As instâncias de VM são unidas por trás de um [balanceador de carga do Azure](/azure/load-balancer/load-balancer-overview), que está associado a um [endereço IP público](/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses) e a um rótulo DNS.  Quando você provisiona um cluster com  *&lt;ClusterName&gt;*  *&lt;&lt; , o nome DNS, ClusterName&gt;. Location&gt;. cloudapp.Azure.com* é o rótulo DNS associado ao balanceador de carga na frente do conjunto de dimensionamento.

As VMs em um cluster têm apenas [endereços IP privados](/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses).  O tráfego de gerenciamento e o tráfego de serviço são roteados por meio do balanceador de carga voltado ao público.  O tráfego de rede é roteado para esses computadores por meio de regras NAT (clientes se conectam a nós/instâncias específicos) ou regras de balanceamento de carga (o tráfego vai para VMs round robin).  Um balanceador de carga tem um IP público associado com um nome DNS no formato:  *&lt;ClusterName&gt;.&lt; Location&gt;. cloudapp.Azure.com*.  Um IP público é outro recurso do Azure no grupo de recursos.  Se você definir vários tipos de nó em um cluster, um balanceador de carga será criado para cada tipo de nó/conjunto de dimensionamento. Ou então, você pode configurar um único balanceador de carga para vários tipos de nó.  O tipo de nó primário tem o rótulo  *&lt;DNS ClusterName.&lt; &gt; Location&gt;. cloudapp.Azure.com*, outros tipos de nó têm o  *&lt;&gt;-&gt;rótulo DNS ClusterName&lt;NodeType. &lt; Location&gt;. cloudapp.Azure.com*.

### <a name="storage-accounts"></a>Contas de armazenamento
Cada tipo de nó de cluster tem suporte de uma [conta de armazenamento do Azure](/azure/storage/common/storage-introduction) e de discos gerenciados.

## <a name="cluster-security"></a>Segurança do cluster
Um Cluster Service Fabric é um recurso que você possui.  É sua responsabilidade proteger seus clusters para ajudar a impedir que usuários não autorizados se conectem a eles. Um cluster seguro é especialmente importante quando você está executando cargas de trabalho de produção no cluster. 

### <a name="node-to-node-security"></a>Segurança de nó para nó
A segurança de nó para nó protege a comunicação entre as VMs ou os computadores em um cluster. Esse cenário de segurança garante que apenas os computadores autorizados a ingressar no cluster possam participar da Hospedagem de aplicativos e serviços no cluster. O Service Fabric usa certificados X. 509 para proteger um cluster e fornecer recursos de segurança de aplicativo.  Um certificado de cluster é necessário para proteger o tráfego de cluster e fornecer autenticação de cluster e de servidor.  Certificados autoassinados podem ser usados para clusters de teste, mas um certificado de uma autoridade de certificação confiável deve ser usado para proteger os clusters de produção.

Para obter mais informações, leia [segurança de nó para nó](service-fabric-cluster-security.md#node-to-node-security)

### <a name="client-to-node-security"></a>Segurança de cliente para nó
A segurança de cliente para nó autentica clientes e ajuda a proteger a comunicação entre um cliente e nós individuais no cluster. Esse tipo de segurança ajuda a garantir que somente usuários autorizados possam acessar o cluster e os aplicativos que são implantados no cluster. Os clientes são identificados exclusivamente por meio das credenciais de segurança do certificado X. 509. Qualquer número de certificados de cliente opcionais pode ser usado para autenticar clientes de administrador ou de usuário com o cluster.

Além dos certificados de cliente, os Azure Active Directory também podem ser configurados para autenticar clientes com o cluster.

Para obter mais informações, leia [segurança de cliente para nó](service-fabric-cluster-security.md#client-to-node-security)

### <a name="role-based-access-control"></a>Controlo de Acesso Baseado em Funções
O RBAC (controle de acesso baseado em função) permite que você atribua controles de acesso refinados nos recursos do Azure.  Você pode atribuir diferentes regras de acesso a assinaturas, grupos de recursos e recursos.  Regras de RBAC são herdadas ao longo da hierarquia de recursos, a menos que sejam substituídas em um nível inferior.  Você pode atribuir qualquer usuário ou grupo de usuários em seu AAD com regras de RBAC para que usuários e grupos designados possam modificar o cluster.  Para obter mais informações, leia a [visão geral do RBAC do Azure](/azure/role-based-access-control/overview).

O Service Fabric também dá suporte ao controle de acesso para limitar o acesso a determinadas operações de cluster para diferentes grupos de usuários. Isso ajuda a tornar o cluster mais seguro. Há suporte para dois tipos de controle de acesso para clientes que se conectam a um cluster: Função de administrador e função de usuário.  

Para obter mais informações, leia [Service Fabric controle de acesso baseado em função (RBAC)](service-fabric-cluster-security.md#role-based-access-control-rbac).

### <a name="network-security-groups"></a>Grupos de segurança de rede 
NSGs (grupos de segurança de rede) controlam o tráfego de entrada e de saída de uma sub-rede, VM ou NIC específica.  Por padrão, quando várias VMs são colocadas na mesma rede virtual, elas podem se comunicar entre si por qualquer porta.  Se você quiser restringir as comunicações entre as máquinas, poderá definir NSGs para segmentar a rede ou isolar as VMs umas das outras.  Se você tiver vários tipos de nó em um cluster, poderá aplicar NSGs a sub-redes para impedir que computadores pertencentes a diferentes tipos de nó se comuniquem entre si.  

Para obter mais informações, leia sobre [grupos de segurança](/azure/virtual-network/security-overview)

## <a name="scaling"></a>Dimensionamento

As demandas de aplicativo mudam ao longo do tempo. Talvez seja necessário aumentar os recursos de cluster para atender à carga de trabalho de aplicativo ou ao tráfego de rede aumentado ou diminuir os recursos de cluster quando a demanda cair. Depois de criar um Cluster Service Fabric, você pode dimensionar o cluster horizontalmente (alterar o número de nós) ou verticalmente (alterar os recursos dos nós). Você pode dimensionar o cluster a qualquer momento, mesmo quando as cargas de trabalho estiverem em execução no cluster. À medida que o cluster é dimensionado, os aplicativos também são dimensionados automaticamente.

Para obter mais informações, leia [dimensionando clusters do Azure](service-fabric-cluster-scaling.md).

## <a name="upgrading"></a>A atualizar
Um Cluster Service Fabric do Azure é um recurso que você possui, mas é parcialmente gerenciado pela Microsoft. A Microsoft é responsável por aplicar patches no sistema operacional subjacente e executar Service Fabric atualizações de tempo de execução em seu cluster. Você pode definir o cluster para receber atualizações de tempo de execução automáticas, quando a Microsoft lançar uma nova versão ou optar por selecionar uma versão de tempo de execução com suporte desejada. Além das atualizações de tempo de execução, você também pode atualizar a configuração de cluster, como certificados ou portas de aplicativo.

Para obter mais informações, leia [atualizando clusters](service-fabric-cluster-upgrade.md).

## <a name="supported-operating-systems"></a>Sistemas operativos suportados
Você pode criar clusters em máquinas virtuais que executam estes sistemas operacionais:

| Sistema operativo | Versão de Service Fabric mais antiga com suporte |
| --- | --- |
| Windows Server 2012 R2 | Todas as versões |
| Windows Server 2016 | Todas as versões |
| Windows Server 1709 | 6.0 |
| Windows Server versão 1803 | 6.4 |
| Windows Server 1809 | 6.4.654.9590 |
| Windows Server de 2019 | 6.4.654.9590 |
| Linux Ubuntu 16.04 | 6.0 |

Para obter informações adicionais, consulte [versões de cluster com suporte no Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-versions#supported-operating-systems)

> [!NOTE]
> Se você decidir implantar Service Fabric no Windows Server 1709, observe que (1) não é um Branch de manutenção em longo prazo, portanto, talvez seja necessário mover versões no futuro e (2) se você implantar contêineres, os contêineres criados no Windows Server 2016 não funcionarão no Windows Server  1709 e vice-versa (você precisará reconstruí-los para implantá-los).
>


## <a name="next-steps"></a>Passos Seguintes
Leia mais sobre como [proteger](service-fabric-cluster-security.md), [dimensionar](service-fabric-cluster-scaling.md)e [Atualizar](service-fabric-cluster-upgrade.md) clusters do Azure.

Saiba mais sobre [as opções de suporte do Service Fabric](service-fabric-support.md).

[Image]: media/service-fabric-azure-clusters-overview/Cluster.PNG
