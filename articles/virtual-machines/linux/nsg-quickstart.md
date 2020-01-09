---
title: Abrir portas para uma VM Linux com CLI do Azure
description: Saiba como abrir uma porta/criar um ponto de extremidade para sua VM do Linux usando o modelo de implantação do Azure Resource Manager e o CLI do Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
ms.assetid: eef9842b-495a-46cf-99a6-74e49807e74e
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: cynthn
ms.openlocfilehash: cfcea8c775b2d6a9bee3c078cf67f0a942af5984
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75354038"
---
# <a name="open-ports-and-endpoints-to-a-linux-vm-with-the-azure-cli"></a>Abrir portas e pontos de extremidade para uma VM Linux com o CLI do Azure

Você abre uma porta ou cria um ponto de extremidade para uma VM (máquina virtual) no Azure criando um filtro de rede em uma sub-rede ou interface de rede de VM. Você coloca esses filtros, que controlam o tráfego de entrada e de saída, em um grupo de segurança de rede anexado ao recurso que recebe o tráfego. Vamos usar um exemplo comum de tráfego da Web na porta 80. Este artigo mostra como abrir uma porta para uma VM com o CLI do Azure. 


Para criar um grupo de segurança de rede e regras, você precisa das [CLI do Azure](/cli/azure/install-az-cli2) mais recentes instaladas e conectadas a uma conta do Azure usando [AZ login](/cli/azure/reference-index).

Nos exemplos a seguir, substitua os nomes de parâmetro de exemplo pelos seus próprios valores. Os nomes de parâmetro de exemplo incluem *MyResource*, *myNetworkSecurityGroup*e *myVnet*.


## <a name="quickly-open-a-port-for-a-vm"></a>Abrir rapidamente uma porta para uma VM
Se precisar abrir rapidamente uma porta para uma VM em um cenário de desenvolvimento/teste, você poderá usar o comando [AZ VM Open-Port](/cli/azure/vm) . Esse comando cria um grupo de segurança de rede, adiciona uma regra e a aplica a uma VM ou sub-rede. O exemplo a seguir abre a porta *80* na VM chamada *myVM* no grupo de recursos chamado *MyResource*Group.

```azure-cli
az vm open-port --resource-group myResourceGroup --name myVM --port 80
```

Para obter mais controle sobre as regras, como definir um intervalo de endereços IP de origem, continue com as etapas adicionais neste artigo.


## <a name="create-a-network-security-group-and-rules"></a>Criar um grupo de segurança de rede e regras
Crie o grupo de segurança de rede com [AZ Network NSG Create](/cli/azure/network/nsg). O exemplo a seguir cria um grupo de segurança de rede chamado *myNetworkSecurityGroup* no local *eastus* :

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNetworkSecurityGroup
```

Adicione uma regra com [AZ Network NSG Rule Create](/cli/azure/network/nsg/rule) para permitir o tráfego HTTP para seu servidor da WebServer (ou ajuste para seu próprio cenário, como acesso SSH ou conectividade de banco de dados). O exemplo a seguir cria uma regra chamada *myNetworkSecurityGroupRule* para permitir o tráfego TCP na porta 80:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --protocol tcp \
    --priority 1000 \
    --destination-port-range 80
```


## <a name="apply-network-security-group-to-vm"></a>Aplicar grupo de segurança de rede à VM
Associe o grupo de segurança de rede à NIC (interface de rede) da VM com [AZ Network NIC Update](/cli/azure/network/nic). O exemplo a seguir associa uma NIC existente chamada *myNic* ao grupo de segurança de rede chamado *myNetworkSecurityGroup*:

```azurecli
az network nic update \
    --resource-group myResourceGroup \
    --name myNic \
    --network-security-group myNetworkSecurityGroup
```

Como alternativa, você pode associar o grupo de segurança de rede a uma sub-rede de rede virtual com [AZ Network vnet subnet Update](/cli/azure/network/vnet/subnet) em vez de apenas para a interface de rede em uma única VM. O exemplo a seguir associa uma sub-rede existente chamada *mysubnet* na rede virtual *MyVnet* com o grupo de segurança de rede chamado *myNetworkSecurityGroup*:

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```

## <a name="more-information-on-network-security-groups"></a>Mais informações sobre grupos de segurança de rede
Os comandos rápidos aqui permitem que você comece a executar o tráfego que flui para sua VM. Os grupos de segurança de rede fornecem muitos ótimos recursos e granularidade para controlar o acesso aos seus recursos. Você pode ler mais sobre como [criar um grupo de segurança de rede e regras de ACL aqui](tutorial-virtual-network.md#secure-network-traffic).

Para aplicativos Web altamente disponíveis, você deve posicionar suas VMs por trás de um Azure Load Balancer. O balanceador de carga distribui o tráfego para VMs, com um grupo de segurança de rede que fornece filtragem de tráfego. Para obter mais informações, consulte [como balancear a carga de máquinas virtuais Linux no Azure para criar um aplicativo altamente disponível](tutorial-load-balancer.md).

## <a name="next-steps"></a>Passos seguintes
Neste exemplo, você criou uma regra simples para permitir o tráfego HTTP. Você pode encontrar informações sobre como criar ambientes mais detalhados nos seguintes artigos:

* [Descrição geral do Azure Resource Manager](../../azure-resource-manager/management/overview.md)
* [O que é um Grupo de Segurança de Rede (NSG)? (What is a Network Security Group (NSG)?)](../../virtual-network/security-overview.md)
