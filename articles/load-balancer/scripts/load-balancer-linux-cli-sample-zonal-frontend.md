---
title: VMs de equilíbrio de carga dentro de uma zona - Azure CLI
description: Este exemplo de script de CLI do Azure mostra como realizar o balanceamento de carga de tráfego para VMs para uma zona de disponibilidade específica
services: load-balancer
documentationcenter: load-balancer
author: asudbring
manager: kumudD
ms.assetid: ''
ms.service: load-balancer
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 06/14/2018
ms.author: allensu
ms.custom: devx-track-azurecli
ms.openlocfilehash: a77927fe1703d061e87fc93071316ae6618e691f
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106056118"
---
# <a name="azure-cli-script-example-load-balance-traffic-to-vms-within-a-specific-availability-zone"></a>Exemplo de script do Azure CLI: Carregar o tráfego de equilíbrio para VMs dentro de uma zona de disponibilidade específica

Este exemplo de script de CLI do Azure cria tudo o que é preciso para executar várias máquinas virtuais com Ubuntu configuradas numa configuração de elevada disponibilidade e com balanceamento de carga numa zona de disponibilidade específica. Depois de executar o script, terá três máquinas virtuais numa única zona de disponibilidade numa região que é acessível através de um Balanceador de Carga do Azure Standard do Azure. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

```azurecli-interactive
  #!/bin/bash

  # Create a resource group.
   az group create \
    --name myResourceGroup \
    --location westeurope

  # Create a virtual network.
   az network vnet create \
    --resource-group myResourceGroup \
    --location westeurope \
    --name myVnet \
    --subnet-name mySubnet

  # Create a zonal Standard public IP address.
   az network public-ip create \
    --resource-group myResourceGroup \
    --name myPublicIP \
    --sku Standard
    --zone 1

  # Create an Azure Load Balancer.
   az network lb create \
    --resource-group myResourceGroup \
    --name myLoadBalancer \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --sku Standard

  # Creates an LB probe on port 80.
   az network lb probe create \
    --resource-group myResourceGroup \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80

  # Creates an LB rule for port 80.
   az network lb rule create \
    --resource-group myResourceGroup \
    --lb-name myLoadBalancer \
    --name myLoadBalancerRuleWeb \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe

  # Create three NAT rules for port 22.
   for i in `seq 1 3`; do
    az network lb inbound-nat-rule create \
     --resource-group myResourceGroup \
     --lb-name myLoadBalancer \
     --name myLoadBalancerRuleSSH$i \
     --protocol tcp \
     --frontend-port 422$i \
     --backend-port 22 \
     --frontend-ip-name myFrontEndPool
   done

  # Create a network security group
   az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup

  # Create a network security group rule for port 22.
   az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleSSH \
    --protocol tcp \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*'  \
    --destination-address-prefix '*' \
    --destination-port-range 22 \
    --access allow \
    --priority 1000

  # Create a network security group rule for port 80.
    az network nsg rule create \
     --resource-group myResourceGroup \
     --nsg-name myNetworkSecurityGroup \
     --name myNetworkSecurityGroupRuleHTTP \
     --protocol tcp \
     --direction inbound \
     --source-address-prefix '*' \
     --source-port-range '*' \
     --destination-address-prefix '*' \
     --destination-port-range 80 \
     --access allow \
     --priority 2000

  # Create three virtual network cards and associate with public IP address and NSG.
  for i in `seq 1 3`; do
   az network nic create \
     --resource-group myResourceGroup \
     --name myNic$i \
     --vnet-name myVnet \
     --subnet mySubnet \
     --network-security-group myNetworkSecurityGroup \
     --lb-name myLoadBalancer \
     --lb-address-pools myBackEndPool \
     --lb-inbound-nat-rules myLoadBalancerRuleSSH$i
  done

# Create three virtual machines, this creates SSH keys if not present.
for i in `seq 1 3`; do
  az vm create \
    --resource-group myResourceGroup \
    --name myVM$i \
    --zone 1 \
    --nics myNic$i \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --no-wait
done

```

## <a name="clean-up-deployment"></a>Limpar a implementação 

Execute o seguinte comando para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar um grupo de recursos, uma máquina virtual, um conjunto de disponibilidade, um balanceador de carga e todos os recursos relacionados. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) | Cria uma rede e sub-rede virtual do Azure. |
| [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) | Cria um endereço IP público com um endereço IP estático e um nome DNS associado. |
| [az network lb create](/cli/azure/network/lb#az-network-lb-create) | Cria um balanceador de carga do Azure. |
| [az network lb probe create](/cli/azure/network/lb/probe#az-network-lb-probe-create) | Cria uma sonda do balanceador de carga. Uma sonda do balanceador de carga é utilizada para monitorizar cada VM no conjunto de balanceadores de carga. Se qualquer VM ficar inacessível, o tráfego não é encaminhado para a VM. |
| [az network lb rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create) | Cria uma regra de balanceador de carga. Neste exemplo, é criada uma regra para a porta 80. À medida que o tráfego HTTP chega ao balanceador de carga, é encaminhado para a porta 80 de uma das VMs no conjunto LB. |
| [az network lb inbound-nat-rule create](/cli/azure/network/lb/inbound-nat-rule#az-network-lb-inbound-nat-rule-create) | Cria uma regra de Tradução de Endereços de Rede (NAT) do balanceador de carga.  As regras NAT mapeiam uma porta do balanceador de carga para uma porta numa VM. Neste exemplo, é criada uma regra NAT para o tráfego SSH para cada VM no conjunto de balanceadores de carga.  |
| [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create) | Cria um grupo de segurança de rede (NSG), que é um limite de segurança entre a Internet e a máquina virtual. |
| [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) | Cria uma regra NSG para permitir tráfego de entrada. Neste exemplo, a porta 22 está aberta para o tráfego SSH. |
| [az network nic create](/cli/azure/network/nic#az-network-nic-create) | Cria a placa da rede virtual e liga-a à rede virtual, à sub-rede e ao NSG. |
| [az vm create](/cli/azure/vm#az-vm-create) | Cria a máquina virtual e liga-a à placa de rede, à rede virtual, à sub-rede e ao NSG. Este comando também especifica a imagem de máquina virtual a ser utilizada e as credenciais administrativas.  |
| [az group delete](/cli/azure/vm/extension#az-vm-extension-set) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Encontrará exemplos adicionais do script de CLI de Redes do Azure na [Documentação das Redes do Azure](../cli-samples.md).