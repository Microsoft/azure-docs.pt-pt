---
title: Balanceindo vários websites - Azure CLI - Azure Load Balancer
description: Este exemplo de script de CLI do Azure mostra como fazer o balanceamento de carga de múltiplos sites para a mesma máquina virtual
documentationcenter: load-balancer
author: asudbring
ms.service: load-balancer
ms.devlang: azurecli
ms.topic: sample
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: allensu
ms.custom: devx-track-azurecli
ms.openlocfilehash: 77dfc42164419d86e174bc47297e2f596e757ffe
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790100"
---
# <a name="azure-cli-script-example-load-balance-multiple-websites"></a>Exemplo de script de CLI do Azure: balanceamento de carga de múltiplos sites

Este exemplo de script de CLI do Azure cria uma rede virtual com duas máquinas virtuais (VM) que são membros de um conjunto de disponibilidade. Um balanceador de carga direciona o tráfego de dois endereços IP separados para as duas VMs. Depois de executar o script, pode implementar o software de servidor Web para as VMs e alojar múltiplos sites, cada um com o seu próprio endereço IP.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo


[!code-azurecli-interactive[main](../../../cli_scripts/load-balancer/load-balance-multiple-web-sites-vm/load-balance-multiple-web-sites-vm.sh  "Load balance multiple web sites")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Execute o seguinte comando para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar um grupo de recursos, uma rede virtual, um balanceador de carga e todos os recursos relacionados. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) | Cria uma rede e sub-rede virtual do Azure. |
| [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) | Cria um endereço IP público com um endereço IP estático e um nome DNS associado. |
| [az network lb create](/cli/azure/network/lb#az_network_lb_create) | Cria um Balanceador de Carga do Azure. |
| [az network lb probe create](/cli/azure/network/lb/probe#az_network_lb_probe_create) | Cria uma sonda do balanceador de carga. Uma sonda do balanceador de carga é utilizada para monitorizar cada VM no conjunto de balanceadores de carga. Se qualquer VM ficar inacessível, o tráfego não é encaminhado para a VM. |
| [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create) | Cria uma regra de balanceador de carga. Neste exemplo, é criada uma regra para a porta 80. À medida que o tráfego HTTP chega ao balanceador de carga, é encaminhado para a porta 80 de uma das VMs no conjunto de balanceadores de carga. |
| [az network lb frontend-ip create](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_create) | Cria um endereço IP de front-end para o Balanceador de Carga. |
| [az network lb address-pool create](/cli/azure/network/lb/address-pool#az_network_lb_address_pool_create) | Cria um conjunto de endereços de back-end. |
| [az network nic create](/cli/azure/network/nic#az_network_nic_create) | Cria a placa da rede virtual e liga-a à rede virtual e à sub-rede. |
| [az vm availability-set create](/cli/azure/network/lb/rule#az_network_lb_rule_create) | Cria um conjunto de disponibilidade. Os conjuntos de disponibilidade garantem o tempo de atividade das aplicações ao propagar as máquinas virtuais nos recursos físicos de modo a que, se ocorrer uma falha, o conjunto completo não seja afetado. |
| [az network nic ip-config create](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_create) | Cria uma configuração IP. Tem de ter a funcionalidade Microsoft.Network/AllowMultipleIpConfigurationsPerNic ativada na sua subscrição. Apenas uma configuração pode ser designada como a configuração de IP principal por NIC ao utilizar o sinalizador make-primary. |
| [az vm create](/cli/azure/vm/availability-set#az_vm_availability_set_create) | Cria a máquina virtual e liga-a à placa de rede, à rede virtual, à sub-rede e ao NSG. Este comando também especifica a imagem de máquina virtual a ser utilizada e as credenciais administrativas.  |
| [az group delete](/cli/azure/vm/extension#az_vm_extension_set) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Encontrará exemplos adicionais de scripts de CLI de redes na [Documentação de Descrição Geral de Redes do Azure](../cli-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).