---
title: Plugin angular para insights de aplicação JavaScript SDK
description: Como instalar e utilizar plugin angular para aplicações Insights JavaScript SDK.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: lagayhar
ms.openlocfilehash: d45d8bed328dc91dfeeabd6ce878074fa1218623
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101737023"
---
# <a name="angular-plugin-for-application-insights-javascript-sdk"></a>Plugin angular para insights de aplicação JavaScript SDK

O plugin angular para o SDK JavaScript de aplicações permite:

- Rastreio de alterações do router

> [!WARNING]
> O plugin angular não é compatível com eCMAScript 3 (ES3).

## <a name="getting-started"></a>Introdução

Instalar pacote npm:

```bash
npm install @microsoft/applicationinsights-angularplugin-js
```

## <a name="basic-usage"></a>Utilização básica

Configurar uma instância de Application Insights no componente de entrada na sua aplicação:

```js
import { Component } from '@angular/core';
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { AngularPlugin } from '@microsoft/applicationinsights-angularplugin-js';
import { Router } from '@angular/router';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
    constructor(
        private router: Router
    ){
        var angularPlugin = new AngularPlugin();
        const appInsights = new ApplicationInsights({ config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [angularPlugin],
        extensionConfig: {
            [angularPlugin.identifier]: { router: this.router }
        }
        } });
        appInsights.loadAppInsights();
    }
}
```

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre o JavaScript SDK, consulte a documentação do [SDK JavaScript Da aplicação](javascript.md)
- [Plugin angular no GitHub](https://github.com/microsoft/ApplicationInsights-JS/tree/master/extensions/applicationinsights-angularplugin-js)
