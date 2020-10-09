---
title: Criar serviço SignalR com Serviço de Aplicações utilizando O Azure CLI
description: Utilize o CLI Azure para criar o Serviço SignalR com o Serviço de Aplicações. Aprenda todos os comandos CLI para o Serviço Azure SignalR.
author: sffamily
ms.service: signalr
ms.devlang: azurecli
ms.topic: sample
ms.date: 11/13/2018
ms.author: zhshang
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 3b0d88b548f68bc365adfa3fdb3149db9c9f43f0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87494745"
---
# <a name="create-a-signalr-service-with-an-app-service"></a>Criar um Serviço SignalR com um Serviço de Aplicações

Este script de exemplo cria um novo recurso do Serviço Azure SignalR, utilizado para efetuar atualizações push de conteúdo em tempo real nos clientes. Este script também adiciona um novo plano do Serviço de Aplicações e Aplicação Web para alojar a sua Aplicação Web ASP.NET Core que utilize o Serviço SignalR. A aplicação Web está configurada com uma Definição de Aplicação denominada *AzureSignalRConnectionString* para ligar ao novo recurso do serviço SignalR.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este artigo requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script de exemplo

Este script utiliza a extensão *signalr* para a CLI do Azure. Execute o seguinte comando para instalar a extensão *signalr* para a CLI do Azure antes de utilizar este script de exemplo:

```azurecli-interactive
#!/bin/bash

# Generate a unique suffix for the service name
let randomNum=$RANDOM*$RANDOM

# Generate unique names for the SignalR service, resource group, 
# app service, and app service plan
SignalRName=SignalRTestSvc$randomNum
#resource name must be lowercase
mySignalRSvcName=${SignalRName,,}
myResourceGroupName=$SignalRName"Group"
myWebAppName=SignalRTestWebApp$randomNum
myAppSvcPlanName=$myAppSvcName"Plan"

# Create resource group 
az group create --name $myResourceGroupName --location eastus

# Create the Azure SignalR Service resource
az signalr create \
  --name $mySignalRSvcName \
  --resource-group $myResourceGroupName \
  --sku Standard_S1 \
  --unit-count 1 \
  --service-mode Default

# Create an App Service plan.
az appservice plan create --name $myAppSvcPlanName --resource-group $myResourceGroupName --sku FREE

# Create the Web App
az webapp create --name $myWebAppName --resource-group $myResourceGroupName --plan $myAppSvcPlanName  

# Get the SignalR primary connection string
primaryConnectionString=$(az signalr key list --name $mySignalRSvcName \
  --resource-group $myResourceGroupName --query primaryConnectionString -o tsv)

#Add an app setting to the web app for the SignalR connection
az webapp config appsettings set --name $myWebAppName --resource-group $myResourceGroupName \
  --settings "AzureSignalRConnectionString=$primaryConnectionString"
```

Anote o nome real gerado para o novo grupo de recursos. Será apresentado no resultado. Irá utilizar esse nome de grupo de recursos quando quiser eliminar todos os recursos do grupo.

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação do script

Cada comando na tabela liga à documentação específica do comando. Este script utiliza os seguintes comandos:

| Comando | Notas |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az signalr create](/cli/azure/signalr#az-signalr-create) | Cria um recurso do Serviço Azure SignalR. |
| [az signalr key list](/cli/azure/signalr/key#az-signalr-key-list) | Lista as chaves, que serão utilizadas pela sua aplicação quando efetuar atualizações push de conteúdo em tempo real com o SignalR. |
| [az appservice plan create](/cli/azure/appservice/plan#az-appservice-plan-create) | Cria um Plano do Serviço de Aplicações do Azure para alojar aplicações Web. |
| [az webapp create](/cli/azure/webapp#az-webapp-create) | Cria uma aplicação Web do Azure com o plano de alojamento do Serviço de Aplicações. |
| [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) | Adiciona a nova definição de aplicação para a aplicação Web. Esta definição de aplicação é utilizada para armazenar a cadeia de ligação SignalR. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Pode ver exemplos de script da CLI do Serviço Azure SignalR adicionais na [documentação do Serviço Azure SignalR](../signalr-reference-cli.md).
