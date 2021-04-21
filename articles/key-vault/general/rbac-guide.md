---
title: Conceder permissão para pedidos de acesso a um cofre de chaves Azure usando Azure RBAC | Microsoft Docs
description: Saiba como fornecer acesso a chaves, segredos e certificados usando o controlo de acesso baseado em funções Azure.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 04/15/2021
ms.author: mbaldwin
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 9806327c7393c3ba1fdab09acfb5545d6026e5cf
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818467"
---
# <a name="provide-access-to-key-vault-keys-certificates-and-secrets-with-an-azure-role-based-access-control"></a>Fornecer acesso a chaves, certificados e segredos do Cofre Chave com um controlo de acesso baseado em funções Azure

> [!NOTE]
> O fornecedor de recursos Key Vault suporta dois tipos de recursos: **cofres** e **HSMs geridos.** O controlo de acesso descrito neste artigo aplica-se apenas aos **cofres.** Para saber mais sobre o controlo de acessos para o HSM gerido, consulte [o controlo de acesso gerido do HSM.](../managed-hsm/access-control.md)

O Azure role-based access control (Azure RBAC) é um sistema de autorização construído no [Azure Resource Manager](../../azure-resource-manager/management/overview.md) que fornece uma gestão de acesso de granulado fino dos recursos Azure.

O Azure RBAC permite que os utilizadores gerem permissões de Key, Secrets e Certificates. Fornece um lugar para gerir todas as permissões em todos os cofres chave. 

O modelo Azure RBAC fornece a capacidade de definir permissões em diferentes níveis de âmbito: grupo de gestão, subscrição, grupo de recursos ou recursos individuais.  Azure RBAC para cofre chave também fornece a capacidade de ter permissões separadas em chaves, segredos e certificados individuais

Para obter mais informações, consulte [o controlo de acesso baseado em funções Azure (Azure RBAC)](../../role-based-access-control/overview.md).

## <a name="best-practices-for-individual-keys-secrets-and-certificates"></a>Boas Práticas para chaves, segredos e certificados individuais

A nossa recomendação é usar um cofre por aplicação por ambiente (Desenvolvimento, Pré-Produção e Produção).

As permissões individuais de chaves, segredos e certificados devem ser utilizadas apenas para cenários específicos:

-   Aplicações em várias camadas que precisam de separar o controlo de acesso entre camadas

-   Partilha de segredo individual entre múltiplas aplicações

Mais sobre as diretrizes de gestão do Azure Key Vault, consulte:

- [Funcionalidades de segurança Azure Key Vault](security-features.md)
- [Limites de serviço Azure Key Vault](service-limits.md)

## <a name="azure-built-in-roles-for-key-vault-data-plane-operations"></a>Azure funções incorporadas para operações de avião de dados key Vault
> [!NOTE]
> `Key Vault Contributor` papel é para operações de avião de gestão para gerir cofres chave. Não permite o acesso a chaves, segredos e certificados.

| Papel incorporado | Description | ID |
| --- | --- | --- |
| Administrador do Cofre-Chave| Execute todas as operações de avião de dados num cofre chave e todos os objetos nele, incluindo certificados, chaves e segredos. Não é possível gerir os recursos-chave do cofre ou gerir atribuições de funções. Só funciona para cofres-chave que utilizam o modelo de permissão de acesso baseado em funções "Azure". | 00482a5a-887f-4fb3-b363-3b7fe8e74483 |
| Oficial de certificados de cofre chave | Efetue qualquer ação nos certificados de um cofre chave, exceto gerir permissões. Só funciona para cofres-chave que utilizam o modelo de permissão de acesso baseado em funções "Azure". | a4417e6f-fecd-4de8-b567-7b0420556985 |
| Oficial cripto-cofre chave | Efetue qualquer ação nas chaves de um cofre, exceto gerir permissões. Só funciona para cofres-chave que utilizam o modelo de permissão de acesso baseado em funções "Azure". | 14b46e9e-c2b7-41b4-b07b-48a6ebf60603 |
| Utilizador de encriptação de serviço crypto de cofre chave | Leia os metadados das teclas e execute operações de embrulho/desembrulhar. Só funciona para cofres-chave que utilizam o modelo de permissão de acesso baseado em funções "Azure". | e147488a-f6f5-4113-8e2d-b22465e65bf6 |
| Utilizador crypto do cofre de chaves  | Efetue operações criptográficas com teclas. Só funciona para cofres-chave que utilizam o modelo de permissão de acesso baseado em funções "Azure". | 12338af0-0e69-4776-bea7-57ae8d297424 |
| Leitor de cofre de chaves | Leia metadados de cofres-chave e certificados, chaves e segredos. Não é possível ler valores sensíveis, tais como conteúdo secreto ou material chave. Só funciona para cofres-chave que utilizam o modelo de permissão de acesso baseado em funções "Azure". | 21090545-7ca7-4776-b22c-e363652d74d2 |
| Oficial dos Segredos do Cofre| Execute qualquer ação sobre os segredos de um cofre, exceto gerir permissões. Só funciona para cofres-chave que utilizam o modelo de permissão de acesso baseado em funções "Azure". | b86a8fe4-44ce-4948-aee5-eccb2c155cd7 |
| Utilizador de segredos de cofre chave | Leia conteúdo secreto. Só funciona para cofres-chave que utilizam o modelo de permissão de acesso baseado em funções "Azure". | 4633458b-17de-408a-b874-0445c86b69e6 |

