---
title: Amostra de script Azure CLI - Implemente a pilha de lâmpadas num conjunto de balança de máquina virtual Load-Balanced
description: Utilize uma extensão de script personalizado para implementar a Pilha LAMP num conjunto de dimensionamento de máquinas virtuais com balanceamento de carga no Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
ms.author: cynthn
tags: azure-service-management
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.custom: mvc, devx-track-azurecli
ms.date: 04/05/2017
ms.openlocfilehash: e71b2f940c637a8b1375be71f9b6ca95fd76628e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87501870"
---
# <a name="deploy-the-lamp-stack-in-a-load-balanced-virtual-machine-scale-set"></a>Implementar a pilha LAMP num conjunto de dimensionamento de máquinas virtuais com balanceamento de carga

Este exemplo cria um conjunto de dimensionamento de máquinas virtuais e aplica uma extensão que executa um script personalizado para implementar a pilha LAMP em cada máquina virtual no conjunto de dimensionamento.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-scaleset-php-ansible/build-stack.sh "Create virtual machine scale set with LAMP stack")]

## <a name="connect"></a>Ligar

Utilize este código para ver como ligar as VMs e o conjunto de dimensionamento.

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-scaleset-php-ansible/how-to-access.sh "Access the virtual machine scale set")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Execute o seguinte comando para remover o grupo de recursos, o conjunto de dimensionamento, as VMs e todos os recursos relacionados.

```azurecli-interactive 
az group delete -n myResourceGroup
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar um grupo de recursos, uma máquina virtual, um conjunto de disponibilidade, um balanceador de carga e todos os recursos relacionados. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az group create](/cli/azure/group) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az vmss create](/cli/azure/vmss) | Cria um conjunto de dimensionamento de máquinas virtuais |
| [az network lb rule create](/cli/azure/network/lb/rule) | Adiciona um ponto final com balanceamento de carga |
| [az vmss extension set](/cli/azure/vmss/extension) | Cria a extensão que executa o script personalizado na implementação de uma VM |
| [az vmss update-instances](/cli/azure/vmss) | Executa o script personalizado nas instâncias da VM implementadas antes da extensão ser aplicada ao conjunto de dimensionamento. |
| [az vmss scale](/cli/azure/vmss) | Aumenta verticalmente o conjunto de dimensionamento ao adicionar mais instâncias da VM. O script personalizado é executado quando forem implementadas. |
| [az network public-ip list](/cli/azure/network/public-ip) | Obtém os endereços IP das VMs criadas pelo exemplo. |
| [az network lb show](/cli/azure/network/lb) | Obtém as portas de front-end e back-end utilizadas pelo balanceador de carga. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Pode ver exemplos do script da CLI da máquina virtual adicionais na [Documentação da VM Linux do Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
