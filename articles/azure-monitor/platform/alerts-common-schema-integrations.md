---
title: Como integrar o esquema comum do alerta com o Logic Apps
description: Saiba como criar uma aplicação lógica que utiliza o esquema comum do alerta para lidar com todos os seus alertas.
author: ananthradhakrishnan
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/27/2019
ms.author: anantr
ms.subservice: alerts
ms.openlocfilehash: 13cb3880662e1665b03dd63f009645acbe97fc75
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734895"
---
# <a name="how-to-integrate-the-common-alert-schema-with-logic-apps"></a>Como integrar o esquema comum do alerta com o Logic Apps

Este artigo mostra-lhe como criar uma aplicação lógica que utiliza o esquema comum do alerta para lidar com todos os seus alertas.

## <a name="overview"></a>Descrição geral

O [esquema comum do alerta](https://aka.ms/commonAlertSchemaDocs) fornece um esquema JSON padronizado e extensível em todos os seus tipos de alerta diferentes. O esquema comum do alerta é mais útil quando utilizado por meio de programação – através de webhooks, runbooks e aplicações lógicas. Neste artigo, demonstraremos como uma aplicação lógica única pode ser criada para lidar com todos os seus alertas. Os mesmos princípios podem ser aplicados a outros métodos de programação. A aplicação de lógica descrita neste artigo cria variáveis bem definidas para o [campos 'essenciais'](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema-definitions#essentials-fields)e também descreve como processar [tipo de alerta](/azure/azure-monitor/platform/alerts-common-schema-definitions#alert-context-fields) lógica específica.


## <a name="prerequisites"></a>Pré-requisitos 

Este artigo pressupõe que o leitor está familiarizado com o 
* Configurar regras de alerta ([métrica](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric), [log](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log), [registo de atividades](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log))
* Configurar [grupos de ação](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)
* Ativar a [esquema comum do alerta](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema#how-do-i-enable-the-common-alert-schema) de dentro de grupos de ação

## <a name="create-a-logic-app-leveraging-the-common-alert-schema"></a>Criar uma aplicação de lógica tirar partido do esquema comum do alerta

1. Siga os [passos descritos para criar a sua aplicação lógica](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups-logic-app). 

1.  Selecione o acionador: **Quando é recebido um pedido HTTP**.

    ![Acionadores da aplicação lógica](media/action-groups-logic-app/logic-app-triggers.png "acionadores da aplicação lógica")

1.  Selecione **editar** para alterar o acionador de pedido HTTP.

    ![Acionadores de pedido HTTP](media/action-groups-logic-app/http-request-trigger-shape.png "acionadores de pedido HTTP")


1.  Copie e cole o seguinte esquema:

    ```json
        {
            "type": "object",
            "properties": {
                "schemaId": {
                    "type": "string"
                },
                "data": {
                    "type": "object",
                    "properties": {
                        "essentials": {
                            "type": "object",
                            "properties": {
                                "alertId": {
                                    "type": "string"
                                },
                                "alertRule": {
                                    "type": "string"
                                },
                                "severity": {
                                    "type": "string"
                                },
                                "signalType": {
                                    "type": "string"
                                },
                                "monitorCondition": {
                                    "type": "string"
                                },
                                "monitoringService": {
                                    "type": "string"
                                },
                                "alertTargetIDs": {
                                    "type": "array",
                                    "items": {
                                        "type": "string"
                                    }
                                },
                                "originAlertId": {
                                    "type": "string"
                                },
                                "firedDateTime": {
                                    "type": "string"
                                },
                                "resolvedDateTime": {
                                    "type": "string"
                                },
                                "description": {
                                    "type": "string"
                                },
                                "essentialsVersion": {
                                    "type": "string"
                                },
                                "alertContextVersion": {
                                    "type": "string"
                                }
                            }
                        },
                        "alertContext": {
                            "type": "object",
                            "properties": {}
                        }
                    }
                }
            }
        }
    ```

1. Selecione **+** **novo passo** e, em seguida, escolha **adicionar uma ação**.

    ![Adicionar uma ação](media/action-groups-logic-app/add-action.png "adicionar uma ação")

1. Nesta fase, pode adicionar uma variedade de conectores (Microsoft Teams, Slack, Salesforce, etc.) com base nos seus requisitos comerciais específicos. Pode utilizar o "campos essenciais"-de-pronta. 

    ![Campos essenciais](media/alerts-common-schema-integrations/logic-app-essential-fields.png "campos essenciais")
    
    Em alternativa, pode criar uma lógica condicional com base no tipo de alerta com a opção "Expression".

    ![Expressão de aplicação lógica](media/alerts-common-schema-integrations/logic-app-expressions.png "expressão de aplicação lógica")
    
     O [campo 'monitoringService'](/azure/azure-monitor/platform/alerts-common-schema-definitions#alert-context-fields) permite-lhe identificar exclusivamente o tipo de alerta com base no que é possível criar a lógica condicional.

    
    Por exemplo, a seguir o trecho de código verifica se o alerta é um alerta de registo do Application Insights com base e, se assim for, imprime os resultados da pesquisa. Caso contrário, imprime "ND".

    ```text
      if(equals(triggerBody()?['data']?['essentials']?['monitoringService'],'Application Insights'),triggerBody()?['data']?['alertContext']?['SearchResults'],'NA')
    ```
    
     Saiba mais sobre [escrever expressões de aplicação de lógica](https://docs.microsoft.com/azure/logic-apps/workflow-definition-language-functions-reference#logical-comparison-functions).

    


## <a name="next-steps"></a>Passos Seguintes

* [Saiba mais sobre os grupos de ação](../../azure-monitor/platform/action-groups.md).
* [Saiba mais sobre o esquema comum do alerta](https://aka.ms/commonAlertSchemaDocs).

