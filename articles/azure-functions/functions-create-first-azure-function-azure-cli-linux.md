---
title: Criar a sua primeira função no Linux no Azure
description: Saiba como criar a sua primeira função alojada no Linux no Azure com a CLI do Azure e as Azure Functions Core Tools.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 03/12/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc, fasttrack-edit
ms.devlang: javascript
manager: jeconnoc
ms.openlocfilehash: 84e05b7afa2746587f2ea5008d493730ccbfad7e
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/12/2019
ms.locfileid: "68950042"
---
# <a name="create-your-first-function-hosted-on-linux-using-core-tools-and-the-azure-cli-preview"></a>Criar a sua primeira função alojada no Linux com as Ferramentas Base e a CLI do Azure (pré-visualização)

As Funções do Azure permitem-lhe executar o seu código num ambiente Linux [sem servidor](https://azure.com/serverless), sem que tenha de criar primeiro uma VM ou publicar uma aplicação Web. A hospedagem do Linux requer [o tempo de execução do functions 2,0](functions-versions.md). O suporte para executar um aplicativo de funções no Linux no [plano de consumo](functions-scale.md#consumption-plan) sem servidor está atualmente em versão prévia. Para saber mais, confira [Este artigo sobre considerações sobre](https://aka.ms/funclinux)a versão prévia.

Este artigo de início rápido explica como utilizar a CLI do Azure para criar a sua primeira aplicação de funções em execução no Linux. O código de função é criado localmente e, em seguida, implementado no Azure através das [Azure Functions Core Tools](functions-run-local.md).

São suportados os seguintes passos num computador Mac, Windows ou Linux. Este artigo mostra-lhe como criar funções em qualquer um dos JavaScript ou C#. Para saber como criar funções do Python, consulte [criar sua primeira função do Python usando as ferramentas básicas e a CLI do Azure (versão prévia)](functions-create-first-function-python.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de executar este exemplo, tem de ter o seguinte:

- Instale [Azure Functions Core Tools](./functions-run-local.md#v2) versão 2.6.666 ou superior.

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

## <a name="create-a-linux-function-app-in-azure"></a>Criar uma aplicação de funções do Linux no Azure

Precisa de uma aplicação de funções para alojar a execução das suas funções no Linux. A aplicação de funções proporciona um ambiente sem servidor para a execução do código da sua função. Permite-lhe agrupar funções como unidades lógicas para uma gestão, implementação e partilha de recursos mais fácil. Utilize o comando [az functionapp create](/cli/azure/functionapp#az-functionapp-create) para criar uma aplicação de funções no Linux.

No comando seguinte, utilize o nome da sua aplicação de funções exclusivo onde vir o marcador de posição `<app_name>` e o nome da conta de armazenamento para `<storage_name>`. O `<app_name>` também é o domínio DNS predefinido para a aplicação de funções. Este nome tem de ser exclusivo em todas as aplicações no Azure. Você também deve definir o `<language>` tempo de execução para seu aplicativo de `dotnet` funçõesC#, de `node` (), (JavaScript/TypeScript `python`) ou.

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