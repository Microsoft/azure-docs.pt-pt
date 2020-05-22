---
title: Monitor aplicações em execução em Funções Azure com Insights de Aplicação - Monitor Azure / Microsoft Docs
description: O Azure Monitor integra-se perfeitamente com a sua aplicação em funcionamento nas Funções Azure, e permite-lhe monitorizar o desempenho e detetar os problemas com as suas apps num instante.
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 05/20/2020
ms.openlocfilehash: a936c77abb9aed5886fae8b2ec4a10bb076b7cb5
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83776742"
---
# <a name="monitoring-azure-functions-with-azure-monitor-application-insights"></a>Monitorização de Funções Azure com Insights de Aplicação do Monitor Azure

[A Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) oferece integração integrada com insights de aplicação Azure para monitorizar funções. 

Application Insights recolhe dados de registo, desempenho e erro e deteta automaticamente anomalias de desempenho. Os Insights de Aplicação incluem ferramentas de análise poderosas para ajudá-lo a diagnosticar problemas e a entender como as suas funções são usadas. Quando tiver visibilidade nos dados da sua aplicação, pode melhorar continuamente o desempenho e a usabilidade. Pode até utilizar os Insights de Aplicação durante o desenvolvimento do projeto de aplicação de funções locais. 

A instrumentação de Insights de Aplicação necessária é incorporada nas Funções Azure. A única coisa que precisa é de uma chave de instrumentação válida para ligar a sua aplicação de função a um recurso Desinformação de Aplicações. A chave de instrumentação deve ser adicionada às definições da sua aplicação quando o recurso da aplicação de função for criado no Azure. Se a sua aplicação de funções ainda não tiver esta chave, pode defini-la manualmente. Para mais informações, leia mais informações sobre monitorização das [Funções Azure](https://docs.microsoft.com/azure/azure-functions/functions-monitoring?tabs=cmd).

## <a name="distributed-tracing-for-java-applications-public-preview"></a>Rastreio distribuído para aplicações java (pré-visualização pública)


> [!IMPORTANT]
> Esta funcionalidade encontra-se atualmente em pré-visualização pública para as Funções Java Azure. Para o plano de consumo tem uma implicação de início frio de 8-9 segundos.

Se as suas aplicações estiverem escritas em Java, pode ver dados mais ricos a partir das suas aplicações de funções, incluindo, pedidos, dependências, registos e métricas. Os dados adicionais também permitem ver e diagnosticar transações de ponta a ponta e ver o mapa de aplicações, que agrega muitas transações para mostrar uma visão topológica de como os sistemas interagem, e quais são as taxas médias de desempenho e erro.

Os diagnósticos de ponta a ponta e o mapa de aplicações proporcionam visibilidade numa única transação/pedido. Juntos, estas duas funcionalidades são muito úteis para encontrar a causa principal de problemas de fiabilidade e estrangulamentos de desempenho por pedido.

### <a name="how-to-enable-distributed-tracing-for-java-function-apps"></a>Como permitir o rastreio distribuído para aplicações Java Function?

Navegue para a aplicação de funções Visão geral da lâmina, vá para configurações. Em definições de aplicação, clique em "+ nova definição de aplicação". Adicione as seguintes duas definições de aplicação com valores abaixo e, em seguida, clique em Guardar na parte superior esquerda. PRONTO!

```
XDT_MicrosoftApplicationInsights_Java -> 1
ApplicationInsightsAgent_EXTENSION_VERSION -> ~2
```

## <a name="next-steps"></a>Passos Seguintes

* Leia mais instruções e informações sobre monitorização das [Funções Azure](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)
* Obtenha uma visão geral do [Rastreio Distribuído](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing)
* Veja o que o Mapa de [Aplicações](https://docs.microsoft.com/azure/azure-monitor/app/app-map?tabs=net) pode fazer para o seu negócio
* Ler sobre [pedidos e dependências para apps Java](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent)
* Saiba mais sobre [o Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/overview) e [insights de aplicação](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)