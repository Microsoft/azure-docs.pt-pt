---
title: Insights de aplicação azure / Microsoft Docs
description: Correlacionar dados de Insights de Aplicação a outros conjuntos de dados, tais como o enriquecimento de dados ou mesas de recolha, fontes de dados de Insights não-Aplicação e dados personalizados.
ms.topic: conceptual
author: eternovsky
ms.author: evternov
ms.date: 08/08/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 121e4699bd6a72f6865d3a6ffdef58c1b3806047
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79082766"
---
# <a name="correlating-application-insights-data-with-custom-data-sources"></a>Correlacionar dados de Insights de Aplicação com fontes de dados personalizadas

Application Insights recolhe vários tipos de dados diferentes: exceções, vestígios, visualizações de páginas, entre outros. Embora isto seja muitas vezes suficiente para investigar o desempenho, fiabilidade e utilização da sua aplicação, existem casos em que é útil correlacionar os dados armazenados em Insights de Aplicação a outros conjuntos de dados completamente personalizados.

Algumas situações em que pode querer dados personalizados incluem:

- Tabelas de enriquecimento de dados ou de procura: por exemplo, complemente um nome de servidor com o proprietário do servidor e a localização do laboratório em que pode ser encontrado 
- Correlação com fontes de dados não-Application Insights: por exemplo, correlacionar dados sobre uma compra numa loja web com informações do seu serviço de realização de compras para determinar a precisão das suas estimativas de tempo de envio 
- Dados completamente personalizados: muitos dos nossos clientes adoram a linguagem de consulta e o desempenho da plataforma de registo Do Monitor Azure que apoia os Insights da Aplicação, e querem usá-lo para consultar dados que não estão de todo relacionados com a Application Insights. Por exemplo, para acompanhar o desempenho do painel solar como parte de uma instalação doméstica inteligente, tal como [aqui](https://www.catapultsystems.com/blogs/using-log-analytics-and-a-special-guest-to-forecast-electricity-generation/)descrito .

## <a name="how-to-correlate-custom-data-with-application-insights-data"></a>Como correlacionar dados personalizados com dados de Insights de Aplicação 

Uma vez que o Application Insights é apoiado pela poderosa plataforma de registo Azure Monitor, somos capazes de usar toda a potência do Monitor Azure para ingerir os dados. Em seguida, escreveremos consultas usando o operador de "join" que irá correlacionar estes dados personalizados com os dados disponíveis nos registos do Monitor Do Azure. 

## <a name="ingesting-data"></a>Ingerir dados

Nesta secção, vamos rever como obter os seus dados nos registos do Monitor Do Azure.

Se ainda não tiver um, forme um novo espaço de trabalho log Analytics seguindo [estas instruções](../learn/quick-collect-azurevm.md) e incluindo o passo "criar um espaço de trabalho".

Para começar a enviar dados de registo para o Monitor Azure. Existem várias opções:

- Para um mecanismo sincronizado, pode ligar diretamente para o [coletor](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api) de dados API ou utilizar o nosso conector de aplicações lógicas – basta procurar "Azure Log Analytics" e escolher a opção "Enviar Dados":

  ![Screenshot escolha e ação](./media/custom-data-correlation/01-logic-app-connector.png)  

- Para uma opção assíncrona, utilize a API do Collector de Dados para construir um pipeline de processamento. Consulte [este artigo](https://docs.microsoft.com/azure/log-analytics/log-analytics-create-pipeline-datacollector-api) para mais detalhes.

## <a name="correlating-data"></a>Correlacionar dados

O Application Insights baseia-se na plataforma de registo do Monitor Azure. Podemos, portanto, utilizar [juntas de recursos cruzados](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search) para correlacionar quaisquer dados que insinfámos no Monitor Azure com os nossos dados de Informação de Aplicação.

Por exemplo, podemos ingerir o nosso inventário de laboratório e localizações numa mesa chamada "LabLocations_CL" num espaço de trabalho de Log Analytics chamado "myLA". Se quiséssemos então rever os nossos pedidos rastreados na aplicação Application Insights chamada "myAI" e correlacionar os nomes das máquinas que serviram os pedidos para os locais destas máquinas armazenadas na tabela personalizada anteriormente mencionada, podemos executar a seguinte consulta a partir do contexto Application Insights ou Azure Monitor:

```
app('myAI').requests
| join kind= leftouter (
    workspace('myLA').LabLocations_CL
    | project Computer_S, Owner_S, Lab_S
) on $left.cloud_RoleInstance == $right.Computer
```

## <a name="next-steps"></a>Passos Seguintes

- Confira a referência [da Data Collector API.](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api)
- Para mais informações sobre [a adesão a recursos cruzados.](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search)