Para obter mais informações sobre as definições de funções incorporadas do [Azure, consulte as funções incorporadas do Azure.](../../role-based-access-control/built-in-roles.md)

## <a name="using-azure-rbac-secret-key-and-certificate-permissions-with-key-vault"></a>Usando permissões secretas, chave e certificados Azure RBAC com Key Vault

O novo modelo de permissão Azure RBAC para o cofre de chaves fornece alternativa ao modelo de permissões de política de acesso ao cofre. 

### <a name="prerequisites"></a>Pré-requisitos

Para adicionar atribuições de funções, você deve ter:

- Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- `Microsoft.Authorization/roleAssignments/write` e `Microsoft.Authorization/roleAssignments/delete` permissões, tais como [Administrador de Acesso ao Utilizador](../../role-based-access-control/built-in-roles.md#user-access-administrator) ou [Proprietário](../../role-based-access-control/built-in-roles.md#owner)

### <a name="enable-azure-rbac-permissions-on-key-vault"></a>Ativar permissões Azure RBAC no Cofre de Chaves

> [!NOTE]
> Alterar o modelo de permissão requer permissão de 'Microsoft.Authorization/roleAssignments/write', que faz parte das funções de Administrador de [Acesso](../../role-based-access-control/built-in-roles.md#user-access-administrator) ao Utilizador e [Ao Proprietário.](../../role-based-access-control/built-in-roles.md#owner) As funções clássicas de administrador de subscrição como "Administrador de Serviço" e "Coadministrador" não são suportadas.

1.  Ativar permissões Azure RBAC em novo cofre chave:

    ![Ativar permissões Azure RBAC - novo cofre](../media/rbac/image-1.png)

2.  Ativar permissões Azure RBAC no cofre de chaves existente:

    ![Ativar permissões Azure RBAC - cofre existente](../media/rbac/image-2.png)

> [!IMPORTANT]
> Definir o modelo de permissão Azure RBAC invalida todas as permissões de políticas de acesso. Pode causar interrupções quando não são atribuídas funções equivalentes do Azure.

### <a name="assign-role"></a>Atribuir função

> [!Note]
> É recomendado usar o ID de papel único em vez do nome de papel nos scripts. Portanto, se um papel for renomeado, os seus scripts continuarão a funcionar. Neste documento o nome da função é utilizado apenas para a legibilidade.

Executar o seguinte comando para criar uma atribuição de função:

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --scope <scope>
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
#Assign by User Principal Name
New-AzRoleAssignment -RoleDefinitionName <role_name> -SignInName <assignee_upn> -Scope <scope>

#Assign by Service Principal ApplicationId
New-AzRoleAssignment -RoleDefinitionName Reader -ApplicationId <applicationId> -Scope <scope>
```
---

No portal Azure, o ecrã de atribuições de funções Azure está disponível para todos os recursos no separador Access control (IAM).

![Atribuição de funções - separador (IAM)](../media/rbac/image-3.png)

### <a name="resource-group-scope-role-assignment"></a>Atribuição de função de âmbito de grupo de recursos

1.  Vá para o Grupo de Recursos do Cofre chave.
    ![Atribuição de funções - grupo de recursos](../media/rbac/image-4.png)

2.  Clique em controlo de acesso (IAM) \> Atribuição de função de adicionar \>

3.  Criar o papel de leitor de cofre de chave "Key Vault Reader" para o utilizador atual

    ![Adicionar papel - grupo de recursos](../media/rbac/image-5.png)

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
```azurecli
az role assignment create --role "Key Vault Reader" --assignee {i.e user@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
#Assign by User Principal Name
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Reader' -SignInName {i.e user@microsoft.com} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}

#Assign by Service Principal ApplicationId
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Reader' -ApplicationId {i.e 8ee5237a-816b-4a72-b605-446970e5f156} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}
```
---

Acima da atribuição de funções fornece a capacidade de listar objetos chave do cofre no cofre de chaves.

### <a name="key-vault-scope-role-assignment"></a>Atribuição de função de âmbito de abas de âmbito do Cofre-Chave

1. Aceda ao separador Key Vault \> Access Control (IAM)

2. Clique em Adicionar a atribuição de função \> de adicionar

3. Crie o papel de Key Secrets Officer "Key Vault Secrets Officer" para o utilizador atual.

    ![Atribuição de funções - cofre chave](../media/rbac/image-6.png)

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
```azurecli
az role assignment create --role "Key Vault Secrets Officer" --assignee {i.e jalichwa@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
#Assign by User Principal Name
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Secrets Officer' -SignInName {i.e user@microsoft.com} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}

#Assign by Service Principal ApplicationId
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Secrets Officer' -ApplicationId {i.e 8ee5237a-816b-4a72-b605-446970e5f156} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}
```
---

Depois de criar a atribuição de funções acima, pode criar/atualizar/eliminar segredos.

4. Crie um novo segredo (Secrets \> +Generate/Import) para testar a atribuição de funções de nível secreto.

    ![Adicionar função - cofre chave](../media/rbac/image-7.png)

### <a name="secret-scope-role-assignment"></a>Atribuição de função de âmbito secreto

1. Abra um dos segredos previamente criados, note o Overview e o controlo de acesso (IAM) 

2. Clique no separador controlo de acesso (IAM)

    ![Atribuição de funções - segredo](../media/rbac/image-8.png)

3. Crie o papel de Key Secrets Officer "Key Vault Secrets Officer" para o utilizador atual, tal como foi feito acima para o Cofre de Chaves.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
```azurecli
az role assignment create --role "Key Vault Secrets Officer" --assignee {i.e user@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}/secrets/RBACSecret
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
#Assign by User Principal Name
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Secrets Officer' -SignInName {i.e user@microsoft.com} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}/secrets/RBACSecret

#Assign by Service Principal ApplicationId
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Secrets Officer' -ApplicationId {i.e 8ee5237a-816b-4a72-b605-446970e5f156} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}/secrets/RBACSecret
```
---

### <a name="test-and-verify"></a>Testar e verificar

> [!NOTE]
> Os navegadores usam caching e a atualização de página é necessária após a remoção de atribuições de funções.<br>
> Deixe vários minutos para atribuições de funções para refrescar

1. Valide a adição de novo segredo sem o papel de "Key Vault Secrets Officer" no nível do cofre chave.

Vá ao separador key vault Access control (IAM) e remova a atribuição de função "Key Vault Secrets Officer" para este recurso.

![Remoção da atribuição - cofre de chaves](../media/rbac/image-9.png)

Navegue para o segredo previamente criado. Pode ver todas as propriedades secretas.

![Vista secreta com acesso](../media/rbac/image-10.png)

Criar um novo segredo (Segredos \> +Gerar/Importar) deve mostrar abaixo o erro:

   ![Criar novo segredo](../media/rbac/image-11.png)

2.  Valide a edição secreta sem o papel de "Key Vault Secret Officer" a nível secreto.

-   Aceda ao separador Secret Access Control (IAM) previamente criado e remova a atribuição de funções de "Key Vault Secrets Officer" para este recurso.

-   Navegue para o segredo previamente criado. Pode ver propriedades secretas.

   ![Vista secreta sem acesso](../media/rbac/image-12.png)

3. Validar segredos lidos sem papel do leitor no nível do cofre chave.

-   Vá ao separador do grupo de recursos do cofre chave Access control (IAM) e remova a atribuição de função "Key Vault Reader".

-   Navegar para o separador Segredos do cofre da chave deve mostrar abaixo o erro:

   ![Separador secreto - erro](../media/rbac/image-13.png)

### <a name="creating-custom-roles"></a>Criação de papéis personalizados 

[definição de função az criar comando](/cli/azure/role/definition#az_role_definition_create)

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
```azurecli
az role definition create --role-definition '{ \
   "Name": "Backup Keys Operator", \
   "Description": "Perform key backup/restore operations", \
    "Actions": [ 
    ], \
    "DataActions": [ \
        "Microsoft.KeyVault/vaults/keys/read ", \
        "Microsoft.KeyVault/vaults/keys/backup/action", \
         "Microsoft.KeyVault/vaults/keys/restore/action" \
    ], \
    "NotDataActions": [ 
   ], \
    "AssignableScopes": ["/subscriptions/{subscriptionId}"] \
}'
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
$roleDefinition = @"
{ 
   "Name": "Backup Keys Operator", 
   "Description": "Perform key backup/restore operations", 
    "Actions": [ 
    ], 
    "DataActions": [ 
        "Microsoft.KeyVault/vaults/keys/read ", 
        "Microsoft.KeyVault/vaults/keys/backup/action", 
         "Microsoft.KeyVault/vaults/keys/restore/action" 
    ], 
    "NotDataActions": [ 
   ], 
    "AssignableScopes": ["/subscriptions/{subscriptionId}"] 
}
"@

$roleDefinition | Out-File role.json

New-AzRoleDefinition -InputFile role.json
```
---

Para obter mais informações sobre como criar funções personalizadas, consulte:

[Funções personalizadas do Azure](../../role-based-access-control/custom-roles.md)

## <a name="known-limits-and-performance"></a>Limites e desempenho conhecidos

-   Atribuições de funções Azure 2000 por subscrição

-   Funitências latência: no desempenho esperado atual, levará até 10 minutos (600 segundos) após a alteração das atribuições de funções para que o papel seja aplicado

## <a name="learn-more"></a>Saber mais

- [Visão geral do Azure RBAC](../../role-based-access-control/overview.md)
- [Tutorial de Papéis Personalizados](../../role-based-access-control/tutorial-custom-role-cli.md)