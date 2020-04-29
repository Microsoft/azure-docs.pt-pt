---
title: Tutorial - recurso onboarding
description: O embarque de recursos através de fornecedores personalizados permite-lhe manipular e estender os recursos Azure existentes.
ms.topic: tutorial
ms.author: jobreen
author: jjbfour
ms.date: 09/17/2019
ms.openlocfilehash: 22d1dcd997a4ddb94aba184c5dace4c00509054d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "75649940"
---
# <a name="tutorial-resource-onboarding-with-azure-custom-providers"></a>Tutorial: Recurso a bordo com fornecedores personalizados Azure

Neste tutorial, irá implementar para o Azure um fornecedor de recursos personalizado que alarga a API do Gestor de Recursos Azure ao tipo de recursos Microsoft.CustomProviders/associações. O tutorial mostra como alargar os recursos existentes que estão fora do grupo de recursos onde se encontra a instância do fornecedor personalizado. Neste tutorial, o fornecedor de recursos personalizados é alimentado por uma aplicação lógica Azure, mas você pode usar qualquer ponto final de API público.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, precisa de saber:

* As capacidades dos [Fornecedores Personalizados Azure.](overview.md)
* Informações básicas sobre [o embarque de recursos com fornecedores personalizados.](concepts-resource-onboarding.md)

## <a name="get-started-with-resource-onboarding"></a>Começar com recurso onboarding

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

### <a name="deploy-the-custom-provider-infrastructure"></a>Implementar a infraestrutura de fornecedor personalizado

A primeira parte do modelo implementa a infraestrutura personalizada do fornecedor. Esta infraestrutura define o efeito do recurso das associações. Se não estiver familiarizado com fornecedores personalizados, consulte o [básico do fornecedor personalizado.](overview.md)

Vamos implantar a infraestrutura de fornecedores personalizados. Ou copia, economiza e desdobra o modelo anterior, ou siga e implante a infraestrutura utilizando o portal Azure.

1. Vá ao [portal Azure.](https://portal.azure.com)

2. Procure **modelos** em **Todos os Serviços** ou utilizando a caixa de pesquisa principal:

   ![Procurar modelos](media/tutorial-resource-onboarding/templates.png)

3. **Selecione Adicionar** no painel de **modelos:**

   ![Selecione Adicionar](media/tutorial-resource-onboarding/templatesadd.png)

4. Em **geral,** insira um **nome** e **descrição** para o novo modelo:

   ![Nome e descrição do modelo](media/tutorial-resource-onboarding/templatesdescription.png)

5. Crie o modelo de Gestor de Recursos copiando no modelo JSON a partir da secção "Começar com recurso onboarding" deste artigo:

   ![Criar um modelo do Resource Manager](media/tutorial-resource-onboarding/templatesarmtemplate.png)

6. Selecione **Adicionar** para criar o modelo. Se o novo modelo não aparecer, selecione **Refresh**.

7. Selecione o modelo recém-criado e, em seguida, selecione **Implementar:**

   ![Selecione o novo modelo e, em seguida, selecione Implementar](media/tutorial-resource-onboarding/templateselectspecific.png)

8. Introduza as definições para os campos necessários e, em seguida, selecione o grupo de subscrição e recursos. Pode deixar a caixa id do fornecedor de **recursos personalizados** vazia.

   | Nome da definição | Necessário? | Descrição |
   | ------------ | -------- | ----------- |
   | Localização | Sim | A localização dos recursos no modelo. |
   | Nome da aplicação lógica | Não | O nome da aplicação lógica. |
   | Nome do fornecedor de recursos personalizados | Não | O nome do fornecedor de recursos personalizados. |
   | Id do fornecedor de recursos personalizado | Não | Um fornecedor de recursos personalizados existente que suporta o recurso da associação. Se especificar um valor aqui, a aplicação lógica e a implementação personalizada do fornecedor serão ignoradas. |
   | Nome da Associação | Não | O nome do recurso da associação. |

   Parâmetros da amostra:

   ![Introduza parâmetros de modelo](media/tutorial-resource-onboarding/templatescustomprovider.png)

9. Vá para a colocação e espere que termine. Devias ver algo como a seguinte imagem. Deve ver o novo recurso de associação como uma saída:

   ![Implementação bem-sucedida](media/tutorial-resource-onboarding/customproviderdeployment.png)

   Aqui está o grupo de recursos, com **tipos ocultos do Show selecionados:**

   ![Implementação personalizada do fornecedor](media/tutorial-resource-onboarding/showhidden.png)

10. Explore a aplicação lógica **Executa** o separador de história para ver as chamadas para a associação criar:

    ![App lógica Corre história](media/tutorial-resource-onboarding/logicapprun.png)

## <a name="deploy-additional-associations"></a>Implementar associações adicionais

Depois de configurar a infraestrutura personalizada do fornecedor, pode facilmente implementar mais associações. O grupo de recursos para associações adicionais não tem de ser o mesmo que o grupo de recursos onde implementou a infraestrutura de fornecedores personalizados. Para criar uma associação, é necessário ter permissões Microsoft.CustomProviders/resourceproviders/write no ID especificado do Fornecedor de Recursos Personalizados.

1. Vá ao fornecedor personalizado **Microsoft.CustomProviders/resourceProviders** recursos no grupo de recursos da implementação anterior. Terá de selecionar a caixa de verificação de **tipos ocultas show:**

   ![Ir para o recurso](media/tutorial-resource-onboarding/showhidden.png)

2. Copie a propriedade de ID de Recursos do fornecedor personalizado.

3. Procure **modelos** em **Todos os Serviços** ou utilizando a caixa de pesquisa principal:

   ![Procurar modelos](media/tutorial-resource-onboarding/templates.png)

4. Selecione o modelo previamente criado e, em seguida, selecione **Implementar:**

   ![Selecione o modelo previamente criado e, em seguida, selecione Implementar](media/tutorial-resource-onboarding/templateselectspecific.png)

5. Introduza as definições para os campos necessários e, em seguida, selecione a subscrição e um grupo de recursos diferentes. Para a definição id do fornecedor de **recursos personalizados,** introduza o ID de recurso que copiou do fornecedor personalizado que implementou anteriormente.

6. Vá para a colocação e espere que termine. Deve agora implantar apenas o recurso das novas associações:

   ![Novos recursos de associações](media/tutorial-resource-onboarding/createdassociationresource.png)

Se quiser, pode voltar à aplicação lógica **Executar o histórico** e ver que outra chamada foi feita para a aplicação lógica. Pode atualizar a aplicação lógica para aumentar a funcionalidade adicional para cada associação criada.

## <a name="getting-help"></a>Obter ajuda

Se tiver dúvidas sobre fornecedores personalizados Azure, tente perguntar-lhes sobre [o Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). Uma pergunta semelhante pode já ter sido respondida, por isso verifique primeiro antes de publicar. Adicione a `azure-custom-providers` etiqueta para obter uma resposta rápida!

