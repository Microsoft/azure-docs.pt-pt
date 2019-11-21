---
title: Criar a sua primeira função no Linux no Azure
description: Learn how to create your first function hosted on Linux in Azure using the command line tools, Azure Functions Core Tools and the Azure CLI.
ms.date: 03/12/2019
ms.topic: quickstart
ms.custom: mvc, fasttrack-edit
ms.openlocfilehash: 19abfee69db53c560dfa2696d85f8c1c3d770c09
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230815"
---
# <a name="quickstart-create-your-first-function-hosted-on-linux-using-command-line-tools"></a>Quickstart: Create your first function hosted on Linux using command line tools

As Funções do Azure permitem-lhe executar o seu código num ambiente Linux [sem servidor](https://azure.com/serverless), sem que tenha de criar primeiro uma VM ou publicar uma aplicação Web. Linux-hosting requires [the Functions 2.x runtime](functions-versions.md). Serverless functions run in the [Consumption plan](functions-scale.md#consumption-plan).

Este artigo de início rápido explica como utilizar a CLI do Azure para criar a sua primeira aplicação de funções em execução no Linux. O código de função é criado localmente e, em seguida, implementado no Azure através das [Azure Functions Core Tools](functions-run-local.md).

São suportados os seguintes passos num computador Mac, Windows ou Linux. Este artigo mostra-lhe como criar funções em qualquer um dos JavaScript ou C#. To learn how to create Python functions, see [Create your first Python function using Core Tools and the Azure CLI](functions-create-first-function-python.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de executar este exemplo, tem de ter o seguinte:

+ Install [Azure Functions Core Tools](./functions-run-local.md#v2) version 2.6.666 or above.

+ Instale a [CLI do Azure]( /cli/azure/install-azure-cli). Este artigo requer a versão 2.0 ou posterior da CLI do Azure. Execute `az --version` para localizar a versão atual. Pode também utilizar o [Azure Cloud Shell](https://shell.azure.com/bash).

+ Uma subscrição ativa do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-create-function-app-cli](../../includes/functions-create-function-app-cli.md)]

## <a name="enable-extension-bundles"></a>Enable extension bundles

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-function-app-in-azure"></a>Criar uma aplicação de funções do Linux no Azure

Precisa de uma aplicação de funções para alojar a execução das suas funções no Linux. A aplicação de funções proporciona um ambiente sem servidor para a execução do código da sua função. Permite-lhe agrupar funções como unidades lógicas para uma gestão, implementação e partilha de recursos mais fácil. Utilize o comando [az functionapp create](/cli/azure/functionapp#az-functionapp-create) para criar uma aplicação de funções no Linux.

No comando seguinte, utilize o nome da sua aplicação de funções exclusivo onde vir o marcador de posição `<app_name>` e o nome da conta de armazenamento para `<storage_name>`. O `<app_name>` também é o domínio DNS predefinido para a aplicação de funções. Este nome tem de ser exclusivo em todas as aplicações no Azure. You should also set the `<language>` runtime for your function app, from `dotnet` (C#), `node` (JavaScript/TypeScript), or `python`.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --consumption-plan-location westus --os-type Linux \
--name <app_name> --storage-account  <storage_name> --runtime <language>
```

Depois de a aplicação de funções estar criada, verá a seguinte mensagem:

```output
Your serverless Linux function app 'myfunctionapp' has been successfully created.
To active this function app, publish your app content using Azure Functions Core Tools or the Azure portal.
```

Agora, pode publicar o seu projeto para a nova aplicação de funções no Azure.

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]