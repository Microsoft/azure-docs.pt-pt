---
title: Azure Application Insights / Microsoft Docs
description: Correlacionar os dados dos Insights de Aplicação a outros conjuntos de dados, tais como o enriquecimento de dados ou tabelas de procura, fontes de dados não-Application Insights e dados personalizados.
ms.topic: conceptual
author: eternovsky
ms.author: evternov
ms.date: 08/08/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 17de697686485d695586ffa798196efb4a34c251
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87092920"
---
# <a name="correlating-application-insights-data-with-custom-data-sources"></a>Correlacionar dados de Insights de Aplicações com fontes de dados personalizadas

O Application Insights recolhe vários tipos de dados diferentes: exceções, vestígios, visualizações de páginas e outros. Embora isto seja muitas vezes suficiente para investigar o desempenho, fiabilidade e utilização da sua aplicação, existem casos em que é útil correlacionar os dados armazenados no Application Insights com outros conjuntos de dados completamente personalizados.

Algumas situações em que pode querer dados personalizados incluem:

- Tabelas de enriquecimento de dados ou de procura: por exemplo, complementar um nome de servidor com o proprietário do servidor e a localização do laboratório em que possa ser encontrado 
- Correlação com fontes de dados não-Application Insights: por exemplo, correlacionar dados sobre uma compra numa loja web com informações do seu serviço de realização de compras para determinar a precisão das suas estimativas de tempo de envio foram 
- Dados completamente personalizados: muitos dos nossos clientes adoram a linguagem de consulta e desempenho da plataforma de registo do Azure Monitor que apoia a Application Insights, e querem usá-lo para consultar dados que não estão de todo relacionados com a Application Insights. Por exemplo, para acompanhar o desempenho do painel solar como parte de uma instalação inteligente em casa, tal como delineado [aqui.](https://www.catapultsystems.com/blogs/using-log-analytics-and-a-special-guest-to-forecast-electricity-generation/)

## <a name="how-to-correlate-custom-data-with-application-insights-data"></a>Como correlacionar dados personalizados com dados de Insights de Aplicação 

Uma vez que o Application Insights é apoiado pela poderosa plataforma de log do Azure Monitor, somos capazes de usar toda a potência do Azure Monitor para ingerir os dados. Em seguida, escreveremos consultas usando o operador de "join" que irá correlacionar estes dados personalizados com os dados disponíveis nos registos do Azure Monitor. 

## <a name="ingesting-data"></a>Ingerir dados

Nesta secção, vamos rever como obter os seus dados nos registos do Azure Monitor.

Se ainda não tiver um, provisa um novo espaço de trabalho do Log Analytics seguindo [estas instruções](../learn/quick-collect-azurevm.md) através e incluindo o passo "criar um espaço de trabalho".

Para começar a enviar dados de registo para o Azure Monitor. Existem várias opções:

- Para um mecanismo sincronizado, pode ligar diretamente para a [API do colecionador de dados](../platform/data-collector-api.md) ou utilizar o nosso conector Logic App – basta procurar "Azure Log Analytics" e escolher a opção "Enviar Dados":

  ![Screenshot escolha e ação](./media/custom-data-correlation/01-logic-app-connector.png)  

- Para uma opção assíncrono, utilize a API do Coletor de Dados para construir um gasoduto de processamento. Consulte [este artigo](../platform/create-pipeline-datacollector-api.md) para mais detalhes.

## <a name="correlating-data"></a>Correlacionar dados

O Application Insights baseia-se na plataforma de registo do Monitor Azure. Por isso, [podemos usar juntas de recursos cruzados](../log-query/cross-workspace-query.md) para correlacionar quaisquer dados que ingerimos no Azure Monitor com os nossos dados de Insights de Aplicação.

Por exemplo, podemos ingerir o nosso inventário de laboratório e localizações numa mesa chamada "LabLocations_CL" num espaço de trabalho do Log Analytics chamado "myLA". Se quiséssemos então rever os nossos pedidos rastreados na app Application Insights chamada "myAI" e correlacionar os nomes das máquinas que serviam os pedidos para as localizações destas máquinas armazenadas na tabela personalizada anteriormente mencionada, podemos executar a seguinte consulta a partir do contexto Application Insights ou Azure Monitor:

```
app('myAI').requests
| join kind= leftouter (
    workspace('myLA').LabLocations_CL
    | project Computer_S, Owner_S, Lab_S
) on $left.cloud_RoleInstance == $right.Computer
```

## <a name="next-steps"></a>Passos Seguintes

- Consulte a referência [da API do Colecionador de Dados.](../platform/data-collector-api.md)
- Para mais informações sobre [a junção de recursos cruzados.](../log-query/cross-workspace-query.md)
