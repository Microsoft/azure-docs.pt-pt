---
title: CLI do Azure exemplo de script – balancear a carga de tráfego para VMs para alta disponibilidade | Microsoft Docs
description: CLI do Azure exemplo de script – balancear a carga de tráfego para VMs para alta disponibilidade
services: load-balancer
documentationcenter: load-balancer
author: asudbring
manager: KumudD
ms.assetid: ''
ms.service: load-balancer
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/07/2017
ms.author: allensu
ms.openlocfilehash: bf5f4bb6bd15fa1fd440e679913a370bbe30b663
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890315"
---
# <a name="load-balance-traffic-to-vms-for-high-availability"></a>Balancear a carga do tráfego para as VMs para alta disponibilidade

Este exemplo de script cria tudo o que é preciso para executar várias máquinas virtuais Ubuntu configuradas numa configuração de elevada disponibilidade e com balanceamento de carga. Depois de executar o script, terá três máquinas virtuais, associadas a um Conjunto de Disponibilidade do Azure e acessíveis através de um Balanceador de Carga do Azure. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-nlb/create-vm-nlb.sh "Quick Create VM")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Execute o seguinte comando para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar um grupo de recursos, uma máquina virtual, um conjunto de disponibilidade, um balanceador de carga e todos os recursos relacionados. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet) | Cria uma rede e sub-rede virtual do Azure. |
| [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip) | Cria um endereço IP público com um endereço IP estático e um nome DNS associado. |
| [az network lb create](https://docs.microsoft.com/cli/azure/network/lb) | Cria um balanceador de carga do Azure. |
| [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe) | Cria uma sonda do balanceador de carga. Uma sonda do balanceador de carga é utilizada para monitorizar cada VM no conjunto de balanceadores de carga. Se qualquer VM ficar inacessível, o tráfego não é encaminhado para a VM. |
| [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule) | Cria uma regra de balanceador de carga. Neste exemplo, é criada uma regra para a porta 80. À medida que o tráfego HTTP chega ao balanceador de carga, é encaminhado para a porta 80 de uma das VMs no conjunto LB. |
| [az network lb inbound-nat-rule create](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-rule) | Cria uma regra de Tradução de Endereços de Rede (NAT) do balanceador de carga.  As regras NAT mapeiam uma porta do balanceador de carga para uma porta numa VM. Neste exemplo, é criada uma regra NAT para o tráfego SSH para cada VM no conjunto de balanceadores de carga.  |
| [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg) | Cria um grupo de segurança de rede (NSG), que é um limite de segurança entre a Internet e a máquina virtual. |
| [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule) | Cria uma regra NSG para permitir tráfego de entrada. Neste exemplo, a porta 22 está aberta para o tráfego SSH. |
| [az network nic create](https://docs.microsoft.com/cli/azure/network/nic) | Cria a placa da rede virtual e liga-a à rede virtual, à sub-rede e ao NSG. |
| [az vm availability-set create](https://docs.microsoft.com/cli/azure/network/lb/rule) | Cria um conjunto de disponibilidade. Os conjuntos de disponibilidade garantem o tempo de atividade das aplicações ao propagar as máquinas virtuais nos recursos físicos de modo a que, se ocorrer uma falha, o conjunto completo não seja afetado. |
| [az vm create](/cli/azure/vm) | Cria a máquina virtual e liga-a à placa de rede, à rede virtual, à sub-rede e ao NSG. Este comando também especifica a imagem de máquina virtual a ser utilizada e as credenciais administrativas.  |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Encontrará exemplos adicionais do script de CLI de Redes do Azure na [Documentação das Redes do Azure](../cli-samples.md).
