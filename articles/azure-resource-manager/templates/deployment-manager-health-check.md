---
title: Implementação da integração na saúde - Azure Deployment Manager
description: Descreve como implementar um serviço em muitas regiões com O Gestor de Implementação Azure. Mostra práticas de implantação seguras para verificar a estabilidade da sua implantação antes de ser lançado para todas as regiões.
author: mumian
ms.topic: conceptual
ms.date: 09/21/2020
ms.author: jgao
ms.openlocfilehash: ae95269dbac3ef1561e19d4b7ea5dd383c1eed73
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99536972"
---
# <a name="introduce-health-integration-rollout-to-azure-deployment-manager-public-preview"></a>Introduzir o lançamento da integração sanitária ao Azure Deployment Manager (visualização pública)

[O Azure Deployment Manager](./deployment-manager-overview.md) permite-lhe executar lançamentos encenados de recursos do Azure Resource Manager. Os recursos são mobilizados região por região de forma ordenada. O controlo de saúde integrado do Azure Deployment Manager pode monitorizar os lançamentos e parar automaticamente os lançamentos problemáticos, para que possa resolver problemas e reduzir a escala do impacto. Esta funcionalidade pode reduzir a indisponibilidade de serviço causada por regressões nas atualizações.

## <a name="health-monitoring-providers"></a>Prestadores de cuidados de saúde

De forma a facilitar a integração na saúde, a Microsoft tem vindo a trabalhar com algumas das principais empresas de monitorização de saúde de serviços para lhe fornecer uma solução simples de cópia/pasta para integrar os controlos de saúde com as suas implementações. Se ainda não está a usar um monitor de saúde, estas são ótimas soluções para começar:

