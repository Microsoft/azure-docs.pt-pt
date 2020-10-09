---
title: Tutorial - recurso a bordo
description: O embarque de recursos através de fornecedores personalizados permite-lhe manipular e ampliar os recursos Azure existentes.
ms.topic: tutorial
ms.author: jobreen
author: jjbfour
ms.date: 09/17/2019
ms.openlocfilehash: 22d1dcd997a4ddb94aba184c5dace4c00509054d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75649940"
---
# <a name="tutorial-resource-onboarding-with-azure-custom-providers"></a>Tutorial: Embarque de recursos com fornecedores personalizados Azure

Neste tutorial, irá implementar para a Azure um fornecedor de recursos personalizado que alarga a API do Gestor de Recursos Azure com o tipo de recurso Microsoft.CustomProviders/associations. O tutorial mostra como estender os recursos existentes que estão fora do grupo de recursos onde está localizada a instância do fornecedor personalizado. Neste tutorial, o fornecedor de recursos personalizados é alimentado por uma aplicação lógica Azure, mas você pode usar qualquer ponto final público da API.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, precisa de saber:

* As capacidades dos [Fornecedores Personalizados Azure.](overview.md)
* Informação básica sobre [o embarque de recursos com fornecedores personalizados.](concepts-resource-onboarding.md)

## <a name="get-started-with-resource-onboarding"></a>Começar com recursos a bordo

Neste tutorial, há duas peças que precisam de ser implantadas: o fornecedor personalizado e a associação. Para facilitar o processo, pode utilizar opcionalmente um único modelo que implementa ambos.

O modelo utilizará estes recursos:

