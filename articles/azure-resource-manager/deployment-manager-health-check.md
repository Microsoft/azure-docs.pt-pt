---
title: Introduzir a implementação de integração do Estado de funcionamento para o Gestor de implementação do Azure
description: Descreve como implementar um serviço ao longo de várias regiões com o Gestor de implementação do Azure. Ela mostra práticas recomendadas de implantação segura para verificar a estabilidade da sua implementação antes de implementar para todas as regiões.
services: azure-resource-manager
documentationcenter: na
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: jgao
ms.openlocfilehash: 0c6d32f06e30bd85c12967ce4b15a053bb505ab7
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594314"
---
# <a name="introduce-health-integration-rollout-to-azure-deployment-manager-public-preview"></a>Introduzir a implementação de integração do Estado de funcionamento para o Azure Deployment Manager (pré-visualização pública)

[O Gestor de implementação do Azure](./deployment-manager-overview.md) permite efetuar uma implementação faseada de recursos do Azure Resource Manager. Os recursos são implementados a região a região de uma forma ordenada. A verificação de estado de funcionamento integrados do Azure Deployment Manager pode monitorizar implementações e automaticamente implementações problemático de parar, para que possa resolver problemas e reduzir a escala do impacto. Esta funcionalidade pode reduzir causada por regressões nas atualizações de indisponibilidade de serviço.

## <a name="health-monitoring-providers"></a>Fornecedores de monitorização de estado de funcionamento

Para fazer a integração de estado de funcionamento mais fácil possível, Microsoft tem trabalhado com algumas da empresas para lhe fornecer uma solução simples de copiar/colar para integrar as verificações de estado de funcionamento com as suas implementações de monitoramento de integridade de principais de serviço. Se ainda não estiver a utilizar um monitor de estado de funcionamento, são soluções excelentes para começar:

