---
title: Integração de recursos com provedores personalizados do Azure
description: A integração de recursos por meio de provedores personalizados permite que você manipule e estenda os recursos existentes do Azure.
services: managed-applications
ms.service: managed-applications
ms.topic: tutorial
ms.author: jobreen
author: jjbfour
ms.date: 09/17/2019
ms.openlocfilehash: 45086987d5ba3a619028ced798712f2870c6d487
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826791"
---
# <a name="tutorial-resource-onboarding-with-azure-custom-providers"></a>Tutorial: integração de recursos com os provedores personalizados do Azure

Neste tutorial, você implantará no Azure um provedor de recursos personalizado que estende a API Azure Resource Manager com o tipo de recurso Microsoft. CustomProviders/Associations. O tutorial mostra como estender os recursos existentes que estão fora do grupo de recursos em que a instância do provedor personalizado está localizada. Neste tutorial, o provedor de recursos personalizado é alimentado por um aplicativo lógico do Azure, mas você pode usar qualquer ponto de extremidade de API pública.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisa saber:

* Os recursos dos [provedores personalizados do Azure](custom-providers-overview.md).
* Informações básicas sobre [a integração de recursos com provedores personalizados](concepts-custom-providers-resourceonboarding.md).

## <a name="get-started-with-resource-onboarding"></a>Introdução à integração de recursos

Neste tutorial, há duas partes que precisam ser implantadas: o provedor personalizado e a associação. Para facilitar o processo, você pode, opcionalmente, usar um único modelo que implanta ambos.

O modelo usará estes recursos:

* Microsoft. CustomProviders/resourceProviders
* Microsoft. Logic/workflows
* Microsoft. CustomProviders/Associations

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

### <a name="deploy-the-custom-provider-infrastructure"></a>Implantar a infraestrutura do provedor personalizado

A primeira parte do modelo implanta a infraestrutura do provedor personalizado. Essa infraestrutura define o efeito do recurso de associações. Se você não estiver familiarizado com provedores personalizados, consulte [noções básicas do provedor personalizado](custom-providers-overview.md).

Vamos implantar a infraestrutura do provedor personalizado. Copie, salve e implante o modelo anterior ou acompanhe e implante a infraestrutura usando o portal do Azure.

1. Aceda ao [Portal do Azure](https://portal.azure.com).

2. Pesquise **modelos** em **todos os serviços** ou usando a caixa de pesquisa principal:

   ![Pesquisar modelos](media/custom-providers-resource-onboarding/templates.png)

3. Selecione **Adicionar** no painel **modelos** :

   ![Selecione Adicionar](media/custom-providers-resource-onboarding/templatesadd.png)

4. Em **geral**, insira um **nome** e uma **Descrição** para o novo modelo:

   ![Nome e descrição do modelo](media/custom-providers-resource-onboarding/templatesdescription.png)

5. Crie o modelo do Resource Manager copiando no modelo JSON da seção "introdução à integração de recursos" deste artigo:

   ![Criar um modelo do Resource Manager](media/custom-providers-resource-onboarding/templatesarmtemplate.png)

6. Selecione **Adicionar** para criar o modelo. Se o novo modelo não aparecer, selecione **Atualizar**.

7. Selecione o modelo recém-criado e, em seguida, selecione **implantar**:

   ![Selecione o novo modelo e, em seguida, selecione implantar](media/custom-providers-resource-onboarding/templateselectspecific.png)

8. Insira as configurações para os campos obrigatórios e, em seguida, selecione a assinatura e o grupo de recursos. Você pode deixar a caixa **ID do provedor de recursos personalizada** vazia.

   | Nome da definição | Necessário? | Descrição |
   | ------------ | -------- | ----------- |
   | Localização | Sim | O local dos recursos no modelo. |
   | Nome do aplicativo lógico | Não | O nome do aplicativo lógico. |
   | Nome do provedor de recursos personalizado | Não | O nome do provedor de recursos personalizado. |
   | ID do provedor de recursos personalizado | Não | Um provedor de recursos personalizado existente que dá suporte ao recurso de associação. Se você especificar um valor aqui, o aplicativo lógico e a implantação do provedor personalizado serão ignorados. |
   | Nome da Associação | Não | O nome do recurso de associação. |

   Parâmetros de exemplo:

   ![Inserir parâmetros de modelo](media/custom-providers-resource-onboarding/templatescustomprovider.png)

9. Vá para a implantação e aguarde sua conclusão. Você deve ver algo semelhante à captura de tela a seguir. Você deverá ver o novo recurso de associação como uma saída:

   ![Implantação bem-sucedida](media/custom-providers-resource-onboarding/customproviderdeployment.png)

   Aqui está o grupo de recursos, com **Mostrar tipos ocultos** selecionados:

   ![Implantação de provedor personalizado](media/custom-providers-resource-onboarding/showhidden.png)

10. Explore a guia **histórico de execuções** do aplicativo lógico para ver as chamadas para a criação da associação:

    ![Histórico de execuções do aplicativo lógico](media/custom-providers-resource-onboarding/logicapprun.png)

## <a name="deploy-additional-associations"></a>Implantar associações adicionais

Depois de configurar a infraestrutura do provedor personalizado, você pode facilmente implantar mais associações. O grupo de recursos para associações adicionais não precisa ser o mesmo que o grupo de recursos em que você implantou a infraestrutura do provedor personalizado. Para criar uma associação, você precisa ter permissões Microsoft. CustomProviders/resourceproviders/Write na ID do provedor de recursos personalizado especificado.

1. Vá para o recurso provedor personalizado **Microsoft. CustomProviders/resourceProviders** no grupo de recursos da implantação anterior. Você precisará marcar a caixa de seleção **Mostrar tipos ocultos** :

   ![Ir para o recurso](media/custom-providers-resource-onboarding/showhidden.png)

2. Copie a propriedade ID do recurso do provedor personalizado.

3. Pesquise **modelos** em **todos os serviços** ou usando a caixa de pesquisa principal:

   ![Pesquisar modelos](media/custom-providers-resource-onboarding/templates.png)

4. Selecione o modelo criado anteriormente e, em seguida, selecione **implantar**:

   ![Selecione o modelo criado anteriormente e, em seguida, selecione implantar](media/custom-providers-resource-onboarding/templateselectspecific.png)

5. Insira as configurações para os campos obrigatórios e, em seguida, selecione a assinatura e um grupo de recursos diferente. Para a configuração **ID do provedor de recursos personalizados** , insira a ID do recurso que você copiou do provedor personalizado que você implantou anteriormente.

6. Vá para a implantação e aguarde sua conclusão. Agora, ele deve implantar apenas o novo recurso de associações:

   ![Novo recurso de associações](media/custom-providers-resource-onboarding/createdassociationresource.png)

Se desejar, você pode voltar para o **histórico de execução** do aplicativo lógico e ver que outra chamada foi feita para o aplicativo lógico. Você pode atualizar o aplicativo lógico para aumentar a funcionalidade adicional de cada associação criada.

## <a name="getting-help"></a>Obter ajuda

Se você tiver dúvidas sobre os provedores personalizados do Azure, tente solicitá-los em [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-custom-providers). Uma pergunta semelhante pode já ter sido respondida, portanto, verifique primeiro antes de postar. Adicione a marca `azure-custom-providers` para obter uma resposta rápida!

