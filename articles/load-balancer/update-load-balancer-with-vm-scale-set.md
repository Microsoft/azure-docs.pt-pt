---
title: Atualizar ou eliminar um balanceador de carga existente utilizado por conjuntos de escala de máquinas virtuais
titleSuffix: Update or delete an existing load balancer used by virtual machine scale sets
description: Com este artigo de como fazer, começa com o Azure Standard Load Balancer e conjuntos de balanças de máquinas virtuais.
services: load-balancer
documentationcenter: na
author: irenehua
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/29/2020
ms.author: irenehua
ms.openlocfilehash: 52f2a2ed301bf734ad605a2ee68a0ab672a97014
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102218728"
---
# <a name="update-or-delete-a-load-balancer-used-by-virtual-machine-scale-sets"></a>Atualizar ou eliminar um equilibrador de carga utilizado por conjuntos de escala de máquina virtual

Quando trabalha com conjuntos de balança de máquinas virtuais e uma instância do Azure Load Balancer, pode:

- Adicione, atualize e elimine as regras.
- Adicione configurações.
- Elimine o equilibrador de carga.

## <a name="set-up-a-load-balancer-for-scaling-out-virtual-machine-scale-sets"></a>Configurar um equilibrador de carga para escalonar conjuntos de escala de máquina virtual

Certifique-se de que a instância do Azure Load Balancer tem uma [piscina NAT de entrada](/cli/azure/network/lb/inbound-nat-pool) configurada e que o conjunto de balanças de máquina virtual é colocado no pool de backend do equilibrador de carga. O Load Balancer criará automaticamente novas regras NAT de entrada na piscina NAT de entrada quando forem adicionadas novas instâncias de máquina virtual ao conjunto de escala de máquina virtual.

Para verificar se a piscina NAT de entrada está devidamente configurada:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. No menu à esquerda, selecione **Todos os recursos.** Em seguida, selecione **MyLoadBalancer** da lista de recursos.
1. Em **Definições**, selecione **as regras DE ENTRADA**. No painel direito, se vires uma lista de regras criadas para cada instância no conjunto de escala de máquina virtual, estás pronto para escalar a qualquer momento.

## <a name="add-inbound-nat-rules"></a>Adicione as regras NAT de entrada

As regras individuais de entrada não podem ser adicionadas. Mas pode adicionar um conjunto de regras NAT de entrada com gama de porta frontal definida e porta traseira para todos os casos no conjunto de escala de máquina virtual.

Para adicionar um conjunto inteiro de regras NAT de entrada para os conjuntos de escala de máquina virtual, primeiro crie uma piscina NAT de entrada no equilibrador de carga. Em seguida, faça referência à piscina NAT de entrada a partir do perfil de rede do conjunto de balanças de máquinas virtuais. É mostrado um exemplo completo utilizando o CLI.

