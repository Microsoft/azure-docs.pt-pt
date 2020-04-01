---
title: Amostra de script Azure CLI - Importar para uma loja de configuração de aplicações
titleSuffix: Azure App Configuration
description: Use script Azure CLI - Configuração de importação para configuração de aplicações azure
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: lcozzens
ms.openlocfilehash: 71d6aafa82f647b9c6164ee9a06b43ed7e9a66af
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "77523628"
---
# <a name="import-to-an-azure-app-configuration-store"></a>Importar para uma loja de configuração de aplicações Azure

Este script de amostra importa definições de valor-chave para uma loja de configuração de aplicações Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o Azure CLI localmente, este artigo requer que esteja a executar a versão 2.0 do Azure CLI ou posterior. Executar `az --version` para localizar a versão. Para instalar ou atualizar, consulte [Instalar o Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script de exemplo

```azurecli-interactive
#!/bin/bash

# Import key-values from a file
az appconfig kv import --name myTestAppConfigStore --source file --path ~/Import.json
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para importar para uma loja de configuração de aplicações. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az appconfig kv importação](/cli/azure/appconfig/kv#az-appconfig-kv-import) | Importações para um recurso de loja de configuração de aplicações. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure CLI, consulte a [documentação Azure CLI](/cli/azure).

Amostras adicionais de script cli configuração de aplicativos podem ser encontradas nas amostras CLI de configuração de [aplicação azure](../cli-samples.md).
