---
title: Configurar identidades geridas com configuração de aplicações Azure
description: Saiba como as identidades geridas funcionam na Configuração de Aplicações Azure e como configurar uma identidade gerida
author: barbkess
ms.topic: article
ms.date: 02/25/2020
ms.author: barbkess
ms.reviewer: lcozzens
ms.service: azure-app-configuration
ms.openlocfilehash: 5424b776b977d8a6939bbb28c5d4c9c428cac444
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102179617"
---
# <a name="how-to-use-managed-identities-for-azure-app-configuration"></a>Como utilizar identidades geridas para configuração de aplicações Azure

Este tópico mostra-lhe como criar uma identidade gerida para a Configuração de Aplicações Azure. Uma identidade gerida a partir do Azure Ative Directory (AAD) permite que a Configuração de Aplicações Azure aceda facilmente a outros recursos protegidos pela AAD, como o Azure Key Vault. A identidade é gerida pela plataforma Azure. Não requer que forneça ou rode segredos. Para obter mais sobre identidades geridas em AAD, consulte [identidades geridas para recursos Azure](../active-directory/managed-identities-azure-resources/overview.md).

O seu pedido pode ser concedido dois tipos de identidades:

- Uma **identidade atribuída ao sistema** está ligada à sua loja de configuração. É apagado se a sua loja de configuração for eliminada. Uma loja de configuração só pode ter uma identidade atribuída ao sistema.
- Uma **identidade atribuída ao utilizador** é um recurso autónomo da Azure que pode ser atribuído à sua loja de configuração. Uma loja de configuração pode ter várias identidades atribuídas pelo utilizador.

## <a name="adding-a-system-assigned-identity"></a>Adicionar uma identidade atribuída ao sistema

A criação de uma loja de Configuração de Aplicativos com uma identidade atribuída ao sistema requer uma propriedade adicional a ser definida na loja.

### <a name="using-the-azure-cli"></a>Com a CLI do Azure

Para configurar uma identidade gerida utilizando o CLI Azure, utilize o comando [de atribuição de identidade az appconfig] contra uma loja de configuração existente. Tem três opções para executar os exemplos nesta secção:

- Utilize [a Azure Cloud Shell](../cloud-shell/overview.md) a partir do portal Azure.
- Utilize o Azure Cloud Shell incorporado através do botão "Try It", localizado no canto superior direito de cada bloco de código abaixo.
- [Instale a versão mais recente do Azure CLI](/cli/azure/install-azure-cli) (2.1 ou posterior) se preferir utilizar uma consola CLI local.

Os seguintes passos irão acompanhá-lo através da criação de uma loja de Configuração de Aplicações e atribuindo-lhe uma identidade utilizando o CLI:

1. Se estiver a utilizar a CLI do Azure numa consola local, primeiro inicie sessão no Azure com [az login]. Utilize uma conta associada à sua subscrição Azure:

    ```azurecli-interactive
    az login
    ```

1. Crie uma loja de configuração de aplicativos utilizando o CLI. Para obter mais exemplos de como utilizar o CLI com configuração de aplicações Azure, consulte [as amostras CLI de Configuração de Aplicações:](scripts/cli-create-service.md)

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. Executar o comando [de atribuição de identidade az appconfig] para criar a identidade atribuída ao sistema para esta loja de configuração:

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup
    ```

## <a name="adding-a-user-assigned-identity"></a>Adicionar uma identidade atribuída ao utilizador

A criação de uma loja de Configuração de Aplicativos com uma identidade atribuída ao utilizador requer que crie a identidade e, em seguida, atribua o seu identificador de recursos à sua loja.

### <a name="using-the-azure-cli"></a>Com a CLI do Azure

Para configurar uma identidade gerida utilizando o CLI Azure, utilize o comando [de atribuição de identidade az appconfig] contra uma loja de configuração existente. Tem três opções para executar os exemplos nesta secção:

- Utilize [a Azure Cloud Shell](../cloud-shell/overview.md) a partir do portal Azure.
- Utilize o Azure Cloud Shell incorporado através do botão "Try It", localizado no canto superior direito de cada bloco de código abaixo.
- [Instale a versão mais recente do Azure CLI](/cli/azure/install-azure-cli) (2.0.31 ou posterior) se preferir utilizar uma consola CLI local.

Os seguintes passos irão acompanhá-lo através da criação de uma identidade atribuída ao utilizador e de uma loja de Configuração de Aplicações, atribuindo a identidade à loja utilizando o CLI:

1. Se estiver a utilizar a CLI do Azure numa consola local, primeiro inicie sessão no Azure com [az login]. Utilize uma conta associada à sua subscrição Azure:

    ```azurecli-interactive
    az login
    ```

1. Crie uma loja de configuração de aplicativos utilizando o CLI. Para obter mais exemplos de como utilizar o CLI com configuração de aplicações Azure, consulte [as amostras CLI de Configuração de Aplicações:](scripts/cli-create-service.md)

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. Criar uma identidade atribuída ao utilizador chamada `myUserAssignedIdentity` usando o CLI.

    ```azurecli-interactive
    az identity create -resource-group myResourceGroup --name myUserAssignedIdentity
    ```

    Na saída deste comando, note o valor do `id` imóvel.

1. Executar o comando [de atribuição de identidade az appconfig] para atribuir a nova identidade atribuída ao utilizador nesta loja de configuração. Utilize o valor do `id` imóvel que notou no passo anterior.

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup --identities /subscriptions/[subscription id]/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity
    ```

## <a name="removing-an-identity"></a>Remover uma identidade

Uma identidade atribuída ao sistema pode ser removida desativando a funcionalidade utilizando o comando [de remoção de identidade az appconfig](/cli/azure/appconfig/identity#az-appconfig-identity-remove) no CLI Azure. As identidades atribuídas ao utilizador podem ser removidas individualmente. A remoção de uma identidade atribuída ao sistema desta forma também a eliminará da AAD. As identidades atribuídas ao sistema também são automaticamente removidas do AAD quando o recurso da aplicação é eliminado.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Crie uma aplicação core ASP.NET com configuração de aplicação Azure](quickstart-aspnet-core-app.md)

[az appconfig identidade atribuir]: /cli/azure/appconfig/identity#az-appconfig-identity-assign
[az login]: /cli/azure/reference-index#az-login
