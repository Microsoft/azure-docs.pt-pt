---
title: Exemplo de script de CLI do Azure – importar para um repositório de configuração de Azure App | Microsoft Docs
description: Fornece informações e scripts de exemplo para importar para um repositório de configuração de Azure App
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
ms.openlocfilehash: cd1e54fc6cfbf254da010c03dfaa859a0ee8213c
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029808"
---
# <a name="import-to-an-azure-app-configuration-store"></a>Importar para um repositório de configurações Azure App

Este script de exemplo importa valores de chave para um repositório de configuração Azure App.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este artigo requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

Você precisa instalar a extensão da CLI de configuração do Azure App primeiro executando o seguinte comando:

        az extension add -n appconfig

## <a name="sample-script"></a>Script de exemplo

```azurecli-interactive
#!/bin/bash

# Import key-values from a file
az appconfig kv import --name myTestAppConfigStore --source file --path ~/Import.json
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação do script

Esse script usa os comandos a seguir para importar um repositório de configurações de aplicativo. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az appconfig import](/cli/azure/ext/appconfig/appconfig/kv#ext-appconfig-az-appconfig-kv-import) | Importa para um recurso de repositório de configuração de aplicativo. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Exemplos de script da CLI de configuração de aplicativo adicionais podem ser encontrados na [documentação de configuração do Azure app](../cli-samples.md).
