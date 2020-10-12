---
title: Reagir plugin para Informações JavaScript SDK
description: Como instalar e utilizar o plugin React para o JavaScript SDK do Application Insights.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: 3a11f77384c520bed9824841269be4ad998adba4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90056205"
---
# <a name="react-plugin-for-application-insights-javascript-sdk"></a>Reagir plugin para Informações JavaScript SDK

Reagir plugin para o JavaScript SDK de Informações, ativa:

- Rastreio das alterações de rota
- Reagir estatísticas de utilização de componentes

## <a name="getting-started"></a>Introdução

Instalar pacote npm:

```bash

npm install @microsoft/applicationinsights-react-js

```

## <a name="basic-usage"></a>Utilização básica

```javascript
import React from 'react';
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ReactPlugin, withAITracking } from '@microsoft/applicationinsights-react-js';
import { createBrowserHistory } from "history";

const browserHistory = createBrowserHistory({ basename: '' });
var reactPlugin = new ReactPlugin();
var appInsights = new ApplicationInsights({
    config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [reactPlugin],
        extensionConfig: {
          [reactPlugin.identifier]: { history: browserHistory }
        }
    }
});
appInsights.loadAppInsights();

// To instrument various React components usage tracking, apply the `withAITracking` higher-order
// component function.

class MyComponent extends React.Component {
    ...
}

export default withAITracking(reactPlugin,appInsights, MyComponent);

```

## <a name="configuration"></a>Configuração

| Nome    | Predefinição | Descrição                                                                                                    |
|---------|---------|----------------------------------------------------------------------------------------------------------------|
| histórico | nulo    | Reagir na história do router. Para obter mais informações, consulte a documentação do [pacote do revesidor de reagem](https://reactrouter.com/web/api/history). Para aprender a aceder ao objeto histórico fora dos componentes, consulte o [Re-router FAQ](https://github.com/ReactTraining/react-router/blob/master/FAQ.md#how-do-i-access-the-history-object-outside-of-components)    |

### <a name="react-components-usage-tracking"></a>Rastreio de utilização de componentes de reagem

Para instrumentar vários componentes react rastreio de utilização, aplique a `withAITracking` função componente de ordem superior.

Medirá o tempo do `ComponentDidMount` evento através do `ComponentWillUnmount` evento. No entanto, para tornar isto mais preciso, subtrairá o tempo em que o utilizador estava inativo `React Component Engaged Time = ComponentWillUnmount timestamp - ComponentDidMount timestamp - idle time` .

Para ver esta métrica no portal Azure é necessário navegar para o recurso Application Insights, selecione o separador "Métricas" e configure as tabelas vazias para apresentar o nome métrico personalizado "Reagir Componente Tempo De entrada (segundos)", selecione agregação (soma, avg, etc.) da sua métrica e aplique divisão seja "Nome componente".

![Screenshot do gráfico que exibe a métrica personalizada "React Component Engaged Time (segundos)" dividida por "Nome do componente"](./media/javascript-react-plugin/chart.png)

Também pode executar consultas personalizadas para dividir os dados do Application Insights para gerar relatórios e visualizações de acordo com os seus requisitos. No portal Azure navegue para o recurso Application Insights, selecione "Analytics" a partir do menu superior do separador Vista Geral e execute a sua consulta.

![Screenshot dos resultados da consulta métrica personalizada.](./media/javascript-react-plugin/query.png)

> [!NOTE]
> Pode levar até 10 minutos para que novas métricas personalizadas apareçam no Portal Azure.

## <a name="sample-app"></a>Aplicativo de amostra

Confira a [demonstração de Informação de Aplicação](https://github.com/Azure-Samples/application-insights-react-demo)React .

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre o JavaScript SDK, consulte a [documentação SDK javaScript da aplicação.](javascript.md)
- Para saber mais sobre a linguagem de consulta de Kusto e consultando dados em Log Analytics, consulte a [visão geral](../../azure-monitor/log-query/log-query-overview.md)da consulta de log .
