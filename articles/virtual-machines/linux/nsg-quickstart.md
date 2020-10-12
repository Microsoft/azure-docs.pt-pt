---
title: Abrir portas para um VM com Azure CLI
description: Aprenda a abrir uma porta / crie um ponto final para o seu VM utilizando o Azure CLI.
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 4c57ec2edf43bf0a710f3c15f30836f751c4d52b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87500168"
---
# <a name="open-ports-and-endpoints-to-a-vm-with-the-azure-cli"></a>Abrir portas e pontos finais para um VM com o Azure CLI

Abre uma porta, ou cria um ponto final, para uma máquina virtual (VM) em Azure, criando um filtro de rede numa interface de rede ou de rede VM. Coloca estes filtros, que controlam o tráfego de entrada e saída, num Grupo de Segurança de Rede ligado ao recurso que recebe o tráfego. Vamos usar um exemplo comum de tráfego web no porto 80. Este artigo mostra-lhe como abrir uma porta a um VM com o Azure CLI. 


Para criar um Grupo de Segurança de Rede e regras, precisa do mais recente [Azure CLI](/cli/azure/install-az-cli2) instalado e iniciado numa conta Azure utilizando [o login az](/cli/azure/reference-index).

Nos exemplos seguintes, substitua os nomes dos parâmetros de exemplo pelos seus próprios valores. Os nomes dos parâmetros incluem *myResourceGroup,* *myNetworkSecurityGroup*e *myVnet*.


## <a name="quickly-open-a-port-for-a-vm"></a>Abra rapidamente uma porta para um VM
Se precisar de abrir rapidamente uma porta para um VM num cenário de dev/teste, pode utilizar o comando [az vm open-port.](/cli/azure/vm) Este comando cria um Grupo de Segurança de Rede, adiciona uma regra e aplica-a a um VM ou sub-rede. O exemplo a seguir abre a porta *80* no VM nomeado *myVM* no grupo de recursos chamado *myResourceGroup*.

```azurecli
az vm open-port --resource-group myResourceGroup --name myVM --port 80
```

Para um maior controlo sobre as regras, como a definição de uma gama de endereços IP fonte, continue com os passos adicionais neste artigo.


## <a name="create-a-network-security-group-and-rules"></a>Criar um Grupo de Segurança de Rede e regras
Crie o grupo de segurança de rede com [a criação de nsg de rede az](/cli/azure/network/nsg). O exemplo a seguir cria um grupo de segurança de rede chamado *myNetworkSecurityGroup* na localização *leste:*

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNetworkSecurityGroup
```

Adicione uma regra com [a regra nsg de rede az criar](/cli/azure/network/nsg/rule) para permitir tráfego HTTP ao seu webserver (ou ajustar para o seu próprio cenário, como acesso SSH ou conectividade de base de dados). O exemplo a seguir cria uma regra chamada *myNetworkSecurityGroupRule* para permitir o tráfego TCP na porta 80:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --protocol tcp \
    --priority 1000 \
    --destination-port-range 80
```


## <a name="apply-network-security-group-to-vm"></a>Aplicar grupo de segurança de rede a VM
Associe o Grupo de Segurança da Rede à interface de rede (NIC) do seu VM com [a atualização do nic da rede Az](/cli/azure/network/nic). O exemplo a seguir associa um NIC existente chamado *myNic* com o Grupo de Segurança da Rede chamado *myNetworkSecurityGroup*:

```azurecli
az network nic update \
    --resource-group myResourceGroup \
    --name myNic \
    --network-security-group myNetworkSecurityGroup
```

Em alternativa, pode associar o seu Grupo de Segurança de Rede a uma sub-rede de rede virtual com [a atualização da sub-rede de rede Az em](/cli/azure/network/vnet/subnet) vez de apenas à interface de rede num único VM. O exemplo a seguir associa uma sub-rede existente chamada *mySubnet* na rede virtual *myVnet* com o Grupo de Segurança da Rede denominado *myNetworkSecurityGroup:*

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```

## <a name="more-information-on-network-security-groups"></a>Mais informações sobre grupos de segurança de rede
Os comandos rápidos aqui permitem-lhe levantar-se e funcionar com o tráfego a fluir para o seu VM. Os Grupos de Segurança de Rede fornecem muitas funcionalidades e granularidade para controlar o acesso aos seus recursos. Pode ler mais sobre [a criação de regras do Network Security Group e da ACL aqui.](tutorial-virtual-network.md#secure-network-traffic)

Para aplicações web altamente disponíveis, deve colocar os seus VMs atrás de um Balançador de Carga Azure. O equilibrador de carga distribui o tráfego para VMs, com um Grupo de Segurança de Rede que fornece filtragem de tráfego. Para obter mais informações, consulte [como carregar as máquinas virtuais Linux em Azure para criar uma aplicação altamente disponível.](tutorial-load-balancer.md)

## <a name="next-steps"></a>Passos seguintes
Neste exemplo, criou uma regra simples para permitir o tráfego HTTP. Pode encontrar informações sobre a criação de ambientes mais detalhados nos seguintes artigos:

* [Visão geral do Gestor de Recursos Azure](../../azure-resource-manager/management/overview.md)
* [O que é um Grupo de Segurança de Rede (NSG)? (What is a Network Security Group (NSG)?)](../../virtual-network/security-overview.md)
