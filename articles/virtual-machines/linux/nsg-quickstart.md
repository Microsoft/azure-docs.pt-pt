---
title: Abrir portas para uma VM do Linux com a CLI do Azure | Documentos da Microsoft
description: Saiba como abrir uma porta / criar um ponto final para a sua VM do Linux com o modelo de implementação do Gestor de recursos do Azure e a CLI do Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: eef9842b-495a-46cf-99a6-74e49807e74e
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: cynthn
ms.openlocfilehash: a12952c73863d10c4fffd013ab594a83ab1b6433
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60771537"
---
# <a name="open-ports-and-endpoints-to-a-linux-vm-with-the-azure-cli"></a>Abrir portas e pontos finais para uma VM do Linux com a CLI do Azure

Abrir uma porta ou criar um ponto de extremidade, a uma máquina virtual (VM) no Azure através da criação de um filtro de rede numa sub-rede ou a interface de rede VM. Estes filtros que controlam o tráfego de entrada e saído, coloca num grupo de segurança de rede anexado ao recurso que recebe o tráfego. Vamos usar um exemplo comum de tráfego da web na porta 80. Este artigo mostra como abrir uma porta para uma VM com a CLI do Azure. 


Para criar um grupo de segurança de rede e regras que tem a versão mais recente [CLI do Azure](/cli/azure/install-az-cli2) instalado e registado à utilização conta do Azure [início de sessão az](/cli/azure/reference-index).

Nos exemplos a seguir, substitua os nomes de parâmetros de exemplo pelos seus próprios valores. Os nomes de parâmetros de exemplo incluem *myResourceGroup*, *myNetworkSecurityGroup*, e *myVnet*.


## <a name="quickly-open-a-port-for-a-vm"></a>Rapidamente, abrir uma porta para uma VM
Se precisar de rapidamente abrir uma porta para uma VM num cenário de programador/teste, pode utilizar o [az vm open-port](/cli/azure/vm) comando. Este comando cria um grupo de segurança de rede, adiciona uma regra e aplicando-a uma VM ou a sub-rede. O exemplo seguinte abre a porta *80* na VM com o nome *myVM* no grupo de recursos com o nome *myResourceGroup*.

```azure-cli
az vm open-port --resource-group myResourceGroup --name myVM --port 80
```

Para obter mais controlo sobre as regras, como na definição de um intervalo de endereços IP de origem, continue com os passos adicionais neste artigo.


## <a name="create-a-network-security-group-and-rules"></a>Criar um grupo de segurança de rede e regras
Criar o grupo de segurança de rede com [nsg de rede de az criar](/cli/azure/network/nsg). O exemplo seguinte cria um grupo de segurança de rede com o nome *myNetworkSecurityGroup* no *eastus* localização:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNetworkSecurityGroup
```

Adicionar uma regra com [criar regra de nsg de rede de az](/cli/azure/network/nsg/rule) para permitir o tráfego HTTP para o seu servidor Web (ou ajustar para o seu cenário, como conectividade de acesso ou a base de dados SSH). O exemplo seguinte cria uma regra com o nome *myNetworkSecurityGroupRule* para permitir o tráfego TCP na porta 80:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --protocol tcp \
    --priority 1000 \
    --destination-port-range 80
```


## <a name="apply-network-security-group-to-vm"></a>Aplicar o grupo de segurança de rede VM
Associar o grupo de segurança de rede com interface de rede da VM (NIC) [atualização de nic de rede de az](/cli/azure/network/nic). O exemplo seguinte associa um NIC existente com o nome *myNic* com o grupo de segurança de rede com o nome *myNetworkSecurityGroup*:

```azurecli
az network nic update \
    --resource-group myResourceGroup \
    --name myNic \
    --network-security-group myNetworkSecurityGroup
```

Em alternativa, pode associar o seu grupo de segurança de rede com uma sub-rede de rede virtual com [atualização de sub-rede de vnet de rede de az](/cli/azure/network/vnet/subnet) em vez de apenas para a interface de rede numa única VM. O exemplo seguinte associa uma sub-rede existente com o nome *mySubnet* no *myVnet* rede virtual com o grupo de segurança de rede com o nome *myNetworkSecurityGroup*:

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```

## <a name="more-information-on-network-security-groups"></a>Obter mais informações sobre grupos de segurança de rede
Os comandos rápidos aqui permitem-lhe começar a trabalhar com o tráfego que flui à sua VM. Grupos de segurança de rede fornecem muitos excelentes recursos e granularidade para controlar o acesso aos seus recursos. Pode ler mais sobre [criar um grupo de segurança de rede e a ACL de regras aqui](tutorial-virtual-network.md#secure-network-traffic).

Para aplicações web de elevada disponibilidade, deve colocar as suas VMs por trás de um balanceador de carga do Azure. O Balanceador de carga distribui o tráfego para VMs, com um grupo de segurança de rede que fornece a filtragem de tráfego. Para obter mais informações, consulte [como a carga de balanceamento de máquinas virtuais do Linux no Azure para criar uma aplicação de elevada disponibilidade](tutorial-load-balancer.md).

## <a name="next-steps"></a>Passos Seguintes
Neste exemplo, criou uma regra simple para permitir tráfego HTTP. Pode encontrar informações sobre como criar ambientes mais detalhadas nos seguintes artigos:

* [Descrição geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)
* [O que é um Grupo de Segurança de Rede (NSG)? (What is a Network Security Group (NSG)?)](../../virtual-network/security-overview.md)
