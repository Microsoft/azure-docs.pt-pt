---
title: Distribuição de integração de integridade-Deployment Manager do Azure
description: Descreve como implantar um serviço em várias regiões com o Azure Deployment Manager. Ele mostra práticas de implantação segura para verificar a estabilidade de sua implantação antes de distribuir para todas as regiões.
services: azure-resource-manager
documentationcenter: na
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: jgao
ms.openlocfilehash: 72ddc900a892e6391d6b54046ac6f3a42358526f
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72528559"
---
# <a name="introduce-health-integration-rollout-to-azure-deployment-manager-public-preview"></a>Introduzir a distribuição de integração de integridade para o Azure Deployment Manager (visualização pública)

O [Azure Deployment Manager](./deployment-manager-overview.md) permite que você execute distribuições em etapas de recursos de Azure Resource Manager. Os recursos são implantados na região por região de maneira ordenada. A verificação de integridade integrada do Azure Deployment Manager pode monitorar as distribuições e parar automaticamente as distribuições problemáticas, para que você possa solucionar problemas e reduzir a escala do impacto. Esse recurso pode reduzir a indisponibilidade de serviço causada por regressões em atualizações.

## <a name="health-monitoring-providers"></a>Provedores de monitoramento de integridade

Para tornar a integração de integridade o mais fácil possível, a Microsoft tem trabalhado com algumas das principais empresas de monitoramento de integridade do serviço para fornecer uma solução simples de copiar/colar para integrar verificações de integridade às suas implantações. Se você ainda não estiver usando um monitor de integridade, estas são excelentes soluções para começar:

