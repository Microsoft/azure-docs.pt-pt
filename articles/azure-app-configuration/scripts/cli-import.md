---
title: Amostra de script Azure CLI - Importar para uma loja de configuração de aplicações
titleSuffix: Azure App Configuration
description: Fornece informações e scripts de amostra para importação para uma loja de configuração de aplicações Azure
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: balans
editor: ''
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: azure-app-configuration
ms.date: 02/24/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: b0704c6262451cef615355e871ce7f26005b3132
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77467679"
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
