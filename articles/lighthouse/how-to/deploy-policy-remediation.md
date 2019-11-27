---
title: Implementar uma política que pode ser corrigida
description: Saiba como integrar um cliente ao gerenciamento de recursos delegado do Azure, permitindo que seus recursos sejam acessados e gerenciados por meio de seu próprio locatário.
ms.date: 10/11/2019
ms.topic: conceptual
ms.openlocfilehash: 4522c9ebad741f5ec0cb7e56e68467312ef8f037
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74463873"
---
# <a name="deploy-a-policy-that-can-be-remediated-within-a-delegated-subscription"></a>Implantar uma política que pode ser corrigida em uma assinatura delegada

O [Azure Lighthouse](../overview.md) permite que os provedores de serviços criem e editem definições de política em uma assinatura delegada. No entanto, para implantar políticas que usam uma [tarefa de correção](https://docs.microsoft.com/azure/governance/policy/how-to/remediate-resources) (ou seja, políticas com o efeito [deployIfNotExists](https://docs.microsoft.com/azure/governance/policy/concepts/effects#deployifnotexists) ou [Modificar](https://docs.microsoft.com/azure/governance/policy/concepts/effects#modify) ), você precisará criar uma [identidade gerenciada](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) no locatário do cliente. Essa identidade gerenciada pode ser usada pelo Azure Policy para implantar o modelo na política. Há etapas necessárias para habilitar esse cenário, quando você carrega o cliente para o gerenciamento de recursos delegado do Azure e quando implanta a política em si.

## <a name="create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant"></a>Criar um usuário que pode atribuir funções a uma identidade gerenciada no locatário do cliente

Ao integrar um cliente para o gerenciamento de recursos delegado do Azure, você usa um [modelo de Azure Resource Manager](https://docs.microsoft.com/azure/lighthouse/how-to/onboard-customer#create-an-azure-resource-manager-template) junto com um arquivo de parâmetros que define os usuários, grupos de usuários e entidades de serviço no seu locatário de gerenciamento que poderá acessar os recursos delegados no locatário do cliente. No arquivo de parâmetros, cada um desses usuários (**PrincipalId**) é atribuído a uma [função interna](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) (**roleDefinitionId**) que define o nível de acesso.

Para permitir que uma **entidade de segurança** crie uma identidade gerenciada no locatário do cliente, você deve definir seu **roleDefinitionId** como administrador de **acesso do usuário**. Embora essa função não tenha suporte em geral, ela pode ser usada nesse cenário específico, permitindo que os usuários com essa permissão atribuam uma ou mais funções internas específicas a identidades gerenciadas. Essas funções são definidas na propriedade **delegatedRoleDefinitionIds** . Você pode incluir qualquer função interna aqui, exceto o administrador de acesso do usuário ou o proprietário.

Depois que o cliente estiver integrado, o **PrincipalId** criado nessa autorização poderá atribuir essas funções internas a identidades gerenciadas no locatário do cliente. No entanto, eles não terão nenhuma outra permissão normalmente associada à função Administrador de acesso do usuário.

O exemplo a seguir mostra um **PrincipalId** que terá a função de administrador de acesso do usuário. Esse usuário poderá atribuir duas funções internas a identidades gerenciadas no locatário do cliente: colaborador e colaborador de Log Analytics.

```json
{
    "principalId": "3kl47fff-5655-4779-b726-2cf02b05c7c4",
    "principalIdDisplayName": "Policy Automation Account",
    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
    "delegatedRoleDefinitionIds": [
         "b24988ac-6180-42a0-ab88-20f7382dd24c",
         "92aaf0da-9dab-42b6-94a3-d43ce8d16293"
    ]
}
```

## <a name="deploy-policies-that-can-be-remediated"></a>Implantar políticas que podem ser corrigidas

Depois de criar o usuário com as permissões necessárias, conforme descrito acima, esse usuário pode implantar políticas no locatário do cliente que usam tarefas de correção.

Por exemplo, digamos que você queria habilitar o diagnóstico em Azure Key Vault recursos no locatário do cliente, conforme ilustrado neste [exemplo](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-enforce-keyvault-monitoring). Um usuário no locatário de gerenciamento com as permissões apropriadas (conforme descrito acima) implantaria um [modelo de Azure Resource Manager](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/policy-enforce-keyvault-monitoring/enforceAzureMonitoredKeyVault.json) para habilitar esse cenário.

Observe que a criação da atribuição de política a ser usada com uma assinatura delegada deve ser feita atualmente por meio de APIs, não na portal do Azure. Ao fazer isso, o **apiVersion** deve ser definido como **2019-04-01-Preview**, que inclui a nova propriedade **delegatedManagedIdentityResourceId** . Essa propriedade permite incluir uma identidade gerenciada que reside no locatário do cliente (em uma assinatura ou grupo de recursos que foi integrado ao gerenciamento de recursos delegado do Azure).

O exemplo a seguir mostra uma atribuição de função com um **delegatedManagedIdentityResourceId**.

```json
"type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2019-04-01-preview",
            "name": "[parameters('rbacGuid')]",
            "dependsOn": [
                "[variables('policyAssignment')]"
            ],
            "properties": {
                "roleDefinitionId": "[concat(subscription().id, '/providers/Microsoft.Authorization/roleDefinitions/', variables('rbacContributor'))]",
                "principalType": "ServicePrincipal",
                "delegatedManagedIdentityResourceId": "[concat(subscription().id, '/providers/Microsoft.Authorization/policyAssignments/', variables('policyAssignment'))]",
                "principalId": "[toLower(reference(concat('/providers/Microsoft.Authorization/policyAssignments/', variables('policyAssignment')), '2018-05-01', 'Full' ).identity.principalId)]"
            }
```

> [!TIP]
> Um [exemplo semelhante](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-add-or-replace-tag) está disponível para demonstrar como implantar uma política que adiciona ou remove uma marca (usando o efeito modificar) para uma assinatura delegada.

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [Azure Policy](https://docs.microsoft.com/azure/governance/policy/).
- Saiba mais sobre [identidades gerenciadas para recursos do Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).
