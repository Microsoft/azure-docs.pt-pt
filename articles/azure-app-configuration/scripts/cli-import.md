---
title: Amostra de script Azure CLI - Importar para uma loja de configuração de aplicativos
titleSuffix: Azure App Configuration
description: Use o script Azure CLI - Configuração de importação para configuração de aplicativo Azure
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: alkemper
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5a80acd1261211fe3efaefa6a4b6025a321e2d65
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774184"
---
# <a name="import-to-an-azure-app-configuration-store"></a>Importar para uma loja de configuração de aplicativos Azure

Este script de amostra importa definições de valor-chave para uma loja de configuração de aplicativos Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Este tutorial requer a versão 2.0 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="sample-script"></a>Script de exemplo

```azurecli-interactive
#!/bin/bash

# Import key-values from a file
az appconfig kv import --name myTestAppConfigStore --source file --format json --path ~/Import.json
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para importar para uma loja de Configuração de Aplicações. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az appconfig kv importação](/cli/azure/appconfig/kv#az_appconfig_kv_import) | Importações para um recurso de loja de configuração de aplicativos. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure CLI, consulte a documentação do [Azure CLI](/cli/azure).

As amostras de script CLI de configuração de aplicação adicionais podem ser encontradas nas [amostras CLI de Configuração de Aplicação Azure](../cli-samples.md).
