---
title: Exemplos da CLI do Azure – Instalar aplicações | Microsoft Docs
description: Exemplos da CLI do Azure
services: virtual-machine-scale-sets
documentationcenter: ''
author: zr-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2018
ms.author: zarhoads
ms.custom: mvc
ms.openlocfilehash: 2469eef981246da9bfd9132ea0164395078fd164
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54468459"
---
# <a name="install-applications-into-a-virtual-machine-scale-set-with-the-azure-cli"></a>Instalar aplicações num conjunto de dimensionamento de máquinas virtuais com a CLI do Azure
Este script cria um conjunto de dimensionamento de máquinas virtuais em Ubuntu e utiliza a Extensão de Script Personalizado para instalar uma aplicação Web básica. Após executar o script, pode aceder à aplicação Web através de um browser.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo
[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine-scale-sets/install-apps/install-apps.sh "Install apps into a scale set")]

## <a name="clean-up-deployment"></a>Limpar a implementação
Execute o seguinte comando para remover o grupo de recursos, o conjunto de dimensionamento e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação do script
Este script utiliza os seguintes comandos para criar um grupo de recursos, um conjunto de dimensionamento de máquinas virtuais e todos os recursos relacionados. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az group create](/cli/azure/ad/group) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az vmss create](/cli/azure/vmss#az_vmss_create) | Cria o conjunto de dimensionamento de máquinas virtuais e liga-o à rede virtual, à sub-rede e ao grupo de segurança de rede. É também criado um balanceador de carga para distribuir o tráfego para instâncias de VM individuais. Este comando também especifica a imagem da VM a ser utilizada e as credenciais administrativas.  |
| [az vmss extension set](/cli/azure/vmss/extension#az_vmss_extension_set) | Instala a Extensão do Script Personalizado do Azure para executar um script que prepara os discos de dados em cada instância da VM. |
| [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create) | Cria uma regra de balanceador de carga para distribuir tráfego na porta TCP 80 pelas instâncias de VM no conjunto de dimensionamento. |
| [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) | Obtém informações sobre o endereço IP público atribuído, utilizado pelo balanceador de carga. |
| [az group delete](/cli/azure/ad/group#delete) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Pode encontrar exemplos adicionais de scripts da CLI do Azure para conjuntos de dimensionamento de máquinas virtuais na [documentação do conjunto de dimensionamento de máquinas virtuais do Azure](../cli-samples.md).
