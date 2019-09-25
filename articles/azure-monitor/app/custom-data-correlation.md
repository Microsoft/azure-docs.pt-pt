---
title: Insights do Aplicativo Azure | Microsoft Docs
description: ''
services: application-insights
documentationcenter: ''
author: eternovsky
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/08/2018
ms.reviewer: mbullwin
ms.author: evternov
ms.openlocfilehash: 3abf50b3467ce9a97f2163a10ad1782f6e1c9c20
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71258433"
---
# <a name="correlating-application-insights-data-with-custom-data-sources"></a>Correlacionando dados de Application Insights com fontes de dados personalizadas

Application Insights coleta vários tipos de dados diferentes: exceções, rastreamentos, exibições de página e outros. Embora isso seja muitas vezes suficiente para investigar o desempenho, a confiabilidade e o uso do aplicativo, há casos em que é útil correlacionar os dados armazenados no Application Insights a outros conjuntos de dados completamente personalizados.

Algumas situações em que você pode querer incluir dados personalizados:

- Enriquecimento de dados ou tabelas de pesquisa: por exemplo, suplementar um nome de servidor com o proprietário do servidor e o local do laboratório no qual ele pode ser encontrado 
- Correlação com fontes de dados não Application Insights: por exemplo, correlacionar dados sobre uma compra em uma loja da Web com informações de seu serviço de preenchimento de compra para determinar a precisão das estimativas de tempo de envio 
- Dados completamente personalizados: muitos de nossos clientes adoram a linguagem de consulta e o desempenho da Azure Monitor plataforma de log que faz o backup Application Insights e desejam usá-lo para consultar dados que não estão relacionados a Application Insights. Por exemplo, para acompanhar o desempenho do painel solar como parte de uma instalação do Smart Home, conforme descrito [aqui](https://www.catapultsystems.com/blogs/using-log-analytics-and-a-special-guest-to-forecast-electricity-generation/).

## <a name="how-to-correlate-custom-data-with-application-insights-data"></a>Como correlacionar dados personalizados com dados de Application Insights 

Como o Application Insights é apoiado pela poderosa plataforma de log de Azure Monitor, podemos usar toda a capacidade de Azure Monitor para ingerir os dados. Em seguida, escreveremos consultas usando o operador "Join" que correlacionará esses dados personalizados com os dados disponíveis nos logs de Azure Monitor. 

## <a name="ingesting-data"></a>Ingestão de dados

Nesta seção, examinaremos como colocar seus dados em logs de Azure Monitor.

Se você ainda não tiver um, provisione um novo espaço de trabalho Log Analytics seguindo [estas instruções](../learn/quick-collect-azurevm.md) por meio de e incluindo a etapa "criar um espaço de trabalho".

Para começar a enviar dados de log para o Azure Monitor. Existem várias opções:

- Para um mecanismo síncrono, você pode chamar diretamente a [API do coletor de dados](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api) ou usar nosso conector de aplicativo lógico – basta procurar "Azure log Analytics" e selecionar a opção "enviar dados":

  ![Escolha e ação de captura de tela](./media/custom-data-correlation/01-logic-app-connector.png)  

- Para uma opção assíncrona, use a API do coletor de dados para criar um pipeline de processamento. Consulte [Este artigo](https://docs.microsoft.com/azure/log-analytics/log-analytics-create-pipeline-datacollector-api) para obter detalhes.

## <a name="correlating-data"></a>Correlacionar dados

O Application Insights é baseado na plataforma de log de Azure Monitor. Portanto, podemos usar [junções entre recursos](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search) para correlacionar todos os dados ingeridos em Azure monitor com nossos dados de Application insights.

Por exemplo, podemos ingerir nosso inventário de laboratório e locais em uma tabela chamada "LabLocations_CL" em um espaço de trabalho Log Analytics chamado "myLA". Se quiséssemos examinar nossas solicitações rastreadas em Application Insights aplicativo chamado "myAI" e correlacionar os nomes dos computadores que forneceram as solicitações para os locais desses computadores armazenados na tabela personalizada mencionada anteriormente, podemos executar a consulta a seguir de o contexto Application Insights ou Azure Monitor:

```
app('myAI').requests
| join kind= leftouter (
    workspace('myLA').LabLocations_CL
    | project Computer_S, Owner_S, Lab_S
) on $left.cloud_RoleInstance == $right.Computer
```

## <a name="next-steps"></a>Próximos Passos

- Confira a referência da [API do coletor de dados](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api) .
- Para obter mais informações sobre [junções entre recursos](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search).
