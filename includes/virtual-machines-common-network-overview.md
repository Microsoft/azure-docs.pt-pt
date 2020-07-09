---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 11/01/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: a665c3c27cca03a084896895ae51c56732ceb7b6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84237675"
---
Quando cria uma máquina virtual (VM) do Azure, tem de criar uma [rede virtual](../articles/virtual-network/virtual-networks-overview.md) (VNet) ou de utilizar uma VNet já existente. Também tem de decidir como pretende que se faça o acesso às VMs na VNet. É importante [planear antes de criar recursos](../articles/virtual-network/virtual-network-vnet-plan-design-arm.md) e ter a certeza de que compreende os [limites dos recursos de rede](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).

Na figura seguinte, as VMs são representadas como servidores Web e servidores de bases de dados. Cada conjunto de VMs é atribuído a sub-redes separadas na VNet.

![Rede virtual do Azure](./media/virtual-machines-common-network-overview/vnetoverview.png)

Pode criar um VNet antes de criar um VM ou pode criar um VM. Os recursos seguintes são criados para suportar a comunicação com as VMs:

- Interfaces de rede
- Endereços IP
- Rede virtual e sub-redes

Para além destes recursos básicos, também deve considerar estes recursos opcionais:

- Grupos de segurança de rede
- Balanceadores de carga 

## <a name="network-interfaces"></a>Interfaces de rede

Uma [interface de rede (NIC)](../articles/virtual-network/virtual-network-network-interface.md) é a interligação entre um VM e uma rede virtual (VNet). As VMs têm de ter, pelo menos, uma NIC, mas podem ter mais, dependendo do tamanho da VM que criar. Saiba quantas NICs cada tamanho de VM suporta no [Windows](../articles/virtual-machines/windows/sizes.md) ou no [Linux](../articles/virtual-machines/linux/sizes.md).

Pode criar uma VM com várias NICs e adicionar ou remover NICs ao longo do ciclo de vida de uma VM. Várias NICs permitem que uma VM se ligue a diferentes sub-redes e envie ou receba tráfego através da interface mais apropriada. Podem existir VMs com qualquer número de interfaces de rede no mesmo conjunto de disponibilidade, até ao número suportado pelo tamanho da VM. 

Cada NIC ligada a uma VM tem de existir na mesma localização e subscrição que a VM. Cada NIC deve estar ligada a uma VNet que exista na mesma localização e subscrição do Azure que essa NIC. Pode alterar a sub-rede à qual uma VM é ligada depois de ser criada, mas não pode alterar a VNet. Atribui-se um endereço MAC a cada NIC ligada a uma VM, o qual não muda até a VM ser eliminada.

Esta tabela lista os métodos que pode utilizar para criar uma interface de rede.

