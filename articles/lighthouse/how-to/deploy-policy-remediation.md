---
title: Implementar uma política que pode ser corrigida
description: Aprenda a embarcar um cliente para a gestão de recursos delegados do Azure, permitindo que os seus recursos sejam acedidos e geridos através do seu próprio inquilino.
ms.date: 10/11/2019
ms.topic: conceptual
ms.openlocfilehash: b625e9e3c96866cfbc655a55b770c9ac07a626bd
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985172"
---
# <a name="deploy-a-policy-that-can-be-remediated-within-a-delegated-subscription"></a>Implementar uma política que pode ser remediada dentro de uma subscrição delegada

[O Azure Lighthouse](../overview.md) permite que os prestadores de serviços criem e editem definições de políticas dentro de uma subscrição delegada. No entanto, para implementar políticas que utilizem uma tarefa de [reparação](../../governance/policy/how-to/remediate-resources.md) (isto é, políticas com o [efeito implementaçãoIfNotExists](../../governance/policy/concepts/effects.md#deployifnotexists) ou [modificar),](../../governance/policy/concepts/effects.md#modify) terá de criar uma [identidade gerida](../../active-directory/managed-identities-azure-resources/overview.md) no inquilino do cliente. Esta identidade gerida pode ser usada pela Política Azure para implementar o modelo dentro da política. Existem passos necessários para permitir este cenário, tanto quando se está a bordo do cliente para a gestão de recursos delegados do Azure, como quando implementa a própria política.

## <a name="create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant"></a>Criar um utilizador que possa atribuir funções a uma identidade gerida no inquilino do cliente

Quando a bordo de um cliente para a gestão de recursos delegados do Azure, utiliza um modelo de Gestor de [Recursos Azure](onboard-customer.md#create-an-azure-resource-manager-template) juntamente com um ficheiro de parâmetros que define os utilizadores, grupos de utilizadores e diretores de serviço no seu inquilino gestor que poderá aceder aos recursos delegados no inquilino do cliente. No seu ficheiro de parâmetros, cada um destes utilizadores **(principalid)** é atribuído a uma [função incorporada](../../role-based-access-control/built-in-roles.md) **(roleDefinitionId)** que define o nível de acesso.

Para permitir que um **principadoId** crie uma identidade gerida no inquilino do cliente, deve definir a sua **funçãoDefinitionId** para **o Administrador**de Acesso ao Utilizador . Embora esta função não seja geralmente suportada, pode ser usada neste cenário específico, permitindo aos utilizadores com esta permissão atribuir uma ou mais funções específicas incorporadas a identidades geridas. Estas funções são definidas na propriedade **deRoleDefinitionIds delegada.** Pode incluir qualquer função incorporada aqui, exceto administrador de acesso ao utilizador ou proprietário.

Depois de o cliente estar a bordo, o **principalid** criado nesta autorização poderá atribuir estas funções incorporadas a identidades geridas no inquilino do cliente. No entanto, não terão quaisquer outras permissões normalmente associadas à função de Administrador de Acesso ao Utilizador.

O exemplo abaixo mostra um **principalid** que terá a função de Administrador de Acesso ao Utilizador. Este utilizador poderá atribuir duas funções incorporadas a identidades geridas no inquilino do cliente: Colaborador e Colaborador de Log Analytics.

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

Uma vez criado o utilizador com as permissões necessárias, conforme descrito acima, esse utilizador pode implementar políticas no inquilino do cliente que utilizam tarefas de reparação.

Por exemplo, digamos que queria permitir diagnósticos nos recursos do Cofre chave Azure no inquilino do cliente, como ilustrado nesta [amostra.](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-keyvault-monitoring) Um utilizador no inquilino gestor com as permissões apropriadas (como descrito acima) implantaria um modelo de Gestor de [Recursos Azure](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/policy-enforce-keyvault-monitoring/enforceAzureMonitoredKeyVault.json) para permitir este cenário.

Note que a criação da atribuição de políticas a utilizar com uma subscrição delegada deve ser feita atualmente através de APIs, e não no portal Azure. Ao fazê-lo, a **apiVersão** deve ser definida para **a pré-visualização 2019-04-01**, que inclui a nova propriedade **delegada ManagedIdentityResourceId.** Este imóvel permite-lhe incluir uma identidade gerida que reside no inquilino do cliente (num grupo de subscrição ou recursos que foi a bordo da gestão de recursos delegados do Azure).

O exemplo seguinte mostra uma atribuição de funções com um **delegadoManagedIdentityResourceId**.

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
> Uma [amostra semelhante](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-add-or-replace-tag) está disponível para demonstrar como implementar uma política que adiciona ou remove uma etiqueta (utilizando o efeito modificado) a uma subscrição delegada.

## <a name="next-steps"></a>Passos seguintes

- Conheça a [Política Azure.](../../governance/policy/index.yml)
- Saiba mais sobre [identidades geridas para os recursos Azure.](../../active-directory/managed-identities-azure-resources/overview.md)
