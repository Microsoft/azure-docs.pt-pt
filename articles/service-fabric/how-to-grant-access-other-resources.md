---
title: Conceder um acesso de aplicação a outros recursos da Azure
description: Este artigo explica como conceder à sua aplicação de Tecido de Serviço ativada por identidade gerida acesso a outros recursos Azure que suportam a autenticação baseada no Diretório Azure Ative.
ms.topic: article
ms.date: 12/09/2019
ms.openlocfilehash: c7560294fbf6d122396b6a5a8ffd3ee93bc89048
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/15/2020
ms.locfileid: "97507460"
---
# <a name="granting-a-service-fabric-applications-managed-identity-access-to-azure-resources"></a>Concessão de um acesso de identidade gerido por uma aplicação de Service Fabric aos recursos da Azure

Antes que a aplicação possa usar a sua identidade gerida para aceder a outros recursos, devem ser concedidas permissões a essa identidade no recurso Azure protegido que está a ser acedido. A concessão de permissões é normalmente uma ação de gestão no "plano de controlo" do serviço Azure que detém o recurso protegido encaminhado através do Azure Resource Manager, que aplicará qualquer verificação de acesso baseada em funções aplicável.

A sequência exata de passos dependerá então do tipo de recurso Azure que está a ser acedido, bem como do idioma/cliente utilizado para conceder permissões. O restante do artigo pressupõe uma identidade atribuída ao utilizador atribuída à aplicação e inclui vários exemplos típicos para a sua conveniência, mas não é de forma alguma uma referência exaustiva para este tópico; consulte a documentação dos respetivos serviços Azure para obter instruções atualizadas sobre a concessão de permissões.  

## <a name="granting-access-to-azure-storage"></a>Concessão de acesso ao Armazenamento Azure
Pode utilizar a identidade gerida da aplicação Service Fabric (atribuída pelo utilizador neste caso) para recuperar os dados de uma bolha de armazenamento Azure. Conceda à identidade as permissões necessárias no portal Azure com os seguintes passos:

1. Navegue para a conta de armazenamento
2. Clique na ligação Controlo de acesso (IAM) no painel esquerdo.
3. (opcional) Verifique o acesso existente: selecione identidade gerida atribuída ao Sistema ou ao Utilizador no controlo 'Localizar'; selecionar a identidade adequada da lista de resultados subsequente
4. Clique + Adicione a atribuição de funções no topo da página para adicionar uma nova atribuição de função para a identidade da aplicação.
Under Role, a partir do dropdown, selecione Storage Blob Data Reader.
5. Na próxima entrega, em atribuição de acesso a, escolha `User assigned managed identity` .
6. Em seguida, certifique-se de que a subscrição adequada está listada na lista pendente Subscrição e, em seguida, defina Grupo de Recursos para Todos os grupos de recursos.
7. Em Select, escolha o UAI correspondente à aplicação 'Tecido de Serviço' e, em seguida, clique em Guardar.

O suporte às identidades geridas pelo Tecido de Serviço atribuído pelo sistema não inclui a integração no portal Azure; se a sua aplicação utilizar uma identidade atribuída ao sistema, terá de encontrar primeiro o ID do cliente da identidade da aplicação e, em seguida, repetir os passos acima, mas selecionando a `Azure AD user, group, or service principal` opção no controlo Encontrar.

## <a name="granting-access-to-azure-key-vault"></a>Concessão de acesso ao Cofre da Chave Azure
Da mesma forma, com o acesso ao armazenamento, pode aproveitar a identidade gerida de uma aplicação de Tecido de Serviço para aceder a um cofre de chaves Azure. Os passos para conceder acesso no portal Azure são semelhantes aos listados acima, e não serão repetidos aqui. Consulte a imagem abaixo para obter diferenças.

![Política de acesso ao Cofre chave](../key-vault/media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

O exemplo a seguir ilustra a concessão de acesso a um cofre através de uma implantação de modelo; adicione o(s) snippet(s) abaixo como outra entrada sob o `resources` elemento do modelo. A amostra demonstra o acesso a tipos de identidade atribuídos pelo utilizador e atribuídos pelo sistema, respectivamente - escolha o aplicável.

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
E para identidades geridas pelo sistema:
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

Para mais detalhes, consulte [Vaults - Update Access Policy](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="next-steps"></a>Passos seguintes
* [Implementar uma aplicação Azure Service Fabric com uma identidade gerida atribuída ao sistema](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Implementar uma aplicação Azure Service Fabric com uma identidade gerida atribuída pelo utilizador](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