| Método | Descrição |
| ------ | ----------- |
| Portal do Azure | Quando cria uma VM no portal do Azure, é criada automaticamente uma interface de rede por si (não pode utilizar uma NIC que tenha criado separadamente). O portal cria uma VM com apenas uma NIC. Se quiser criar uma VM com mais de uma NIC, tem de criá-la com outro método. |
| [Azure PowerShell](../articles/virtual-machines/windows/multiple-nics.md) | Utilize [o New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) com o parâmetro **-PublicIpAddressId** para fornecer o identificador do endereço IP público que criou anteriormente. |
| [CLI do Azure](../articles/virtual-machines/linux/multiple-nics.md) | Para fornecer o identificador do endereço IP público que criou anteriormente, utilize [o nic de rede az criar](https://docs.microsoft.com/cli/azure/network/nic) com o parâmetro de endereço **--público-ip-endereço.** |
| [Modelo](../articles/virtual-network/template-samples.md) | Utilize [Network Interface in a Virtual Network with Public IP Address (Interface de Rede numa Rede Virtual com Endereço IP Público)](https://github.com/Azure/azure-quickstart-templates/tree/master/101-nic-publicip-dns-vnet) como guia para implementar interfaces de rede através de um modelo. |

## <a name="ip-addresses"></a>Endereços IP 

Pode atribuir estes tipos de [endereços IP](../articles/virtual-network/public-ip-addresses.md) a uma NIC no Azure:

- **Endereços IP públicos** - utilizados para comunicação de entrada e saída (sem tradução de endereços de rede [NAT]) com a Internet e outros recursos do Azure não ligados a uma VNet. A atribuição de endereços IP públicos a uma NIC é opcional. Os endereços IP públicos têm uma taxa nominal, e há um número máximo que pode ser usado por subscrição.
- **Endereços IP privados** - utilizados para comunicação dentro de VNets, com a sua rede no local e com a Internet (com NAT). Tem de atribuir, pelo menos, um endereço IP privado a uma VM. Para saber mais sobre a NAT no Azure, leia [Understanding outbound connections in Azure (Compreender as ligações de saída no Azure)](../articles/load-balancer/load-balancer-outbound-connections.md).

Pode atribuir endereços IP públicos a VMs ou a balanceadores de carga com acesso à Internet. Pode atribuir endereços IP privados a VMs e a balanceadores de carga internos. Para atribuir endereços IP a uma VM, é utilizada uma interface de rede.

Existem dois métodos através dos quais os endereços IP podem ser alocados a recursos - dinâmico ou estático. O método de alocação predefinido é o dinâmico, no qual o endereço IP não é alocado quando é criado. Em vez disso, é alocado quando cria uma VM ou inicia uma VM parada. O endereço IP é libertado quando para ou elimina a VM. 

Para garantir que o endereço IP da VM fica o mesmo, pode definir o método de alocação explicitamente como estático. Neste caso, é atribuído imediatamente um endereço IP. Só é libertado quando elimina a VM ou altera o método de alocação para dinâmico.
    
Esta tabela lista os métodos que pode utilizar para criar um endereço IP.

| Método | Descrição |
| ------ | ----------- |
| [Portal do Azure](../articles/virtual-network/virtual-network-deploy-static-pip-arm-portal.md) | Por predefinição, os endereços IP públicos são dinâmicos e o endereço associado aos mesmos pode mudar quando a VM é parada ou eliminada. Para garantir que a VM utiliza sempre o mesmo endereço IP público, crie um endereço IP público estático. Por predefinição, o portal atribui um endereço IP privado dinâmico a uma NIC quando é criada uma VM. Pode alterar este endereço IP para estática após a criação do VM.|
| [Azure PowerShell](../articles/virtual-network/virtual-network-deploy-static-pip-arm-ps.md) | Utiliza [o New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) com o parâmetro **-AllocationMethod** como Dinâmico ou Estático. |
| [CLI do Azure](../articles/virtual-network/virtual-network-deploy-static-pip-arm-cli.md) | Utiliza [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip) com o parâmetro **--allocation-method** como Dinâmico ou Estático. |
| [Modelo](../articles/virtual-network/template-samples.md) | Utilize [Network Interface in a Virtual Network with Public IP Address (Interface de Rede numa Rede Virtual com Endereço IP Público)](https://github.com/Azure/azure-quickstart-templates/tree/master/101-nic-publicip-dns-vnet) como guia para implementar endereços IP públicos através de um modelo. |

Depois de criar um endereço IP público, pode associá-lo a uma VM ao atribuí-lo a uma NIC.

## <a name="virtual-network-and-subnets"></a>Rede virtual e sub-redes

Uma sub-rede é um intervalo de endereços IP na VNet. Pode dividir uma VNet em várias sub-redes por questões de organização e de segurança. Cada NIC numa VM está ligada a uma sub-rede numa VNet. As NICs ligadas a sub-redes (às mesmas ou a diferentes) dentro de uma VNet podem comunicar entre si sem qualquer configuração adicional.

Quando configura uma VNet, especifica a topologia, incluindo os espaços de endereços e as sub-redes disponíveis. Se a VNet for ligada a outras VNets ou a redes no local, tem de selecionar intervalos de endereços que não se sobreponham. Os endereços IP são privados e não podem ser acedidos a partir da Internet, o que só se aplica aos endereços IP não rotejáveis, tais como 10.0.0.0/8, 172.16.0.0/12, ou 192.168.0.0/16. Agora, o Azure trata todos os intervalos de endereços como fazendo parte do espaço de endereços IP privados da VNet que só está acessível dentro da VNet, dentro de VNets interligadas e dentro da sua localização no local. 

Se trabalha numa organização em que outra pessoa é responsável pelas redes internas, deve contactá-la antes de selecionar o espaço de endereços. Certifique-se de que não há sobreposição e deixe-os saber o espaço que pretende utilizar para que não tentem utilizar a mesma gama de endereços IP. 

Por predefinição, não existem limites de segurança entre sub-redes, pelo que as VMs em cada uma dessas sub-redes podem comunicar entre si. No entanto, pode configurar Grupos de Segurança de Rede (NSG), que lhe permitem controlar o fluxo de tráfego de e para as sub-redes e VMs. 

Esta tabela lista os métodos que pode utilizar para criar uma VNet e sub-redes.    

| Método | Descrição |
| ------ | ----------- |
| [Portal do Azure](../articles/virtual-network/quick-create-portal.md) | Se permitir que o Azure crie uma VNet quando cria uma VM, o nome é uma combinação do nome do grupo de recursos que contém a VNet e de **-vnet**. O espaço de endereços é 10.0.0.0/24, o nome da sub-rede obrigatório é **default** e o intervalo de endereços da sub-rede é 10.0.0.0/24. |
| [Azure PowerShell](../articles/virtual-network/quick-create-powershell.md) | Utiliza [a New-AzVirtualNetWorkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworkSubnetConfig) e [a New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork) para criar uma sub-rede e um VNet. Também pode utilizar [Add-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/Az.Network/Add-AzVirtualNetworkSubnetConfig) para adicionar uma sub-rede a um VNet existente. |
| [CLI do Azure](../articles/virtual-network/quick-create-cli.md) | A sub-rede e a VNet são criadas ao mesmo tempo. Forneça um parâmetro **--subnet-name** para [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet) com o nome da sub-rede. |
| Modelo | A forma mais fácil de criar um VNet e sub-redes é descarregar um modelo existente, como [a Rede Virtual com duas sub-redes,](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets)e modificá-lo para as suas necessidades. |

## <a name="network-security-groups"></a>Grupos de segurança de rede

Os [grupo de segurança de rede (NSG)](../articles/virtual-network/virtual-network-vnet-plan-design-arm.md) contêm uma lista de regras da Lista de Controlo de Acesso (ACL) que permitem ou negam o tráfego de rede para sub-redes, NICs ou ambas. Os NSGs podem ser associados a sub-redes ou a NICs individuais ligadas a sub-redes. Quando um NSG é associado a uma sub-rede, as regras da ACL são aplicadas a todas as VM nessa sub-rede. Além disso, o tráfego para uma NIC individual pode ser restringido ao associar um NSG diretamente à NIC.

Os NSGs contêm dois conjuntos de regras: de entrada e de saída. A prioridade para uma regra tem de ser exclusiva dentro de cada conjunto. Cada regra tem propriedades de protocolo, de intervalos de portas de origem e destino, de prefixos de endereços, de direção do tráfego, de prioridade e de tipo de acesso. 

Todos os NSGs contêm um conjunto de regras predefinidas. As regras predefinidas não podem ser eliminadas, mas como lhes é atribuída a prioridade mais baixa, podem ser substituídas pelas regras que criar. 

Quando associa um NSG a um NIC, as regras de acesso à rede no NSG só são aplicadas a esse NIC. Se for aplicado um NSG a uma NIC individual numa VM multi-NIC, o tráfego para as outras NIC não é afetado. Pode associar NSGs diferentes a uma NIC (ou VM, consoante o modelo de implementação) e à sub-rede à qual a NIC ou VM está vinculada. A prioridade é atribuída com base na direção do tráfego.

Certifique-se de que [planeia](../articles/virtual-network/virtual-network-vnet-plan-design-arm.md) os NSGs quando planear as VMs e a VNet.

Esta tabela lista os métodos que pode utilizar para criar um grupo de segurança de rede.

| Método | Descrição |
| ------ | ----------- |
| [Portal do Azure](../articles/virtual-network/tutorial-filter-network-traffic.md) | Quando cria uma VM no portal do Azure, é criado automaticamente um NSG e associado à NIC que o portal cria. O nome do NSG é uma combinação do nome da VM e de **-nsg**. Este NSG contém uma regra de entrada com prioridade de 1000, o serviço definido como RDP, o protocolo como TCP, a porta como a 3389 e a ação Permitir. Se quiser permitir outro tráfego de entrada para a VM, tem de adicionar regras ao NSG. |
| [Azure PowerShell](../articles/virtual-network/tutorial-filter-network-traffic.md) | Utilize [o New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig) e forneça as informações necessárias à regra. Utilize [o New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup) para criar o NSG. Utilize [o Set-AzVirtualNetSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworksubnetconfig) para configurar o NSG para a sub-rede. Utilize [o Set-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetwork) para adicionar o NSG ao VNet. |
| [CLI do Azure](../articles/virtual-network/tutorial-filter-network-traffic-cli.md) | Utilize [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg) para criar inicialmente o NSG. Utilize [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule) para adicionar regras ao NSG. Utilize [az network vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet) para adicionar o NSG à sub-rede. |
| [Modelo](../articles/virtual-network/template-samples.md) | Utilize [Create a Network Security Group (Criar um Grupo de Segurança de Rede)](https://github.com/Azure/azure-quickstart-templates/tree/master/101-security-group-create) como guia para implementar um grupo de segurança de rede através de um modelo. |

## <a name="load-balancers"></a>Balanceadores de carga

O [Balanceador de Carga do Azure](../articles/load-balancer/load-balancer-overview.md) oferece elevada disponibilidade e elevado desempenho de rede às suas aplicações. Pode ser configurado um balanceador de carga para [balancear o tráfego de entrada da Internet](../articles/load-balancer/load-balancer-internet-overview.md) para VMs ou [balancear o tráfego entre VMs numa VNet](../articles/load-balancer/load-balancer-internal-overview.md). Os balanceadores de carga também podem balancear o tráfego entre computadores no local e VMs numa rede em vários locais ou encaminhar tráfego externo para uma VM específica.

Os balanceadores de carga mapeiam o tráfego de entrada e saída entre o endereço IP público e a porta do balanceador de carga e o endereço IP privado e a porta da VM.

Quando cria um balanceador de carga, tem também de considerar estes elementos de configuração:

- **Configuração IP frontal** – Um equilibrador de carga pode incluir um ou mais endereços IP front-end. Estes endereços IP servem como entrada para o tráfego.
- **Conjunto de endereços de back-end** – endereços IP que estão associados à NIC na qual a carga é distribuída.
- **[Port Forwarding](../articles/load-balancer/tutorial-load-balancer-port-forwarding-portal.md)** - Define como o tráfego de entrada flui através do IP frontal e distribuído para o IP de back-end utilizando as regras NAT de entrada.
- **Regras de balanceador de carga** -mapeiam uma determinada combinação de IP e porta de front-end para um conjunto de combinação de endereços IP e porta de back-end. Um balanceador de carga individual pode ter várias regras de balanceamento de carga. Cada regra é uma combinação de um IP de front-end e porta e IP de back-end e porta associados a VMs
- **[Sondas](../articles/load-balancer/load-balancer-custom-probe-overview.md)** - monitorizam o estado de funcionamento das VMs. Quando uma sonda não consegue responder, o balanceador de carga deixa de enviar ligações novas para a VM em mau estado de funcionamento. As ligações existentes não são afetadas e as novas são enviadas para as VMs em bom estado de funcionamento.
- **[Regras de saída](../articles/load-balancer/load-balancer-outbound-rules-overview.md)** - Uma regra de saída configura a tradução de endereço de rede de saída (NAT) para todas as máquinas virtuais ou instâncias identificadas pelo pool de backend do seu Balancer de Carga Padrão a traduzir para o frontend.

Esta tabela lista os métodos que pode utilizar para criar um balanceador de carga com acesso à Internet.

| Método | Descrição |
| ------ | ----------- |
| Portal do Azure |  Pode [carregar o tráfego de internet de equilíbrio para VMs utilizando o portal Azure](../articles/load-balancer/tutorial-load-balancer-standard-manage-portal.md). |
| [Azure PowerShell](/azure/load-balancer/load-balancer-get-started-ilb-arm-ps) | Para fornecer o identificador do endereço IP público que criou anteriormente, utilize [o New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig) com o parâmetro **-PublicIpAddress.** Utilize [o New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) para criar a configuração do conjunto de endereços back-end. Utilize [New-AzLoadBalancerInboundNatRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) para criar regras NAT de entrada associadas à configuração IP frontal que criou. Utilize [o New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig) para criar as sondas de que necessita. Utilize [o New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig) para criar a configuração do balançador de carga. Utilize [o New-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer) para criar o equilibrador de carga.|
| [CLI do Azure](../articles/load-balancer/load-balancer-get-started-internet-arm-cli.md) | Utilize [az network lb create](https://docs.microsoft.com/cli/azure/network/lb) para criar a configuração inicial do balanceador de carga. Utilize [az network lb frontend-ip create](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip) para adicionar o endereço IP público que criou anteriormente. Utilize [az network lb address-pool create](https://docs.microsoft.com/cli/azure/network/lb/address-pool) para adicionar a configuração do conjunto de endereços do back-end. Utilize [az network lb inbound-nat-rule create](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-rule) para adicionar regras NAT. Utilize [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule) para adicionar as regras do balanceador de carga. Utilize [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe) para adicionar as sondas. |
| [Modelo](../articles/load-balancer/quickstart-load-balancer-standard-public-template.md) | Utilize [2 VMs in a Load Balancer and configure NAT rules on the LB (Duas VMs num Balanceador de Carga e configurar regras NAT no LB)](https://github.com/Azure/azure-quickstart-templates/tree/master/101-load-balancer-standard-create) como guia para implementar um balanceador de carga através de um modelo. |
    
Esta tabela lista os métodos que pode utilizar para criar um balanceador de carga interno.

| Método | Descrição |
| ------ | ----------- |
| Portal do Azure | Pode [equilibrar a carga de tráfego interno com um equilibrador de carga no portal Azure](../articles/load-balancer/tutorial-load-balancer-standard-internal-portal.md). |
| [Azure PowerShell](../articles/load-balancer/load-balancer-get-started-ilb-arm-ps.md) | Para fornecer um endereço IP privado na sub-rede de rede, utilize [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig) com o parâmetro **-PrivateIpAddress.** Utilize [o New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) para criar a configuração do conjunto de endereços back-end. Utilize [New-AzLoadBalancerInboundNatRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) para criar regras NAT de entrada associadas à configuração IP frontal que criou. Utilize [o New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig) para criar as sondas de que necessita. Utilize [o New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig) para criar a configuração do balançador de carga. Utilize [o New-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer) para criar o equilibrador de carga.|
| [CLI do Azure](../articles/load-balancer/load-balancer-get-started-ilb-arm-cli.md) | Utilize o comando [az network lb create](https://docs.microsoft.com/cli/azure/network/lb) para criar a configuração inicial do balanceador de carga. Para definir o endereço IP privado, utilize [az network lb frontend-ip create](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip) com o parâmetro **--private-ip-address**. Utilize [az network lb address-pool create](https://docs.microsoft.com/cli/azure/network/lb/address-pool) para adicionar a configuração do conjunto de endereços do back-end. Utilize [az network lb inbound-nat-rule create](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-rule) para adicionar regras NAT. Utilize [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule) para adicionar as regras do balanceador de carga. Utilize [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe) para adicionar as sondas.|
| [Modelo](../articles/load-balancer/load-balancer-get-started-ilb-arm-template.md) | Utilize [2 VMs in a Load Balancer and configure NAT rules on the LB (Duas VMs num Balanceador de Carga e configurar regras NAT no LB)](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-internal-load-balancer) como guia para implementar um balanceador de carga através de um modelo. |

### <a name="virtual-machine-scale-sets"></a>Conjuntos de dimensionamento de máquinas virtuais

Para obter mais informações sobre os conjuntos de balança de carga e de escala de máquinas virtuais, consulte [conjuntos de balanças de máquinas virtuais Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-networking).

## <a name="vms"></a>VMs

As VMs podem ser criadas na mesma VNet e podem ligar-se entre si através de endereços IP privados. Podem ligar-se mesmo que estejam em sub-redes diferentes sem que seja necessário configurar um gateway ou utilizar endereços IP públicos. Para colocar VMs em VNets, cria primeiro a VNet e, depois, à medida que cria cada VM, vai adicionado-a à VNet e à sub-rede. As VMs adquirem as definições de rede durante a implementação ou o arranque.  

É atribuído um endereço IP às VMs quando são implementadas. Se implementar várias VMs numa VNet ou sub-rede, os endereços IP são-lhes atribuídos quando são arrancadas. Também pode alocar um IP estático a um VM. Se atribuir um IP estático, deve considerar a utilização de uma sub-rede específica para evitar a reutilização acidental de um IP estático para outro VM.  

Se criar uma VM e, posteriormente, quiser migrá-la para uma VNet, não é uma alteração de configuração simples. Tem de reimplementar a VM na VNet. A forma mais fácil de reimplementar é eliminar a VM, mas não os discos ligados à mesma, e utilizar os discos originais na VNet para recriá-la. 

Esta tabela lista os métodos que pode utilizar para criar uma VM numa VNet.

| Método | Descrição |
| ------ | ----------- |
| [Portal do Azure](../articles/virtual-machines/windows/quick-create-portal.md) | Utiliza as predefinições de rede que foram mencionadas anteriormente para criar uma VM com uma única NIC. Para criar uma VM com várias NICs, tem de utilizar outro método. |
| [Azure PowerShell](../articles/virtual-machines/windows/tutorial-manage-vm.md) | Inclui a utilização de [Add-AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface) para adicionar o NIC que criou anteriormente à configuração VM. |
| [CLI do Azure](../articles/virtual-machines/linux/create-cli-complete.md) | Crie e ligue um VM a um Vnet, subnet e NIC que constroem como passos individuais. |
| [Modelo](../articles/virtual-machines/windows/ps-template.md) | Utilize [Very simple deployment of a Windows VM (Implementação muito simples de uma VM do Windows)](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows) como guia para implementar uma VM através de um modelo. |

## <a name="next-steps"></a>Próximos passos
Para passos específicos em VM sobre como gerir redes virtuais Azure para VMs, consulte os tutoriais [Windows](../articles/virtual-machines/windows/tutorial-virtual-network.md) ou [Linux.](../articles/virtual-machines/linux/tutorial-virtual-network.md)

Existem também tutoriais sobre como carregar VMs de equilíbrio e criar aplicações altamente disponíveis para [Windows](../articles/virtual-machines/windows/tutorial-load-balancer.md) ou [Linux.](../articles/virtual-machines/linux/tutorial-load-balancer.md)

- Saiba como configurar [rotas definidas pelo utilizador e encaminhamento de IP](../articles/virtual-network/virtual-networks-udr-overview.md). 
- Saiba como configurar [ligações VNet a VNet](../articles/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).
- Saiba como [Resolver problemas de rotas](../articles/virtual-network/diagnose-network-routing-problem.md).
- Saiba mais sobre [a largura de banda da rede de máquinas virtuais.](../articles/virtual-network/virtual-machine-network-throughput.md)
