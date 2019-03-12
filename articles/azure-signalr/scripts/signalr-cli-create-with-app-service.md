---
title: CLI do Azure exemplo do Script - criar um serviço SignalR com um serviço de aplicações
description: Exemplo de Script da CLI do Azure – Criar um Serviço SignalR com um Serviço de Aplicações
author: sffamily
ms.service: signalr
ms.devlang: azurecli
ms.topic: sample
ms.date: 04/20/2018
ms.author: zhshang
ms.custom: mvc
ms.openlocfilehash: 5fd9e593a9a5158b35d3c0e17436d54bc7c1bd03
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57547789"
---
# <a name="create-a-signalr-service-with-an-app-service"></a>Criar um Serviço SignalR com um Serviço de Aplicações

Este script de exemplo cria um novo recurso do Serviço Azure SignalR, utilizado para efetuar atualizações push de conteúdo em tempo real nos clientes. Este script também adiciona um novo plano do Serviço de Aplicações e Aplicação Web para alojar a sua Aplicação Web ASP.NET Core que utilize o Serviço SignalR. A aplicação Web está configurada com uma Definição de Aplicação denominada *AzureSignalRConnectionString* para ligar ao novo recurso do serviço SignalR.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este artigo requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script de exemplo

Este script utiliza a extensão *signalr* para a CLI do Azure. Execute o seguinte comando para instalar a extensão *signalr* para a CLI do Azure antes de utilizar este script de exemplo:

```azurecli-interactive
az extension add -n signalr
```

[!code-azurecli-interactive[main](../../../cli_scripts/azure-signalr/create-signalr-with-app-service/create-signalr-with-app-service.sh "Create a new Azure SignalR Service and Web App")]

Anote o nome real gerado para o novo grupo de recursos. Será apresentado no resultado. Irá utilizar esse nome de grupo de recursos quando quiser eliminar todos os recursos do grupo.

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação do script

Cada comando na tabela liga à documentação específica do comando. Este script utiliza os seguintes comandos:

| Comando | Notas |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az signalr create](/cli/azure/ext/signalr/signalr#ext-signalr-az-signalr-create) | Cria um recurso do Serviço Azure SignalR. |
| [az signalr key list](/cli/azure/ext/signalr/signalr/key#ext-signalr-az-signalr-key-list) | Lista as chaves, que serão utilizadas pela sua aplicação quando efetuar atualizações push de conteúdo em tempo real com o SignalR. |
| [az appservice plan create](/cli/azure/appservice/plan#az-appservice-plan-create) | Cria um Plano do Serviço de Aplicações do Azure para alojar aplicações Web. |
| [az webapp create](/cli/azure/webapp#az-webapp-create) | Cria uma aplicação Web do Azure com o plano de alojamento do Serviço de Aplicações. |
| [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) | Adiciona a nova definição de aplicação para a aplicação Web. Esta definição de aplicação é utilizada para armazenar a cadeia de ligação SignalR. |

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Pode ver exemplos de script da CLI do Serviço Azure SignalR adicionais na [documentação do Serviço Azure SignalR](../signalr-reference-cli.md).
