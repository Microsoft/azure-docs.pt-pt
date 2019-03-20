---
title: Application Insights do Azure | Documentos da Microsoft
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
ms.author: Evgeny.Ternovsky
ms.openlocfilehash: cbb144cc8aac6dc8e90d196147b0c154471b7239
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58102065"
---
# <a name="correlating-application-insights-data-with-custom-data-sources"></a>Correlacionar dados do Application Insights com origens de dados personalizados

O Application Insights recolhe vários tipos de dados diferentes: exceções, rastreios, vistas de página e outras pessoas. Enquanto isso, muitas vezes, é suficiente para investigar o desempenho, a confiabilidade e a utilização do seu aplicativo, há casos em que é útil correlacionar os dados armazenados no Application Insights para outros conjuntos de dados totalmente personalizados.

Algumas situações em que pode desejar dados personalizados incluem:

- As tabelas de melhoria ou de pesquisa de dados: por exemplo, complementar um nome de servidor com o proprietário do servidor e a localização de laboratório em que podem ser encontrados 
- Correlação com origens de dados não - Application Insights: por exemplo, foram a correlação de dados sobre uma compra numa loja web com as informações de seu serviço de preenchimento de compra para determinar as estimativas do tempo de envio de grau de precisão 
- Dados totalmente personalizada: muitos dos nossos clientes gosta de usar a linguagem de consulta e o desempenho da plataforma de registo do Azure Monitor faz o Application Insights e pretender utilizá-lo para consultar os dados que não estão relacionados para o Application Insights. Por exemplo controlar o desempenho do painel de solar como parte de uma instalação de raiz inteligente como descrito [aqui]( https://blogs.catapultsystems.com/cfuller/archive/2017/10/04/using-log-analytics-and-a-special-guest-to-forecast-electricity-generation/).

## <a name="how-to-correlate-custom-data-with-application-insights-data"></a>Como correlacionar dados personalizados com dados do Application Insights 

Uma vez que o Application Insights apoiado pela plataforma do log poderosa do Azure Monitor, é possível utilizar todo o poder do Azure Monitor para incorporar os dados. Em seguida, vamos escrever consultas usando o operador "aderir" que será correlacionar esses dados com os dados disponíveis para nós nos registos do Azure Monitor. 

## <a name="ingesting-data"></a>Ingestão de dados

Nesta secção, vamos rever como colocar os seus dados sobre os registos do Azure Monitor.

Se ainda não tiver uma, aprovisionar uma nova área de trabalho do Log Analytics ao seguir [estas instruções](../learn/quick-collect-azurevm.md) através de e incluindo o passo "Criar uma área de trabalho".

Para começar a enviar dados de registo no Azure Monitor. Existem várias opções:

- Para um mecanismo síncrono, pode optar por chamar diretamente o [API do recoletor de dados](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api) ou utilizar o conector de aplicação lógica – simplesmente procurar por "Azure Log Analytics" e escolha a opção "Enviar dados":

  ![Escolha de captura de ecrã e ação](./media/custom-data-correlation/01-logic-app-connector.png)  

- Para obter uma opção assíncrona, utilize a API de Recoletor de dados para criar um pipeline de processamento. Ver [este artigo](https://docs.microsoft.com/azure/log-analytics/log-analytics-create-pipeline-datacollector-api) para obter detalhes.

## <a name="correlating-data"></a>Correlacionar dados

O Application Insights baseia-se na plataforma de registo do Azure Monitor. Pode, por isso, usamos [associações entre recursos](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search) para correlacionar dados podemos ingeridos no Azure Monitor com nossos dados do Application Insights.

Por exemplo, podemos pode ingerir nosso inventário de laboratório e localizações numa tabela denominada "LabLocations_CL" numa área de trabalho do Log Analytics chamada "myLA". Se quiséssemos, em seguida, reveja os nossos pedidos monitorizados na aplicação do Application Insights chamada "myAI" e correlacionar os nomes das máquinas que serviu os pedidos para as localizações destas máquinas armazenados na tabela personalizada mencionada anteriormente, podemos executar a seguinte consulta do o Application Insights ou o Azure Monitor contexto:

```
app('myAI').requests
| join kind= leftouter (
    workspace('myLA').LabLocations_CL
    | project Computer_S, Owner_S, Lab_S
) on $left.cloud_RoleInstance == $right.Computer
```

## <a name="next-steps"></a>Próximos Passos

- Veja a [API do Recoletor de dados](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api) referência.
- Para obter mais informações sobre [associações entre recursos](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search).
