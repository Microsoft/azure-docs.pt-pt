---
title: 'Quickstart: Definir e recuperar uma chave do Cofre de Chaves Azure'
description: Quickstart mostrando como definir e recuperar uma chave do Cofre chave Azure usando O Azure CLI
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.date: 03/30/2020
ms.author: mbaldwin
ms.openlocfilehash: defc5317c127d771786989748e404285ca0c0584
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424210"
---
# <a name="quickstart-set-and-retrieve-a-key-from-azure-key-vault-using-azure-cli"></a>Quickstart: Definir e recuperar uma chave do Cofre de Chaves Azure usando o Azure CLI

Neste arranque rápido, cria-se um cofre chave no Cofre chave Azure com o Azure CLI. O Azure Key Vault é um serviço cloud que funciona como um arquivo de segredos seguro. Pode armazenar chaves, palavras-passe, certificados e outros segredos em segurança. Para mais informações sobre o Key Vault poderá rever a [visão geral](../general/overview.md). A CLI do Azure é utilizada para criar e gerir recursos do Azure através de comandos ou scripts. Assim que tiver concluído, armazenará uma chave.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este início rápido requer a versão 2.0.4 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

Para iniciar sessão no Azure utilizando o CLI pode escrever:

```azurecli
az login
```

Para obter mais informações sobre as opções de login através do CLI, dê uma olhada no [login com o Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)

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

## <a name="add-a-key-to-key-vault"></a>Adicione uma chave para o cofre da chave

Para adicionar uma chave ao cofre, só precisa dar alguns passos adicionais. Esta chave pode ser usada por uma aplicação. 

Digite os comandos abaixo para criar um chamado **ExemploChave:**

```azurecli
az keyvault key create --vault-name "Contoso-Vault2" -n ExampleKey --protection software
```

Agora pode fazer referência a esta chave que adicionou ao Cofre de Chaves Azure utilizando o seu URI. Use **https://Contoso-Vault2.vault.azure.net/keys/ExampleKey** para obter a versão atual. 

Para visualizar a chave previamente armazenada:

```azurecli

az keyvault key show --name "ExampleKey" --vault-name "Contoso-Vault2"
```

Agora, criaste um Cofre chave, armazenaste uma chave e recuperaste-a.

## <a name="clean-up-resources"></a>Limpar recursos

Outros inícios rápidos e tutoriais desta coleção têm por base este início rápido. Se quiser continuar a trabalhar com os inícios rápidos e tutoriais subsequentes, pode manter estes recursos.
Quando já não forem necessários, pode utilizar o comando [az group delete](/cli/azure/group) para remover o grupo de recursos e todos os recursos relacionados. Pode eliminar os recursos da seguinte forma:

```azurecli
az group delete --name ContosoResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criaste um Cofre chave e guardaste uma chave nele. Para saber mais sobre o Key Vault e como integrá-lo com as suas aplicações, continue para os artigos abaixo.

- Leia uma [visão geral do Cofre chave Azure](../general/overview.md)
- Consulte a referência para os [comandos do cofre azure CLI az](/cli/azure/keyvault?view=azure-cli-latest)
- Rever [as melhores práticas do Cofre de Chaves Azure](../general/best-practices.md)
