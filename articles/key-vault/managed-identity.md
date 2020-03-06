---
title: Use uma identidade gerida atribuída ao sistema para aceder ao Cofre chave Azure
description: Saiba como criar uma identidade gerida para aplicações de Serviço de Aplicações e como usá-la para aceder ao Cofre chave Azure
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 36a4871339401629300eedd77b6441aed10aabf3
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78395937"
---
# <a name="provide-key-vault-authentication-with-a-managed-identity"></a>Forneça a autenticação do Cofre Chave com uma identidade gerida

Uma identidade gerida do Azure Ative Directory permite que a sua aplicação aceda facilmente a outros recursos protegidos por AD Azure. A identidade é gerida pela plataforma Azure e não o obriga a fornecer ou rodar quaisquer segredos. Para mais informações, consulte [identidades geridas para recursos Azure](../active-directory/managed-identities-azure-resources/overview.md). 

Este artigo mostra-lhe como criar uma identidade gerida para uma aplicação do Serviço de Aplicações e usá-la para aceder ao Cofre chave Azure. Para aplicações hospedadas em VMs Azure, consulte Utilize uma identidade gerida com sistema [Windows VM para aceder ao Cofre de Chaves Azure](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md).


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Pré-requisitos 

Para completar este guia, deve dispor dos seguintes recursos. 

- Um cofre chave. Você pode usar um cofre chave existente, ou criar um novo seguindo os passos em um destes quickstarts:
   - [Crie um cofre chave com o Azure CLI](quick-create-cli.md)
   - [Crie um cofre chave com Azure PowerShell](quick-create-powershell.md)
   - [Crie um cofre chave com o portal Azure.](quick-create-portal.md)
- Uma aplicação existente do Serviço de Aplicações para a qual concede acesso ao cofre chave. Pode criar rapidamente um seguindo os passos na documentação do Serviço de [Aplicações.](../app-service/overview.md)
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) ou [Azure PowerShell.](/powershell/azure/overview) Em alternativa, pode utilizar o [portal Azure.](https://portal.azure.com)


## <a name="adding-a-system-assigned-identity"></a>Adicionar uma identidade atribuída ao sistema 

Em primeiro lugar, deve adicionar uma identidade atribuída ao sistema a uma aplicação. 
 
### <a name="azure-portal"></a>Portal do Azure 

Para configurar uma identidade gerida no portal, primeiro criará uma aplicação normalmente e, em seguida, ativará a funcionalidade. 

1. Se utilizar uma aplicação de função, navegue para **as funcionalidades da Plataforma**. Para outros tipos de aplicações, desloque-se para o grupo **Definições** na navegação à esquerda. 

1. Selecione **identidade gerida**. 

1. Dentro do separador designado pelo **Sistema,** mude o **Estado** para **o ligado**. Clique em **Guardar**. 

    ![](./media/managed-identity-system-assigned.png)

### <a name="azure-cli"></a>CLI do Azure

Este quickstart requer a versão Azure CLI 2.0.4 ou posterior. Execute `az --version` para encontrar a versão atual. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest). 

Para iniciar sessão com o Azure CLI, utilize o comando [de login az:](/cli/azure/reference-index?view=azure-cli-latest#az-login)

```azurecli-interactive
az login
```

Para obter mais informações sobre as opções de login com o Azure CLI, consulte [Iniciar sessão com o Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest). 

Para criar a identidade para esta aplicação, utilize o comando de [](/cli/azure/functionapp/identity?view=azure-cli-latest#az-functionapp-identity-assign) design de design webapp Azure CLI [az:](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign)


```azurecli-interactive
az webapp identity assign --name myApp --resource-group myResourceGroup
```

```azurecli-interactive
az functionapp identity assign --name myApp --resource-group myResourceGroup
```

Tome nota do `PrincipalId`, que será necessário na próxima secção.

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```
## <a name="grant-your-app-access-to-key-vault"></a>Conceda o acesso da sua aplicação ao Key Vault 

### <a name="azure-portal"></a>Portal do Azure

1.  Navegue para o recurso Key Vault. 

1.  Selecione **políticas** de acesso e clique em **Adicionar Política de Acesso**. 

1.  Em **permissões secretas,** selecione **Get, List**. 

1.  Escolha **Selecionar Principal,** e no campo de pesquisa insira o nome da aplicação.  Selecione a aplicação na lista de resultados e clique em **Selecionar**. 

1.  Clique em **Adicionar** para terminar de adicionar a nova política de acesso.

    ![](./media/managed-identity-access-policy.png)

### <a name="azure-cli"></a>CLI do Azure

Para conceder o acesso à sua aplicação ao seu cofre chave, utilize o comando de definição de definição de teclado Azure CLI [az,](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) fornecendo o parâmetro **ObjectId** com o **principalid** acima referido.

```azurecli-interactive
az keyvault set-policy --name myKeyVault --object-id <PrincipalId> --secret-permissions get list 
```

## <a name="next-steps"></a>Passos seguintes

- [Segurança do Cofre chave Azure: Gestão de identidade e acesso](overview-security.md#identity-and-access-management)
- [Forneça a autenticação do Cofre chave com uma política de controlo de acesso](key-vault-group-permissions-for-apps.md)
- [Sobre chaves, segredos e certificados](about-keys-secrets-and-certificates.md)
- [Proteja o cofre da chave.](key-vault-secure-your-key-vault.md)
- [Guia de desenvolvedor do Cofre de Chaves Azure](key-vault-developers-guide.md)
- Rever [as melhores práticas do Cofre de Chaves Azure](key-vault-best-practices.md)