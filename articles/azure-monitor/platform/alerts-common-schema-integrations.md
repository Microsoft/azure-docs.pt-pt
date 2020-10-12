---
title: Como integrar o esquema de alerta comum com apps lógicas
description: Saiba como criar uma aplicação lógica que aproveite o esquema de alerta comum para lidar com todos os seus alertas.
ms.topic: conceptual
ms.subservice: alerts
ms.date: 05/27/2019
ms.openlocfilehash: a7893f41e3e4cce737853fc168c1931f3bf7b532
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87322096"
---
# <a name="how-to-integrate-the-common-alert-schema-with-logic-apps"></a>Como integrar o esquema de alerta comum com apps lógicas

Este artigo mostra-lhe como criar uma aplicação lógica que aproveita o esquema de alerta comum para lidar com todos os seus alertas.

## <a name="overview"></a>Descrição geral

O [esquema de alerta comum](https://aka.ms/commonAlertSchemaDocs) fornece um esquema JSON padronizado e extensível em todos os seus diferentes tipos de alerta. O esquema de alerta comum é mais útil quando alavancado programáticamente – através de webhooks, runbooks e aplicações lógicas. Neste artigo, demonstramos como uma única aplicação lógica pode ser da autoria de todos os seus alertas. Os mesmos princípios podem ser aplicados a outros métodos programáticos. A aplicação lógica descrita neste artigo cria variáveis bem definidas para os [campos 'essenciais'](alerts-common-schema-definitions.md#essentials)e também descreve como pode lidar com a lógica específica [do tipo de alerta.](alerts-common-schema-definitions.md#alert-context)


## <a name="prerequisites"></a>Pré-requisitos 

Este artigo assume que o leitor está familiarizado com 
* Configuração de regras de alerta[(métrica,](./alerts-metric.md) [log,](./alerts-log.md) [log, log de atividades)](./alerts-activity-log.md)
* Criação de [grupos de ação](./action-groups.md)
* Permitir o [esquema de alerta comum](./alerts-common-schema.md#how-do-i-enable-the-common-alert-schema) dentro de grupos de ação

## <a name="create-a-logic-app-leveraging-the-common-alert-schema"></a>Crie uma aplicação lógica aproveitando o esquema de alerta comum

1. Siga os [passos delineados para criar a sua aplicação lógica.](./action-groups-logic-app.md) 

1.  Selecione o gatilho: **Quando um pedido HTTP for recebido**.

    ![Acionadores da aplicação lógica](media/action-groups-logic-app/logic-app-triggers.png "Acionadores da aplicação lógica")

1.  **Selecione editar** para alterar o gatilho de pedido HTTP.

    ![Detonadores de pedidos HTTP](media/action-groups-logic-app/http-request-trigger-shape.png "Detonadores de pedidos HTTP")


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

1. Selecione **+** **Novo passo** e, em seguida, escolha **Adicionar uma ação**.

    ![Adicionar uma ação](media/action-groups-logic-app/add-action.png "Adicionar uma ação")

1. Nesta fase, pode adicionar uma variedade de conectores (Microsoft Teams, Slack, Salesforce, etc.) com base nos seus requisitos específicos de negócio. Pode utilizar os "campos essenciais" fora da caixa. 

    ![Campos essenciais](media/alerts-common-schema-integrations/logic-app-essential-fields.png "Campos essenciais")
    
    Em alternativa, pode escrever lógica condicional com base no tipo de alerta utilizando a opção 'Expressão'.

    ![Expressão de aplicativo lógico](media/alerts-common-schema-integrations/logic-app-expressions.png "Expressão de aplicativo lógico")
    
     O [campo 'monitoringService'](alerts-common-schema-definitions.md#alert-context) permite identificar de forma única o tipo de alerta, com base no qual pode criar a lógica condicional.

    
    Por exemplo, o snippet abaixo verifica se o alerta é um alerta de registo baseado em Insights de Aplicação e, se assim for, imprime os resultados da pesquisa. Caso contrário, imprime 'NA'.

    ```text
      if(equals(triggerBody()?['data']?['essentials']?['monitoringService'],'Application Insights'),triggerBody()?['data']?['alertContext']?['SearchResults'],'NA')
    ```
    
     Saiba mais sobre [a escrita de expressões de aplicativos lógicos.](../../logic-apps/workflow-definition-language-functions-reference.md#logical-comparison-functions)

    


## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre grupos de ação.](./action-groups.md)
* [Saiba mais sobre o esquema de alerta comum.](https://aka.ms/commonAlertSchemaDocs)

