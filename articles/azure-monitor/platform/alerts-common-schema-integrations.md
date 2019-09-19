---
title: Como integrar o esquema de alerta comum com aplicativos lógicos
description: Saiba como criar um aplicativo lógico que aproveita o esquema de alerta comum para lidar com todos os seus alertas.
author: ananthradhakrishnan
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/27/2019
ms.author: anantr
ms.subservice: alerts
ms.openlocfilehash: f431e5e5f4537d1a5f889457eb81b881e47ee178
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091794"
---
# <a name="how-to-integrate-the-common-alert-schema-with-logic-apps"></a>Como integrar o esquema de alerta comum com aplicativos lógicos

Este artigo mostra como criar um aplicativo lógico que aproveita o esquema de alerta comum para lidar com todos os seus alertas.

## <a name="overview"></a>Descrição geral

O [esquema de alerta comum](https://aka.ms/commonAlertSchemaDocs) fornece um esquema JSON padronizado e extensível em todos os tipos de alerta diferentes. O esquema de alerta comum é mais útil quando aproveitado programaticamente – por meio de WebHooks, runbooks e aplicativos lógicos. Neste artigo, demonstramos como um único aplicativo lógico pode ser criado para lidar com todos os seus alertas. Os mesmos princípios podem ser aplicados a outros métodos programáticos. O aplicativo lógico descrito neste artigo cria variáveis bem definidas para os [campos ' essenciais '](alerts-common-schema-definitions.md#essentials)e também descreve como você pode manipular a lógica específica do [tipo de alerta](alerts-common-schema-definitions.md#alert-context) .


## <a name="prerequisites"></a>Pré-requisitos 

Este artigo pressupõe que o leitor esteja familiarizado com 
* Configurando regras de alerta ([métrica](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric), [log](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log), [log de atividades](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log))
* Configurando [grupos de ação](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)
* Habilitando o [esquema de alerta comum](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema#how-do-i-enable-the-common-alert-schema) de dentro dos grupos de ação

## <a name="create-a-logic-app-leveraging-the-common-alert-schema"></a>Criar um aplicativo lógico aproveitando o esquema de alerta comum

1. Siga as [etapas descritas para criar seu aplicativo lógico](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups-logic-app). 

1.  Selecione o gatilho: **Quando uma solicitação HTTP é recebida**.

    ![Gatilhos de aplicativo lógico](media/action-groups-logic-app/logic-app-triggers.png "Gatilhos de aplicativo lógico")

1.  Selecione **Editar** para alterar o gatilho de solicitação HTTP.

    ![Gatilhos de solicitação HTTP](media/action-groups-logic-app/http-request-trigger-shape.png "Gatilhos de solicitação HTTP")


1.  Copie e cole o esquema a seguir:

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

1. Selecione **+** **nova etapa** e, em seguida, escolha **Adicionar uma ação**.

    ![Adicionar uma ação](media/action-groups-logic-app/add-action.png "Adicionar uma ação")

1. Neste estágio, você pode adicionar uma variedade de conectores (Microsoft Teams, margem de atraso, Salesforce, etc.) com base em seus requisitos específicos de negócios. Você pode usar os ' campos essenciais ' prontos para uso. 

    ![Campos essenciais](media/alerts-common-schema-integrations/logic-app-essential-fields.png "Campos essenciais")
    
    Como alternativa, você pode criar a lógica condicional com base no tipo de alerta usando a opção ' Expression '.

    ![Expressão de aplicativo lógico](media/alerts-common-schema-integrations/logic-app-expressions.png "Expressão de aplicativo lógico")
    
     O [campo ' monitoringService '](alerts-common-schema-definitions.md#alert-context) permite que você identifique exclusivamente o tipo de alerta, com base no qual você pode criar a lógica condicional.

    
    Por exemplo, o trecho de código abaixo verifica se o alerta é um alerta de log baseado Application Insights e, em caso positivo, imprime os resultados da pesquisa. Caso contrário, ele imprimirá ' NA '.

    ```text
      if(equals(triggerBody()?['data']?['essentials']?['monitoringService'],'Application Insights'),triggerBody()?['data']?['alertContext']?['SearchResults'],'NA')
    ```
    
     Saiba mais sobre como [escrever expressões de aplicativo lógico](https://docs.microsoft.com/azure/logic-apps/workflow-definition-language-functions-reference#logical-comparison-functions).

    


## <a name="next-steps"></a>Passos Seguintes

* [Saiba mais sobre grupos de ações](../../azure-monitor/platform/action-groups.md).
* [Saiba mais sobre o esquema de alerta comum](https://aka.ms/commonAlertSchemaDocs).

