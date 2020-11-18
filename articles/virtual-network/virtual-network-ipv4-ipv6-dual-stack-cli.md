---
title: Implementar aplicação de pilha dupla IPv6 - Balanceador de Carga Básica - CLI
titlesuffix: Azure Virtual Network
description: Aprenda a implementar uma aplicação de pilha dupla (IPv4 + IPv6) com Balanceador de Carga Básica utilizando O Azure CLI.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 9e9b8dbaa4877c5bf6e7d300c3bb2fc1abf586ee
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94742951"
---
# <a name="deploy-an-ipv6-dual-stack-application-using-basic-load-balancer---cli"></a>Implementar uma aplicação de pilha dupla IPv6 utilizando o Balancer de Carga Básica - CLI

Este artigo mostra-lhe como implementar uma aplicação dual stack (IPv4 + IPv6) com Balanceador de Carga Básica utilizando O Azure CLI que inclui uma rede virtual de dupla pilha com uma sub-rede de dupla pilha, um Balanceador de Carga Básica com configurações frontais duplas (IPv4 + IPv6), VMs com NICs que têm uma configuração IP dupla, regras de grupo de segurança de rede dupla e IPs públicos duplos.

Para implementar uma aplicação de pilha dupla (IPV4 + IPv6) utilizando o Balanceador de Carga Padrão, consulte [implementar uma aplicação de dupla pilha IPv6 com o Balanceador de Carga Padrão utilizando o Azure CLI](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-cli.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo requer a versão 2.0.49 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Antes de criar a sua rede virtual de dupla pilha, tem de criar um grupo de recursos com [criação de grupo az](/cli/azure/group). O exemplo a seguir cria um grupo de recursos chamado *DsResourceGroup01* na localização *leste:*

```azurecli-interactive
az group create \
--name DsResourceGroup01 \
--location eastus
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses-for-load-balancer"></a>Criar endereços IP públicos IPv4 e IPv6 para equilibristas de carga
Para aceder aos seus pontos finais IPv4 e IPv6 na Internet, necessita de endereços IP públicos IPv4 e IPv6 para o equilibrador de carga. Criar um endereço IP público com [az network public-ip create](/cli/azure/network/public-ip). O exemplo a seguir cria endereço IP público IPv4 e IPv6 nomeado *dsPublicIP_v4* e *dsPublicIP_v6* no grupo de recursos *DsResourceGroup01:*

```azurecli-interactive
# Create an IPV4 IP address
az network public-ip create \
--name dsPublicIP_v4  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4

# Create an IPV6 IP address
az network public-ip create \
--name dsPublicIP_v6  \
--resource-group DsResourceGroup01  \
--location eastus \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv6

```

## <a name="create-public-ip-addresses-for-vms"></a>Criar endereços IP públicos para VMs

Para aceder remotamente aos seus VMs na internet, necessita de endereços IP públicos IPv4 para os VMs. Criar um endereço IP público com [az network public-ip create](/cli/azure/network/public-ip).

```azurecli-interactive
az network public-ip create \
--name dsVM0_remote_access  \
--resource-group DsResourceGroup01 \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4

az network public-ip create \
--name dsVM1_remote_access  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4
```

## <a name="create-basic-load-balancer"></a>Criar Balanceador de Carga Básico

Nesta secção, configura o DUPLO FRONTEND IP (IPv4 e IPv6) e o conjunto de endereços de back-end para o balançador de carga e, em seguida, criar um Balanceador de Carga Básico.

### <a name="create-load-balancer"></a>Criar um balanceador de carga

Crie o Balanceador de Carga Básica com [a az network lb criar](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) o nome **dsLB** que inclui uma piscina frontal chamada **dsLbFrontEnd_v4**, um pool de backend nomeado **dsLbBackEndPool_v4** que está associado ao endereço IP público IPv4 **dsPublicIP_v4** que criou no passo anterior. 

```azurecli-interactive
az network lb create \
--name dsLB  \
--resource-group DsResourceGroup01 \
--sku Basic \
--location eastus \
--frontend-ip-name dsLbFrontEnd_v4  \
--public-ip-address dsPublicIP_v4  \
--backend-pool-name dsLbBackEndPool_v4
```

### <a name="create-ipv6-frontend"></a>Criar frontend IPv6

Crie um IP de frontend IPV6 com [a criação de frontend-ip da rede Az](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create)lb . O exemplo a seguir cria uma configuração IP de frontend chamada *dsLbFrontEnd_v6* e anexa o endereço *dsPublicIP_v6:*

```azurecli-interactive
az network lb frontend-ip create \
--lb-name dsLB  \
--name dsLbFrontEnd_v6  \
--resource-group DsResourceGroup01  \
--public-ip-address dsPublicIP_v6

```

### <a name="configure-ipv6-back-end-address-pool"></a>Configure piscina de endereços traseiros IPv6

Crie uma piscina de endereços de fundo IPv6 com [a criação de pool de endereços LB de rede Az.](https://docs.microsoft.com/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create) O exemplo a seguir cria um pool de endereços back-end nomeado *dsLbBackEndPool_v6*  para incluir VMs com configurações IPv6 NIC:

```azurecli-interactive
az network lb address-pool create \
--lb-name dsLB  \
--name dsLbBackEndPool_v6  \
--resource-group DsResourceGroup01
```

### <a name="create-a-health-probe"></a>Criar uma sonda de estado de funcionamento
Crie uma sonda de estado de funcionamento com [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest) para monitorizar o estado de funcionamento das máquinas virtuais. 

```azurecli-interactive
az network lb probe create -g DsResourceGroup01  --lb-name dsLB -n dsProbe --protocol tcp --port 3389
```

### <a name="create-a-load-balancer-rule"></a>Criar uma regra de balanceador de carga

É utilizada uma regra de balanceador de carga para definir a forma como o tráfego é distribuído pelas VMs. Pode definir a configuração de IP de front-end do tráfego de entrada e o conjunto de IPs de back-end para receber o tráfego, juntamente com a porta de origem e de destino necessárias. 

Crie uma regra de balanceador de carga com [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create). O exemplo a seguir cria regras de compensação de carga denominada *dsLBrule_v4* e *dsLBrule_v6* e equilibra o tráfego na porta *TCP* *80* para as configurações IP de frontend IPv4 e IPv6:

```azurecli-interactive
az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v4  \
--resource-group DsResourceGroup01  \
--frontend-ip-name dsLbFrontEnd_v4  \
--protocol Tcp  \
--frontend-port 80  \
--backend-port 80  \
--probe-name dsProbe \
--backend-pool-name dsLbBackEndPool_v4


az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v6  \
--resource-group DsResourceGroup01 \
--frontend-ip-name dsLbFrontEnd_v6  \
--protocol Tcp  \
--frontend-port 80 \
--backend-port 80  \
--probe-name dsProbe \
--backend-pool-name dsLbBackEndPool_v6

```

## <a name="create-network-resources"></a>Criar recursos de rede
Antes de implementar alguns VMs, tem de criar recursos de rede de suporte - conjunto de disponibilidade, grupo de segurança de rede, rede virtual e NICs virtuais. 
### <a name="create-an-availability-set"></a>Criar um conjunto de disponibilidade
Para melhorar a disponibilidade da sua aplicação, coloque os seus VMs num conjunto de disponibilidade.

Crie um conjunto de disponibilidade com [a criação de disponibilidade de az vm](https://docs.microsoft.com/cli/azure/vm/availability-set?view=azure-cli-latest). O exemplo a seguir cria um conjunto de disponibilidade denominado *dsAVset:*

```azurecli-interactive
az vm availability-set create \
--name dsAVset  \
--resource-group DsResourceGroup01  \
--location eastus \
--platform-fault-domain-count 2  \
--platform-update-domain-count 2  
```

### <a name="create-network-security-group"></a>Criar grupo de segurança de rede

Crie um grupo de segurança de rede para as regras que regem a comunicação de entrada e saída no seu VNET.

#### <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Criar um grupo de segurança de rede com [a az rede nsg criar](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create)


```azurecli-interactive
az network nsg create \
--name dsNSG1  \
--resource-group DsResourceGroup01  \
--location eastus

```

#### <a name="create-a-network-security-group-rule-for-inbound-and-outbound-connections"></a>Criar uma regra de grupo de segurança de rede para ligações de entrada e saída

Crie uma regra do grupo de segurança de rede para permitir ligações RDP através da porta 3389, ligação à Internet através da porta 80, e para ligações de saída com [a regra nsg de rede az criar](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create).

```azurecli-interactive
# Create inbound rule for port 3389
az network nsg rule create \
--name allowRdpIn  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 100  \
--description "Allow Remote Desktop In"  \
--access Allow  \
--protocol "*"  \
--direction Inbound  \
--source-address-prefixes "*"  \
--source-port-ranges "*"  \
--destination-address-prefixes "*"  \
--destination-port-ranges 3389

# Create inbound rule for port 80
az network nsg rule create \
--name allowHTTPIn  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 200  \
--description "Allow HTTP In"  \
--access Allow  \
--protocol "*"  \
--direction Inbound  \
--source-address-prefixes "*"  \
--source-port-ranges 80  \
--destination-address-prefixes "*"  \
--destination-port-ranges 80

# Create outbound rule

az network nsg rule create \
--name allowAllOut  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 300  \
--description "Allow All Out"  \
--access Allow  \
--protocol "*"  \
--direction Outbound  \
--source-address-prefixes "*"  \
--source-port-ranges "*"  \
--destination-address-prefixes "*"  \
--destination-port-ranges "*"
```


### <a name="create-a-virtual-network"></a>Criar uma rede virtual

Crie uma rede virtual com [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-create). O exemplo a seguir cria uma rede virtual chamada *dsVNET* com sub-redes *dsSubNET_v4* e *dsSubNET_v6:*

```azurecli-interactive
# Create the virtual network
az network vnet create \
--name dsVNET \
--resource-group DsResourceGroup01 \
--location eastus  \
--address-prefixes "10.0.0.0/16" "ace:cab:deca::/48"

# Create a single dual stack subnet

az network vnet subnet create \
--name dsSubNET \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--address-prefixes "10.0.0.0/24" "ace:cab:deca:deed::/64" \
--network-security-group dsNSG1
```

### <a name="create-nics"></a>Criar NICs

Crie NICs virtuais para cada VM com [a az network nic create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create). O exemplo a seguir cria um NIC virtual para cada VM. Cada NIC tem duas configurações IP (1 IPv4 config, 1 IPv6 config). Cria a configuração IPV6 com [a criação de nic ip-config de rede az.](https://docs.microsoft.com/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-create)

```azurecli-interactive
# Create NICs
az network nic create \
--name dsNIC0  \
--resource-group DsResourceGroup01 \
--network-security-group dsNSG1  \
--vnet-name dsVNET  \
--subnet dsSubNet  \
--private-ip-address-version IPv4 \
--lb-address-pools dsLbBackEndPool_v4  \
--lb-name dsLB  \
--public-ip-address dsVM0_remote_access

az network nic create \
--name dsNIC1 \
--resource-group DsResourceGroup01 \
--network-security-group dsNSG1 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv4 \
--lb-address-pools dsLbBackEndPool_v4 \
--lb-name dsLB \
--public-ip-address dsVM1_remote_access

# Create IPV6 configurations for each NIC

az network nic ip-config create \
--name dsIp6Config_NIC0  \
--nic-name dsNIC0  \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB

az network nic ip-config create \
--name dsIp6Config_NIC1 \
--nic-name dsNIC1 \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB
```

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

Criar os VMs com [az vm criar](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create). O exemplo a seguir cria dois VMs e os componentes de rede virtuais necessários se ainda não existirem. 

Criar máquina virtual *dsVM0* da seguinte forma:

```azurecli-interactive
 az vm create \
--name dsVM0 \
--resource-group DsResourceGroup01 \
--nics dsNIC0 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest  
```

Criar a máquina virtual *dsVM1* da seguinte forma:

```azurecli-interactive
az vm create \
--name dsVM1 \
--resource-group DsResourceGroup01 \
--nics dsNIC1 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest 
```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Ver rede virtual de pilha dupla IPv6 no portal Azure
Pode ver a rede virtual de pilha dupla IPv6 no portal Azure da seguinte forma:
1. Na barra de pesquisa do portal, *insira o DSVnet*.
2. Quando **myVirtualNetwork** aparecer nos resultados da pesquisa, selecione-a. Isto lança a página **geral** da rede virtual dual stack chamada *dsVnet*. A rede virtual dual stack mostra os dois NICs com configurações IPv4 e IPv6 localizadas na sub-rede de pilha dupla chamada *dsSubnet*.

  ![IPv6 dual stack rede virtual em Azure](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)



## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não é necessário, pode utilizar o comando de eliminação do [grupo az](/cli/azure/group#az-group-delete) para remover o grupo de recursos, VM e todos os recursos relacionados.

```azurecli-interactive
 az group delete --name DsResourceGroup01
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, criou um Balanceador de Carga Básica com uma configuração IP de dois frontend (IPv4 e IPv6). Também criou duas máquinas virtuais que incluíam NICs com configurações IP duplas (IPV4 + IPv6) que foram adicionadas ao pool back-end do equilibrista de carga. Para saber mais sobre o suporte do IPv6 nas redes virtuais Azure, veja [o que é o IPv6 para a Rede Virtual Azure?](ipv6-overview.md)
