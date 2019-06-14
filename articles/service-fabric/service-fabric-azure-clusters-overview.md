---
title: Criar clusters do Azure Service Fabric no Windows Server e Linux | Documentos da Microsoft
description: Clusters do Service Fabric executados no Windows Server e Linux, que significa que será capaz de implantar e alojar as aplicações do Service Fabric em qualquer lugar, pode executar Windows Server ou Linux.
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
ms.openlocfilehash: d1681aee9dc11f0dbd3133bced0b919a8c1623b8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60310935"
---
# <a name="overview-of-service-fabric-clusters-on-azure"></a>Descrição geral do Service Fabric clusters no Azure
Um cluster do Service Fabric é um conjunto ligado à rede de máquinas virtuais ou físicas, no qual os microsserviços são implementados e geridos. Uma máquina ou VM que faça parte de um cluster é chamado de um nó de cluster. Clusters podem ser dimensionados para milhares de nós. Se adicionar novos nós ao cluster, o Service Fabric rebalances as réplicas de partição de serviço e as instâncias em aumento do número de nós. Em geral melhora o desempenho do aplicativo e reduz a contenção de acesso à memória. Se os nós do cluster não estão a ser utilizados com eficiência, pode diminuir o número de nós do cluster. Service Fabric rebalances novamente as instâncias e réplicas de partição entre o menor número de nós para garantir uma melhor utilização do hardware em cada nó.

