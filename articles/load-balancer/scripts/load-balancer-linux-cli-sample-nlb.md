---
title: Exemplo de CLI – balanceamento de carga de tráfego para VMs para elevada disponibilidade – Azure | Microsoft Docs
description: Este exemplo de script de CLI do Azure mostra como efetuar o balanceamento de carga de tráfego para VMs para elevada disponibilidade
services: load-balancer
documentationcenter: load-balancer
author: KumudD
manager: jeconnoc
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: kumud
ms.openlocfilehash: 039667eae1dc605706100b7eabf0cd144709fd7f
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/02/2018
ms.locfileid: "39443839"
---
# <a name="azure-cli-script-example-load-balance-traffic-to-vms-for-high-availability"></a>Exemplo de script de CLI do Azure: balanceamento de carga de tráfego para VMs para elevada disponibilidade

Este exemplo de script de CLI do Azure cria tudo o que é preciso para executar várias máquinas virtuais com Ubuntu configuradas numa configuração de elevada disponibilidade e com balanceamento de carga. Depois de executar o script, terá três máquinas virtuais, associadas a um Conjunto de Disponibilidade do Azure e acessíveis através de um Balanceador de Carga do Azure. 

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
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#az-network-vnet-create) | Cria uma rede e sub-rede virtual do Azure. |
| [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip#az-network-public-ip-create) | Cria um endereço IP público com um endereço IP estático e um nome DNS associado. |
| [az network lb create](https://docs.microsoft.com/cli/azure/network/lb#az-network-lb-create) | Cria um balanceador de carga do Azure. |
| [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe#az-network-lb-probe-create) | Cria uma sonda do balanceador de carga. Uma sonda do balanceador de carga serve para monitorizar cada VM no conjunto de balanceadores de carga. Se qualquer VM ficar inacessível, o tráfego não é encaminhado para a VM. |
| [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule#az-network-lb-rule-create) | Cria uma regra de balanceador de carga. Neste exemplo, é criada uma regra para a porta 80. À medida que o tráfego HTTP chega ao balanceador de carga, é encaminhado para a porta 80 de uma das VMs no conjunto LB. |
| [az network lb inbound-nat-rule create](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-rule#az-network-lb-inbound-nat-rule-create) | Cria uma regra de Tradução de Endereços de Rede (NAT) do balanceador de carga.  As regras NAT mapeiam uma porta do balanceador de carga para uma porta numa VM. Neste exemplo, é criada uma regra NAT para o tráfego SSH para cada VM no conjunto de balanceadores de carga.  |
| [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg#az-network-nsg-create) | Cria um grupo de segurança de rede (NSG), que é um limite de segurança entre a Internet e a máquina virtual. |
| [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule#az-network-nsg-rule-create) | Cria uma regra NSG para permitir tráfego de entrada. Neste exemplo, a porta 22 está aberta para o tráfego SSH. |
| [az network nic create](https://docs.microsoft.com/cli/azure/network/nic#az-network-nic-create) | Cria a placa da rede virtual e liga-a à rede virtual, à sub-rede e ao NSG. |
| [az vm availability-set create](https://docs.microsoft.com/cli/azure/network/lb/rule#az-network-lb-rule-create) | Cria um conjunto de disponibilidade. Os conjuntos de disponibilidade garantem o tempo de atividade das aplicações ao propagar as máquinas virtuais nos recursos físicos de modo a que, se ocorrer uma falha, o conjunto completo não seja afetado. |
| [az vm create](/cli/azure/vm#az-vm-create) | Cria a máquina virtual e liga-a à placa de rede, à rede virtual, à sub-rede e ao NSG. Este comando também especifica a imagem de máquina virtual a ser utilizada e as credenciais administrativas.  |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Encontrará exemplos adicionais do script de CLI de Redes do Azure na [Documentação das Redes do Azure](../cli-samples.md).