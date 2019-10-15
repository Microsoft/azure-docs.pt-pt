---
title: 'Início rápido: definir e recuperar um segredo de Azure Key Vault'
description: Início Rápido que mostra como definir e obter um segredo do Azure Key Vault com a CLI do Azure
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019
ms.date: 09/03/2019
ms.author: mbaldwin
ms.openlocfilehash: f005266b6e2e12a13f79b3f2007b1bbbafb4acef
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2019
ms.locfileid: "72311556"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-azure-cli"></a>Início Rápido: Definir e obter um segredo do Azure Key Vault com a CLI do Azure

Neste guia de início rápido, você cria um cofre de chaves em Azure Key Vault com CLI do Azure. O Azure Key Vault é um serviço cloud que funciona como um arquivo de segredos seguro. Pode armazenar chaves, palavras-passe, certificados e outros segredos em segurança. Para obter mais informações sobre o Key Vault, pode ver a [Descrição Geral](key-vault-overview.md). A CLI do Azure é utilizada para criar e gerir recursos do Azure através de comandos ou scripts. Depois de concluir este passo, irá armazenar um segredo.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este início rápido requer a versão 2.0.4 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

Para entrar no Azure usando a CLI, você pode digitar:

```azurecli
az login
```

Para obter mais informações sobre as opções de logon por meio da CLI, dê uma olhada na [entrada com CLI do Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos. O exemplo seguinte cria um grupo de recursos com o nome *ContosoResourceGroup* na localização *eastus*.

```azurecli
az group create --name "ContosoResourceGroup" --location eastus
```

## <a name="create-a-key-vault"></a>Criar um Key Vault

A seguir, vai criar um Key Vault no grupo de recursos criado no passo anterior. Terá de fornecer algumas informações:

- Para este início rápido, utilizamos **Contoso-vault2**. Tem de fornecer um nome exclusivo no teste.
- **ContosoResourceGroup** como o nome do grupo de recursos.
- **E.U.A. Leste** como a localização.

```azurecli
az keyvault create --name "Contoso-Vault2" --resource-group "ContosoResourceGroup" --location eastus
```

O resultado deste cmdlet mostra as propriedades do Key Vault recém-criado. Tome nota das duas propriedades listadas abaixo:

- **Nome do Cofre**: no exemplo, o nome é **Contoso-Vault2**. Irá utilizar este nome para outros comandos do Key Vault.
- **URI do Cofre**: no exemplo, isto é https://contoso-vault2.vault.azure.net/. As aplicações que utilizam o cofre através da respetiva API têm de utilizar este URI.

Nesta altura, a sua conta do Azure é a única autorizada a realizar quaisquer operações neste novo cofre.

## <a name="add-a-secret-to-key-vault"></a>Adicionar um segredo ao Key Vault

Para adicionar um segredo ao cofre, apenas tem de efetuar alguns passos adicionais. Esta palavra-passe pode ser utilizada por uma aplicação. A senha será chamada de **ExamplePassword** e armazenará o valor de **hVFkk965BuUv** nela.

Digite os comandos abaixo para criar um segredo em Key Vault chamado **ExamplePassword** , que armazenará o valor **hVFkk965BuUv** :

```azurecli
az keyvault secret set --vault-name "Contoso-Vault2" --name "ExamplePassword" --value "hVFkk965BuUv"
```

Agora, pode referenciar esta palavra-passe que adicionou ao Azure Key Vault com o respetivo URI. Utilize **https://ContosoVault.vault.azure.net/secrets/ExamplePassword** para obter a versão atual. 

Para ver o valor contido no segredo como texto simples:

```azurecli
az keyvault secret show --name "ExamplePassword" --vault-name "Contoso-Vault2"
```

Agora, criou um Key Vault, armazenou um segredo e obteve-o.

## <a name="clean-up-resources"></a>Limpar recursos

Outros inícios rápidos e tutoriais desta coleção têm por base este início rápido. Se quiser continuar a trabalhar com os inícios rápidos e tutoriais subsequentes, pode manter estes recursos.
Quando já não forem necessários, pode utilizar o comando [az group delete](/cli/azure/group) para remover o grupo de recursos e todos os recursos relacionados. Pode eliminar os recursos da seguinte forma:

```azurecli
az group delete --name ContosoResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, você criou um Key Vault e armazenou um segredo nele. Para saber mais sobre Key Vault e como integrá-lo a seus aplicativos, continue nos artigos abaixo.

- Leia uma [visão geral do Azure Key Vault](key-vault-overview.md)
- Consulte a referência para os [comandos do CLI do Azure AZ keyvault](/cli/azure/keyvault?view=azure-cli-latest)
- Saiba mais sobre [chaves, segredos e certificados](about-keys-secrets-and-certificates.md)
- Examinar [Azure Key Vault práticas recomendadas](key-vault-best-practices.md)
