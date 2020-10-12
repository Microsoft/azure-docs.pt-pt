---
title: Configurar métricas e registos de nuvem para a Azure API Management auto-hospedado gateway / Microsoft Docs
description: Saiba como configurar métricas de nuvem e registos para a Azure API Management porta de entrada auto-hospedada
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/30/2020
ms.author: apimpm
ms.openlocfilehash: 3bbfd167e89ae1b5f9b7de1df5fd1cb72c720cb6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86254538"
---
# <a name="configure-cloud-metrics-and-logs-for-azure-api-management-self-hosted-gateway"></a>Configurar métricas e registos de nuvem para a Azure API Management porta de entrada auto-hospedada

Este artigo fornece detalhes para configurar métricas de nuvem e troncos para o [gateway auto-hospedado](./self-hosted-gateway-overview.md).

O gateway auto-hospedado tem de ser associado a um serviço de gestão API e requer conectividade TCP/IP de saída para Azure na porta 443. O gateway aproveita a ligação de saída para enviar telemetria para Azure, se configurado para fazê-lo. 

## <a name="metrics"></a>Métricas
Por padrão, o gateway auto-hospedado emite uma série de métricas através do [Azure Monitor](https://azure.microsoft.com/services/monitor/), o mesmo que o gateway gerido [na nuvem](api-management-howto-use-azure-monitor.md). 

A funcionalidade pode ser ativada ou desativada utilizando a `telemetry.metrics.cloud` chave no ConfigMap da implementação do gateway. Abaixo está uma desagregação das configurações disponíveis:

| Campo  | Predefinição | Descrição |
| ------------- | ------------- | ------------- |
| telemetria.metrics.cloud  | `true` | Permite fazer login através do Azure Monitor. O valor pode `true` `false` ser, . . |


Aqui está uma configuração de amostra:

```yaml
    apiVersion: v1
    kind: ConfigMap
    metadata:
        name: contoso-gateway-environment
    data:
        config.service.endpoint: "<contoso-gateway-management-endpoint>"
        telemetry.metrics.cloud: "true"
```

O gateway auto-hospedado emite atualmente as seguintes métricas através do Azure Monitor:

| Métrica  | Descrição |
| ------------- | ------------- |
| Pedidos  | Número de pedidos de API no período |
| Duração dos pedidos de gateway | Número de milissegundos a partir do momento em que o gateway recebeu o pedido até ao momento em que a resposta é enviada integralmente |
| Duração dos pedidos de backend | Número de milissegundos despendidos na E/S de back-end global (ligar, enviar e receber bytes)  |

## <a name="logs"></a>Registos

O gateway auto-hospedado atualmente não envia [registos de diagnóstico](./api-management-howto-use-azure-monitor.md#activity-logs) para a nuvem. No entanto, é possível [configurar e persistir registos localmente](how-to-configure-local-metrics-logs.md) onde o gateway auto-hospedado é implantado. 

Se um gateway for implantado no [Serviço Azure Kubernetes,](https://azure.microsoft.com/services/kubernetes-service/)pode ativar [o Azure Monitor para que os contentores](../azure-monitor/insights/container-insights-overview.md) recolham troncos dos seus contentores e os vejam no Log Analytics. 


## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre a porta de entrada auto-hospedada, consulte [a Azure API Management auto-hospedada gateway overview](self-hosted-gateway-overview.md)
* Saiba mais sobre [configurar e persistir registos localmente](how-to-configure-local-metrics-logs.md)
