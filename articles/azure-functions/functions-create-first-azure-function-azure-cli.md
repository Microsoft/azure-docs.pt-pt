---
title: Criar a sua primeira função com a CLI do Azure
description: Saiba como criar a sua primeira Função do Azure para execução sem servidor através da CLI do Azure e das Ferramentas Base das Funções do Azure.
author: ggailey777
ms.author: glenga
ms.assetid: 674a01a7-fd34-4775-8b69-893182742ae0
ms.date: 11/13/2018
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
manager: gwallace
ms.openlocfilehash: 82fbaa35d8d06cdb1999a76a36fb4a1dade017e9
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70883883"
---
# <a name="create-your-first-function-from-the-command-line"></a>Criar a primeira função a partir da linha de comandos

Este tópico do guia de introdução mostra-lhe como criar a sua primeira função a partir da linha de comandos ou do terminal. Vai utilizar a CLI do Azure para criar uma aplicação de funções, que é a infraestrutura [sem servidor](https://azure.microsoft.com/solutions/serverless/) que aloja a sua função. O projeto de código de função é gerado a partir de um modelo com as [Ferramentas Base das Funções do Azure](functions-run-local.md), que também é utilizado para implementar o projeto de aplicação de funções no Azure.

Pode seguir os passos abaixo num computador Mac, Windows ou Linux.

## <a name="prerequisites"></a>Pré-requisitos

Antes de executar este exemplo, tem de ter o seguinte:

+ Instale [Azure Functions Core Tools](./functions-run-local.md#v2) versão 2.6.666 ou posterior.

+ Instale a [CLI do Azure]( /cli/azure/install-azure-cli). Este artigo requer a versão 2.0 ou posterior da CLI do Azure. Execute `az --version` para localizar a versão atual. Pode também utilizar o [Azure Cloud Shell](https://shell.azure.com/bash).

+ Uma subscrição ativa do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-create-function-app-cli](../../includes/functions-create-function-app-cli.md)]

## <a name="enable-extension-bundles"></a>Habilitar pacotes de extensão

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app"></a>Criar uma aplicação de função

Precisa de uma aplicação Function App para alojar a execução das suas funções. A aplicação Function App proporciona um ambiente para a execução sem servidor do código da sua função. Permite-lhe agrupar funções como unidades lógicas para uma gestão, implementação e partilha de recursos mais fácil. Utilize o comando [az functionapp create](/cli/azure/functionapp#az-functionapp-create) para criar uma aplicação Function App. 

No comando seguinte, substitua o nome da sua aplicação de funções exclusivo onde vir o marcador de posição `<APP_NAME>` e o nome da conta de armazenamento para `<STORAGE_NAME>`. O `<APP_NAME>` vai ser utilizado como o domínio DNS predefinido para a aplicação Function App, daí que o nome tenha de ser exclusivo em todas as aplicações no Azure. Você também deve definir o `<language>` tempo de execução para seu aplicativo de `dotnet` funçõesC#, de `node` () ou (JavaScript).

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --consumption-plan-location westeurope \
--name <APP_NAME> --storage-account  <STORAGE_NAME> --runtime <language>
```

Definir o parâmetro _consumption-plan-location_ significa que a aplicação de funções é alojada num plano de alojamento de Consumo. Neste plano sem servidor, os recursos são adicionados dinamicamente conforme exigido pelas suas funções e paga apenas quando as funções estão em execução. Para obter mais informações, veja [Choose the correct hosting plan](functions-scale.md) (Escolher o plano de alojamento certo).

Depois de a aplicação Function App ter sido criada, a CLI do Azure mostra informações semelhantes às do exemplo seguinte:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.net",
    "quickstart.scm.azurewebsites.net"
  ],
   ....
    // Remaining output has been truncated for readability.
}
```

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]

