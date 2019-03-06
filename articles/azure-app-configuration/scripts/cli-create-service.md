---
title: CLI do Azure exemplo do Script - criar um Store de configuração de aplicações do Azure | Documentos da Microsoft
description: Script da CLI do Azure de exemplo - criar um arquivo de configuração de aplicações do Azure
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
ms.openlocfilehash: 7833b5d6b9b94ddcd4b94d96201ccc85497f2529
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57446933"
---
# <a name="create-an-azure-app-configuration-store"></a>Criar um Store de configuração de aplicações do Azure

Este script de exemplo cria uma nova instância da configuração de aplicações do Azure num novo grupo de recursos com um nome aleatório.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este artigo requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

Tem de instalar a extensão de CLI de configuração de aplicações do Azure pela primeira vez ao executar o comando seguinte:

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

Este script utiliza os seguintes comandos para criar um novo grupo de recursos e um arquivo de configuração de aplicação. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az appconfig create](/cli/azure/ext/appconfig/appconfig) | Cria um recurso de arquivo de configuração de aplicação. |
| [az appconfig key list](/cli/azure/ext/appconfig/appconfig/kv) | Liste as chaves armazenadas num arquivo de configuração de aplicação. |

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Exemplos do script da CLI de configuração de aplicações adicionais podem ser encontrados no [documentação de configuração de aplicações do Azure](../cli-samples.md).
