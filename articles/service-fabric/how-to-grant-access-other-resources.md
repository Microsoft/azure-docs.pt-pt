---
title: Conceder a um aplicativo acesso a outros recursos do Azure
description: Este artigo explica como conceder acesso ao aplicativo Service Fabric habilitado para identidade gerenciada a outros recursos do Azure que dão suporte à autenticação baseada em Azure Active Directory.
ms.topic: article
ms.date: 12/09/2019
ms.openlocfilehash: 3b1feab1e67e993df771564a1a7c1aba4236b2c0
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75614798"
---
# <a name="granting-a-service-fabric-applications-managed-identity-access-to-azure-resources-preview"></a>Concedendo um acesso de identidade gerenciada de Service Fabric aplicativo aos recursos do Azure (versão prévia)

Antes que o aplicativo possa usar sua identidade gerenciada para acessar outros recursos, as permissões devem ser concedidas a essa identidade no recurso protegido do Azure que está sendo acessado. A concessão de permissões é normalmente uma ação de gerenciamento no ' plano de controle ' do serviço do Azure que possui o recurso protegido roteado via Azure Resource Manager, o que irá impor qualquer verificação de acesso baseada em função aplicável.

A sequência exata de etapas dependerá do tipo de recurso do Azure que está sendo acessado, bem como o idioma/cliente usado para conceder permissões. O restante do artigo pressupõe uma identidade atribuída pelo usuário atribuída ao aplicativo e inclui vários exemplos típicos para sua conveniência, mas não é uma referência completa para este tópico; consulte a documentação dos respectivos serviços do Azure para obter instruções atualizadas sobre como conceder permissões.  

## <a name="granting-access-to-azure-storage"></a>Concedendo acesso ao armazenamento do Azure
Você pode usar a identidade gerenciada do aplicativo Service Fabric (atribuída pelo usuário neste caso) para recuperar os dados de um blob de armazenamento do Azure. Conceda à identidade as permissões necessárias no portal do Azure com as seguintes etapas:

1. Navegue até a conta de armazenamento
2. Clique no link controle de acesso (IAM) no painel esquerdo.
3. adicional Verificar o acesso existente: selecione a identidade gerenciada atribuída pelo sistema ou pelo usuário no controle ' Localizar '; Selecione a identidade apropriada na lista de resultados informados
4. Clique em + Adicionar atribuição de função na parte superior da página para adicionar uma nova atribuição de função para a identidade do aplicativo.
Em função, no menu suspenso, selecione leitor de dados de blob de armazenamento.
5. No menu suspenso seguinte, em atribuir acesso a, escolha `User assigned managed identity`.
6. Em seguida, verifique se a assinatura apropriada está listada na lista suspensa assinatura e, em seguida, defina grupo de recursos como todos os grupos de recursos.
7. Em selecionar, escolha o UAI correspondente ao aplicativo Service Fabric e, em seguida, clique em salvar.

O suporte para identidades gerenciadas Service Fabric atribuídas pelo sistema não inclui integração no portal do Azure; Se seu aplicativo usar uma identidade atribuída pelo sistema, você precisará localizar primeiro a ID do cliente da identidade do aplicativo e, em seguida, repetir as etapas acima, mas selecionando a opção `Azure AD user, group, or service principal` no controle localizar.

## <a name="granting-access-to-azure-key-vault"></a>Concedendo acesso ao Azure Key Vault
Da mesma forma, com o acesso ao armazenamento, você pode aproveitar a identidade gerenciada de um aplicativo Service Fabric para acessar um cofre de chaves do Azure. As etapas para conceder acesso no portal do Azure são semelhantes às listadas acima e não serão repetidas aqui. Consulte a imagem abaixo para obter diferenças.

![Política de acesso de Key Vault](../key-vault/media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

O exemplo a seguir ilustra a concessão de acesso a um cofre por meio de uma implantação de modelo; Adicione o (s) trecho (es) abaixo como outra entrada no elemento `resources` do modelo. O exemplo demonstra a concessão de acesso para os tipos de identidade atribuídos pelo usuário e pelo sistema, respectivamente, escolha o aplicável.

```json
    # under 'variables':
  "variables": {
        "userAssignedIdentityResourceId" : "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]",
    }
    # under 'resources':
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2018-02-14",
        "properties": {
            "accessPolicies": [
                {
                    "tenantId": "[reference(variables('userAssignedIdentityResourceId'), '2018-11-30').tenantId]",
                    "objectId": "[reference(variables('userAssignedIdentityResourceId'), '2018-11-30').principalId]",
                    "dependsOn": [
                        "[variables('userAssignedIdentityResourceId')]"
                    ],
                    "permissions": {
                        "keys":         ["get", "list"],
                        "secrets":      ["get", "list"],
                        "certificates": ["get", "list"]
                    }
                }
            ]
        }
    },
```
E para identidades gerenciadas atribuídas pelo sistema:
```json
    # under 'variables':
  "variables": {
        "sfAppSystemAssignedIdentityResourceId": "[concat(resourceId('Microsoft.ServiceFabric/clusters/applications/', parameters('clusterName'), parameters('applicationName')), '/providers/Microsoft.ManagedIdentity/Identities/default')]"
    }
    # under 'resources':
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2018-02-14",
        "properties": {
            "accessPolicies": [
            {
                    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
                    "tenantId": "[reference(variables('sfAppSystemAssignedIdentityResourceId'), '2018-11-30').tenantId]",
                    "objectId": "[reference(variables('sfAppSystemAssignedIdentityResourceId'), '2018-11-30').principalId]",
                    "dependsOn": [
                        "[variables('sfAppSystemAssignedIdentityResourceId')]"
                    ],
                    "permissions": {
                        "secrets": [
                            "get",
                            "list"
                        ],
                        "certificates": 
                        [
                            "get", 
                            "list"
                        ]
                    }
            },
        ]
        }
    }
```

Para obter mais detalhes, consulte [cofres – atualizar política de acesso](https://docs.microsoft.com/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="next-steps"></a>Passos seguintes
* [Implantar um aplicativo de Service Fabric do Azure com uma identidade gerenciada atribuída pelo sistema](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Implantar um aplicativo de Service Fabric do Azure com uma identidade gerenciada atribuída pelo usuário](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)