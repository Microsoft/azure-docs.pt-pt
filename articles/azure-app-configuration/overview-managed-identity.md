---
title: Configure identidades geridas com configuração de app Azure
description: Saiba como funcionam identidades geridas na Configuração de Aplicações Azure e como configurar uma identidade gerida
author: jpconnock
ms.topic: article
ms.date: 02/18/2020
ms.author: jeconnoc
ms.reviewer: lcozzens
ms.service: azure-app-configuration
ms.openlocfilehash: 751b10c0d80412671028e85debb2dcd4422c8459
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77493153"
---
# <a name="how-to-use-managed-identities-for-azure-app-configuration"></a>Como usar identidades geridas para configuração de aplicações do Azure

Este tópico mostra-lhe como criar uma identidade gerida para a Configuração de Aplicações Azure. Uma identidade gerida do Azure Ative Directory (AAD) permite que a Configuração de Aplicações Azure aceda facilmente a outros recursos protegidos pela AAD, como o Azure Key Vault. A identidade é gerida pela plataforma Azure. Não requer que forneça ou rode segredos. Para saber mais sobre identidades geridas em AAD, consulte [identidades geridas para recursos Azure](../active-directory/managed-identities-azure-resources/overview.md).

A sua aplicação pode ser concedida a dois tipos de identidades:

- Uma **identidade atribuída** ao sistema está ligada à sua loja de configuração. É apagado se a sua loja de configuração for eliminada. Uma loja de configuração só pode ter uma identidade atribuída ao sistema.
- Uma **identidade atribuída ao utilizador** é um recurso Azure autónomo que pode ser atribuído à sua loja de configuração. Uma loja de configuração pode ter várias identidades atribuídas ao utilizador.

## <a name="adding-a-system-assigned-identity"></a>Adicionar uma identidade atribuída ao sistema

A criação de uma loja de configuração de aplicações com uma identidade atribuída ao sistema requer que seja definida uma propriedade adicional na loja.

### <a name="using-the-azure-cli"></a>Com a CLI do Azure

Para configurar uma identidade gerida utilizando o Azure CLI, utilize o comando de atribuição de [identidade az appconfig] contra uma loja de configuração existente. Tem três opções para executar os exemplos nesta secção:

- Utilize a Casca de [Nuvem Azure](../cloud-shell/overview.md) do portal Azure.
- Utilize a casca de nuvem azure incorporada através do botão "Try It", localizado no canto superior direito de cada bloco de código abaixo.
- [Instale a versão mais recente do Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.1 ou mais tarde) se preferir utilizar uma consola CLI local.

Os seguintes passos irão acompanhá-lo através da criação de uma loja de Configuração de Aplicações e atribuindo-lhe uma identidade usando o CLI:

1. Se estiver a utilizar a CLI do Azure numa consola local, primeiro inicie sessão no Azure com [az login]. Utilize uma conta que esteja associada à sua subscrição Azure:

    ```azurecli-interactive
    az login
    ```

1. Crie uma loja de configuração de aplicações utilizando o CLI. Para mais exemplos de como utilizar o CLI com configuração de aplicações Azure, consulte [as amostras CLI](scripts/cli-create-service.md)de configuração da aplicação:

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. Executar o comando de atribuição de [identidade az appconfig] para criar a identidade atribuída ao sistema para esta loja de configuração:

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup
    ```

## <a name="adding-a-user-assigned-identity"></a>Adicionar uma identidade atribuída ao utilizador

Criar uma loja de Configuração de Aplicações com uma identidade atribuída ao utilizador requer que crie a identidade e, em seguida, atribua o seu identificador de recursos à sua loja.

### <a name="using-the-azure-cli"></a>Com a CLI do Azure

Para configurar uma identidade gerida utilizando o Azure CLI, utilize o comando de atribuição de [identidade az appconfig] contra uma loja de configuração existente. Tem três opções para executar os exemplos nesta secção:

- Utilize a Casca de [Nuvem Azure](../cloud-shell/overview.md) do portal Azure.
- Utilize a casca de nuvem azure incorporada através do botão "Try It", localizado no canto superior direito de cada bloco de código abaixo.
- [Instale a versão mais recente do Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.31 ou mais tarde) se preferir utilizar uma consola CLI local.

Os seguintes passos irão acompanhá-lo através da criação de uma identidade atribuída ao utilizador e de uma loja de configuração de aplicações, atribuindo a identidade à loja utilizando o CLI:

1. Se estiver a utilizar a CLI do Azure numa consola local, primeiro inicie sessão no Azure com [az login]. Utilize uma conta que esteja associada à sua subscrição Azure:

    ```azurecli-interactive
    az login
    ```

1. Crie uma loja de configuração de aplicações utilizando o CLI. Para mais exemplos de como utilizar o CLI com configuração de aplicações Azure, consulte [as amostras CLI](scripts/cli-create-service.md)de configuração da aplicação:

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. Crie uma identidade atribuída ao utilizador chamada `myUserAssignedIdentity` utilizando o CLI.

    ```azurecli-interactive
    az identity create -resource-group myResourceGroup --name myUserAssignedIdentity
    ```

    Na saída deste comando, note o valor da propriedade `id`.

1. Executar o comando de atribuição de [identidade az appconfig] para atribuir a nova identidade atribuída ao utilizador a esta loja de configuração. Utilize o valor do imóvel `id` que observou no passo anterior.

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup --identities /subscriptions/[subscription id]/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity
    ```

## <a name="removing-an-identity"></a>Remover uma identidade

Uma identidade atribuída ao sistema pode ser removida desativando a funcionalidade utilizando o comando de remoção de [identidade az appconfig](/cli/azure/appconfig/identity?view=azure-cli-latest#az-appconfig-identity-remove) no ClI Azure. As identidades atribuídas ao utilizador podem ser removidas individualmente. A remoção de uma identidade atribuída ao sistema desta forma também a eliminará da AAD. As identidades atribuídas ao sistema também são automaticamente removidas do AAD quando o recurso da aplicação é eliminado.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Crie uma app ASP.NET Core com configuração de aplicações azure](quickstart-aspnet-core-app.md)

[identidade az appconfig]: /cli/azure/appconfig/identity?view=azure-cli-latest#az-appconfig-identity-assign
[az login]: /cli/azure/reference-index#az-login
