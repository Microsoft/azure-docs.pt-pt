---
title: Implementar uma política que pode ser corrigida
description: Para implementar políticas que utilizem uma tarefa de reparação através do Farol Azure, você precisará criar uma identidade gerida no inquilino do cliente.
ms.date: 01/14/2021
ms.topic: how-to
ms.openlocfilehash: 01070133241117596bdf2b8e1e7c3fa101fc656c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98233887"
---
# <a name="deploy-a-policy-that-can-be-remediated-within-a-delegated-subscription"></a>Implementar uma política que pode ser remediada dentro de uma subscrição delegada

[O Azure Lighthouse](../overview.md) permite que os prestadores de serviços criem e editem definições de política dentro de uma subscrição delegada. No entanto, para implementar políticas que utilizem uma [tarefa de remediação](../../governance/policy/how-to/remediate-resources.md) (isto é, políticas com o [efeito implantadoIfNotExists](../../governance/policy/concepts/effects.md#deployifnotexists) ou [modificar](../../governance/policy/concepts/effects.md#modify) o efeito), terá de criar uma [identidade gerida](../../active-directory/managed-identities-azure-resources/overview.md) no arrendatário do cliente. Esta identidade gerida pode ser usada pela Azure Policy para implementar o modelo dentro da política. Existem passos necessários para ativar este cenário, tanto quando você está a bordo do cliente para a Azure delegado gestão de recursos, e quando você implementa a própria política.

> [!TIP]
> Embora nos refiramos a prestadores de serviços e clientes neste tópico, [as empresas que gerem vários inquilinos](../concepts/enterprise.md) podem usar os mesmos processos.

## <a name="create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant"></a>Criar um utilizador que possa atribuir funções a uma identidade gerida no inquilino do cliente

Quando você está a bordo de um cliente para o Azure Lighthouse, você usa um [modelo de Gestor de Recursos Azure](onboard-customer.md#create-an-azure-resource-manager-template) juntamente com um arquivo de parâmetros para definir autorizações que concedem acesso a recursos delegados no inquilino do cliente. Cada autorização especifica um **principalid** que corresponde a um utilizador, grupo ou principal de serviço da Azure no inquilino gerente, e um **papelDefinitionId** que corresponde à função incorporada do [Azure](../../role-based-access-control/built-in-roles.md) que será concedida.

Para permitir que um **diretor-adjunto** crie uma identidade gerida no inquilino do cliente, deve definir a sua **funçãoDefinitionId** ao Administrador de Acesso ao **Utilizador.** Embora esta função não seja geralmente suportada, pode ser usada neste cenário específico, permitindo que as contas dos utilizadores com esta permissão atribuam uma ou mais funções incorporadas específicas a identidades geridas. Estas funções são definidas na propriedade **delegadaRoleDefinitionIds,** e podem incluir qualquer [papel incorporado Azure suportado,](../concepts/tenants-users-roles.md#role-support-for-azure-lighthouse) exceto para administrador ou proprietário de acesso ao utilizador.

Após o cliente estar a bordo, o **principalId** criado nesta autorização poderá atribuir estas funções incorporadas a identidades geridas no inquilino do cliente. No entanto, não terão quaisquer outras permissões normalmente associadas à função de Administrador de Acesso ao Utilizador.

O exemplo abaixo mostra um **principalid** que terá a função de Administrador de Acesso ao Utilizador. Este utilizador poderá atribuir duas funções incorporadas a identidades geridas no cliente inquilino: Colaborador e Colaborador de Log Analytics.

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

## <a name="deploy-policies-that-can-be-remediated"></a>Implementar políticas que possam ser remediadas

Uma vez criado o utilizador com as permissões necessárias, tal como acima descrito, esse utilizador pode implementar políticas que utilizem tarefas de reparação dentro de subscrições de clientes delegadas.

Por exemplo, digamos que queria ativar os diagnósticos dos recursos do Azure Key Vault no inquilino do cliente, como ilustrado nesta [amostra.](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-keyvault-monitoring) Um utilizador no inquilino gerente com as permissões apropriadas (como descrito acima) implementaria um [modelo de Gestor de Recursos Azure](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/policy-enforce-keyvault-monitoring/enforceAzureMonitoredKeyVault.json) para permitir este cenário.

Note que a criação da atribuição de políticas a utilizar com uma subscrição delegada deve ser feita atualmente através de APIs, e não no portal Azure. Ao fazê-lo, a **apiVersão** deve ser definida para **2019-04-01-pré-visualização**, que inclui a nova propriedade **delegada Da autoridade DeresourceId.** Esta propriedade permite-lhe incluir uma identidade gerida que reside no inquilino do cliente (em um grupo de subscrição ou recursos que foi a bordo do Farol de Azure).

O exemplo a seguir mostra uma atribuição de funções com um **delegadoManagedIdentityResourceId**.

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
> Uma [amostra semelhante](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-add-or-replace-tag) está disponível para demonstrar como implementar uma política que adiciona ou remove uma etiqueta (usando o efeito modificar) a uma subscrição delegada.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [a Política Azure.](../../governance/policy/index.yml)
- Conheça [as identidades geridas para os recursos da Azure.](../../active-directory/managed-identities-azure-resources/overview.md)
