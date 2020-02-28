---
title: Como integrar o esquema de alerta comum com apps lógicas
description: Aprenda a criar uma aplicação lógica que aproveite o esquema de alerta comum para lidar com todos os seus alertas.
ms.topic: conceptual
ms.subservice: alerts
ms.date: 05/27/2019
ms.openlocfilehash: 9042ed8ddbb698192e638fa7538f74561574c262
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77668235"
---
# <a name="how-to-integrate-the-common-alert-schema-with-logic-apps"></a>Como integrar o esquema de alerta comum com apps lógicas

Este artigo mostra-lhe como criar uma aplicação lógica que aproveita o esquema de alerta comum para lidar com todos os seus alertas.

## <a name="overview"></a>Descrição geral

O esquema de [alerta comum](https://aka.ms/commonAlertSchemaDocs) fornece um esquema JSON padronizado e extensível em todos os seus diferentes tipos de alerta. O esquema de alerta comum é mais útil quando alavancado programáticamente – através de webhooks, runbooks e aplicações lógicas. Neste artigo, demonstramos como uma única aplicação lógica pode ser da autoria de todos os seus alertas. Os mesmos princípios podem ser aplicados a outros métodos programáticos. A aplicação lógica descrita neste artigo cria variáveis bem definidas para os [campos 'essenciais',](alerts-common-schema-definitions.md#essentials)e também descreve como pode lidar com lógica específica do tipo de [alerta.](alerts-common-schema-definitions.md#alert-context)


## <a name="prerequisites"></a>Pré-requisitos 

Este artigo assume que o leitor está familiarizado com 
* Definição de regras de alerta[(métrica,](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric) [log,](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) [registo de atividade)](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)
* Criação de [grupos](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) de ação
* Permitir o esquema de [alerta comum](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema#how-do-i-enable-the-common-alert-schema) de grupos de ação

## <a name="create-a-logic-app-leveraging-the-common-alert-schema"></a>Criar uma aplicação lógica alavancando o esquema de alerta comum

1. Siga os [passos delineados para criar a sua aplicação lógica](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups-logic-app). 

1.  Selecione o gatilho: **Quando um pedido HTTP for recebido**.

    ![Gatilhos de aplicativológico](media/action-groups-logic-app/logic-app-triggers.png "Acionadores da aplicação lógica")

1.  Selecione **Editar** para alterar o gatilho de pedido HTTP.

    ![GATILHOS de pedido http](media/action-groups-logic-app/http-request-trigger-shape.png "GATILHOS de pedido http")


1.  Copiar e colar o seguinte esquema:

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

1. Selecione **+** **Novo passo** e, em seguida, escolha Adicionar uma **ação**.

    ![Adicione uma ação](media/action-groups-logic-app/add-action.png "Adicionar uma ação")

1. Nesta fase, você pode adicionar uma variedade de conectores (Microsoft Teams, Slack, Salesforce, etc.) com base nos seus requisitos de negócio específicos. Pode utilizar os "campos essenciais" fora da caixa. 

    ![Campos essenciais](media/alerts-common-schema-integrations/logic-app-essential-fields.png "Campos essenciais")
    
    Em alternativa, pode ser autor de lógica condicional com base no tipo de alerta utilizando a opção 'Expressão'.

    ![Expressão de aplicativo lógico](media/alerts-common-schema-integrations/logic-app-expressions.png "Expressão de aplicativo lógico")
    
     O [campo 'monitorservice'](alerts-common-schema-definitions.md#alert-context) permite identificar de forma única o tipo de alerta, com base no qual pode criar a lógica condicional.

    
    Por exemplo, o snippet abaixo verifica se o alerta é um alerta de registo baseado em Informações de Aplicação e, em caso afirmativo, imprime os resultados da pesquisa. Caso contrário, imprime "NA".

    ```text
      if(equals(triggerBody()?['data']?['essentials']?['monitoringService'],'Application Insights'),triggerBody()?['data']?['alertContext']?['SearchResults'],'NA')
    ```
    
     Saiba mais sobre [escrever expressões de aplicativos lógicas.](https://docs.microsoft.com/azure/logic-apps/workflow-definition-language-functions-reference#logical-comparison-functions)

    


## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre grupos de ação.](../../azure-monitor/platform/action-groups.md)
* Saiba mais sobre o esquema de [alerta comum.](https://aka.ms/commonAlertSchemaDocs)