| ![provedor de monitoramento de integridade do Gerenciador de implantação do Azure datadog](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-datadog.svg) | ![provedor de monitoramento de integridade do Gerenciador de implantação do Azure site24x7](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-site24x7.svg) | ![provedor de monitoramento de integridade do Gerenciador de implantação do Azure Wavefront](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-wavefront.svg) |
|-----|------|------|
|Datadog, a principal plataforma de monitoramento e análise para ambientes de nuvem modernos. Veja [como o Datadog se integra ao Azure Deployment Manager](https://www.datadoghq.com/azure-deployment-manager/).|Site24x7, a solução de monitoramento de serviços de nuvem pública e privado tudo em um. Veja [como o Site24x7 se integra ao Azure Deployment Manager](https://www.site24x7.com/azure/adm.html).| Wavefront, a plataforma de monitoramento e análise para ambientes de aplicativos de várias nuvens. Veja [como o Wavefront se integra ao Azure Deployment Manager](https://go.wavefront.com/wavefront-adm/).|

## <a name="how-service-health-is-determined"></a>Como a integridade do serviço é determinada

Os [provedores de monitoramento de integridade](#health-monitoring-providers) oferecem vários mecanismos para monitorar serviços e alertá-lo sobre qualquer problema de integridade do serviço. [Azure monitor](../azure-monitor/overview.md) é um exemplo de uma dessas ofertas. Azure Monitor pode ser usado para criar alertas quando determinados limites são excedidos. Por exemplo, o pico de utilização da memória e da CPU ultrapassa os níveis esperados quando você implanta uma nova atualização para seu serviço. Quando notificado, você pode tomar medidas corretivas.

Esses provedores de integridade normalmente oferecem APIs REST para que o status dos monitores de seu serviço possa ser examinado programaticamente. As APIs REST podem voltar com um simples sinal íntegro/não íntegro (determinado pelo código de resposta HTTP) e/ou com informações detalhadas sobre os sinais que ele está recebendo.

A nova etapa *HealthCheck* no Azure Deployment Manager permite que você declare códigos http que indicam um serviço íntegro ou, para resultados Rest mais complexos, você pode até mesmo especificar expressões regulares que, se corresponderem, indicam uma resposta íntegra.

O fluxo para obter a instalação com as verificações de integridade do Azure Deployment Manager:

1. Crie seus monitores de integridade por meio de um provedor de serviços de integridade de sua escolha.
1. Crie uma ou mais etapas do healthCheck como parte de sua distribuição de Deployment Manager do Azure. Preencha as etapas de healthCheck com as seguintes informações:

    1. O URI para a API REST para seus monitores de integridade (conforme definido pelo seu provedor de serviços de integridade).
    1. Informações de autenticação. Atualmente, somente a autenticação de estilo de chave de API tem suporte.
    1. [Códigos de status http](https://www.wikipedia.org/wiki/List_of_HTTP_status_codes) ou expressões regulares que definem uma resposta íntegra. Observe que você pode fornecer expressões regulares, as quais todas devem corresponder para que a resposta seja considerada íntegra, ou você pode fornecer expressões das quais qualquer um deve corresponder para que a resposta seja considerada íntegra. Os dois métodos têm suporte.

    O JSON a seguir é um exemplo:

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

1. Invoque as etapas de healthCheck no momento apropriado em sua distribuição de Deployment Manager do Azure. No exemplo a seguir, uma etapa de verificação de integridade é invocada em **postDeploymentSteps** de **stepGroup2**.

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

Para percorrer um exemplo, consulte [tutorial: usar a verificação de integridade no Azure Deployment Manager](./deployment-manager-health-check.md).

## <a name="phases-of-a-health-check"></a>Fases de uma verificação de integridade

Neste ponto, o Azure Deployment Manager sabe como consultar a integridade do seu serviço e em quais fases em sua distribuição fazer isso. No entanto, o Azure Deployment Manager também permite uma configuração profunda do tempo dessas verificações. Uma etapa healthCheck é executada em três fases sequenciais, todas com durações configuráveis: 

1. Wait

    1. Após a conclusão de uma operação de implantação, as VMs podem ser reinicializadas, reconfiguradas com base em novos dados ou até serem iniciadas pela primeira vez. Também leva tempo para que os serviços comecem a emitir sinais de integridade para serem agregados pelo provedor de monitoramento de integridade em algo útil. Durante esse processo de tumultuada, talvez não faça sentido verificar a integridade do serviço, pois a atualização ainda não atingiu um estado estável. Na verdade, o serviço pode ser oscillating entre Estados íntegros e não íntegros à medida que os recursos são liquidados. 
    1. Durante a fase de espera, a integridade do serviço não é monitorada. Isso é usado para permitir que os recursos implantados detortam antes de iniciar o processo de verificação de integridade. 
1. Elástico

    1. Como é impossível saber em todos os casos quanto tempo os recursos levarão para serem disparados antes de se tornarem estáveis, a fase elástica permite um período de tempo flexível entre o momento em que os recursos são potencialmente instáveis e quando eles são necessários para manter uma integridade estável status.
    1. Quando a fase elástica começa, o Azure Deployment Manager começa a sondar o ponto de extremidade REST fornecido para integridade do serviço periodicamente. O intervalo de sondagem é configurável. 
    1. Se o Health Monitor voltar com sinais indicando que o serviço não está íntegro, esses sinais serão ignorados, a fase elástica continuará e a sondagem continuará. 
    1. Assim que o monitor de integridade volta com sinais indicando que o serviço está íntegro, a fase elástica termina e a fase Íntegrostate começa. 
    1. Assim, a duração especificada para a fase elástica é a quantidade máxima de tempo que pode ser gasta sondando a integridade do serviço antes que uma resposta íntegra seja considerada obrigatória. 
1. Estado íntegro

    1. Durante a fase Íntegrostate, a integridade do serviço é sondada continuamente no mesmo intervalo da fase elástica. 
    1. Espera-se que o serviço mantenha sinais íntegros do provedor de monitoramento de integridade para toda a duração especificada. 
    1. Se a qualquer momento uma resposta não íntegra for detectada, o Azure Deployment Manager interromperá toda a distribuição e retornará a resposta REST que está realizando os sinais de serviço não íntegros.
    1. Depois que a duração de Íntegrostate terminar, o healthCheck estará concluído e a implantação continuará na próxima etapa.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu sobre como integrar o monitoramento de integridade no Azure Deployment Manager. Vá para o próximo artigo para aprender a implantar com o Deployment Manager.

> [!div class="nextstepaction"]
> [Tutorial: integrar a verificação de integridade no Azure Deployment Manager](./deployment-manager-tutorial-health-check.md)