---
title: Como usar o agente de In-Process de informação de Aplicações Java em Azure Spring Cloud
description: Como monitorizar aplicações e microserviços usando App Insights Java In-Process Agente em Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 12/04/2020
ms.custom: devx-track-java
ms.openlocfilehash: c4871c3de8028eec1b6184c1d03ac2180b50f57d
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881355"
---
# <a name="application-insights-java-in-process-agent-in-azure-spring-cloud-preview"></a>Insights de aplicação Java In-Process Agente em Azure Spring Cloud (Preview)

Este documento explica como monitorizar aplicações e microserviços utilizando o agente Java Application Insights em Azure Spring Cloud. 

Com esta funcionalidade pode:

* Pesquisar dados de rastreio com filtros diferentes.
* Ver mapa de dependência de microserviços.
* Verifique o desempenho do pedido.
* Monitorize métricas em tempo real.
* Verifique as falhas do pedido.
* Verifique as métricas da aplicação.

Os Insights de Aplicação fornecem muitas perspetivas observáveis, incluindo:

* Mapa da aplicação
* Desempenho
* Falhas
* Métricas
* Live Metrics
* Disponibilidade

## <a name="enable-java-in-process-agent-for-application-insights"></a>Ativar o Agente de In-Process de Java para insights de aplicações

Ativar a pré-visualização do Agente Java In-Process utilizando o seguinte procedimento.

1. Aceda à página geral do serviço da sua instância de serviço.
2. Clique na entrada **do Application Insights** sob a lâmina de monitorização.
3. Clique no botão **'Ativar insights de aplicação'** para ativar a integração **de Insights de Aplicação.**
4. Selecione uma instância existente de Application Insights ou crie uma nova.
5. Chick **Enable Java em processo agente** para ativar a funcionalidade de agente de pré-visualização Java no processo. Aqui também pode personalizar a taxa de amostragem de 0 a 100.
6.  Clique em **Guardar** para guardar a alteração.

## <a name="portal"></a>Portal

1. Vá ao **serviço | Visão geral** e selecione **Insights de Aplicação** na secção **De Monitorização.** 
2. Clique **em Ativar Insights de Aplicação** para ativar insights de aplicações em Azure Spring Cloud.
3. Clique **em Ativar o agente em processo de Java** para ativar a funcionalidade de pré-visualização java IPA. Quando uma pré-visualização do IPA está ativada, pode configurar uma taxa de amostragem opcional (padrão 10,0%).

  [![IPA 0](media/spring-cloud-application-insights/insights-process-agent-0.png)](media/spring-cloud-application-insights/insights-process-agent-0.png)

## <a name="using-the-application-insights-feature"></a>Utilizando a funcionalidade Insights de Aplicação

Quando a funcionalidade **'Insights de Aplicação'** estiver ativada, pode:

No painel de navegação à esquerda, clique em **Insights de Aplicação** para saltar para a página **geral** de Insights de Aplicação. 

* Clique no **Mapa de Aplicações** para ver o estado das chamadas entre aplicações.

  [![IPA 2](media/spring-cloud-application-insights/insights-process-agent-2-map.png)](media/spring-cloud-application-insights/insights-process-agent-2-map.png)

* Clique no link entre o cliente-serviço e `petclinic` para ver mais detalhes, como uma consulta da SQL.

* No painel de navegação à esquerda, clique em **Performance** para ver os dados de desempenho das operações de todas as aplicações, bem como dependências e funções.

  [![IPA 4](media/spring-cloud-application-insights/insights-process-agent-4-performance.png)](media/spring-cloud-application-insights/insights-process-agent-4-performance.png)

* No painel de navegação esquerdo, clique em **Falhas** para ver se algo inesperado das suas aplicações.

  [![IPA 6](media/spring-cloud-application-insights/insights-process-agent-6-failures.png)](media/spring-cloud-application-insights/insights-process-agent-6-failures.png)

* No painel de navegação esquerdo, clique em **Métricas** e selecione o espaço de nome, verá ambas as métricas de Bota de primavera e métricas personalizadas, se houver.

  [![IPA 7](media/spring-cloud-application-insights/insights-process-agent-5-metrics.png)](media/spring-cloud-application-insights/insights-process-agent-5-metrics.png)

* No painel de navegação esquerdo, clique em **Métricas Vivas** para ver as métricas em tempo real para diferentes dimensões.

  [![IPA 8](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)

* No painel de navegação à esquerda, clique em **Disponibilidade** para monitorizar a disponibilidade e capacidade de resposta das aplicações web, criando [testes de Disponibilidade em Insights de Aplicação.](../azure-monitor/app/monitor-web-app-availability.md)

  [![IPA 9](media/spring-cloud-application-insights/petclinic-microservices-availability.jpg)](media/spring-cloud-application-insights/petclinic-microservices-availability.jpg)

## <a name="arm-template"></a>Modelo do ARM
Para utilizar o modelo Azure Resource Manager, copie o seguinte conteúdo para `azuredeploy.json` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.AppPlatform/Spring",
            "name": "customize this",
            "apiVersion": "2020-07-01",
            "location": "[resourceGroup().location]",
            "resources": [
                {
                    "type": "monitoringSettings",
                    "apiVersion": "2020-11-01-preview",
                    "name": "default",
                    "properties": {
                        "appInsightsInstrumentationKey": "00000000-0000-0000-0000-000000000000",
                        "appInsightsSamplingRate": 88.0
                    },
                    "dependsOn": [
                        "[resourceId('Microsoft.AppPlatform/Spring', 'customize this')]"
                    ]
                }
            ],
            "properties": {}
        }
    ]
}
```

## <a name="cli"></a>CLI
Aplicar o modelo ARM com o comando CLI:

* Para um exemplo existente da Nuvem de primavera de Azure:

```azurecli
az spring-cloud app-insights update [--app-insights/--app-insights-key] "assignedName" [--sampling-rate] "samplingRate" –name "assignedName" –resource-group "resourceGroupName"
```
* Para um recém-criado exemplo de Nuvem de primavera Azure:

```azurecli
az spring-cloud create/update [--app-insights]/[--app-insights-key] "assignedName" --disable-app-insights false --enable-java-agent true --name "assignedName" –resource-group "resourceGroupName"
```
* Para desativar a informação sobre aplicações:

```azurecli
az spring-cloud app-insights update --disable –name "assignedName" –resource-group "resourceGroupName"

```

## <a name="see-also"></a>Veja também
* [Use rastreio distribuído com Azure Spring Cloud](spring-cloud-tutorial-distributed-tracing.md)
* [Analisar registos e métricas](diagnostic-services.md)
* [Transmitir em fluxo registos em tempo real](spring-cloud-howto-log-streaming.md)