* Microsoft.CustomProviders/resourceProviders
* Microsoft.Logic/workflows
* Microsoft.CustomProviders/associações

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "allowedValues": [
                "australiaeast",
                "eastus",
                "westeurope"
            ],
            "metadata": {
                "description": "Location for the resources."
            }
        },
        "logicAppName": {
            "type": "string",
            "defaultValue": "[uniqueString(resourceGroup().id)]",
            "metadata": {
                "description": "Name of the logic app to be created."
            }
        },
        "customResourceProviderName": {
            "type": "string",
            "defaultValue": "[uniqueString(resourceGroup().id)]",
            "metadata": {
                "description": "Name of the custom provider to be created."
            }
        },
        "customResourceProviderId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The resource ID of an existing custom provider. Provide this to skip deployment of new logic app and custom provider."
            }
        },
        "associationName": {
            "type": "string",
            "defaultValue": "myAssociationResource",
            "metadata": {
                "description": "Name of the custom resource that is being created."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2017-05-10",
            "condition": "[empty(parameters('customResourceProviderId'))]",
            "name": "customProviderInfrastructureTemplate",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                        "logicAppName": {
                            "type": "string",
                            "defaultValue": "[parameters('logicAppName')]"
                        }
                    },
                    "resources": [
                        {
                            "type": "Microsoft.Logic/workflows",
                            "apiVersion": "2017-07-01",
                            "name": "[parameters('logicAppName')]",
                            "location": "[parameters('location')]",
                            "properties": {
                                "state": "Enabled",
                                "definition": {
                                    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
                                    "actions": {
                                        "Switch": {
                                            "cases": {
                                                "Case": {
                                                    "actions": {
                                                        "CreateCustomResource": {
                                                            "inputs": {
                                                                "body": {
                                                                    "properties": "@addProperty(triggerBody().Body['properties'], 'myDynamicProperty', 'myDynamicValue')"
                                                                },
                                                                "statusCode": 200
                                                            },
                                                            "kind": "Http",
                                                            "type": "Response"
                                                        }
                                                    },
                                                    "case": "CREATE"
                                                }
                                            },
                                            "default": {
                                                "actions": {
                                                    "DefaultHttpResponse": {
                                                        "inputs": {
                                                            "statusCode": 200
                                                        },
                                                        "kind": "Http",
                                                        "type": "Response"
                                                    }
                                                }
                                            },
                                            "expression": "@triggerBody().operationType",
                                            "type": "Switch"
                                        }
                                    },
                                    "contentVersion": "1.0.0.0",
                                    "outputs": {},
                                    "parameters": {},
                                    "triggers": {
                                        "CustomProviderWebhook": {
                                            "inputs": {
                                                "schema": {}
                                            },
                                            "kind": "Http",
                                            "type": "Request"
                                        }
                                    }
                                }
                            }
                        },
                        {
                            "type": "Microsoft.CustomProviders/resourceProviders",
                            "apiVersion": "2018-09-01-preview",
                            "name": "[parameters('customResourceProviderName')]",
                            "location": "[parameters('location')]",
                            "properties": {
                                "resourceTypes": [
                                    {
                                        "name": "associations",
                                        "mode": "Secure",
                                        "routingType": "Webhook,Cache,Extension",
                                        "endpoint": "[[listCallbackURL(concat(resourceId('Microsoft.Logic/workflows', parameters('logicAppName')), '/triggers/CustomProviderWebhook'), '2017-07-01').value]"
                                    }
                                ]
                            }
                        }
                    ],
                    "outputs": {
                        "customProviderResourceId": {
                            "type": "string",
                            "value": "[resourceId('Microsoft.CustomProviders/resourceProviders', parameters('customResourceProviderName'))]"
                        }
                    }
                }
            }
        },
        {
            "type": "Microsoft.CustomProviders/associations",
            "apiVersion": "2018-09-01-preview",
            "name": "[parameters('associationName')]",
            "location": "global",
            "properties": {
                "targetResourceId": "[if(empty(parameters('customResourceProviderId')), reference('customProviderInfrastructureTemplate').outputs.customProviderResourceId.value, parameters('customResourceProviderId'))]",
                "myCustomInputProperty": "myCustomInputValue",
                "myCustomInputObject": {
                    "Property1": "Value1"
                }
            }
        }
    ],
    "outputs": {
        "associationResource": {
            "type": "object",
            "value": "[reference(parameters('associationName'), '2018-09-01-preview', 'Full')]"
        }
    }
}
```

### <a name="deploy-the-custom-provider-infrastructure"></a>Implementar a infraestrutura de fornecedores personalizados

A primeira parte do modelo implementa a infraestrutura de fornecedores personalizados. Esta infraestrutura define o efeito do recurso das associações. Se não estiver familiarizado com fornecedores personalizados, consulte [o básico do fornecedor personalizado.](overview.md)

Vamos implantar a infraestrutura de fornecedores personalizados. Ou copie, guarde e implemente o modelo anterior, ou siga e implemente a infraestrutura utilizando o portal Azure.

1. Aceda ao [portal do Azure](https://portal.azure.com).

2. Procure **modelos** em **Todos os Serviços** ou utilizando a caixa de pesquisa principal:

   ![Procurar modelos](media/tutorial-resource-onboarding/templates.png)

3. **Selecione Adicionar** no **painel de modelos:**

   ![Selecione Adicionar](media/tutorial-resource-onboarding/templatesadd.png)

4. Em **Geral,** insira um **Nome** e **Descrição** para o novo modelo:

   ![Nome do modelo e descrição](media/tutorial-resource-onboarding/templatesdescription.png)

5. Crie o modelo de Gestor de Recursos copiando no modelo JSON a partir da secção "Começar com recurso no embarque" deste artigo:

   ![Criar um modelo do Resource Manager](media/tutorial-resource-onboarding/templatesarmtemplate.png)

6. **Selecione Adicionar** para criar o modelo. Se o novo modelo não aparecer, selecione **Refresh**.

7. Selecione o modelo recém-criado e, em seguida, **selecione Implementar**:

   ![Selecione o novo modelo e, em seguida, selecione Implementar](media/tutorial-resource-onboarding/templateselectspecific.png)

8. Introduza as definições para os campos necessários e, em seguida, selecione o grupo de subscrição e recursos. Pode deixar a caixa **de identificação do fornecedor de recursos personalizado** vazia.

   | Nome da definição | Necessário? | Descrição |
   | ------------ | -------- | ----------- |
   | Localização | Sim | A localização dos recursos no modelo. |
   | Nome de aplicativo lógica | Não | O nome da aplicação lógica. |
   | Nome do fornecedor de recursos personalizados | Não | O nome do fornecedor de recursos personalizado. |
   | Id do fornecedor de recursos personalizados | Não | Um fornecedor de recursos personalizados existente que suporta o recurso de associação. Se especificar um valor aqui, a aplicação lógica e a implementação do fornecedor personalizado serão ignorados. |
   | Nome da Associação | Não | O nome do recurso da associação. |

   Parâmetros da amostra:

   ![Introduza os parâmetros do modelo](media/tutorial-resource-onboarding/templatescustomprovider.png)

9. Vá para o destacamento e espere que termine. Devias ver algo como a seguinte imagem. Deve ver o novo recurso de associação como uma saída:

   ![Implementação bem-sucedida](media/tutorial-resource-onboarding/customproviderdeployment.png)

   Aqui está o grupo de recursos, com **tipos escondidos do Show selecionados:**

   ![Implementação personalizada do fornecedor](media/tutorial-resource-onboarding/showhidden.png)

10. Explore a aplicação lógica Executa o separador **de história** para ver as chamadas para a associação criar:

    ![App lógica executa a história](media/tutorial-resource-onboarding/logicapprun.png)

## <a name="deploy-additional-associations"></a>Implementar associações adicionais

Depois de configurar a infraestrutura de fornecedores personalizados, pode facilmente implementar mais associações. O grupo de recursos para associações adicionais não tem de ser o mesmo que o grupo de recursos onde implementou a infraestrutura de fornecedores personalizados. Para criar uma associação, precisa de ter permissões microsoft.CustomProviders/resourceproviders/write no ID do Fornecedor de Recursos Personalizados especificado.

1. Aceda ao fornecedor personalizado **Microsoft.CustomProviders/resourceProviders** no grupo de recursos da implementação anterior. Terá de selecionar a caixa de verificação **dos tipos ocultos do Show:**

   ![Vá para o recurso](media/tutorial-resource-onboarding/showhidden.png)

2. Copie a propriedade de ID de recursos do fornecedor personalizado.

3. Procure **modelos** em **Todos os Serviços** ou utilizando a caixa de pesquisa principal:

   ![Procurar modelos](media/tutorial-resource-onboarding/templates.png)

4. Selecione o modelo previamente criado e, em seguida, **selecione Implementar**:

   ![Selecione o modelo previamente criado e, em seguida, selecione Implementar](media/tutorial-resource-onboarding/templateselectspecific.png)

5. Introduza as definições para os campos necessários e, em seguida, selecione a subscrição e um grupo de recursos diferente. Para a definição **de ID do Fornecedor de Recursos Personalizados,** insira o ID de recursos que copiou do fornecedor personalizado que implementou anteriormente.

6. Vá para o destacamento e espere que termine. Deve agora utilizar apenas o recurso das novas associações:

   ![Recurso de novas associações](media/tutorial-resource-onboarding/createdassociationresource.png)

Se quiser, pode voltar à aplicação lógica **Executar histórico** e ver se foi feita outra chamada para a aplicação lógica. Pode atualizar a aplicação lógica para aumentar a funcionalidade adicional para cada associação criada.

## <a name="getting-help"></a>Obter ajuda

Se tiver dúvidas sobre os Fornecedores Personalizados Azure, tente fazê-los no [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). Uma pergunta semelhante pode já ter sido respondida, por isso verifique primeiro antes de publicar. Adicione a etiqueta `azure-custom-providers` para obter uma resposta rápida!