| ![implementação do Azure manager health monitor fornecedor datadog](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-datadog.svg) | ![site24x7 de fornecedor do monitor de estado de funcionamento do implementação do Azure Gestor](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-site24x7.svg) | ![wavefront de fornecedor do monitor de estado de funcionamento do implementação do Azure Gestor](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-wavefront.svg) |
|-----|------|------|
|O Datadog, o líder de monitorização e plataforma de análise para ambientes de cloud modernas. Ver [como o Datadog se integra com o Gestor de implementação do Azure](https://www.datadoghq.com/azure-deployment-manager/).|Site24x7, a nuvem privada e pública de tudo-em-um dos serviços de solução de monitorização. Ver [como Site24x7 se integra com o Gestor de implementação do Azure](https://www.site24x7.com/azure/adm.html).| Wavefront, a plataforma de monitorização e análise nos ambientes de aplicativos de várias Clouds. Ver [como Wavefront se integra com o Gestor de implementação do Azure](https://go.wavefront.com/wavefront-adm/).|

## <a name="how-service-health-is-determined"></a>Como é determinado Estado de funcionamento do serviço

[Fornecedores de monitorização de estado de funcionamento](#health-monitoring-providers) oferecem vários mecanismos para serviços de monitorização e alerta-de quaisquer problemas de estado de funcionamento do serviço. [O Azure Monitor](../azure-monitor/overview.md) é um exemplo de uma oferta. Monitor do Azure pode ser utilizado para criar alertas quando determinados limites são ultrapassados. Por exemplo, sua memória e utilização da CPU expandam para além dos níveis esperados ao implementar uma nova atualização ao seu serviço. Quando for notificado, pode efetuar ações corretivas.

Esses provedores de estado de funcionamento oferecem, normalmente, as APIs REST, para que o estado dos monitores do seu serviço pode ser examinado por meio de programação. As APIs REST pode optar por voltar com um sinal de bom estado de funcionamento/mau estado de funcionamento simple (determinado pelo código da resposta HTTP) e/ou com informações detalhadas sobre os sinais de que está a receber.

A nova *healthCheck* passo no Gestor de implementação do Azure permite-lhe declarar os códigos HTTP que indicam um serviço de estado de funcionamento, ou, para os resultados da REST mais complexos, também pode especificar as expressões regulares, se coincidirem, que indicam que um bom estado de funcionamento resposta.

O fluxo para obter a configuração com verificações de estado de funcionamento do Gestor de implementação do Azure:

1. Crie os monitores de estado de funcionamento através de um fornecedor de serviço de estado de funcionamento da sua preferência.
1. Crie um ou mais passos de healthCheck como parte da sua implementação do Gestor de implementação do Azure. Preencha os passos de healthCheck com as seguintes informações:

    1. O URI para a API REST para o estado de funcionamento monitoriza (conforme definido pelo seu fornecedor de serviço de estado de funcionamento).
    1. Informações de autenticação. Atualmente a autenticação de estilo de chave de API só é suportada.
    1. [Códigos de estado HTTP](https://www.wikipedia.org/wiki/List_of_HTTP_status_codes) ou expressões regulares que definem uma resposta de bom estado de funcionamento. Tenha em atenção de que pode fornecer as expressões regulares, que tem todos os correspondência para a resposta para ser considerado em bom estado, ou pode fornecer a expressões que qualquer tem de corresponder para a resposta ser considerado em bom estado. Ambos os métodos são suportados.

    O Json seguinte é um exemplo:

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

1. Invoca os passos de healthCheck no momento apropriado na sua implementação do Gestor de implementação do Azure. No exemplo a seguir, um passo de verificação de estado de funcionamento é invocado **postDeploymentSteps** dos **stepGroup2**.

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

Para percorrer um exemplo, consulte [Tutorial: Utilizar a verificação de estado de funcionamento no Gestor de implementação do Azure](./deployment-manager-health-check.md).

## <a name="phases-of-a-health-check"></a>Fases de uma verificação de estado de funcionamento

Neste momento o Gestor de implementação do Azure sabe como consultar o estado de funcionamento do seu serviço e em quais fases na sua implementação para o fazer. No entanto, o Gestor de implementação do Azure também permite que para o período de tempo dessas verificações de configuração avançado. Um passo de healthCheck é executado em 3 fases sequenciais, todos os quais têm durações configuráveis: 

1. Wait

    1. Depois de concluída uma operação de implementação, poderão ser reiniciar VMs, reconfiguração baseadas em novos dados ou até mesmo a ser iniciada pela primeira vez. Ela também leva tempo para os serviços começar a emitir o sinais de estado de funcionamento para serem agregados pelo Estado de funcionamento de provedor de monitoramento em algo útil. Durante este processo tumultuado, talvez não faça sentido para verificar o estado de funcionamento do serviço, uma vez que a atualização ainda não atingiu um estado estável. Na verdade, o serviço pode ser oscillating entre os Estados com e sem integridade como os recursos de liquidação. 
    1. Durante a fase de espera, o estado de funcionamento do serviço não é monitorizado. Isto é utilizado para permitir que os recursos implementados o tempo para inserir antes de iniciar o processo de verificação de estado de funcionamento. 
1. Elástico

    1. Uma vez que é impossível saber em todos os casos, o tempo que recursos irão demorar para implantar antes que se tornem estável, a fase de elástica permite um período de tempo de flexível entre quando os recursos estão potencialmente instáveis e quando elas são necessárias para manter um bom estado de funcionamento estável Estado.
    1. Quando é iniciada a elástica fase, o Gestor de implementação do Azure começa a consulta o ponto final REST fornecido para o service health periodicamente. O intervalo de consulta é configurável. 
    1. Se o monitor de estado de funcionamento volta com sinais que indica que o serviço está em mau estado de funcionamento, esses sinais são ignoradas, continua a fase de elástica e continua a consulta. 
    1. Assim que o monitor de estado de funcionamento volta com sinais que indica que o serviço está em bom estado, fase de elástico ends e a fase de HealthyState começa. 
    1. Portanto, a duração especificada para a fase de elástica é a quantidade máxima de tempo que pode ser gasto a consulta de estado de funcionamento do serviço antes de uma resposta de bom estado de funcionamento é considerada obrigatória. 
1. HealthyState

    1. Durante a fase de HealthyState, o estado de funcionamento do serviço continuamente é consultado no mesmo intervalo como a fase de elástica. 
    1. O serviço é esperado para manter o bom estado de funcionamento sinais do fornecedor de monitorização de estado de funcionamento para a duração especificada. 
    1. Se a qualquer momento for detetada uma resposta de mau estado de funcionamento, Gestor de implementação do Azure irá parar a implementação de toda e retornar a resposta REST com os sinais de mau estado de funcionamento do serviço.
    1. Assim que a duração de HealthyState terminou, o healthCheck está concluída, e continua a implementação para o passo seguinte.

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu sobre como integrar a monitorização de estado de funcionamento no Gestor de implementação do Azure. Avance para o artigo seguinte para saber como implementar com o Gestor de implementação.

> [!div class="nextstepaction"]
> [Tutorial: integrar a verificação de estado de funcionamento no Gestor de implementação do Azure](./deployment-manager-tutorial-health-check.md)