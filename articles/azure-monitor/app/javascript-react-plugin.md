---
title: Reagir plugin para Informações JavaScript SDK
description: Como instalar e utilizar o plugin React para o JavaScript SDK do Application Insights.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: 4970cacb0995678bdad87f14ba971b8fb88ffa09
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100593648"
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

Inicializar uma ligação aos Insights de Aplicação:

```javascript
// AppInsights.js
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ReactPlugin } from '@microsoft/applicationinsights-react-js';
import { createBrowserHistory } from 'history';

const browserHistory = createBrowserHistory({ basename: '' });
const reactPlugin = new ReactPlugin();
const appInsights = new ApplicationInsights({
    config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [reactPlugin],
        extensionConfig: {
          [reactPlugin.identifier]: { history: browserHistory }
        }
    }
});
appInsights.loadAppInsights();
export { reactPlugin, appInsights };
```

Embrulhe o seu componente com a função componente de ordem superior para ativar os Insights de Aplicação:

```javascript
import React from 'react';
import { withAITracking } from '@microsoft/applicationinsights-react-js';
import { reactPlugin, appInsights } from './AppInsights';

// To instrument various React components usage tracking, apply the `withAITracking` higher-order
// component function.

class MyComponent extends React.Component {
    ...
}

// withAITracking takes 4 parameters ( reactPlugin, Component, ComponentName, className) 
// the first two are required and the other two are optional.

export default withAITracking(reactPlugin, MyComponent);
```

## <a name="configuration"></a>Configuração

| Name    | Predefinição | Description                                                                                                    |
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

## <a name="using-react-hooks"></a>Usando ganchos de reação

[React Hooks](https://reactjs.org/docs/hooks-reference.html) é uma abordagem à gestão do estado e do ciclo de vida numa aplicação React sem depender de componentes react baseados na classe. O plugin Application Insights React fornece uma série de integrações de Ganchos que operam de forma semelhante à abordagem componente de ordem superior.

### <a name="using-react-context"></a>Usando o contexto de reagem

Os Ganchos de Reação para Insights de Aplicação são projetados para usar [o Contexto de Reação](https://reactjs.org/docs/context.html) como um aspeto contendo para o mesmo. Para utilizar o Contexto, inicialize os Insights de Aplicação como acima e, em seguida, importe o objeto Contexto:

```javascript
import React from "react";
import { AppInsightsContext } from "@microsoft/applicationinsights-react-js";
import { reactPlugin } from "./AppInsights";

const App = () => {
    return (
        <AppInsightsContext.Provider value={reactPlugin}>
            /* your application here */
        </AppInsightsContext.Provider>
    );
};
```

Este Fornecedor de Contexto disponibilizará insights de aplicação como gancho `useContext` em todos os componentes infantis do mesmo.

```javascript
import React from "react";
import { useAppInsightsContext } from "@microsoft/applicationinsights-react-js";

const MyComponent = () => {
    const appInsights = useAppInsightsContext();
    
    appInsights.trackMetric("Component 'MyComponent' is in use");
    
    return (
        <h1>My Component</h1>
    );
}
export default MyComponent;
```

### `useTrackMetric`

O `useTrackMetric` Gancho replica a funcionalidade do componente de ordem `withAITracking` superior, sem adicionar um componente adicional à estrutura do componente. O Gancho leva dois argumentos, primeiro é a instância Application Insights (que pode ser obtida a partir do `useAppInsightsContext` Gancho), e um identificador para o componente de rastreio (como o seu nome).

```javascript
import React from "react";
import { useAppInsightsContext, useTrackMetric } from "@microsoft/applicationinsights-react-js";

const MyComponent = () => {
    const appInsights = useAppInsightsContext();
    const trackComponent = useTrackMetric(appInsights, "MyComponent");
    
    return (
        <h1 onHover={trackComponent} onClick={trackComponent}>My Component</h1>
    );
}
export default MyComponent;
```

Funcionará como o componente de ordem superior, mas responderá a eventos do ciclo de vida do Hooks, em vez de um ciclo de vida componente. O Gancho tem de ser explicitamente fornecido aos eventos do utilizador se houver necessidade de executar interações específicas.

### `useTrackEvent`

O `useTrackEvent` Gancho é utilizado para rastrear qualquer evento personalizado que uma aplicação possa precisar de rastrear, como um clique de botão ou outra chamada API. Leva dois argumentos, o primeiro é a instância Application Insights (que pode ser obtida a partir do `useAppInsightsContext` Gancho), e um nome para o evento.

```javascript
import React, { useState, useEffect } from "react";
import { useAppInsightsContext, useTrackEvent } from "@microsoft/applicationinsights-react-js";

const ProductCart = () => {
    const appInsights = useAppInsightsContext();
    const trackCheckout = useTrackEvent(appInsights, "Checkout");
    const trackCartUpdate = useTrackEvent(appInsights, "Cart Updated");
    const [cart, setCart] = useState([]);
    
    useEffect(() => {
        trackCartUpdate({ cartCount: cart.length });
    }, [cart]);
    
    const performCheckout = () => {
        trackCheckout();
        // submit data
    };
    
    return (
        <div>
            <ul>
                <li>Product 1 <button onClick={() => setCart([...cart, "Product 1"])}>Add to Cart</button>
                <li>Product 2 <button onClick={() => setCart([...cart, "Product 2"])}>Add to Cart</button>
                <li>Product 3 <button onClick={() => setCart([...cart, "Product 3"])}>Add to Cart</button>
                <li>Product 4 <button onClick={() => setCart([...cart, "Product 4"])}>Add to Cart</button>
            </ul>
            <button onClick={performCheckout}>Checkout</button>
        </div>
    );
}
export default MyComponent;
```

Quando o Gancho é utilizado, pode ser-lhe fornecida uma carga útil de dados para adicionar dados adicionais ao evento quando este for armazenado no Application Insights.

## <a name="react-error-boundaries"></a>Reagir limites de erro

[Os Limites de Erro](https://reactjs.org/docs/error-boundaries.html) fornecem uma forma de lidar graciosamente com uma exceção quando ocorre dentro de uma aplicação React, e quando tal erro ocorre é provável que a exceção tenha de ser registada. O Plugin react para Insights de Aplicação fornece um componente de Limite de Erro que registará automaticamente o erro quando ocorre.

```javascript
import React from "react";
import { reactPlugin } from "./AppInsights";
import { AppInsightsErrorBoundary } from "@microsoft/applicationinsights-react-js";

const App = () => {
    return (
        <AppInsightsErrorBoundary onError={() => <h1>I believe something went wrong</h1>} appInsights={reactPlugin}>
            /* app here */
        </AppInsightsErrorBoundary>
    );
};
```

Requer `AppInsightsErrorBoundary` dois adereços para lhe serem passados, o `ReactPlugin` exemplo criado para a aplicação e um componente a ser prestado quando ocorre um erro. Quando ocorre um erro não manipulado, `trackException` é chamado com as informações fornecidas à Fronteira de Erro e o componente é `onError` apresentado.

## <a name="sample-app"></a>Aplicativo de amostra

Confira a [demonstração de Informação de Aplicação](https://github.com/Azure-Samples/application-insights-react-demo)React .

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre o JavaScript SDK, consulte a [documentação SDK javaScript da aplicação.](javascript.md)
- Para saber mais sobre a linguagem de consulta de Kusto e consultando dados em Log Analytics, consulte a [visão geral](../../azure-monitor/logs/log-query-overview.md)da consulta de log .
