---
title: Amostra de script Azure CLI - Trabalhe com valores-chave na Loja de Configuração de Aplicações
titleSuffix: Azure App Configuration
description: Utilize o script Azure CLI para criar, visualizar, atualizar e eliminar valores-chave da loja de Configuração de Aplicações
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: lcozzens
ms.custom: devx-track-azurecli
ms.openlocfilehash: e3407a1da469bf4f31e5d54d83ee837bddbafffc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87494762"
---
# <a name="work-with-key-values-in-an-azure-app-configuration-store"></a>Trabalhe com valores-chave numa loja de configuração de aplicações Azure

Este guião da amostra mostra como:
* Criar um novo par de valores-chave
* Listar todos os pares de valores-chave existentes
* Atualizar o valor de uma chave recém-criada
* Eliminar o novo par de valores-chave

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Este artigo requer a versão 2.0 do Azure CLI ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script de exemplo

```azurecli-interactive
#!/bin/bash

appConfigName=myTestAppConfigStore
newKey="TestKey"
refKey="KeyVaultReferenceTestKey"
uri="[URL to value stored in Key Vault]"
uri2="[URL to another value stored in Key Vault]"

# Create a new key-value 
az appconfig kv set --name $appConfigName --key $newKey --value "Value 1"

# List current key-values
az appconfig kv list --name $appConfigName

# Update new key's value
az appconfig kv set --name $appConfigName --key $newKey --value "Value 2"

# List current key-values
az appconfig kv list --name $appConfigName

# Create a new key-value referencing a value stored in Azure Key Vault
az appconfig kv set --name $appConfigName --key $refKey --content-type "application/vnd.microsoft.appconfig.keyvaultref+json;charset=utf-8" --value "{\"uri\":\"$uri\"}"

# List current key-values
az appconfig kv list --name $appConfigName

# Update Key Vault reference
az appconfig kv set --name $appConfigName --key $refKey --value "{\"uri\":\"$uri2\"}"

# List current key-values
az appconfig kv list --name $appConfigName

# Delete new key
az appconfig kv delete  --name $appConfigName --key $newKey

# Delete Key Vault reference
az appconfig kv delete --name $appConfigName --key $refKey

# List current key-values
az appconfig kv list --name $appConfigName
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação do script

Esta tabela lista os comandos utilizados no nosso script de amostra. 

| Comando | Notas |
|---|---|
| [az appconfig kv conjunto](/cli/azure/appconfig/kv#az-appconfig-kv-set) | Criar ou atualizar um par de valor-chave. |
| [az appconfig kv lista](/cli/azure/appconfig/kv#az-appconfig-kv-list) | Listar pares de valores-chave numa loja de configuração de aplicações. |
| [az appconfig kv excluir](/cli/azure/appconfig/kv#az-appconfig-kv-delete) | Apague um par de valor-chave. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

As amostras de script CLI de configuração de aplicação adicionais podem ser encontradas nas [amostras CLI de Configuração de Aplicação Azure](../cli-samples.md).
