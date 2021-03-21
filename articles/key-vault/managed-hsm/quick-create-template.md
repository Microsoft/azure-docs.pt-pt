---
title: Azure Quickstart - Criar um HSM gerido usando um modelo de Gestor de Recursos Azure
description: Quickstart mostrando como criar Azure um Azure Key Vault Gerido HSM usando o modelo de Gestor de Recursos
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli
ms.date: 09/15/2020
ms.author: mbaldwin
ms.openlocfilehash: d9fb9e0221ad6a5749899c89bbd9dc5631e7a91c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102213271"
---
# <a name="quickstart-create-an-key-vault-managed-hsm-using-an-azure-resource-manager-template"></a>Quickstart: Criar um Cofre chave gerido HSM usando um modelo de Gestor de Recursos Azure

O HSM gerido é um serviço de nuvem totalmente gerido, altamente disponível, de inquilino único, que permite salvaguardar chaves criptográficas para as suas aplicações em nuvem, utilizando HSMs validados **FIPS 140-2 Nível 3.**  

Este quickstart foca-se no processo de implantação de um modelo de Gestor de Recursos para criar um HSM gerido.  O [modelo do Resource Manager](../../azure-resource-manager/templates/overview.md) é um ficheiro JavaScript Object Notation (JSON) que define a infraestrutura e a configuração do seu projeto. O modelo utiliza a sintaxe declarativa, que permite afirmar o que quer implementar sem ter de escrever a sequência de comandos de programação para a criar. Se quiser saber mais sobre o desenvolvimento de modelos do Resource Manager, veja a [documentação do Resource Manager](../../azure-resource-manager/index.yml) e a [referência do modelo](/azure/templates/microsoft.keyvault/allversions).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para completar os passos deste artigo, tem de ter os seguintes itens:

- Uma subscrição ao Microsoft Azure. Se não tiver uma, pode inscrever-se numa [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial).
- A versão Azure CLI 2.12.0 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, consulte [instalar o Azure CLI]( /cli/azure/install-azure-cli)


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Para iniciar seduca em Azure usando o CLI pode escrever:

```azurecli
az login
```

Para obter mais informações sobre as opções de login através do CLI, consulte [o login com o Azure CLI](/cli/azure/authenticate-azure-cli)

## <a name="create-a-manage-hsm"></a>Criar um HSM de gestão

O modelo utilizado neste início rápido pertence aos [modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-managed-hsm-create/).

O recurso Azure definido no modelo:

* **Microsoft.KeyVault/managedHSMs**: crie um Cofre de Chaves Azure gerido HSM.

Mais amostras de modelo de azure key vault podem ser encontradas [aqui.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault)

O modelo requer o ID do objeto associado à sua conta. Para encontrá-lo, utilize o comando de [exibição de anúncios](/cli/azure/ad/user#az_ad_user_show) Azure CLI az, passando o seu endereço de e-mail para o `--id` parâmetro. Pode limitar a saída ao ID do objeto apenas com o `--query` parâmetro.

```azurecli-interactive
az ad user show --id <your-email-address> --query "objectId"
```

Você também pode precisar da identificação do seu inquilino. Para encontrá-lo, utilize o comando de [exibição de anúncios](/cli/azure/account#az_account_show) Azure CLI az. Pode limitar a saída à identificação do inquilino apenas com o `--query` parâmetro.

 ```azurecli-interactive
 az account show --query "tenantId"
 ```

1. Selecione a imagem seguinte para iniciar sessão no Azure e abrir um modelo. O modelo cria um cofre chave e um segredo.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-managed-hsm-create%2Fazuredeploy.json"><img src="../media/deploy-to-azure.svg" alt="deploy to azure"/></a>

2. Selecione ou introduza os seguintes valores.

    A menos que seja especificado, use o valor padrão para criar o cofre da chave e um segredo.

    - **Assinatura**: Selecione uma subscrição Azure.
    - **Grupo de recursos**: Selecione **Criar novo,** insira um nome único para o grupo de recursos e, em seguida, clique em **OK**.
    - **Localização**: Selecione uma localização. Por exemplo, **South Central US**.
    - **nome geridoHSM :** Introduza um nome para o seu HSM gerido.
    - **ID do inquilino**: A função do modelo recupera automaticamente o ID do seu inquilino; não altere o valor padrão.  Se não houver valor, insira o ID do Inquilino que recuperou em [Pré-requisitos.](#prerequisites)
    * **InicialmenteAdminObjectIds**: Introduza o ID do objeto que recuperou em [Pré-requisitos](#prerequisites).

3. Selecione **Comprar**. Depois de o cofre da chave ter sido implantado com sucesso, recebe uma notificação:

O portal do Azure é utilizado para implementar o modelo. Além do portal Azure, também pode utilizar o Azure PowerShell, Azure CLI e REST API. Para aprender outros métodos de implementação, consulte [os modelos de implementação](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um HSM gerido. Este HSM gerido não estará totalmente funcional até que seja ativado. Consulte [Ativar o seu HSM gerido](quick-create-cli.md#activate-your-managed-hsm) para aprender a ativar o seu HSM.

- Leia uma [visão geral do HSM gerido](overview.md)
- Saiba mais [sobre gerir chaves num HSM gerido](key-management.md)
- Rever [as melhores práticas do HSM gerido](best-practices.md)
