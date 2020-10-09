---
title: Amostra de script Azure CLI - Exportação de uma Loja de Configuração de Aplicativos Azure
titleSuffix: Azure App Configuration
description: Use o script Azure CLI para exportar a configuração da Configuração da Aplicação Azure
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: lcozzens
ms.custom: devx-track-azurecli
ms.openlocfilehash: 991471f43c92b12073062db1e4e5fdb32fb4b0f6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87494796"
---
# <a name="export-from-an-azure-app-configuration-store"></a>Exportação de uma loja de configuração de aplicativos Azure

Este script de amostra exporta valores-chave de uma loja de configuração de aplicações Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este artigo requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script de exemplo

```azurecli-interactive
#!/bin/bash

# Export all key-values
az appconfig kv export --name myTestAppConfigStore --file ~/Export.json
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para exportar a partir de uma loja de Configuração de Aplicações. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [exportação az appconfig kv](/cli/azure/appconfig/kv#az-appconfig-kv-export) | Exportações de um recurso de loja de configuração de aplicativos. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

As amostras de script CLI de configuração de aplicação adicionais podem ser encontradas nas [amostras CLI de Configuração de Aplicação Azure](../cli-samples.md).