Um tipo de nó define o tamanho, número e propriedades de um conjunto de nós (máquinas virtuais) no cluster. Cada tipo de nó pode então ser aumentado ou reduzido verticalmente de forma independente, pode ter conjuntos diferentes de portas abertas e ter métricas de capacidade diferente. Os tipos de nó são utilizados para definir funções para um conjunto de nós de cluster, como o "front-end" ou o "back-end". O cluster pode ter mais do que um tipo de nó, mas o tipo de nó primário tem de ter, pelo menos, cinco VMs para clusters de produção (ou, pelo menos, três VMs para clusters de teste). Os [serviços do sistema do Service Fabric](service-fabric-technical-overview.md#system-services) são colocados em nós do tipo de nó primário. 

## <a name="cluster-components-and-resources"></a>Componentes de cluster e recursos
Um cluster do Service Fabric no Azure é um recurso do Azure que utiliza e interage com outros recursos do Azure:
* VMs e placas de rede virtual
* conjuntos de dimensionamento de máquinas virtuais
* redes virtuais
* Balanceadores de carga
* Contas de armazenamento
* Endereços IP públicos

![Cluster do Service Fabric][Image]

### <a name="virtual-machine"></a>Máquina virtual
R [máquina virtual](/azure/virtual-machines/) que parte de um cluster é chamado de nó, embora, tecnicamente, um nó de cluster é um processo de tempo de execução do Service Fabric. É atribuído um nome de nó (uma cadeia) a cada nó. Nós têm características, como [as propriedades de colocação](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints). Cada máquina ou a VM tem um serviço de início automático *FabricHost.exe*, que começa a ser executado no momento da inicialização e, em seguida, inicia dois executáveis, *Fabric.exe* e *FabricGateway.exe* , que compõem o nó. Uma implementação de produção é um nó por máquina física ou virtual. Para cenários de teste, pode alojar vários nós numa única máquina ou VM ao executar várias instâncias *Fabric.exe* e *FabricGateway.exe*.

Cada VM está associada uma placa de interface de rede virtual (NIC) e cada NIC está atribuído um endereço IP privado.  Uma VM é atribuída a uma rede virtual e o Balanceador de local por meio da NIC.

Todas as VMs num cluster são colocadas numa rede virtual.  Todos os nós no mesmo conjunto de dimensionamento/tipo de nó são colocados na mesma sub-rede na rede virtual.  Estes nós apenas tem endereços IP privados e não são diretamente endereçáveis fora da rede virtual.  Os clientes podem aceder a serviços em nós através do Balanceador de carga do Azure.

### <a name="scale-setnode-type"></a>Tipo de nó/conjunto de dimensionamento
Quando cria um cluster, define um ou mais tipos de nó.  Os nós ou VMs, num tipo de nó de ter o mesmo tamanho e características, como o número de CPUs, memória, número de discos e e/s de disco.  Por exemplo, um tipo de nó pode ser para VMs pequenas, front-end com portas abrem para a internet, enquanto outro tipo de nó pode ser para VMs de grandes e de back-end que processam dados. Em clusters do Azure, cada tipo de nó é mapeado para um [conjunto de dimensionamento de máquina virtual](/azure/virtual-machine-scale-sets/).

Pode utilizar os conjuntos de dimensionamento para implementar e gerir uma coleção de máquinas virtuais como um conjunto. Cada tipo de nó que definem num cluster do Azure Service Fabric define um conjunto de dimensionamento separado. O tempo de execução do Service Fabric é reiniciado em cada máquina virtual no conjunto de dimensionamento com extensões de VM do Azure. Forma independente pode aumentar cada tipo de nó ou reduzir verticalmente, alterar o SKU de SO em execução em cada nó de cluster, têm conjuntos diferentes de portas abertas e utilize métricas de capacidade diferente. Um conjunto de dimensionamento tem cinco [domínios de atualização](service-fabric-cluster-resource-manager-cluster-description.md#upgrade-domains) e cinco [domínios de falha](service-fabric-cluster-resource-manager-cluster-description.md#fault-domains) e pode ter até 100 VMs.  Pode criar clusters com mais de 100 nós ao criar vários tipos de nó/conjuntos de dimensionamento.

> [!IMPORTANT]
> Escolher o número de tipos de nó para o seu cluster e as propriedades de cada tipo de nó (tamanho, primária, com acesso à internet, o número de VMs, etc.) é uma tarefa importante.  Para obter mais informações, leia [considerações de planeamento de capacidade do cluster](service-fabric-cluster-capacity.md).

Para obter mais informações, leia [conjuntos de dimensionamento de máquina virtual e tipos de nó do Service Fabric](service-fabric-cluster-nodetypes.md).

### <a name="azure-load-balancer"></a>Azure Load Balancer
Instâncias de VM estão associadas por trás de um [Balanceador de carga do Azure](/azure/load-balancer/load-balancer-overview), que está associado um [endereço IP público](/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses) e etiqueta de DNS.  Se aprovisionar um cluster com  *&lt;clustername&gt;* , o nome DNS  *&lt;clustername&gt;.&lt; localização&gt;. cloudapp.azure.com* é a etiqueta DNS associada ao balanceador de carga à frente do conjunto de dimensionamento.

As VMs num cluster tem apenas [endereços IP privados](/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses).  Tráfego de gestão e o tráfego de serviço são encaminhados através do Balanceador de carga destinado ao público.  Tráfego de rede é encaminhado para estas máquinas por meio de regras NAT (os clientes ligam-se a nós/instâncias específicas) ou regras de balanceamento de carga (o tráfego segue para VMs round robin).  Um balanceador de carga tem um IP público associado com um nome DNS no formato:  *&lt;clustername&gt;.&lt; localização&gt;. cloudapp.azure.com*.  Um IP público é outro recurso do Azure no grupo de recursos.  Se definir vários tipos de nó num cluster, é criado um balanceador de carga para cada conjunto de dimensionamento/tipo de nó. Em alternativa, pode configurar um balanceador de carga individual para vários tipos de nó.  O tipo de nó principal tem a etiqueta DNS  *&lt;clustername&gt;.&lt; localização&gt;. cloudapp.azure.com*, outros tipos de nó tem a etiqueta DNS  *&lt;clustername&gt;-&lt;nodetype&gt;.&lt; localização&gt;. cloudapp.azure.com*.

### <a name="storage-accounts"></a>Contas de armazenamento
Cada tipo de nó de cluster é suportado por um [conta de armazenamento do Azure](/azure/storage/common/storage-introduction) e discos geridos.

## <a name="cluster-security"></a>Segurança do cluster
Um cluster do Service Fabric é um recurso que é proprietário.  É da responsabilidade do cliente para proteger os seus clusters para ajudar a impedir que os utilizadores não autorizados a ligar aos mesmos. Um cluster seguro é especialmente importante quando estiver a executar cargas de trabalho de produção no cluster. 

### <a name="node-to-node-security"></a>Segurança de nó para nó
Segurança de nó para nó protege a comunicação entre VMs ou computadores num cluster. Neste cenário de segurança garante que apenas os computadores que estejam autorizados a aderir o cluster podem participar de hospedar aplicativos e serviços no cluster. O Service Fabric utiliza certificados X.509 para proteger um cluster e fornecer recursos de segurança do aplicativo.  É necessário para proteger o tráfego de cluster e disponibilizar a autenticação do cluster e servidor de um certificado de cluster.  Gestão personalizados de certificados assinados podem ser utilizados para clusters de teste, mas um certificado de uma autoridade de certificado fidedigno deve ser utilizado para proteger clusters de produção.

Para obter mais informações, leia [segurança de nó para nó](service-fabric-cluster-security.md#node-to-node-security)

### <a name="client-to-node-security"></a>Segurança de cliente para nó
Segurança de cliente para nó autentica os clientes e ajuda a comunicação segura entre um cliente e nós individuais no cluster. Este tipo de segurança ajuda a garantir que apenas os utilizadores autorizados conseguem aceder ao cluster e as aplicações que são implementadas no cluster. Os clientes são exclusivamente identificados por meio de qualquer uma de suas credenciais de segurança do certificado X.509. Qualquer número de certificados de cliente opcional pode ser utilizado para autenticar clientes de administrador ou utilizador com o cluster.

Para além dos certificados de cliente, o Azure Active Directory também pode ser configurado para autenticar os clientes com o cluster.

Para obter mais informações, leia [segurança de cliente para nó](service-fabric-cluster-security.md#client-to-node-security)

### <a name="role-based-access-control"></a>Controlo de Acesso Baseado em Funções
Controlo de acesso baseado em funções (RBAC) permite-lhe atribuir controlos de acesso detalhadas em recursos do Azure.  Pode atribuir as regras de acesso diferentes subscrições, grupos de recursos e recursos.  Regras RBAC são herdadas ao longo da hierarquia de recursos, a menos que substituída num nível inferior.  Pode atribuir qualquer utilizador ou grupos de utilizadores no seu AAD com regras RBAC para que os utilizadores designados e grupos podem modificar o seu cluster.  Para obter mais informações, leia os [descrição geral de RBAC do Azure](/azure/role-based-access-control/overview).

O Service Fabric também suporta o controlo de acesso para limitar o acesso a determinadas operações de cluster para diferentes grupos de utilizadores. Isto ajuda a tornar o cluster mais segura. Dois tipos de controle de acesso são suportados para clientes que se ligam a um cluster: Função de administrador e a função de utilizador.  

Para obter mais informações, leia [controlo de acesso de Service Fabric Role-Based (RBAC)](service-fabric-cluster-security.md#role-based-access-control-rbac).

### <a name="network-security-groups"></a>Grupos de segurança de rede 
Grupos de segurança de rede (NSGs) controlam o tráfego de entrada e saído de uma sub-rede, VM ou NIC específicas.  Por predefinição, quando várias VMs são colocadas na mesma rede virtual que possam comunicar entre si através de qualquer porta.  Se quiser restringir comunicações entre as máquinas, pode definir os NSGs para segmentar a rede ou isolar as VMs entre si.  Se tiver vários tipos de nó num cluster, pode aplicar NSGs a sub-redes para impedir que as máquinas que pertencem ao tipos de nó diferente de se comunicar entre si.  

Para obter mais informações, leia sobre [grupos de segurança](/azure/virtual-network/security-overview)

## <a name="scaling"></a>Dimensionamento

Alteram pedidos de aplicações ao longo do tempo. Poderá ter de aumentar os recursos do cluster para corresponder ao tráfego de rede ou de carga de trabalho de aplicações mais ou diminuir recursos do cluster quando a procura cair. Depois de criar um cluster do Service Fabric, pode dimensionar o cluster horizontalmente (alterar o número de nós) ou na vertical (alterar os recursos de nós). Pode dimensionar o cluster em qualquer altura, mesmo quando as cargas de trabalho em execução no cluster. Como dimensiona o cluster, as aplicações são dimensionadas automaticamente também.

Para obter mais informações, leia [clusters do Azure de dimensionamento](service-fabric-cluster-scaling.md).

## <a name="upgrading"></a>A atualizar
Um cluster do Azure Service Fabric é um recurso que o proprietário, mas está parcialmente gerida pela Microsoft. A Microsoft é responsável pela aplicação de patches de sistema operacional subjacente e a execução de atualizações de tempo de execução do Service Fabric no seu cluster. Pode definir o seu cluster para receber atualizações de tempo de execução automática, quando a Microsoft lança uma nova versão, ou optar por selecionar uma versão de runtime suportada que pretende. Para além das atualizações de tempo de execução, também pode atualizar a configuração de cluster, tais como certificados ou as portas da aplicação.

Para obter mais informações, leia [atualizar clusters](service-fabric-cluster-upgrade.md).

## <a name="supported-operating-systems"></a>Sistemas operativos suportados
É possível criar clusters de máquinas virtuais que executam esses sistemas operacionais:

* Windows Server 2012 R2
* Windows Server 2016 
* Windows Server 1709
* Windows Server versão 1803
* Linux Ubuntu 16.04
* Red Hat Enterprise Linux 7.4 (suporte de pré-visualização)

> [!NOTE]
> Se optar por implementar o Service Fabric no Windows Server 1709, tenha em atenção que (1) não é do long term servicing branch, pelo que poderá ter de mover as versões no futuro, e (2) se implementa contentores, contentores criados no Windows Server 2016 não funcionam no Windows Server  1709 e vice-versa (terá de recriá-los para implementá-las).
>


## <a name="next-steps"></a>Passos Seguintes
Leia mais sobre [protegendo](service-fabric-cluster-security.md), [dimensionamento](service-fabric-cluster-scaling.md), e [atualizar](service-fabric-cluster-upgrade.md) clusters do Azure.

Saiba mais sobre [opções de suporte do Service Fabric](service-fabric-support.md).

[Image]: media/service-fabric-azure-clusters-overview/Cluster.PNG