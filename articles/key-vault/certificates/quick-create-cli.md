---
title: Quickstart - Definir & ver certificados Azure Key Vault com Azure CLI
description: Quickstart mostrando como definir e recuperar um certificado do Azure Key Vault usando Azure CLI
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-azurecli
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: 56e51d74358bcda96a6859a481e53710a6f78ec3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99072426"
---
# <a name="quickstart-set-and-retrieve-a-certificate-from-azure-key-vault-using-azure-cli"></a>Quickstart: set and recuperar um certificado do Azure Key Vault usando Azure CLI

Neste arranque rápido, você cria um cofre chave em Azure Key Vault com Azure CLI. O Azure Key Vault é um serviço cloud que funciona como um arquivo de segredos seguro. Pode armazenar chaves, palavras-passe, certificados e outros segredos em segurança. Para mais informações sobre o Key Vault, poderá rever a [Visão Geral.](../general/overview.md) A CLI do Azure é utilizada para criar e gerir recursos do Azure através de comandos ou scripts. Uma vez que tenha concluído isso, irá guardar um certificado.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Este arranque rápido requer a versão 2.0.4 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [Create a resource group](../../../includes/key-vault-cli-rg-creation.md)]

## <a name="create-a-key-vault"></a>Criar um cofre de chaves

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-kv-creation.md)]

## <a name="add-a-certificate-to-key-vault"></a>Adicione um certificado ao Cofre de Chaves

Para adicionar um certificado ao cofre, só precisa dar alguns passos adicionais. Este certificado pode ser utilizado por um pedido. 

Digite os comandos abaixo para criar um certificado auto-assinado com política padrão chamada **ExemploCertificate** :

```azurecli
az keyvault certificate create --vault-name "<your-unique-keyvault-name>" -n ExampleCertificate -p "$(az keyvault certificate get-default-policy)"
```

Pode agora fazer referência a este certificado que adicionou ao Azure Key Vault utilizando o seu URI. Utilize **'https://<o seu nome>.vault.azure.net/certificates/ExampleCertificate exclusivo-keyvault'** para obter a versão atual. 

Para ver o certificado previamente armazenado:

```azurecli

az keyvault certificate show --name "ExampleCertificate" --vault-name "<your-unique-keyvault-name>"
```

Criaste um Cofre-Chave, armazenaste um certificado e recuperaste-o.

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-delete-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste quickstart criou um Cofre-Chave e guardou um certificado nele. Para saber mais sobre o Key Vault e como integrá-lo com as suas aplicações, continue para os artigos abaixo.

- Leia uma [visão geral do cofre da chave Azure](../general/overview.md)
- Consulte a referência para os [comandos Azure CLI az keyvault](/cli/azure/keyvault)
- Reveja a visão geral da [segurança do Cofre-Chave](../general/security-overview.md)
