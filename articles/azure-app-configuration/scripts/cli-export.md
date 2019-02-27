---
title: Exemplo do Script da CLI do Azure – exportar a partir de um Store de configuração de aplicações do Azure | Documentos da Microsoft
description: Fornece os scripts de exemplo e informações para exportar a partir de um arquivo de configuração de aplicações do Azure
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
ms.openlocfilehash: 3310dc5d72284e8d94b95b855fee90d560205fa4
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56884784"
---
# <a name="export-from-an-azure-app-configuration-store"></a>Exportar de um arquivo de configuração de aplicações do Azure

Este script de exemplo exporta os valores de chave de um arquivo de configuração de aplicações do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este artigo requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

Tem de instalar a extensão de CLI de configuração de aplicações do Azure pela primeira vez ao executar o comando seguinte:

        az extension add -n appconfig

## <a name="sample-script"></a>Script de exemplo

```azurecli-interactive
#!/bin/bash

# Export all key-values
az appconfig kv export --name myTestAppConfigStore --file ~/Export.json
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para exportar um arquivo de configuração de aplicação. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az appconfig export](/cli/azure/ext/appconfig/appconfig#ext-appconfig-az-appconfig-export) | Exportações de uma configuração de aplicação armazenam recursos. |

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Exemplos do script da CLI de configuração de aplicações adicionais podem ser encontrados no [documentação de configuração de aplicações do Azure](../cli-samples.md).
