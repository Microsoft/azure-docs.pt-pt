---
title: Lançamento da integração em saúde - Gestor de Implantação Azure
description: Descreve como implementar um serviço ao longo de várias regiões com o Gestor de implementação do Azure. Ela mostra práticas recomendadas de implantação segura para verificar a estabilidade da sua implementação antes de implementar para todas as regiões.
author: mumian
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: jgao
ms.openlocfilehash: aa99bdfcbc2f42ae81bdd55c266bcd7d87808031
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78388151"
---
# <a name="introduce-health-integration-rollout-to-azure-deployment-manager-public-preview"></a>Introduzir o lançamento da integração em saúde ao Gestor de Implantação do Azure (pré-visualização pública)

[O Gestor de Implantação azure](./deployment-manager-overview.md) permite-lhe realizar lançamentos encenados de recursos do Gestor de Recursos Do Azure. Os recursos são implantados região por região de forma ordenada. O exame de saúde integrado do Gestor de Implantação do Azure pode monitorizar os lançamentos e parar automaticamente os lançamentos problemáticos, para que possa resolver problemas e reduzir a escala do impacto. Esta funcionalidade pode reduzir a indisponibilidade do serviço causada por regressões em atualizações.

## <a name="health-monitoring-providers"></a>Prestadores de cuidados de saúde

Para facilitar a integração em saúde o mais possível, a Microsoft tem trabalhado com algumas das principais empresas de monitorização da saúde para lhe fornecer uma solução simples de cópia/pasta para integrar os controlos de saúde com as suas implementações. Se ainda não está a usar um monitor de saúde, estas são ótimas soluções para começar:

