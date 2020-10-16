---
title: Gestão gerida da gestão de funções de plano de dados HSM - Azure Key Vault / Microsoft Docs
description: Utilize este artigo para gerir atribuições de funções para o seu HSM gerido
services: key-vault
author: amitbapat
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 814167425fcd39e90edccd952e1a3e4fbd570988
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91818033"
---
# <a name="managed-hsm-role-management"></a>Gestão de funções do HSM Gerido

> [!NOTE]
> O Key Vault suporta dois tipos de recursos: cofres e HSMs geridos. Este artigo é sobre **O HSM Gerido.** Se quiser aprender a gerir um cofre, consulte [o Key Vault utilizando o Azure CLI](../general/manage-with-cli2.md).

Para uma visão geral do HSM gerido, veja [o que é O HSM gerido?](overview.md) Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Este artigo mostra-lhe como gerir funções para um plano de dados gerido do HSM. Para saber mais sobre o modelo de controlo de acesso gerido do HSM, consulte [o controlo de acesso gerido do HSM.](access-control.md)

Para permitir que um principal de segurança (como um utilizador, um principal de serviço, um grupo ou uma identidade gerida) execute operações geridas de planos de dados HSM, deve ser-lhes atribuída uma função que permita a realização dessas operações. Por exemplo, se pretender permitir que uma aplicação execute uma operação de sinalização utilizando uma chave, deve ser atribuída uma função que contenha a "Microsoft.KeyVault/managedHSM/keys/sign/action" como uma das ações de dados. Uma função pode ser atribuída num âmbito específico. O RBAC local gerido do HSM suporta dois âmbitos, à escala de HSM `/` `/keys` (ou) e por chave ( `/keys/<keyname>` ).

Para obter uma lista de todas as funções integradas do HSM geridas e as operações que permitem, consulte [as funções integradas do HSM geridas.](built-in-roles.md)

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar os comandos Azure CLI neste artigo, tem de ter os seguintes itens:

* Uma subscrição ao Microsoft Azure. Se não tiver uma, pode inscrever-se numa [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial).
* A versão Azure CLI 2.12.0 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).
* Um HSM gerido na sua subscrição. Consulte [Quickstart: Provisão e ativação de um HSM gerido utilizando o Azure CLI](quick-create-cli.md) para provisões e ativar um HSM gerido.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Para iniciar seduca em Azure usando o CLI pode escrever:

```azurecli
az login
```

Para obter mais informações sobre as opções de login através do CLI, consulte [o login com o Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest&preserve-view=true)

## <a name="create-a-new-role-assignment"></a>Criar uma nova atribuição de funções

### <a name="assign-roles-for-all-keys"></a>Atribuir funções para todas as teclas

Utilize `az keyvault role assignment create` o comando para atribuir uma função de **Crypto Officer gerido** ao utilizador identificado pelo utilizador principal do utilizador ** \@ contoso.com** para todas as  **teclas** (âmbito) no `/keys` ContosoHSM.

```azurecli-interactive
az keyvault role assignment create --hsm-name ContosoMHSM --role "Managed HSM Crypto Officer" --assignee user2@contoso.com  --scope /keys
```

### <a name="assign-role-for-a-specific-key"></a>Atribuir papel para uma chave específica

Utilize `az keyvault role assignment create` o comando para atribuir uma função de **Crypto Officer gerido** ao utilizador identificado pelo utilizador principal do utilizador ** \@ contoso.com** para uma chave específica chamada **myrsakey**.

```azurecli-interactive
az keyvault role assignment create --hsm-name ContosoMHSM --role "Managed HSM Crypto Officer" --assignee user2@contoso.com  --scope /keys/myrsakey
```

## <a name="list-existing-role-assignments"></a>Listar atribuições de funções existentes

Use `az keyvault role assignment list` para listar atribuições de funções.

Todas as atribuições de funções no âmbito / (predefinição quando não é especificado o âmbito de aplicação) para todos os utilizadores (padrão quando --atribuídor é especificado)

```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM
```

Todas as atribuições de funções ao nível do HSM para um utilizador **user1@contoso.com** específico.

```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM --assignee user@contoso.com
```

Todas as atribuições de funções para um utilizador específico **user2@contoso.com** para uma oportunidade **de**chave específica .

```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM --assignee user2@contoso.com --scope /keys/myrsakey
```

Uma atribuição de funções específica para **função Gerido HSM Crypto Officer** para um utilizador específico **user2@contoso.com** para uma **oportunidade** específica


```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM --assignee user2@contoso.com --scope /keys/myrsakey --role "Managed HSM Crypto Officer"
```

## <a name="delete-a-role-assignment"></a>Excluir uma atribuição de funções

Utilize `az keyvault role assignment delete` o comando para eliminar uma **função gerida do Oficial de Cripto HSM** atribuída ao **utilizador2 \@ contoso.com** para a chave **myrsakey2**.

```azurecli-interactive
az keyvault role assignment delete --hsm-name ContosoMHSM --role "Managed HSM Crypto Officer" --assignee user2@contoso.com  --scope /keys/myrsakey2
```

## <a name="list-all-available-role-definitions"></a>Listar todas as definições de funções disponíveis

Use `az keyvault role definition list` o comando para listar todas as definições de funções.

```azurecli-interactive
az keyvault role definition list --hsm-name ContosoMHSM
```

## <a name="next-steps"></a>Passos seguintes

- Consulte uma visão geral do [controlo de acesso baseado em funções Azure (RBAC)](../../role-based-access-control/overview.md).
- Veja um tutorial sobre [gestão de funções gerida do HSM](role-management.md)
- Saiba mais sobre [o modelo de controlo de acesso gerido do HSM](access-control.md)
- Veja todas as [funções incorporadas para o CSC local gerido do HSM](built-in-roles.md)
