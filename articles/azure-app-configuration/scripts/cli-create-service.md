---
title: Amostra de script Azure CLI - Criar uma Loja de Configuração de Aplicativos Azure
titleSuffix: Azure App Configuration
description: Crie uma loja de configuração de aplicativos Azure utilizando uma amostra de script Azure CLI. Consulte as ligações de artigos de referência aos comandos utilizados no script.
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: sample
ms.date: 01/24/2020
ms.author: lcozzens
ms.custom: devx-track-azurecli
ms.openlocfilehash: 13b8e88d10d66f96db6b50e505d4e579cb1f2f2b
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94566916"
---
# <a name="create-an-azure-app-configuration-store"></a>Criar uma Loja de Configuração de Aplicativos Azure

Este script de amostra cria uma nova instância da Configuração de Aplicações Azure num novo grupo de recursos.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Este tutorial requer a versão 2.0 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

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
  --query endpoint \
  --sku free \
  -o tsv
  )

# Get the AppConfig connection string 
appConfigConnectionString=$(az appconfig credential list \
--resource-group $myResourceGroupName \
--name $myAppConfigStoreName \
--query "[?name=='Secondary Read Only'] .connectionString" -o tsv)

# Echo the connection string for use in your application
echo "$appConfigConnectionString"
```

Anote o nome real gerado para o novo grupo de recursos. Irá utilizar esse nome de grupo de recursos quando quiser eliminar todos os recursos do grupo.

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar um novo grupo de recursos e uma loja de Configuração de Aplicações. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az appconfig criar](/cli/azure/appconfig#az-appconfig-create) | Cria um recurso de loja de configuração de aplicativos. |
| [lista de credenciais az apconfig](/cli/azure/appconfig/credential#az-appconfig-credential-list) | Lista as teclas de acesso para uma loja de configuração de aplicativos. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

As amostras de script CLI de configuração de aplicação adicionais podem ser encontradas nas [amostras CLI de Configuração de Aplicação Azure](../cli-samples.md).
