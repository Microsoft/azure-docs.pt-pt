---
title: Criar e recuperar atributos de uma chave no Cofre da Chave Azure - Azure CLI
description: Quickstart mostrando como definir e recuperar uma chave do Azure Key Vault usando Azure CLI
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.date: 03/30/2020
ms.author: mbaldwin
ms.custom: devx-track-azurecli
ms.openlocfilehash: 19c4a966bb4099e70efea224127bc9b7292ffd0a
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2020
ms.locfileid: "91940553"
---
# <a name="quickstart-set-and-retrieve-a-key-from-azure-key-vault-using-azure-cli"></a>Quickstart: set and recuperar uma chave do Azure Key Vault usando Azure CLI

Neste arranque rápido, você cria um cofre chave em Azure Key Vault com Azure CLI. O Azure Key Vault é um serviço cloud que funciona como um arquivo de segredos seguro. Pode armazenar chaves, palavras-passe, certificados e outros segredos em segurança. Para mais informações sobre o Key Vault, poderá rever a [Visão Geral.](../general/overview.md) A CLI do Azure é utilizada para criar e gerir recursos do Azure através de comandos ou scripts. Uma vez concluída, guardará uma chave.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este início rápido requer a versão 2.0.4 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

Para iniciar seduca em Azure usando o CLI pode escrever:

```azurecli
az login
```

Para obter mais informações sobre as opções de login através do CLI, dê uma olhada [no login com o Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos. O exemplo seguinte cria um grupo de recursos com o nome *ContosoResourceGroup* na localização *eastus*.

```azurecli
az group create --name "ContosoResourceGroup" --location eastus
```

## <a name="create-a-key-vault"></a>Criar um Key Vault

A seguir, vai criar um Key Vault no grupo de recursos criado no passo anterior. Terá de fornecer algumas informações:

- Para este início rápido, utilizamos **Contoso-vault2**. Tem de fornecer um nome exclusivo no teste.
- Nome de grupo de recursos **ContosoResourceGroup**.
- **E.U.A. Leste** como a localização.

```azurecli
az keyvault create --name "Contoso-Vault2" --resource-group "ContosoResourceGroup" --location eastus
```

O resultado deste cmdlet mostra as propriedades do Key Vault recém-criado. Tome nota das duas propriedades listadas abaixo:

- **Nome do Cofre**: no exemplo, o nome é **Contoso-Vault2**. Irá utilizar este nome para outros comandos do Key Vault.
- **URI do Cofre**: no exemplo, isto é https://contoso-vault2.vault.azure.net/. As aplicações que utilizam o cofre através da respetiva API têm de utilizar este URI.

Nesta altura, a sua conta do Azure é a única autorizada a realizar quaisquer operações neste novo cofre.

## <a name="add-a-key-to-key-vault"></a>Adicione uma chave ao Cofre de Chaves

Para adicionar uma chave ao cofre, só precisa dar alguns passos adicionais. Esta chave pode ser utilizada por uma aplicação. 

Digite os comandos abaixo para criar um **chamado ExemploKey** :

```azurecli
az keyvault key create --vault-name "Contoso-Vault2" -n ExampleKey --protection software
```

Agora pode fazer referência a esta chave que adicionou ao Azure Key Vault utilizando o seu URI. Use **' https://Contoso-Vault2.vault.azure.net/keys/ExampleKey para** obter a versão atual. 

Para ver a chave previamente armazenada:

```azurecli

az keyvault key show --name "ExampleKey" --vault-name "Contoso-Vault2"
```

Criaste um Cofre-Chave, armazenaste uma chave e recuperaste-a.

## <a name="clean-up-resources"></a>Limpar os recursos

Outros inícios rápidos e tutoriais desta coleção têm por base este início rápido. Se quiser continuar a trabalhar com os inícios rápidos e tutoriais subsequentes, pode manter estes recursos.
Quando já não forem necessários, pode utilizar o comando [az group delete](/cli/azure/group) para remover o grupo de recursos e todos os recursos relacionados. Pode eliminar os recursos da seguinte forma:

```azurecli
az group delete --name ContosoResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Neste quickstart criou um Cofre-Chave e guardou uma chave nele. Para saber mais sobre o Key Vault e como integrá-lo com as suas aplicações, continue para os artigos abaixo.

- Leia uma [visão geral do cofre da chave Azure](../general/overview.md)
- Consulte a referência para os [comandos Azure CLI az keyvault](/cli/azure/keyvault?view=azure-cli-latest)
- Rever [as melhores práticas do Azure Key Vault](../general/best-practices.md)
