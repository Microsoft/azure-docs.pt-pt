---
title: Exemplo de script de CLI do Azure – exportar de um repositório de configuração de Azure App
titleSuffix: Azure App Configuration
description: Fornece informações e scripts de exemplo para exportar de um repositório de configuração Azure App
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: azure-app-configuration
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 28066fcea50180189cff7f9af790908c52a4a261
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75433606"
---
# <a name="export-from-an-azure-app-configuration-store"></a>Exportar de um repositório de configuração Azure App

Este script de exemplo exporta valores de chave de um repositório de configuração Azure App.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este artigo requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

Você precisa instalar a extensão da CLI de configuração do Azure App primeiro executando o seguinte comando:

        az extension add -n appconfig

## <a name="sample-script"></a>Script de exemplo

```azurecli-interactive
#!/bin/bash

# Export all key-values
az appconfig kv export --name myTestAppConfigStore --file ~/Export.json
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação do script

Esse script usa os comandos a seguir para exportar de um repositório de configuração de aplicativo. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [AZ AppConfig kV Export](/cli/azure/ext/appconfig/appconfig/kv#ext-appconfig-az-appconfig-kv-export) | Exporta de um recurso de repositório de configuração de aplicativo. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Exemplos de script da CLI de configuração de aplicativo adicionais podem ser encontrados nos [exemplos da CLI de configuração do Azure app](../cli-samples.md).
