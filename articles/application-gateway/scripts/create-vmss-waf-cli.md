---
title: Exemplo de Script da CLI do Azure – Restringir o tráfego da Web | Microsoft Docs
description: Exemplo de Script da CLI do Azure – Criar um gateway de aplicação com uma firewall de aplicações Web e um conjunto de dimensionamento de máquinas virtuais que utiliza regras OWASP para restringir o tráfego.
services: application-gateway
documentationcenter: networking
author: vhorne
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/29/2018
ms.author: victorh
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: e9201f41c9552b6a60f9ccd8eacda60ac46f89eb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99591639"
---
# <a name="restrict-web-traffic-using-the-azure-cli"></a>Restringir o tráfego da Web com a CLI do Azure

Este script cria um gateway de aplicação com uma firewall de aplicações Web que utiliza um conjunto de dimensionamento de máquinas virtuais para servidores back-end. A firewall de aplicações Web restringe o tráfego da Web com base nas regras OWASP. Depois de executar o script, pode testar o gateway de aplicação com o respetivo endereço IP público.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/application-gateway/create-vmss/create-vmss-waf.sh "Create application gateway")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Execute o seguinte comando para remover o grupo de recursos, o gateway de aplicação e todos os recursos relacionados.

```azurecli-interactive 
az group delete --name myResourceGroupAG --yes
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar a implementação. Cada item na tabela liga a documentação específica do comando.

| Comando | Notas |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) | Cria uma rede virtual. |
| [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) | Cria uma sub-rede numa rede virtual. |
| [az network public-ip create](/cli/azure/network/public-ip) | Cria o endereço IP público do gateway de aplicação. |
| [az network application-gateway create](/cli/azure/network/application-gateway) | Cria um gateway de aplicação. |
| [az vmss create](/cli/azure/vmss#az-vmss-create) | Cria um conjunto de dimensionamento de máquinas virtuais. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Cria uma conta de armazenamento. |
| [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) | Cria uma conta de armazenamento. |
| [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) | Obtém o endereço IP público do gateway de aplicação. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure/overview).

Pode encontrar exemplos adicionais de script da CLI do gateway de aplicação na [Documentação do gateway de aplicação do Azure](../cli-samples.md).
