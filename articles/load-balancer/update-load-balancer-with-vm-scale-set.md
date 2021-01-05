---
title: Atualizar ou eliminar o balanceador de carga azure existente utilizado pelo Conjunto de Escala de Máquina Virtual
titleSuffix: Update or delete existing Azure Load Balancer used by Virtual Machine Scale Set
description: Com este artigo de como fazer, começa com o Azure Standard Load Balancer e os Conjuntos de Balanças de Máquinas Virtuais.
services: load-balancer
documentationcenter: na
author: irenehua
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/30/2020
ms.author: irenehua
ms.openlocfilehash: f8f664375e53a1cef28b0c7b95207770434f67fa
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/05/2021
ms.locfileid: "97893283"
---
# <a name="how-to-updatedelete-azure-load-balancer-used-by-virtual-machine-scale-sets"></a>Como atualizar/eliminar o balançador de carga Azure utilizado por conjuntos de balanças de máquinas virtuais

## <a name="how-to-set-up-azure-load-balancer-for-scaling-out-virtual-machine-scale-sets"></a>Como configurar o balançador de carga Azure para escalonar conjuntos de balanças de máquinas virtuais
  * Certifique-se de que o Balanceador de Carga tem [a piscina NAT](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-pool?view=azure-cli-latest) de entrada configurada e que o Conjunto de Balanças de Máquina Virtual é colocado no pool de backend do Balanceador de Carga. O Azure Load Balancer criará automaticamente novas regras NAT de entrada na piscina NAT de entrada quando forem adicionadas novas instâncias da Máquina Virtual ao Conjunto de Escala de Máquina Virtual. 
  * Para verificar se a piscina NAT de entrada está devidamente configurada, 
  1. Inicie sessão no portal do Azure em https://portal.azure.com.
  
  1. Selecione **todos os recursos** no menu esquerdo e, em seguida, selecione **MyLoadBalancer** na lista de recursos.
  
  1. Em **Definições**, selecione **Inbound NAT Rules**.
Se vir no painel direito, uma lista de regras criadas para cada instância no Conjunto de Escala de Máquina Virtual, os parabéns que todos você está disposto a ir para escalar a qualquer momento.

## <a name="how-to-add-inbound-nat-rules"></a>Como adicionar regras NAT de entrada? 
  * A regra NAT de entrada individual não pode ser adicionada. No entanto, pode adicionar um conjunto de regras NAT de entrada com gama de porta frontal definida e porta de backend para todos os casos no Conjunto de Escala de Máquina Virtual.
  * Para adicionar um conjunto inteiro de regras NAT de entrada para os conjuntos de escala de máquina virtual, você precisa primeiro criar uma piscina NAT de entrada no Balancer de carga e, em seguida, fazer referência ao pool NAT de entrada a partir do perfil de rede do Conjunto de Escala de Máquina Virtual. Um exemplo completo utilizando o CLI é mostrado abaixo.
  * A nova piscina NAT de entrada não deve ter uma gama portuária de frontend sobreposta com piscinas NAT de entrada existentes. Para visualizar as piscinas NAT existentes, pode utilizar este [comando CLI](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-pool?view=azure-cli-latest#az_network_lb_inbound_nat_pool_list)
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
## <a name="how-to-update-inbound-nat-rules"></a>Como atualizar as regras de ENTRADA? 
  * A regra NAT de entrada individual não pode ser atualizada. No entanto, pode atualizar um conjunto de regras NAT de entrada com a gama de portas frontend definida e porta de backend para todos os casos no Conjunto de Escala de Máquina Virtual.
  * Para atualizar um conjunto de regras NAT de entrada para os conjuntos de escala de máquina virtual, é necessário atualizar o pool NAT de entrada no Balancer de Carga. 
```azurecli-interactive
az network lb inbound-nat-pool update 
        -g MyResourceGroup 
        --lb-name MyLb 
        -n MyNatPool
        --protocol Tcp 
        --backend-port 8080
```

## <a name="how-to-delete-inbound-nat-rules"></a>Como eliminar as regras NAT de entrada? 
* A regra NAT de entrada individual não pode ser eliminada. No entanto, pode eliminar todo o conjunto de regras NAT de entrada.
* Para eliminar todo o conjunto de regras NAT de entrada utilizadas pelo Conjunto de Escala, é necessário remover primeiro a piscina NAT do conjunto de escalas. Um exemplo completo utilizando o CLI é mostrado abaixo:
```azurecli-interactive
  az vmss update
     --resource-group MyResourceGroup
     --name MyVMSS
   az vmss update-instances 
     --instance-ids "*" 
     --resource-group MyResourceGroup
     --name MyVMSS
  az network lb inbound-nat-pool delete
     --resource-group MyResourceGroup
     -–lb-name MyLoadBalancer
     --name MyNatPool
```

## <a name="how-to-add-multiple-ip-configurations"></a>Como adicionar várias configurações IP:
1. Selecione **todos os recursos** no menu esquerdo e, em seguida, selecione **MyLoadBalancer** na lista de recursos.
   
1. Em **Definições**, selecione **Configurações IP frontend** e, em seguida, selecione **Adicionar**.
   
1. Na página de **endereço IP frontend** Add, digite os valores e selecione **OK**

1. Siga [o Passo 5](https://docs.microsoft.com/azure/load-balancer/load-balancer-multiple-ip#step-5-configure-the-health-probe) e [o Passo 6](https://docs.microsoft.com/azure/load-balancer/load-balancer-multiple-ip#step-5-configure-the-health-probe) neste tutorial se forem necessárias novas regras de equilíbrio de carga

1. Crie um novo conjunto de regras NAT de entrada utilizando as configurações IP de frontend recém-criadas, se necessário. O exemplo pode ser encontrado aqui na [secção anterior].

## <a name="how-to-delete-frontend-ip-configuration-used-by-virtual-machine-scale-set"></a>Como eliminar a configuração IP frontend utilizada pelo Conjunto de Escala de Máquina Virtual: 
 1. Para eliminar a Configuração IP frontend em uso pelo Conjunto de Escala, é necessário eliminar primeiro a piscina NAT de entrada (conjunto de regras NAT de entrada) que faz referência à configuração IP frontend. As instruções sobre como eliminar as regras de entrada podem ser encontradas na secção anterior.
 1. Elimine a regra de equilíbrio de carga referente à configuração IP frontend. 
 1. Elimine a configuração IP frontend.
 

## <a name="how-to-delete-azure-load-balancer-used-by-virtual-machine-scale-set"></a>Como eliminar o balançador de carga Azure utilizado pelo conjunto de balanças de máquinas virtuais: 
 1. Para eliminar a Configuração IP frontend em uso pelo Conjunto de Escala, é necessário eliminar primeiro a piscina NAT de entrada (conjunto de regras NAT de entrada) que faz referência à configuração IP frontend. As instruções sobre como eliminar as regras de entrada podem ser encontradas na secção anterior.
 
 1. Elimine a regra de equilíbrio de carga referindo a piscina de backend contendo o conjunto de balança de máquina virtual.
 
 1. Remova a referência loadBalancerBackendAddressPool do perfil de rede do Conjunto de Balanças de Máquina Virtual. Um exemplo completo utilizando o CLI é mostrado abaixo:
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
Por fim, elimine o recurso balanceador de carga.
 
## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o Azure Load Balancer e o Virtual Machine Scale set, leia mais sobre os conceitos.

> [Balançador de carga Azure com conjuntos de escala de máquina virtual Azure](load-balancer-standard-virtual-machine-scale-sets.md)
