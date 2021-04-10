---
title: Amostras Azure CLI - Instalar aplicativos
description: Este script cria um conjunto de dimensionamento de máquinas virtuais em Ubuntu e utiliza a Extensão de Script Personalizado para instalar uma aplicação Web básica.
author: mimckitt
ms.author: mimckitt
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.date: 03/27/2018
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 40a5b4005e55e507ffff5bf8e7dbf740a1e50cf3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105932289"
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
| [az vmss create](/cli/azure/vmss) | Cria o conjunto de dimensionamento de máquinas virtuais e liga-o à rede virtual, à sub-rede e ao grupo de segurança de rede. É também criado um balanceador de carga para distribuir o tráfego para instâncias de VM individuais. Este comando também especifica a imagem da VM a ser utilizada e as credenciais administrativas.  |
| [az vmss extension set](/cli/azure/vmss/extension) | Instala a Extensão do Script Personalizado do Azure para executar um script que prepara os discos de dados em cada instância da VM. |
| [az network lb rule create](/cli/azure/network/lb/rule) | Cria uma regra de balanceador de carga para distribuir tráfego na porta TCP 80 pelas instâncias de VM no conjunto de dimensionamento. |
| [az network public-ip show](/cli/azure/network/public-ip) | Obtém informações sobre o endereço IP público atribuído, utilizado pelo balanceador de carga. |
| [az group delete](/cli/azure/ad/group) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure/overview).