| ![monitor de saúde azure gestor de saúde monitor azure monitor](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-azure-monitor.svg)| ![datadog de acompanhamento de saúde gestor de implementação azure](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-datadog.svg) | ![azure gestor de monitorização de saúde site24x7](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-site24x7.svg) | ![azure gestor de monitorização de saúde monitor de saúde frente de onda](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-wavefront.svg) |
|-----|-----|------|------|
|Azure Monitor, plataforma de observabilidade completa da Microsoft para monitorização e análise híbridas nativas da nuvem &. |Datadog, a principal plataforma de monitorização e análise para ambientes modernos em nuvem. Veja [como o Datadog se integra com o Azure Deployment Manager](https://www.datadoghq.com/azure-deployment-manager/).|Site24x7, a solução de monitorização de serviços de nuvem privada e pública. Veja [como o Site24x7 se integra com o Gestor de Implementação do Azure](https://www.site24x7.com/azure/adm.html).| Wavefront, a plataforma de monitorização e análise para ambientes de aplicações multi-nuvem. Veja [como a Wavefront se integra com o Azure Deployment Manager](https://go.wavefront.com/wavefront-adm/).|

## <a name="how-service-health-is-determined"></a>Como a saúde do serviço é determinada

[Os prestadores de cuidados de saúde](#health-monitoring-providers) oferecem vários mecanismos de monitorização dos serviços e alertando-o de quaisquer problemas de saúde do serviço. [O Azure Monitor](../../azure-monitor/overview.md) é um exemplo de uma dessas ofertas. O Azure Monitor pode ser utilizado para criar alertas quando determinados limiares são ultrapassados. Por exemplo, a sua memória e o pico de utilização do CPU para além dos níveis esperados quando implementa uma nova atualização para o seu serviço. Quando notificado, pode tomar medidas corretivas.

Estes prestadores de saúde normalmente oferecem APIs DE REST para que o estado dos monitores do seu serviço possa ser examinado programáticamente. As APIs REST podem voltar com um sinal simples saudável/insalubre (determinado pelo código de resposta HTTP) e/ou com informações detalhadas sobre os sinais que está a receber.

O novo `healthCheck` passo no Azure Deployment Manager permite-lhe declarar códigos HTTP que indicam um serviço saudável. Para resultados complexos de REST pode especificar expressões regulares que, quando combinados, indicam uma resposta saudável.

O fluxo para configurar os controlos de saúde do Gestor de Implantação do Azure:

1. Crie os seus monitores de saúde através de um prestador de serviços de saúde à sua escolha.
1. Crie um ou mais `healthCheck` passos como parte do seu lançamento do Azure Deployment Manager. Preencha os `healthCheck` passos com as seguintes informações:

    1. O URI para a API REST para os seus monitores de saúde (conforme definido pelo seu prestador de serviços de saúde).
    1. Informação de autenticação. Atualmente apenas a autenticação do estilo chave API é suportada. Para o Azure Monitor, o tipo de autenticação deve ser definido como `RolloutIdentity` a identidade gerida atribuída pelo utilizador utilizada para o lançamento do Azure Deployment Manager estende-se para o Azure Monitor.
    1. [Códigos de estado HTTP](https://www.wikipedia.org/wiki/List_of_HTTP_status_codes) ou expressões regulares que definem uma resposta saudável. Pode fornecer expressões regulares, que TODOS devem corresponder para que a resposta seja considerada saudável, ou pode fornecer expressões das quais ANY deve corresponder para que a resposta seja considerada saudável. Ambos os métodos são apoiados.

    O JSON seguinte é um exemplo para integrar o Azure Monitor com o Azure Deployment Manager. O exemplo utiliza `RolloutIdentity` e estabelece um exame de saúde onde um lançamento prossegue se não houver alertas. A única API suportada do Monitor AZure: [Alertas – Get All](/rest/api/monitor/alertsmanagement/alerts/getall).

    ```json
    {
      "type": "Microsoft.DeploymentManager/steps",
      "apiVersion": "2018-09-01-preview",
      "name": "healthCheckStep",
      "location": "[parameters('azureResourceLocation')]",
      "properties": {
        "stepType": "healthCheck",
        "attributes": {
          "waitDuration": "PT1M",
          "maxElasticDuration": "PT1M",
          "healthyStateDuration": "PT1M",
          "type": "REST",
          "properties": {
            "healthChecks": [
              {
                "name": "appHealth",
                "request": {
                  "method": "GET",
                  "uri": "[parameters('healthCheckUrl')]",
                  "authentication": {
                    "type": "RolloutIdentity"
                  }
                },
                "response": {
                  "successStatusCodes": [
                    "200"
                  ],
                  "regex": {
                    "matches": [
                      "\"value\":\\[\\]"
                    ],
                    "matchQuantifier": "All"
                  }
                }
              }
            ]
          }
        }
      }
    }
    ```

    O seguinte JSON é um exemplo para todos os outros prestadores de cuidados de saúde:

    ```json
    {
      "type": "Microsoft.DeploymentManager/steps",
      "apiVersion": "2018-09-01-preview",
      "name": "healthCheckStep",
      "location": "[parameters('azureResourceLocation')]",
      "properties": {
        "stepType": "healthCheck",
        "attributes": {
          "waitDuration": "PT0M",
          "maxElasticDuration": "PT0M",
          "healthyStateDuration": "PT1M",
          "type": "REST",
          "properties": {
            "healthChecks": [
              {
                "name": "appHealth",
                "request": {
                  "method": "GET",
                  "uri": "[parameters('healthCheckUrl')]",
                  "authentication": {
                    "type": "ApiKey",
                    "name": "code",
                    "in": "Query",
                    "value": "[parameters('healthCheckAuthAPIKey')]"
                  }
                },
                "response": {
                  "successStatusCodes": [
                    "200"
                  ],
                  "regex": {
                    "matches": [
                      "Status: healthy",
                      "Status: warning"
                    ],
                    "matchQuantifier": "Any"
                  }
                }
              }
            ]
          }
        }
      }
    },
    ```

1. Invoque os `healthCheck` passos no momento apropriado no seu lançamento do Azure Deployment Manager. No exemplo seguinte, é invocado um `healthCheck` passo de `postDeploymentSteps` `stepGroup2` .

    ```json
    "stepGroups": [
        {
            "name": "stepGroup1",
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup2",
            "dependsOnStepGroups": ["stepGroup1"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit1.name)]",
            "postDeploymentSteps": [
                {
                    "stepId": "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
                }
            ]
        },
        {
            "name": "stepGroup3",
            "dependsOnStepGroups": ["stepGroup2"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup4",
            "dependsOnStepGroups": ["stepGroup3"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit1.name)]",
            "postDeploymentSteps": []
        }
    ]
    ```

Para percorrer um exemplo, consulte [Tutorial: Use o exame de saúde no Azure Deployment Manager](./deployment-manager-tutorial-health-check.md).

## <a name="phases-of-a-health-check"></a>Fases de um exame de saúde

Neste ponto, o Azure Deployment Manager sabe como consultar a saúde do seu serviço e em que fases do seu lançamento o fazem. No entanto, o Azure Deployment Manager também permite uma configuração profunda do tempo destas verificações. Um `healthCheck` passo é executado em três fases sequenciais, todas com durações configuráveis:

1. Wait

    1. Após a conclusão de uma operação de implantação, os VMs podem estar a reiniciar, reconfigurar com base em novos dados ou mesmo a ser iniciados pela primeira vez. Também leva tempo para que os serviços comecem a emitir sinais de saúde para serem agregados pelo prestador de cuidados de saúde em algo útil. Durante este processo tumultuoso, pode não fazer sentido verificar a saúde do serviço, uma vez que a atualização ainda não atingiu um estado estável. Na verdade, o serviço pode estar a oscilar entre estados saudáveis e pouco saudáveis à medida que os recursos se instalam.
    1. Durante a fase de espera, a saúde do serviço não é monitorizada. Isto é usado para permitir aos recursos implantados o tempo para cozer antes de iniciar o processo de verificação de saúde.

1. Elástico

    1. Uma vez que é impossível saber em todos os casos quanto tempo demorará até que os recursos se tornem estáveis, a fase elástica permite um período de tempo flexível entre quando os recursos são potencialmente instáveis e quando são necessários para manter um estado estável saudável.
    1. Quando a fase elástica começa, o Gestor de Implantação do Azure começa a sondar periodicamente o ponto final providenciado rest para a saúde do serviço. O intervalo de votação é configurável.
    1. Se o monitor de saúde voltar com sinais que indicam que o serviço não é saudável, estes sinais são ignorados, a fase elástica continua, e as sondagens continuam.
    1. Quando o monitor de saúde devolve sinais indicando que o serviço está saudável, a fase elástica termina e começa a fase HealthyState.
    1. Assim, a duração especificada para a fase elástica é o tempo máximo que pode ser gasto a votar para a saúde do serviço antes de uma resposta saudável ser considerada obrigatória.

1. Estado Saudável

    1. Durante a fase HealthyState, a saúde do serviço é continuamente sondada no mesmo intervalo que a fase elástica.
    1. Espera-se que o serviço mantenha sinais saudáveis do prestador de cuidados de saúde durante toda a duração especificada.
    1. Se em algum momento for detetada uma resposta pouco saudável, o Gestor de Implementação Azure irá parar todo o lançamento e devolver a resposta REST carregando os sinais de serviço pouco saudáveis.
    1. Após o fim da duração do Estado Saudável, o `healthCheck` está completo e a implementação continua para o passo seguinte.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu sobre como integrar a monitorização de saúde no Azure Deployment Manager. Dirija-se ao próximo artigo para aprender a implementar com o Gestor de Implementação.

> [!div class="nextstepaction"]
> [Tutorial: Use o cheque de saúde no Azure Deployment Manager](./deployment-manager-tutorial-health-check.md)
