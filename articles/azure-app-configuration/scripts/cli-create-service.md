---
title: Exemplo de script CLI do Azure-criar um repositório de configuração de Azure App
titleSuffix: Azure App Configuration
description: Exemplo de script CLI do Azure-criar um repositório de configuração de Azure App
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
ms.openlocfilehash: d57de8219cb73864ed722c6906a1bd75fec51a50
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75433594"
---
# <a name="create-an-azure-app-configuration-store"></a>Criar um repositório de configuração de Azure App

Este script de exemplo cria uma nova instância do Azure App configuração em um novo grupo de recursos com um nome aleatório.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este artigo requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

Você precisa instalar a extensão da CLI de configuração do Azure App primeiro executando o seguinte comando:

        az extension add -n appconfig

## <a name="sample-script"></a>Script de exemplo

```azurecli-interactive
#!/bin/bash

appConfigName=myTestAppConfigStore
#resource name must be lowercase
myAppConfigStoreName=${appConfigName,,}
myResourceGroupName=$appConfigName"Group"

# Create resource group 
az group create --name $myResourceGroupName --location eastus

# Create the Azure AppConfig Service resource and query the hostName
appConfigHostname=$(az appconfig create \
  --name $myAppConfigStoreName \
  --location eastus \
  --resource-group $myResourceGroupName \
  --query hostName \
  -o tsv)

# Get the AppConfig primary key 
appConfigPrimaryKey=$(az appconfig key list --name $myAppConfigStoreName \
  --resource-group $myResourceGroupName --query primaryKey -o tsv)

# Form the connection string for use in your application
connstring="Endpoint=https://$appConfigHostname;AccessKey=$appConfigPrimaryKey;"
echo "$connstring"
```

Anote o nome real gerado para o novo grupo de recursos. Irá utilizar esse nome de grupo de recursos quando quiser eliminar todos os recursos do grupo.

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação do script

Esse script usa os comandos a seguir para criar um novo grupo de recursos e um repositório de configuração de aplicativo. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az appconfig create](/cli/azure/ext/appconfig/appconfig#ext-appconfig-az-appconfig-create) | Cria um recurso de repositório de configuração de aplicativo. |
| [az appconfig kv list](/cli/azure/ext/appconfig/appconfig/kv#ext-appconfig-az-appconfig-kv-list) | Liste as chaves armazenadas em um repositório de configuração de aplicativo. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Exemplos de script da CLI de configuração de aplicativo adicionais podem ser encontrados nos [exemplos da CLI de configuração do Azure app](../cli-samples.md).
