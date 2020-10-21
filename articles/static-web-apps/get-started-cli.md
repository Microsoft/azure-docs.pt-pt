---
title: 'Quickstart: Construir a sua primeira aplicação web estática com aplicações web estáticas Azure usando o Azure CLI'
description: Aprenda a construir uma instância de Aplicações Web Estáticas Azure com o Azure Static Web Apps CLI.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: ed49892284b53844c5c578b752a598cdbdd9b946
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92320334"
---
# <a name="quickstart-building-your-first-static-web-app-using-the-azure-cli"></a>Quickstart: Construir a sua primeira aplicação web estática usando o Azure CLI

A Azure Static Web Apps publica um site para um ambiente de produção construindo aplicações a partir de um repositório GitHub. Neste arranque rápido, implementa uma aplicação web para aplicações Web Azure Static usando o Azure CLI.

Se não tiver uma subscrição do Azure, [crie uma conta de teste gratuita](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Pré-requisitos

- Conta do [GitHub](https://github.com)
- [Token de acesso pessoal do GitHub](https://docs.github.com/github/authenticating-to-github/creating-a-personal-access-token)
- [Conta Azure](https://portal.azure.com)
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) instalado (versão 2.8.0 e superior)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

[!INCLUDE [clone the repository](../../includes/static-web-apps-get-started-clone-repo.md)]

Em seguida, mude para a nova pasta utilizando o seguinte comando.

```bash
cd my-first-static-web-app
```

## <a name="create-a-static-web-app"></a>Criar uma aplicação Web estática

Agora que o repositório foi criado, pode criar uma aplicação web estática a partir do Azure CLI.

> [!IMPORTANT]
> Certifique-se de que está na minha primeira pasta _de aplicações web estáticas_ no seu terminal.

1. Inscreva-se no Azure CLI utilizando o seguinte comando.

    ```bash
    az login
    ```

1. Crie uma nova aplicação web estática a partir do seu repositório.

    # <a name="no-framework"></a>[Sem Enquadramento](#tab/vanilla-javascript)

    ```bash
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b master \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    # <a name="angular"></a>[Angular](#tab/angular)

    ```bash
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b master \
        --app-artifact-location "dist/angular-basic" \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    # <a name="react"></a>[React](#tab/react)

    ```bash
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b master \
        --app-artifact-location "build" \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    # <a name="vue"></a>[Vue](#tab/vue)

    ```bash
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b master \
        --app-artifact-location "dist" \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    ---

    - `<RESOURCE_GROUP_NAME>`: Substitua este valor por um nome de grupo de recursos Azure existente.

    - `<YOUR_GITHUB_ACCOUNT_NAME>`: Substitua este valor pelo seu nome de utilizador GitHub.

    - `<LOCATION>`: Substitua este valor pela localização mais próxima. As opções incluem: _CentralUS_, _EastAsia,_ _EastUS2,_ _WestEurope_e _WestUS2_.

    - `<YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>`: Substitua este valor pelo [token de acesso pessoal GitHub](https://docs.github.com/github/authenticating-to-github/creating-a-personal-access-token) que gerou anteriormente.

    Agora pode ver a aplicação criada em Azure.

1. Abra o [portal do Azure](https://portal.azure.com).

1. Procure **a minha primeira web-web-app** a partir da barra de pesquisa de topo.

1. Selecione **a minha primeira web-web-static-app**.

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

Se não continuar a utilizar esta aplicação, pode eliminar a instância Azure Static Web Apps executando o seguinte comando:

```bash
az staticwebapp delete \
    --name my-first-static-web-app
    --resource-group my-first-static-web-app
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Adicionar uma API](add-api.md)