---
title: Conceder um acesso a outros recursos do Azure
description: Este artigo explica como conceder o acesso à aplicação de tecido de serviço gerido com identidade a outros recursos Azure que suportam a autenticação baseada no Diretório Ativo azure.
ms.topic: article
ms.date: 12/09/2019
ms.openlocfilehash: 3b1feab1e67e993df771564a1a7c1aba4236b2c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614798"
---
# <a name="granting-a-service-fabric-applications-managed-identity-access-to-azure-resources-preview"></a>Concessão do acesso de identidade gerido por uma aplicação de tecido de serviço aos recursos azure (pré-visualização)

Antes de a aplicação poder utilizar a sua identidade gerida para aceder a outros recursos, devem ser concedidas permissões a essa identidade sobre o recurso Azure protegido que está a ser acedido. A concessão de permissões é tipicamente uma ação de gestão no "plano de controlo" do serviço Azure que detém os recursos protegidos encaminhados através do Azure Resource Manager, que irá impor qualquer verificação de acesso baseada em funções aplicáveis.

A sequência exata de passos dependerá então do tipo de recurso Azure a ser acedido, bem como do idioma/cliente utilizado para conceder permissões. O restante do artigo assume uma identidade atribuída ao utilizador atribuída à aplicação e inclui vários exemplos típicos para a sua conveniência, mas não é de forma alguma uma referência exaustiva para este tópico; Consultar a documentação dos respetivos serviços Azure para instruções atualizadas sobre a concessão de permissões.  

## <a name="granting-access-to-azure-storage"></a>Concessão de acesso ao Armazenamento Azure
Pode utilizar a identidade gerida da aplicação Service Fabric (atribuída ao utilizador neste caso) para recuperar os dados de uma bolha de armazenamento Azure. Conceda à identidade as permissões necessárias no portal Azure com os seguintes passos:

1. Navegue para a conta de armazenamento
2. Clique na ligação Controlo de acesso (IAM) no painel esquerdo.
3. (opcional) Verifique o acesso existente: selecione identidade gerida atribuída pelo Sistema ou pelo Utilizador no controlo 'Encontrar'; selecionar a identidade apropriada da lista de resultados que se segue
4. Clique + Adicione a atribuição de funções em cima da página para adicionar uma nova atribuição de funções para a identidade da aplicação.
Em Função, a partir do dropdown, selecione Storage Blob Data Reader.
5. No próximo dropdown, sob acesso de `User assigned managed identity`atribuir, escolha .
6. Em seguida, certifique-se de que a subscrição adequada está listada na lista pendente Subscrição e, em seguida, defina Grupo de Recursos para Todos os grupos de recursos.
7. Em Select, escolha o UAI correspondente à aplicação Service Fabric e, em seguida, clique em Guardar.

O suporte a identidades geridas pelo tecido de serviço atribuído ao sistema não inclui a integração no portal Azure; se a sua aplicação utilizar uma identidade atribuída ao sistema, terá de encontrar primeiro a identificação do `Azure AD user, group, or service principal` cliente da identidade da aplicação e, em seguida, repetir os passos acima, mas selecionando a opção no controlo Find.

## <a name="granting-access-to-azure-key-vault"></a>Concessão de acesso ao Cofre chave Azure
Da mesma forma com o acesso ao armazenamento, pode aproveitar a identidade gerida de uma aplicação Service Fabric para aceder a um cofre chave Azure. Os passos para conceder acesso no portal Azure são semelhantes aos acima listados, e não serão repetidos aqui. Consulte a imagem abaixo para obter diferenças.

![Política de acesso ao cofre chave](../key-vault/media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

O exemplo que se segue ilustra a concessão de acesso a um cofre através de uma implantação de modelos; adicione o corte(s) abaixo como outra `resources` entrada sob o elemento do modelo. A amostra demonstra a concessão de acesso tanto para os tipos de identidade atribuídos pelo utilizador como para os tipos de identidade atribuídos pelo sistema, respectivamente - escolha o aplicável.

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
E para identidades geridas atribuídas pelo sistema:
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

Para mais detalhes, consulte [Vaults - Atualizar a Política](https://docs.microsoft.com/rest/api/keyvault/vaults/updateaccesspolicy)de Acesso.

## <a name="next-steps"></a>Passos seguintes
* [Implementar uma aplicação Azure Service Fabric com uma identidade gerida atribuída pelo sistema](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Implementar uma aplicação Azure Service Fabric com uma identidade gerida atribuída pelo utilizador](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)