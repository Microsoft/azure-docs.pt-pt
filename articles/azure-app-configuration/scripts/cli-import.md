---
title: Amostra de script Azure CLI - Importar para uma loja de configuração de aplicativos
titleSuffix: Azure App Configuration
description: Use o script Azure CLI - Configuração de importação para configuração de aplicativo Azure
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: lcozzens
ms.custom: devx-track-azurecli
ms.openlocfilehash: fe4c22042c31d55575320d9c3823783902700f9f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87494779"
---
# <a name="import-to-an-azure-app-configuration-store"></a>Importar para uma loja de configuração de aplicativos Azure

Este script de amostra importa definições de valor-chave para uma loja de configuração de aplicativos Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o Azure CLI localmente, este artigo requer que esteja a executar a versão 2.0 ou mais tarde do Azure CLI. Executar `az --version` para localizar a versão. Para instalar ou atualizar, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script de exemplo

```azurecli-interactive
#!/bin/bash

# Import key-values from a file
az appconfig kv import --name myTestAppConfigStore --source file --path ~/Import.json
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para importar para uma loja de Configuração de Aplicações. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az appconfig kv importação](/cli/azure/appconfig/kv#az-appconfig-kv-import) | Importações para um recurso de loja de configuração de aplicativos. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure CLI, consulte a documentação do [Azure CLI](/cli/azure).

As amostras de script CLI de configuração de aplicação adicionais podem ser encontradas nas [amostras CLI de Configuração de Aplicação Azure](../cli-samples.md).
