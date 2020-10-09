---
title: Exemplo do Script da CLI do Azure - Instalar o IIS
description: Exemplo do Script da CLI do Azure - Instalar o IIS
services: virtual-machines-Windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-Windows
ms.workload: infrastructure
ms.date: 02/28/2017
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 27235c131f6d87856ab0c21b01c578056f941503
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87499216"
---
# <a name="create-a-virtual-machine-with-the-azure-cli-with-a-custom-script-to-install-iis"></a>Crie uma máquina virtual com o Azure CLI com um script personalizado para instalar o IIS

Este script cria uma Máquina Virtual do Azure com o Windows Server 2016 e utiliza a Extensão de Script Personalizado da Máquina Virtual do Azure para instalar o IIS. Depois de executar o script, pode aceder ao site do IIS predefinido no endereço IP público da máquina virtual.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-windows-iis/create-vm-windows-iis.sh "Quick Create VM")]

## <a name="clean-up-deployment"></a>Limpar a implementação

Execute o seguinte comando para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar um grupo de recursos, uma máquina virtual e todos os recursos relacionados. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az group create](/cli/azure/group) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az vm create](/cli/azure/vm) | Cria a máquina virtual e liga-a à placa de rede, à rede virtual, à sub-rede e ao grupo de segurança de rede. Este comando também especifica a imagem de máquina virtual a ser utilizada e as credenciais administrativas.  |
| [az vm open-port](/cli/azure/network/nsg/rule) | Cria uma regra do grupo de segurança de rede para permitir tráfego de entrada. Neste exemplo, a porta 80 está aberta para o tráfego HTTP. |
| [azure vm extension set](/cli/azure/vm/extension) | Adiciona e executa uma extensão de máquina virtual numa VM. Neste exemplo, é utilizada a extensão de script personalizada para instalar o IIS.|
| [az group delete](/cli/azure/vm/extension) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Pode ver exemplos do script da CLI da máquina virtual adicionais na [Documentação da VM Windows do Azure](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
