---
title: Implementar uma aplicação de pilha dupla de IPv6 na rede virtual do Azure - CLI
titlesuffix: Azure Virtual Network
description: Este artigo mostra como implementar uma aplicação de pilha dupla de IPv6 na rede virtual do Azure com a CLI do Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/22/2019
ms.author: kumud
ms.openlocfilehash: 9e591bdf2ff0b6493f092d666d02c2614c907700
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798967"
---
# <a name="deploy-an-ipv6-dual-stack-application-in-azure-virtual-network---cli-preview"></a>Implementar uma aplicação de pilha dupla de IPv6 na rede virtual do Azure - CLI (pré-visualização)

Este artigo mostra-lhe como implementar uma aplicação de pilha dupla (IPv4 + IPv6) no Azure, que inclui uma rede virtual de pilha dupla com uma sub-rede de pilha dupla, um balanceador de carga com configurações de front-end dupla (IPv4 + IPv6), as VMs com NICs que têm uma configuração de IP dupla, regras do grupo de segurança de rede duplos e IPs públicos duplo.

> [!Important]
> Pilha dupla de IPv6 para a rede Virtual do Azure está atualmente em pré-visualização pública. Esta pré-visualização é disponibilizada sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) agora.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI do Azure localmente em vez disso, este início rápido requer a utilização da CLI do Azure versão 2.0.49 ou posterior. Para localizar a versão instalada, execute `az --version`. Ver [instalar a CLI do Azure](/cli/azure/install-azure-cli) para instalação ou atualização de informações.

## <a name="prerequisites"></a>Pré-requisitos
Para utilizar o IPv6 para recursos de rede virtual do Azure, tem de configurar a sua subscrição com o Azure PowerShell da seguinte forma:

```azurecli
az feature register --name AllowIPv6VirtualNetwork --namespace Microsoft.Network
```
Demora até 30 minutos para o registo de funcionalidade concluir. Pode verificar o estado de registo ao executar o seguinte comando da CLI do Azure:

```azurelci
az feature show --name AllowIPv6VirtualNetwork --namespace Microsoft.Network
```
Após o registo estiver concluído, execute o seguinte comando:

```azurelci
az provider register --namespace Microsoft.Network
```
## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Antes de poder criar a rede virtual de pilha dupla, tem de criar um grupo de recursos com [criar grupo az](/cli/azure/group). O exemplo seguinte cria um grupo de recursos chamado *myRGDualStack* no *eastus* localização:

```azurecli
az group create \
--name DsResourceGroup01 \
--location eastus
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses-for-load-balancer"></a>Criar o IPv4 e IPv6 endereços IP públicos para o Balanceador de carga
Para acessar os pontos finais de IPv4 e IPv6 na Internet, terá de endereços IP públicos IPv4 e IPv6 para o Balanceador de carga. Criar um endereço IP público com [az network public-ip create](/cli/azure/network/public-ip). O exemplo seguinte cria o IPv4 e IPv6 endereço IP público com o nome *dsPublicIP_v4* e *dsPublicIP_v6* no *myRGDualStack* grupo de recursos:

```azurecli
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

## <a name="create-public-ip-addresses-for-vms"></a>Crie endereços IP públicos para VMs

Para aceder remotamente as suas VMs na internet, terá de endereços IP públicos de IPv4 para as VMs. Criar um endereço IP público com [az network public-ip create](/cli/azure/network/public-ip).

```azurecli
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

Nesta secção, configure o IP de front-end dupla (IPv4 e IPv6) e o conjunto de endereços de back-end do Balanceador de carga e, em seguida, criar um balanceador de carga básico.

### <a name="create-load-balancer"></a>Criar um balanceador de carga

Criar o Balanceador de carga básico com [criar az rede lb](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) com o nome **dsLB** que inclua um conjunto de front-end com o nome **dsLbFrontEnd_v4**, um conjunto de back-end com o nome  **dsLbBackEndPool_v4** que está associado com o endereço IP público IPv4 **dsPublicIP_v4** que criou no passo anterior. 

```azurecli
az network lb create \
--name dsLB  \
--resource-group DsResourceGroup01 \
--sku Basic \
--location eastus \
--frontend-ip-name dsLbFrontEnd_v4  \
--public-ip-address dsPublicIP_v4  \
--backend-pool-name dsLbBackEndPool_v4
```

### <a name="create-ipv6-frontend"></a>Crie front-end de IPv6

Criar um IP de front-end IPV6 com [az rede lb frontend-ip criar](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create). O exemplo seguinte cria uma configuração de IP de front-end com o nome *dsLbFrontEnd_v6* e anexa o *dsPublicIP_v6* endereço:

```azurepowershell-interactive
az network lb frontend-ip create \
--lb-name dsLB  \
--name dsLbFrontEnd_v6  \
--resource-group DsResourceGroup01  \
--public-ip-address dsPublicIP_v6

