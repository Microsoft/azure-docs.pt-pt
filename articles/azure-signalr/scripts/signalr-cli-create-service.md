---
title: CLI do Azure exemplo do Script - criar um serviço SignalR
description: Exemplo de Script da CLI do Azure – Criar um Serviço SignalR
author: sffamily
ms.service: signalr
ms.devlang: azurecli
ms.topic: sample
ms.date: 04/20/2018
ms.author: zhshang
ms.custom: mvc
ms.openlocfilehash: 122b175d026101dd4b15be4458e67ddd8a0d92d6
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57533192"
---
# <a name="create-a-signalr-service"></a>Criar um Serviço SignalR 

Este script de exemplo cria um novo recurso do Serviço Azure SignalR num novo grupo de recursos com um nome aleatório.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este artigo requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script de exemplo

Este script utiliza a extensão *signalr* para a CLI do Azure. Execute o seguinte comando para instalar a extensão *signalr* para a CLI do Azure antes de utilizar este script de exemplo:

```azurecli-interactive
az extension add -n signalr
```

Este script cria um novo recurso do Serviço SignalR e um novo grupo de recursos. 

[!code-azurecli-interactive[main](../../../cli_scripts/azure-signalr/create-signalr-service-and-group/create-signalr-service-and-group.sh "Creates a new Azure SignalR Service resource and resource group")]

Anote o nome real gerado para o novo grupo de recursos. Irá utilizar esse nome de grupo de recursos quando quiser eliminar todos os recursos do grupo.

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação do script

Cada comando na tabela liga à documentação específica do comando. Este script utiliza os seguintes comandos:

| Comando | Notas |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az signalr create](/cli/azure/ext/signalr/signalr#ext-signalr-az-signalr-create) | Cria um recurso do Serviço Azure SignalR. |
| [az signalr key list](/cli/azure/ext/signalr/signalr/key#ext-signalr-az-signalr-key-list) | Lista as chaves, que serão utilizadas pela sua aplicação quando efetuar atualizações push de conteúdo em tempo real com o SignalR. |


## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Pode ver exemplos de script da CLI do Serviço Azure SignalR adicionais na [documentação do Serviço Azure SignalR](../signalr-reference-cli.md).
