---
title: Monitor de aplicações em execução em Funções Azure com Insights de Aplicação - Monitor Azure Microsoft Docs
description: O Azure Monitor integra-se perfeitamente com a sua aplicação em execução em Funções Azure, e permite-lhe monitorizar o desempenho e detetar os problemas com as suas apps num instante.
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 06/26/2020
ms.openlocfilehash: 093448ad0280ada039f1d4e5abd0e83e4be19b03
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85482110"
---
# <a name="monitoring-azure-functions-with-azure-monitor-application-insights"></a>Monitorização de funções do Azure com Insights de Aplicação do Monitor Azure

[A Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) oferece integração incorporada com a Azure Application Insights para monitorizar funções. 

O Application Insights recolhe dados de registo, desempenho e erro e deteta automaticamente anomalias de desempenho. O Application Insights inclui ferramentas de análise poderosas para ajudá-lo a diagnosticar problemas e a compreender como as suas funções são usadas. Quando tem a visibilidade nos dados da sua aplicação, pode melhorar continuamente o desempenho e a usabilidade. Pode até usar o Application Insights durante o desenvolvimento de projetos de aplicações de funções locais. 

A instrumentação necessária para insights de aplicação é incorporada em Funções Azure. A única coisa que precisa é de uma chave de instrumentação válida para ligar a sua aplicação de função a um recurso Application Insights. A chave de instrumentação deve ser adicionada às definições da sua aplicação quando o recurso da aplicação de função for criado em Azure. Se a sua aplicação de função ainda não tiver esta chave, pode defini-la manualmente. Para obter mais informações, leia mais sobre [a monitorização das Funções Azure](https://docs.microsoft.com/azure/azure-functions/functions-monitoring?tabs=cmd).

## <a name="distributed-tracing-for-java-applications-on-windows-public-preview"></a>Rastreio distribuído para aplicações Java no Windows (pré-visualização pública)

> [!IMPORTANT]
> Esta funcionalidade encontra-se atualmente em pré-visualização pública para As Funções Java Azure no Windows, não sendo suportada a localização para As Funções Java Azure no Linux. Para o plano de consumo tem um início frio de 8-9 segundos.

Se as suas aplicações estiverem escritas em Java, pode visualizar dados mais ricos das suas funções, incluindo, pedidos, dependências, registos e métricas. Os dados adicionais também permitem ver e diagnosticar transações de ponta a ponta e ver o mapa de aplicações, que agrega muitas transações para mostrar uma visão topológica de como os sistemas interagem, e quais são as taxas médias de desempenho e erro.

Os diagnósticos de ponta a ponta e o mapa de aplicações proporcionam visibilidade numa única transação/pedido. Juntas estas duas características são muito úteis para encontrar a causa principal de problemas de fiabilidade e estrangulamentos de desempenho numa base de pedido.

### <a name="how-to-enable-distributed-tracing-for-java-function-apps"></a>Como ativar o rastreio distribuído para aplicações Java Function?

Navegue para a app funções Vista geral, vá para configurações. Nas Definições de Aplicação, clique em "+ Nova definição de aplicação". Adicione as duas definições de aplicação abaixo com valores abaixo e, em seguida, clique em Guardar no canto superior esquerdo. PRONTO, OBRIGADO!

```
XDT_MicrosoftApplicationInsights_Java -> 1
ApplicationInsightsAgent_EXTENSION_VERSION -> ~2
```

## <a name="next-steps"></a>Passos Seguintes

* Ler mais instruções e informações sobre monitorização das funções de [Azure](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)
* Obtenha uma visão geral do [Rastreio Distribuído](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing)
* Veja o que o [Mapa de Aplicações](https://docs.microsoft.com/azure/azure-monitor/app/app-map?tabs=net) pode fazer para o seu negócio
* Leia sobre [pedidos e dependências de aplicativos Java](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent)
* Saiba mais sobre [o Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) e [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)