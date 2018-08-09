---
title: Exemplo de Script da CLI do Azure – Restringir o tráfego da Web | Microsoft Docs
description: Exemplo de Script da CLI do Azure – Criar um gateway de aplicação com uma firewall de aplicações Web e um conjunto de dimensionamento de máquinas virtuais que utiliza regras OWASP para restringir o tráfego.
services: application-gateway
documentationcenter: networking
author: vhorne
manager: jpconnock
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/29/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 76c45c985f95979caf6a00a7d2e3a36d9ec3ff77
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/02/2018
ms.locfileid: "39422931"
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
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#az-net) | Cria uma rede virtual. |
| [az network vnet subnet create](https://docs.microsoft.com/cli/azure/network/vnet/subnet#az-network_vnet_subnet_create) | Cria uma sub-rede numa rede virtual. |
| [az network public-ip create](https://docs.microsoft.com/en-us/cli/azure/network/public-ip?view=azure-cli-latest) | Cria o endereço IP público do gateway de aplicação. |
| [az network application-gateway create](https://docs.microsoft.com/en-us/cli/azure/network/application-gateway?view=azure-cli-latest) | Cria um gateway de aplicação. |
| [az vmss create](https://docs.microsoft.com/cli/azure/vmss#az-vmss-create) | Cria um conjunto de dimensionamento de máquinas virtuais. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-create) | Cria uma conta de armazenamento. |
| [az monitor diagnostic-settings create](https://docs.microsoft.com/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) | Cria uma conta de armazenamento. |
| [az network public-ip show](https://docs.microsoft.com/cli/azure/network/public-ip#az-network_public_ip_show) | Obtém o endereço IP público do gateway de aplicação. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Pode encontrar exemplos adicionais de script da CLI do gateway de aplicação na [Documentação do gateway de aplicação do Azure](../cli-samples.md).