```

### <a name="configure-ipv6-back-end-address-pool"></a>Configurar o conjunto de endereços de back-end IPv6

Criar um endereço de back-end IPv6 conjuntos com [az rede lb-conjunto de endereços criar](https://docs.microsoft.com/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create). O exemplo seguinte cria o conjunto de endereços de back-end com o nome *dsLbBackEndPool_v6* para incluir as VMs com configurações de IPv6 de NIC:

```azurecli
az network lb address-pool create \
--lb-name dsLB  \
--name dsLbBackEndPool_v6  \
--resource-group DsResourceGroup01
```

### <a name="create-a-load-balancer-rule"></a>Crie uma regra de balanceador de carga

É utilizada uma regra de balanceador de carga para definir a forma como o tráfego é distribuído pelas VMs. Pode definir a configuração de IP de front-end do tráfego de entrada e o conjunto de IPs de back-end para receber o tráfego, juntamente com a porta de origem e de destino necessárias. 

Crie uma regra de balanceador de carga com [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create). O exemplo seguinte cria regras de Balanceador de carga com o nome *dsLBrule_v4* e *dsLBrule_v6* e faz o balanceamento de tráfego no *TCP* porta *80* para os IPv4 e IPv6 front-end configurações de IP:

```azurecli
az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v4  \
--resource-group DsResourceGroup01  \
--frontend-ip-name dsLbFrontEnd_v4  \
--protocol Tcp  \
--frontend-port 80  \
--backend-port 80  \
--backend-pool-name dsLbBackEndPool_v4


az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v6  \
--resource-group DsResourceGroup01 \
--frontend-ip-name dsLbFrontEnd_v6  \
--protocol Tcp  \
--frontend-port 80 \
--backend-port 80  \
--backend-pool-name dsLbBackEndPool_v6

```

## <a name="create-network-resources"></a>Criar recursos de rede
Antes de implementar algumas VMs, tem de criar os recursos de rede de apoio - conjunto de disponibilidade, o grupo de segurança de rede, rede virtual e NICs virtuais. 
### <a name="create-an-availability-set"></a>Criar um conjunto de disponibilidade
Para melhorar a disponibilidade da sua aplicação, coloque as VMs num conjunto de disponibilidade.

Crie um conjunto de disponibilidade com [az vm availability-set create](https://docs.microsoft.com/cli/azure/vm/availability-set?view=azure-cli-latest). O exemplo seguinte cria um conjunto de disponibilidade designado *dsAVset*:

```azurecli
az vm availability-set create \
--name dsAVset  \
--resource-group DsResourceGroup01  \
--location eastus \
--platform-fault-domain-count 2  \
--platform-update-domain-count 2  
```

### <a name="create-network-security-group"></a>Criar grupo de segurança de rede

Crie um grupo de segurança de rede para as regras que serão regem a comunicação de entrada e saída na sua VNET.

#### <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Crie um grupo de segurança de rede com [criar az rede nsg](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create)


```azurecli
az network nsg create \
--name dsNSG1  \
--resource-group DsResourceGroup01  \
--location eastus

```

#### <a name="create-a-network-security-group-rule-for-inbound-and-outbound-connections"></a>Criar uma regra de grupo de segurança de rede para ligações de entrada e saídas

Criar uma regra de grupo de segurança de rede para permitir ligações de RDP através da conexão com porta 3389, a internet através da porta 80 bem como para as ligações de saída com [criar regra de nsg de rede de az](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create).

```azurecli
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

Crie uma rede virtual com [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-create). O exemplo seguinte cria uma rede virtual denominada *dsVNET* com sub-redes *dsSubNET_v4* e *dsSubNET_v6*:

```azurecli
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

Criar NICs virtuais para cada VM com [nic da rede de az criar](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create). O exemplo seguinte cria uma NIC virtual para cada VM. Cada NIC tem duas configurações de IP (1 configuração de IPv4, 1 configuração de IPv6). Criar a configuração de IPV6 com [criar ip de nic de rede de az-config](https://docs.microsoft.com/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-create).
 
```azurecli
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

Criar as VMs com [az vm criar](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create). O exemplo seguinte cria duas VMs e os componentes de rede virtual necessários, se eles ainda não existir. 

Criar máquina virtual *dsVM0* da seguinte forma:

```azurecli
 az vm create \
--name dsVM0 \
--resource-group DsResourceGroup01 \
--nics dsNIC0 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest  
```
Criar máquina virtual *dsVM1* da seguinte forma:

```azurecli
az vm create \
--name dsVM1 \
--resource-group DsResourceGroup01 \
--nics dsNIC1 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest 
```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Ver a rede virtual de pilha dupla de IPv6 no portal do Azure
Pode ver a rede virtual de pilha dupla de IPv6 no portal do Azure da seguinte forma:
1. Na barra de pesquisa do portal, introduza *dsVnet*.
2. Quando **myVirtualNetwork** aparecer nos resultados da pesquisa, selecione-a. Isso inicia o **descrição geral** página da rede virtual pilha dupla com o nome *dsVnet*. A rede virtual de pilha dupla mostra dois NICs com configurações de IPv4 e IPv6 localizadas na sub-rede de pilha dupla com o nome *dsSubnet*.

  ![Rede virtual com uma pilha dupla de IPv6 no Azure](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)

> [!NOTE]
> O IPv6 para a rede virtual do Azure está disponível no portal do Azure no só de leitura para esta versão de pré-visualização.


## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, pode utilizar o comando [az group delete](/cli/azure/group#az-group-delete) para remover o Grupo de Recursos, a VM e todos os recursos relacionados.

```azurecli
 az group delete --name DsRG1
```

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, criou um balanceador de carga básico com uma configuração de IP de front-end dupla (IPv4 e IPv6). Também criou um duas máquinas virtuais que acompanha o NICs duplas configurações de IP (IPV4 + IPv6) que foram adicionadas ao agrupamento de back-end de Balanceador de carga. Para saber mais sobre o suporte ao IPv6 em redes virtuais do Azure, veja [o que é o IPv6 para a rede Virtual do Azure?](ipv6-overview.md)
