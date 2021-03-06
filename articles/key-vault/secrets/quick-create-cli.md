---
title: Quickstart - set and retrieve a secret from Azure Key Vault
description: Início Rápido que mostra como definir e obter um segredo do Azure Key Vault com a CLI do Azure
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-azurecli
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: dc5fa0eeabc437f16ca64c34b4bb72e62c0c3b21
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815028"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-azure-cli"></a>Início Rápido: Definir e obter um segredo do Azure Key Vault com a CLI do Azure

Neste arranque rápido, você cria um cofre chave em Azure Key Vault com Azure CLI. O Azure Key Vault é um serviço cloud que funciona como um arquivo de segredos seguro. Pode armazenar chaves, palavras-passe, certificados e outros segredos em segurança. Para mais informações sobre o Key Vault, poderá rever a [Visão Geral.](../general/overview.md) A CLI do Azure é utilizada para criar e gerir recursos do Azure através de comandos ou scripts. Depois de concluir este passo, irá armazenar um segredo.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Este arranque rápido requer a versão 2.0.4 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [Create a resource group](../../../includes/key-vault-cli-rg-creation.md)]

## <a name="create-a-key-vault"></a>Criar um cofre de chaves

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-kv-creation.md)]

## <a name="add-a-secret-to-key-vault"></a>Adicionar um segredo ao Key Vault

Para adicionar um segredo ao cofre, apenas tem de efetuar alguns passos adicionais. Esta palavra-passe pode ser utilizada por uma aplicação. A palavra-passe será chamada **ExemploPassword** e armazenará o valor de **hVFkk965BuUv** na sua.

Utilize o comando secreto Azure CLI [az keyvault](/cli/azure/keyvault/secret#az_keyvault_secret_set) abaixo para criar um segredo no Key Vault chamado **ExemploPassword** que irá armazenar o valor **hVFkk965BuUv** :

```azurecli
az keyvault secret set --vault-name "<your-unique-keyvault-name>" --name "ExamplePassword" --value "hVFkk965BuUv"
```

## <a name="retrieve-a-secret-from-key-vault"></a>Recupere um segredo do Cofre de Chaves

Agora, pode referenciar esta palavra-passe que adicionou ao Azure Key Vault com o respetivo URI. Utilize **'https://<o seu nome>.vault.azure.net/secrets/ExamplePassword exclusivo-keyvault'** para obter a versão atual.

Para ver o valor contido no segredo como texto simples:

```azurecli
az keyvault secret show --name "ExamplePassword" --vault-name "<your-unique-keyvault-name>" --query "value"
```

Agora, criou um Key Vault, armazenou um segredo e obteve-o.

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-delete-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido criaste um Cofre-Chave e guardaste um segredo nele. Para saber mais sobre o Key Vault e como integrá-lo com as suas aplicações, continue para os artigos abaixo.

- Leia uma [visão geral do cofre da chave Azure](../general/overview.md)
- Saiba como [armazenar segredos multiline em Key Vault](multiline-secrets.md)
- Consulte a referência para os [comandos Azure CLI az keyvault](/cli/azure/keyvault)
- Reveja a visão geral da [segurança do Cofre-Chave](../general/security-features.md)
