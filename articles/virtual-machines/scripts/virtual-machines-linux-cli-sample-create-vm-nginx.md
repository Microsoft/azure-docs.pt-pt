---
title: Exemplo do Script da CLI do Azure – Criar uma VM do Linux com o NGINX
description: Exemplo do Script da CLI do Azure – Criar uma VM do Linux com o NGINX
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 00bbc74a78be93dd7167625fa98aa7f9500c1be2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87502074"
---
# <a name="create-a-vm-with-nginx"></a>Criar uma VM com o NGINX

Este script cria uma Máquina Virtual do Azure e utiliza a Extensão de Script Personalizado da Máquina Virtual do Azure para instalar o NGINX. Depois de executar o script, pode aceder a um site de demonstração no endereço IP público da máquina virtual.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-nginx/create-vm-nginx.sh "Quick Create VM")]

## <a name="custom-script-extension"></a>Extensão de Script Personalizado

A extensão de script personalizado copia este script para a máquina virtual. Em seguida, o script é executado para instalar e configurar um servidor Web NGINX.

```bash
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="clean-up-deployment"></a>Limpar a implementação

Execute o seguinte comando para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar um grupo de recursos, uma máquina virtual e todos os recursos relacionados. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az group create](/cli/azure/group) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az vm create](/cli/azure/vm) | Cria a máquina virtual. Este comando também especifica a imagem da máquina virtual a ser utilizada e as credenciais administrativas.  |
| [az vm open-port](/cli/azure/network/nsg/rule) | Cria uma regra do grupo de segurança de rede para permitir tráfego de entrada. Neste exemplo, a porta 80 está aberta para o tráfego HTTP. |
| [azure vm extension set](/cli/azure/vm/extension) | Adiciona e executa uma extensão de máquina virtual numa VM. Neste exemplo, é utilizada a extensão de script personalizado para instalar o NGINX.|
| [az group delete](/cli/azure/vm/extension) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Pode ver exemplos do script da CLI da máquina virtual adicionais na [Documentação da VM Linux do Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
