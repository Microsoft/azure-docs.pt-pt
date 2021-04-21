---
title: Atribuir uma política de acesso a azure key vault (CLI)
description: Como utilizar o CLI do Azure para atribuir uma política de acesso ao Cofre chave a um diretor de segurança ou identidade de aplicação.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: 349d7453962a736c9f15bb7d31d5a44098f463a4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791954"
---
# <a name="assign-a-key-vault-access-policy"></a>Atribuir uma política de acesso ao Cofre de Chaves

Uma política de acesso ao Cofre-Chave determina se um dado principal de segurança, nomeadamente um utilizador, aplicação ou grupo de utilizadores, pode realizar diferentes operações em [segredos](../secrets/index.yml)do Cofre chave, [chaves](../keys/index.yml)e [certificados](../certificates/index.yml). Pode atribuir políticas de acesso utilizando o [portal Azure,](assign-access-policy-portal.md)o Azure CLI (este artigo) ou [Azure PowerShell](assign-access-policy-powershell.md).

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

Para obter mais informações sobre a criação de grupos no Azure Ative Directory utilizando o Azure CLI, consulte [o grupo az ad create](/cli/azure/ad/group#az_ad_group_create) e a [az ad group add](/cli/azure/ad/group/member#az_ad_group_member_add).

## <a name="configure-the-azure-cli-and-sign-in"></a>Configure o CLI Azure e inscreva-se

1. Para executar os comandos Azure CLI localmente, instale o [Azure CLI](/cli/azure/install-azure-cli).
 
    Para executar comandos diretamente na nuvem, utilize a Concha da [Nuvem Azure](../../cloud-shell/overview.md).

1. Apenas CLI local: inscreva-se no Azure `az login` utilizando:

    ```bash
    az login
    ```

    O `az login` comando abre uma janela do navegador para recolher credenciais, se necessário.

## <a name="acquire-the-object-id"></a>Adquira o ID do objeto

Determine o ID do objeto da aplicação, grupo ou utilizador ao qual pretende atribuir a política de acesso:

- Aplicações e outros principais serviços: utilize o comando [da lista ad sp az](/cli/azure/ad/sp#az_ad_sp_list) para recuperar os seus principais serviços. Examine a saída do comando para determinar a identificação do objeto do principal de segurança ao qual pretende atribuir a política de acesso.

    ```azurecli-interactive
    az ad sp list --show-mine
    ```

- Grupos: utilize o comando [da lista de anúncios az,](/cli/azure/ad/group#az_ad_group_list) filtrando os resultados com o `--display-name` parâmetro:

     ```azurecli-interactive
    az ad group list --display-name <search-string>
    ```

- Utilizadores: utilize o comando de exibição de [anúncios az,](/cli/azure/ad/user#az_ad_user_show) passando o endereço de e-mail do utilizador no `--id` parâmetro:

    ```azurecli-interactive
    az ad user show --id <email-address-of-user>
    ```

## <a name="assign-the-access-policy"></a>Atribuir a política de acesso
    
Utilize o comando [de definição de chave-tevault az](/cli/azure/keyvault#az_keyvault_set_policy) para atribuir as permissões desejadas:

```azurecli-interactive
az keyvault set-policy --name myKeyVault --object-id <object-id> --secret-permissions <secret-permissions> --key-permissions <key-permissions> --certificate-permissions <certificate-permissions>
```

`<object-id>`Substitua-o pela identificação do objeto do seu principal de segurança.

Só é necessário incluir `--secret-permissions` , e ao atribuir `--key-permissions` `--certificate-permissions` permissões a esses tipos específicos. Os valores admissíveis para `<secret-permissions>` `<key-permissions>` , e são `<certificate-permissions>` dados na documentação [de definição de keyvault az.](/cli/azure/keyvault#az_keyvault_set_policy)

## <a name="next-steps"></a>Passos seguintes

- [Segurança do Cofre Azure Key: Gestão de identidade e acesso](security-overview.md#identity-management)
- [Prenda o cofre da chave.](security-overview.md)
- [Guia de desenvolvedores do Azure Key Vault](developers-guide.md)