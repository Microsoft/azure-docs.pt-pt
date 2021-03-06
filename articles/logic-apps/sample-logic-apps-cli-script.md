---
title: Azure CLI script sample - crie uma app lógica
description: Roteire para criar uma aplicação lógica através da extensão de Apps Lógicas no Azure CLI.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.custom: mvc, devx-track-azurecli
ms.date: 07/30/2020
ms.openlocfilehash: a4553ceee482fb232e9ab56deca650be93f9dc6b
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102218048"
---
# <a name="azure-cli-script-sample---create-a-logic-app"></a>Azure CLI script sample - crie uma app lógica

Este script cria uma aplicação lógica de amostra através da [extensão Azure CLI Logic Apps](/cli/azure/ext/logic/logic)( `az logic` ). Para obter um guia detalhado para criar e gerir aplicações lógicas através do Azure CLI, consulte o [quickstart das Aplicações Lógicas para o Azure CLI](quickstart-logic-apps-azure-cli.md).

> [!WARNING]
> A extensão Azure CLI Logic Apps é atualmente *experimental* e *não coberta pelo apoio ao cliente.* Utilize com cuidado esta extensão CLI, especialmente se optar por utilizar a extensão em ambientes de produção.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* O [CLI Azure](/cli/azure/install-azure-cli) instalado no seu computador local.
* A [extensão Logic Apps Azure CLI](/cli/azure/azure-cli-extensions-list) instalada no seu computador. Para instalar esta extensão, utilize este comando: `az extension add --name logic`
* Uma [definição de fluxo de trabalho](quickstart-logic-apps-azure-cli.md#workflow-definition) para a sua aplicação lógica. Este ficheiro JSON deve seguir o [esquema linguístico de Definição de Fluxo de Trabalho](logic-apps-workflow-definition-language.md).
* Uma ligação API a uma conta de e-mail através de um [conector De Aplicações Lógicas](../connectors/apis-list.md) suportado no mesmo grupo de recursos que a sua aplicação lógica. Este exemplo utiliza o conector [Office 365 Outlook,](../connectors/connectors-create-api-office365-outlook.md) mas também pode utilizar outros conectores como [Outlook.com](../connectors/connectors-create-api-outlook.md).

### <a name="prerequisite-check"></a>Verificação pré-requisito

Valide o seu ambiente antes de começar:

* Inscreva-se no portal Azure e verifique se a sua subscrição está ativa executando `az login` .

* Verifique a sua versão do Azure CLI numa janela de terminal ou comando em funcionamento `az --version` . Para a versão mais recente, consulte as [últimas notas de lançamento](/cli/azure/release-notes-azure-cli).

  * Se não tiver a versão mais recente, atualize a sua instalação seguindo o [guia de instalação do seu sistema operativo ou plataforma.](/cli/azure/install-azure-cli)

### <a name="sample-workflow-explanation"></a>Explicação do fluxo de trabalho da amostra

Este ficheiro de definição de fluxo de trabalho de exemplo cria a mesma aplicação lógica básica que o [quickstart das Aplicações Lógicas para o portal Azure.](quickstart-create-first-logic-app-workflow.md) 

Este fluxo de trabalho da amostra: 

1. Especifica um esquema, `$schema` para a aplicação lógica.

1. Define um gatilho para a aplicação lógica na lista de gatilhos, `triggers` . O gatilho repete-se `recurrence` a cada 3 horas. As ações são desencadeadas quando um novo item de alimentação é publicado ( `When_a_feed_item_is_published` ) para o feed RSS especificado ( `feedUrl` ).

1. Define uma ação para a aplicação lógica na lista de ações, `actions` . A ação envia um e-mail `Send_an_email_(V2)` através da Microsoft 365 com detalhes dos itens de alimentação RSS, conforme especificado na secção do corpo `body` ( ) das entradas da ação ( `inputs` ).

## <a name="sample-workflow-definition"></a>Definição de fluxo de trabalho de amostra

Antes de executar o script da amostra, tem primeiro de criar uma [definição de fluxo de trabalho de](#prerequisites)amostra .

1. Crie um ficheiro `testDefinition.json` JSON, no seu computador. 

1. Copie o seguinte conteúdo no ficheiro JSON: 
    ```json
    
    {
        "definition": {
            "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
            "actions": {
                "Send_an_email_(V2)": {
                    "inputs": {
                        "body": {
                            "Body": "<p>@{triggerBody()?['publishDate']}<br>\n@{triggerBody()?['title']}<br>\n@{triggerBody()?['primaryLink']}</p>",
                            "Subject": "@triggerBody()?['title']",
                            "To": "test@example.com"
                        },
                        "host": {
                            "connection": {
                                "name": "@parameters('$connections')['office365']['connectionId']"
                            }
                        },
                        "method": "post",
                        "path": "/v2/Mail"
                    },
                    "runAfter": {},
                    "type": "ApiConnection"
                }
            },
            "contentVersion": "1.0.0.0",
            "outputs": {},
            "parameters": {
                "$connections": {
                    "defaultValue": {},
                    "type": "Object"
                }
            },
            "triggers": {
                "When_a_feed_item_is_published": {
                    "inputs": {
                        "host": {
                            "connection": {
                                "name": "@parameters('$connections')['rss']['connectionId']"
                            }
                        },
                        "method": "get",
                        "path": "/OnNewFeed",
                        "queries": {
                            "feedUrl": "https://www.pbs.org/now/rss.xml"
                        }
                    },
                    "recurrence": {
                        "frequency": "Hour",
                        "interval": 3
                    },
                    "splitOn": "@triggerBody()?['value']",
                    "type": "ApiConnection"
                }
            }
        },
        "parameters": {
            "$connections": {
                "value": {
                    "office365": {
                        "connectionId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testResourceGroup/providers/Microsoft.Web/connections/office365",
                        "connectionName": "office365",
                        "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Web/locations/westus/managedApis/office365"
                    },
                    "rss": {
                        "connectionId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testResourceGroup/providers/Microsoft.Web/connections/rss",
                        "connectionName": "rss",
                        "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Web/locations/westus/managedApis/rss"
                    }
                }
            }
        }
    }
    
    ```

1. Atualize os valores do espaço reservado com as suas próprias informações:

    1. Substitua o endereço de e-mail do espaço reservado `"To": "test@example.com"` (). Você precisa usar um endereço de e-mail compatível com conectores Logic Apps. Para mais informações, consulte os [pré-requisitos.](#prerequisites)

    1. Substitua detalhes adicionais do conector se estiver a utilizar outro conector de e-mail que o conector Do Office 365 Outlook.

    1. Substitua os valores de subscrição do espaço reservado `00000000-0000-0000-0000-000000000000` () para os seus identificadores de ligação `connectionId` `id` (e) sob o parâmetro de ligações `$connections` () pelos seus próprios valores de subscrição.

1. Guarde as alterações.

## <a name="sample-script"></a>Script de exemplo

> [!NOTE]
> Esta amostra está escrita para a `bash` concha. Se pretender executar esta amostra noutra concha, como o Windows PowerShell ou o Command Prompt, poderá ter de fazer modificações no seu script.

Antes de executar este script de amostra, execute este comando para ligar ao Azure:

```azurecli-interactive

az login

```

Em seguida, navegue para o diretório no qual criou a definição de fluxo de trabalho. Por exemplo, se criou o ficheiro JSON de definição de fluxo de trabalho no seu ambiente de trabalho:

```azurecli

cd ~/Desktop

```

Em seguida, executar este script para criar uma aplicação lógica. 

```azurecli-interactive

#!/bin/bash

# Create a resource group

az group create --name testResourceGroup --location westus

# Create your logic app

az logic workflow create --resource-group "testResourceGroup" --location "westus" --name "testLogicApp" --definition "testDefinition.json"

```

### <a name="clean-up-deployment"></a>Limpar a implementação

Depois de terminar de usar o script da amostra, execute o seguinte comando para remover o seu grupo de recursos e todos os seus recursos aninhados, incluindo a aplicação lógica.

```azurecli-interactive

az group delete --name testResourceGroup --yes

```

## <a name="script-explanation"></a>Explicação do script

Este script de amostra utiliza os seguintes comandos para criar um novo grupo de recursos e uma aplicação lógica.

| Comando | Notas |
| ------- | ----- |
| [`az group create`](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual os recursos da sua aplicação lógica são armazenados. |
| [`az logic workflow create`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create) | Cria uma aplicação lógica baseada no fluxo de trabalho definido no `--definition` parâmetro. |
| [`az group delete`](/cli/azure/vm/extension) | Elimina um grupo de recursos e todos os seus recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure CLI, consulte a documentação do [Azure CLI](/cli/azure/).

Pode encontrar amostras de scripts CLI de aplicações lógicas adicionais no [navegador de amostras de código da Microsoft.](/samples/browse/?products=azure-logic-apps)
