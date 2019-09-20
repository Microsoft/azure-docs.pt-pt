---
title: Exemplo de CLI do Azure – criar uma zona DNS e um registro para um nome de domínio
description: Este exemplo de script da CLI do Azure mostra como criar e registar uma zona DNS para um nome de domínio
services: dns
author: vhorne
ms.service: dns
ms.topic: sample
ms.date: 09/20/2019
ms.author: victorh
ms.openlocfilehash: 691eeebf61ffd4ea7bee6b5ac6c60cafb076e11d
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71156227"
---
# <a name="azure-cli-script-example-create-a-dns-zone-and-record"></a>Exemplo de script CLI do Azure: Criar uma zona e registo DNS

Este exemplo de script da CLI do Azure cria e regista uma zona DNS para um nome de domínio. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/dns/create-dns-zone-and-record/create-dns-zone-and-record.sh "Create DNS zone and record")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Execute o seguinte comando para remover o grupo de recursos, a zona DNS e todos os recursos relacionados.

```azurecli
az group delete -n myResourceGroup
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar um grupo de recursos, uma máquina virtual, um conjunto de disponibilidade, um balanceador de carga e todos os recursos relacionados. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az network dns zone create](/cli/azure/network/dns/zone#az-network-dns-zone-create) | Cria uma zona DNS do Azure. |
| [az network dns record-set a add-record](/cli/azure/network/dns/record-set) | Adiciona um registo *A* a uma zona DNS. |
| [az network dns record-set list](/cli/azure/network/dns/record-set) | Lista todos os conjuntos de registos *A* numa zona DNS. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