A nova piscina NAT de entrada não deve ter uma gama de portas frontal sobreposta com piscinas NAT existentes. Para visualizar as piscinas NAT existentes que são configurados, utilize este [comando CLI](/cli/azure/network/lb/inbound-nat-pool#az_network_lb_inbound_nat_pool_list):
  
```azurecli-interactive
  az network lb inbound-nat-pool create 
          -g MyResourceGroup 
          --lb-name MyLb
          -n MyNatPool 
          --protocol Tcp 
          --frontend-port-range-start 80 
          --frontend-port-range-end 89 
          --backend-port 80 
          --frontend-ip-name MyFrontendIp
  az vmss update 
          -g MyResourceGroup 
          -n myVMSS 
          --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools "{'id':'/subscriptions/mySubscriptionId/resourceGroups/MyResourceGroup/providers/Microsoft.Network/loadBalancers/MyLb/inboundNatPools/MyNatPool'}"
            
  az vmss update-instances
          -–instance-ids *
          --resource-group MyResourceGroup
          --name MyVMSS
```
## <a name="update-inbound-nat-rules"></a>Atualizar as regras da NAT de entrada

As regras individuais de entrada não podem ser atualizadas. Mas pode atualizar um conjunto de regras NAT de entrada com uma gama de porta frontal definida e uma porta traseira para todos os casos no conjunto de escala de máquina virtual.

Para atualizar um conjunto de regras NAT de entrada para conjuntos de escala de máquina virtual, atualize o pool NAT de entrada no balançador de carga.
    
```azurecli-interactive
az network lb inbound-nat-pool update 
        -g MyResourceGroup 
        --lb-name MyLb 
        -n MyNatPool
        --protocol Tcp 
        --backend-port 8080
```

## <a name="delete-inbound-nat-rules"></a>Eliminar as regras NAT de entrada

As regras individuais de entrada nat não podem ser eliminadas, mas pode eliminar todo o conjunto de regras NAT de entrada, eliminando o pool NAT de entrada.

Para eliminar a piscina NAT, retire-a primeiro do conjunto de escala. Um exemplo completo da utilização do CLI é mostrado aqui:

```azurecli-interactive
    az vmss update
       --resource-group MyResourceGroup
       --name MyVMSS
       --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools
     az vmss update-instances 
       --instance-ids "*" 
       --resource-group MyResourceGroup
       --name MyVMSS
    az network lb inbound-nat-pool delete
       --resource-group MyResourceGroup
       -–lb-name MyLoadBalancer
       --name MyNatPool
```

## <a name="add-multiple-ip-configurations"></a>Adicionar várias configurações IP

Para adicionar várias configurações IP:

1. No menu à esquerda, selecione **Todos os recursos.** Em seguida, selecione **MyLoadBalancer** da lista de recursos.
1. Em **Definições**, selecione **a configuração IP frontend**. Em seguida, selecione **Adicionar**.
1. Na página de **endereço IP frontend,** insira os valores e selecione **OK**.
1. Siga [o passo 5](./load-balancer-multiple-ip.md#step-5-configure-the-health-probe) e [o passo 6](./load-balancer-multiple-ip.md#step-5-configure-the-health-probe) neste tutorial se forem necessárias novas regras de equilíbrio de carga.
1. Crie um novo conjunto de regras NAT de entrada utilizando as configurações IP de extremidade frontal recém-criadas, se necessário. Um exemplo encontra-se na secção anterior.

## <a name="multiple-virtual-machine-scale-sets-behind-a-single-load-balancer"></a>Conjuntos de escala de máquinas virtuais múltiplos atrás de um único balanceador de carga

Crie o NAT Pool de entrada no Balanceador de Carga, faça referência ao pool NAT de entrada no perfil de rede de um Conjunto de Escala de Máquina Virtual e, finalmente, atualize as instâncias para que as alterações produzam efeitos. Repita os passos para todos os conjuntos de balanças de máquinas virtuais.

Certifique-se de criar piscinas NAT de entrada separadas com gamas de portas frontais não sobrepostas.
  
```azurecli-interactive
  az network lb inbound-nat-pool create 
          -g MyResourceGroup 
          --lb-name MyLb
          -n MyNatPool 
          --protocol Tcp 
          --frontend-port-range-start 80 
          --frontend-port-range-end 89 
          --backend-port 80 
          --frontend-ip-name MyFrontendIpConfig
  az vmss update 
          -g MyResourceGroup 
          -n myVMSS 
          --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools "{'id':'/subscriptions/mySubscriptionId/resourceGroups/MyResourceGroup/providers/Microsoft.Network/loadBalancers/MyLb/inboundNatPools/MyNatPool'}"
            
  az vmss update-instances
          -–instance-ids *
          --resource-group MyResourceGroup
          --name MyVMSS
          
  az network lb inbound-nat-pool create 
          -g MyResourceGroup 
          --lb-name MyLb
          -n MyNatPool2
          --protocol Tcp 
          --frontend-port-range-start 100 
          --frontend-port-range-end 109 
          --backend-port 80 
          --frontend-ip-name MyFrontendIpConfig2
  az vmss update 
          -g MyResourceGroup 
          -n myVMSS2 
          --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools "{'id':'/subscriptions/mySubscriptionId/resourceGroups/MyResourceGroup/providers/Microsoft.Network/loadBalancers/MyLb/inboundNatPools/MyNatPool2'}"
            
  az vmss update-instances
          -–instance-ids *
          --resource-group MyResourceGroup
          --name MyVMSS2
```

## <a name="delete-the-front-end-ip-configuration-used-by-the-virtual-machine-scale-set"></a>Elimine a configuração IP frontal utilizada pelo conjunto de escala de máquina virtual

Para eliminar a configuração IP frontal em uso pelo conjunto de escala:

 1. Primeiro, elimine a piscina NAT de entrada (o conjunto de regras NAT de entrada) que faz referência à configuração IP frontal. As instruções sobre como eliminar as regras de entrada encontram-se na secção anterior.
 1. Elimine a regra de equilíbrio de carga que faz referência à configuração IP frontal.
 1. Elimine a configuração IP frontal.

## <a name="delete-a-load-balancer-used-by-a-virtual-machine-scale-set"></a>Elimine um equilibrador de carga utilizado por um conjunto de escala de máquina virtual

Para eliminar a configuração IP frontal em uso pelo conjunto de escala:

 1. Primeiro, elimine a piscina NAT de entrada (o conjunto de regras NAT de entrada) que faz referência à configuração IP frontal. As instruções sobre como eliminar as regras de entrada encontram-se na secção anterior.
 1. Elimine a regra de equilíbrio de carga que faz referência à piscina traseira que contém o conjunto de balanças de máquina virtual.
 1. Retire a `loadBalancerBackendAddressPool` referência do perfil de rede do conjunto de balanças de máquinas virtuais.
 
 Um exemplo completo da utilização do CLI é mostrado aqui:

```azurecli-interactive
    az vmss update
       --resource-group MyResourceGroup
       --name MyVMSS
       --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools
    az vmss update-instances 
       --instance-ids "*" 
       --resource-group MyResourceGroup
       --name MyVMSS
```
Por último, elimine o recurso do balanceador de carga.
 
## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o Azure Load Balancer e os conjuntos de escala de máquinas virtuais, leia mais sobre os conceitos.

> [Balançador de carga Azure com conjuntos de escala de máquina virtual](load-balancer-standard-virtual-machine-scale-sets.md)
