---
title: Use uma identidade gerida atribuída ao sistema para aceder ao Cofre da Chave Azure
description: Saiba como criar uma identidade gerida para aplicações do App Service e como usá-la para aceder ao Cofre da Chave Azure
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 0c81f88821d841002ef2489c727071e9629da9e1
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750770"
---
# <a name="provide-key-vault-authentication-with-a-managed-identity"></a>Fornecer autenticação key vault com uma identidade gerida

Uma identidade gerida a partir do Azure Ative Directory permite que a sua app aceda facilmente a outros recursos protegidos por Azure. A identidade é gerida pela plataforma Azure e não requer que forneça ou rode quaisquer segredos. Para obter mais informações, consulte [identidades geridas para recursos Azure](../../active-directory/managed-identities-azure-resources/overview.md). 

Este artigo mostra-lhe como criar uma identidade gerida para uma aplicação de Serviço de Aplicações e usá-la para aceder ao Cofre da Chave Azure. Para aplicações hospedadas em VMs Azure, consulte [utilizar uma identidade gerida do sistema Windows VM para aceder ao Cofre da Chave Azure](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md).


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Pré-requisitos 

Para completar este guia, você deve ter os seguintes recursos. 

- Um cofre. Você pode usar um cofre de chaves existente, ou criar um novo seguindo os passos em um destes arranques rápidos:
   - [Crie um cofre-chave com o Azure CLI](../secrets/quick-create-cli.md)
   - [Crie um cofre com Azure PowerShell](../secrets/quick-create-powershell.md)
   - [Crie um cofre-chave com o portal Azure](../secrets/quick-create-portal.md).
- Uma aplicação existente do Serviço de Aplicações para conceder acesso ao cofre chave. Pode criar rapidamente um seguindo os passos na documentação do [Serviço de Aplicações.](../../app-service/overview.md)
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) ou [Azure PowerShell](/powershell/azure/). Em alternativa, pode utilizar o [portal Azure](https://portal.azure.com).


## <a name="adding-a-system-assigned-identity"></a>Adicionar uma identidade atribuída ao sistema 

Primeiro, deve adicionar uma identidade atribuída ao sistema a uma aplicação. 
 
### <a name="azure-portal"></a>Portal do Azure 

Para configurar uma identidade gerida no portal, irá primeiro criar uma aplicação normal e depois ativar a funcionalidade. 

1. Se utilizar uma aplicação de função, navegue para **as funcionalidades da Plataforma.** Para outros tipos de **aplicativos,** desloque-se até ao grupo Definições na navegação à esquerda. 

1. Selecione **identidade gerida**. 

1. Dentro do separador **Designado sistema,** **altere o Estado** para **ligar**. Clique em **Guardar**. 

   ![Screenshot que mostra salvar uma identidade atribuída ao sistema.](../media/managed-identity-system-assigned.png)

### <a name="azure-cli"></a>CLI do Azure

Este arranque rápido requer a versão Azure CLI 2.0.4 ou posterior. Execute `az --version` para encontrar a versão atual. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest). 

Para iniciar sessão com o Azure CLI, utilize o comando [de login az:](/cli/azure/reference-index?view=azure-cli-latest#az-login)

```azurecli-interactive
az login
```

Para obter mais informações sobre as opções de login com o Azure CLI, consulte [Iniciar sessão com o Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest). 

Para criar a identidade para esta aplicação, utilize o comando de atribuição de identidade Azure CLI [az webapp](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) ou o comando [de atribuição de identidade az functionapp:](/cli/azure/functionapp/identity?view=azure-cli-latest#az-functionapp-identity-assign)


```azurecli-interactive
az webapp identity assign --name myApp --resource-group myResourceGroup
```

```azurecli-interactive
az functionapp identity assign --name myApp --resource-group myResourceGroup
```

Tome nota do `PrincipalId` , que será necessário na próxima secção.

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```
## <a name="grant-your-app-access-to-key-vault"></a>Conceda à sua aplicação acesso ao Key Vault 

### <a name="azure-portal"></a>Portal do Azure

1.  Navegue para o recurso Key Vault. 

1.  Selecione **as políticas de acesso** e clique em Adicionar Política de **Acesso.** 

1.  Em **permissões secretas**, selecione **Get, List**. 

1.  Escolha **Select Principal**, e no campo de pesquisa insira o nome da aplicação.  Selecione a aplicação na lista de resultados e clique em **Selecionar**. 

1.  Clique **em Adicionar** para terminar a adição da nova política de acesso.

    ![Screenshot que mostra a adição de uma nova política de acesso no portal Azure.](../media/managed-identity-access-policy.png)

### <a name="azure-cli"></a>CLI do Azure

Para garantir o acesso à sua aplicação ao seu cofre chave, utilize o comando [de definição de chave Azure](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) CLI az, fornecendo o parâmetro **ObjectId** com o **principal** que observou acima.

```azurecli-interactive
az keyvault set-policy --name myKeyVault --object-id <PrincipalId> --secret-permissions get list 
```

## <a name="next-steps"></a>Passos seguintes

- [Segurança do Cofre Azure Key: Gestão de identidade e acesso](overview-security.md#identity-and-access-management)
- [Fornecer autenticação key vault com uma política de controlo de acesso](group-permissions-for-apps.md)
- [Prenda o cofre da chave.](secure-your-key-vault.md)
- [Guia de desenvolvedores do Azure Key Vault](developers-guide.md)
- Rever [as melhores práticas do Azure Key Vault](best-practices.md)