| ![azure deployment manager health monitor health monitor datadog](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-datadog.svg) | ![site de monitorização de saúde do gestor de implantação azure 24x7](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-site24x7.svg) | ![azure deployment manager health monitor de saúde provedor de ondas](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-wavefront.svg) |
|-----|------|------|
|Datadog, a principal plataforma de monitorização e análise para ambientes de nuvem moderna. Veja como o Datadog se integra com o Gestor de [Implantação azure.](https://www.datadoghq.com/azure-deployment-manager/)|Site24x7, a solução de monitorização de serviços de nuvem privada e pública. Veja [como o Site24x7 se integra com o Gestor de Implantação do Azure.](https://www.site24x7.com/azure/adm.html)| Wavefront, a plataforma de monitorização e análise para ambientes de aplicações multi-cloud. Veja como a Wavefront se integra com o Gestor de [Implantação Azure.](https://go.wavefront.com/wavefront-adm/)|

## <a name="how-service-health-is-determined"></a>Como a saúde do serviço é determinada

[Os prestadores](#health-monitoring-providers) de cuidados de saúde oferecem vários mecanismos de monitorização dos serviços e alertam-no para quaisquer problemas de saúde do serviço. [O Azure Monitor](../../azure-monitor/overview.md) é um exemplo de uma dessas ofertas. O Monitor Azure pode ser utilizado para criar alertas quando determinados limiares são ultrapassados. Por exemplo, a sua memória e utilização de CPU aumentam para além dos níveis esperados quando implementa uma nova atualização para o seu serviço. Quando notificado, pode tomar medidas corretivas.

Estes prestadores de saúde normalmente oferecem APIs REST para que o estado dos monitores do seu serviço possa ser examinado programáticamente. As APIs rest podem voltar com um simples sinal saudável/insalubre (determinado pelo código de resposta HTTP) e/ou com informações detalhadas sobre os sinais que está a receber.

O novo passo *healthCheck* no Azure Deployment Manager permite-lhe declarar códigos HTTP que indicam um serviço saudável, ou, para resultados REST mais complexos, pode até especificar expressões regulares que, se coincidirem, indicam uma resposta saudável.

O fluxo para a configuração com o Gestor de Destacamento Azure verifica a saúde:

1. Crie os seus monitores de saúde através de um prestador de serviços de saúde à sua escolha.
1. Crie um ou mais passos de verificação de saúde Como parte do lançamento do seu Gestor de Implantação Azure. Preencha os passos de saúdeConfira os passos com as seguintes informações:

    1. O URI para a API REST para os seus monitores de saúde (conforme definido pelo seu prestador de serviços de saúde).
    1. Informação de autenticação. Atualmente, apenas a autenticação de estilo chave API é suportada.
    1. [Códigos](https://www.wikipedia.org/wiki/List_of_HTTP_status_codes) de estado HTTP ou expressões regulares que definem uma resposta saudável. Note que pode fornecer expressões regulares, que TODOS devem corresponder para que a resposta seja considerada saudável, ou pode fornecer expressões das quais qualquer deve corresponder para que a resposta seja considerada saudável. Ambos os métodos são apoiados.

    O seguinte Json é um exemplo:

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

1. Invoque os passos healthCheck no momento apropriado no lançamento do seu Gestor de Implantação Azure. No exemplo seguinte, é invocado um passo de verificação de saúde no **postDeploymentSteps** do **stepGroup2**.

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

Para percorrer um exemplo, consulte [Tutorial: Use o health check in Azure Deployment Manager](./deployment-manager-health-check.md).

## <a name="phases-of-a-health-check"></a>Fases de um exame de saúde

Neste momento, o Gestor de Implantação azure sabe como consultar a saúde do seu serviço e em que fases do seu lançamento o fará. No entanto, o Gestor de Implementação do Azure também permite uma configuração profunda do tempo destas verificações. Uma etapa healthCheck é executada em 3 fases sequenciais, todas com durações configuráveis: 

1. Wait

    1. Após a conclusão de uma operação de implantação, os VMs podem estar a reiniciar, reconfigurando-se com base em novos dados, ou mesmo sendo iniciados pela primeira vez. Os serviços demoram também a que os serviços comecem a emitir sinais de saúde para serem agregados pelo prestador de cuidados de saúde em algo útil. Durante este processo tumultuoso, pode não fazer sentido verificar a saúde do serviço, uma vez que a atualização ainda não chegou a um estado estável. Com efeito, o serviço pode oscilar entre estados saudáveis e pouco saudáveis à medida que os recursos se instalam. 
    1. Durante a fase de espera, a saúde do serviço não é monitorizada. Isto é usado para permitir aos recursos implantados o tempo para assar antes de iniciar o processo de verificação de saúde. 
1. Elástico

    1. Uma vez que é impossível saber em todos os casos quanto tempo os recursos demorarão a assar antes de se tornarem estáveis, a fase Elástica permite um período de tempo flexível entre quando os recursos são potencialmente instáveis e quando são necessários para manter um estável saudável estado.
    1. Quando a fase Elástica começa, o Gestor de Implantação azure começa a sondar periodicamente o ponto final de REST fornecido para a saúde do serviço. O intervalo de votação é configurável. 
    1. Se o monitor de saúde voltar com sinais que indicam que o serviço não é saudável, estes sinais são ignorados, a fase Elástica continua e as sondagens continuam. 
    1. Assim que o monitor de saúde volta com sinais indicando que o serviço é saudável, a fase Elástica termina e começa a fase HealthyState. 
    1. Assim, a duração especificada para a fase Elástica é o tempo máximo que pode ser gasto a votar para a saúde do serviço antes que uma resposta saudável seja considerada obrigatória. 
1. HealthyState

    1. Durante a fase HealthyState, a saúde do serviço é continuamente sondada no mesmo intervalo que a fase Elástica. 
    1. Espera-se que o serviço mantenha sinais saudáveis do prestador de monitorização da saúde durante toda a duração especificada. 
    1. Se em algum momento for detetada uma resposta pouco saudável, o Gestor de Implantação do Azure irá parar todo o lançamento e devolver a resposta REST transportando os sinais de serviço pouco saudáveis.
    1. Uma vez terminada a duração do HealthyState, o healthCheck está completo, e a implementação continua para o próximo passo.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a integrar a monitorização da saúde no Gestor de Implantação do Azure. Avance para o artigo seguinte para saber como implementar com o Gestor de implementação.

> [!div class="nextstepaction"]
> [Tutorial: integrar o exame de saúde no Gestor de Implantação do Azure](./deployment-manager-tutorial-health-check.md)