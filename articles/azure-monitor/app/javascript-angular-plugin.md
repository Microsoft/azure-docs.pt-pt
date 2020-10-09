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
ms.openlocfilehash: 152ba4b1c8a4e09db0bce759f5b67f577ec5d584
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2020
ms.locfileid: "91844030"
---
# <a name="angular-plugin-for-application-insights-javascript-sdk"></a>Plugin angular para insights de aplicação JavaScript SDK

O plugin angular para o SDK JavaScript de aplicações permite:

- Rastreio de alterações do router
- Estatísticas de utilização de componentes angulares

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
import { Component, OnInit } from '@angular/core';
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { AngularPlugin, AngularPluginService } from '@microsoft/applicationinsights-angularplugin-js';
import { Router } from '@angular/router';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent implements OnInit {
    private appInsights;
    constructor(
        private router: Router,
        private angularPluginService: AngularPluginService 
    ){
        var angularPlugin = new AngularPlugin();
        this.angularPluginService.init(angularPlugin, this.router);
        this.appInsights = new ApplicationInsights({ config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [angularPlugin],
        extensionConfig: {
            [angularPlugin.identifier]: { router: this.router }
        }
        } });
    }

    ngOnInit() {
        this.appInsights.loadAppInsights();
    }
}

```

Para utilizar o `trackMetric` método para acompanhar a utilização do componente Angular, adicione como fornecedor na lista de `AngularPluginService` fornecedores em `app.module.ts` ficheiro.

```js
import { AngularPluginService } from '@microsoft/applicationinsights-angularplugin-js';

@NgModule({
    ...
  providers: [ AngularPluginService ],
})
export class AppModule { }
```

Para acompanhar a vida útil de um componente, ligue `trackMetric` para o método desse `ngOnDestroy` componente. Quando o componente for destruído, irá desencadear um `trackMetric` evento que envia o tempo que o utilizador permaneceu na página e o nome do componente.

```js
import { Component, OnDestroy, HostListener } from '@angular/core';
import { AngularPluginService } from '@microsoft/applicationinsights-angularplugin-js';

@Component({
  selector: 'app-test',
  templateUrl: './test.component.html',
  styleUrls: ['./test.component.css']
})
export class TestComponent implements OnDestroy {

  constructor(private angularPluginService: AngularPluginService) {}

  @HostListener('window:beforeunload')
  ngOnDestroy() {
    this.angularPluginService.trackMetric();
  }
}
```

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre o JavaScript SDK, consulte a documentação do [SDK JavaScript Da aplicação](javascript.md)
- [Plugin angular no GitHub](https://github.com/microsoft/ApplicationInsights-JS/tree/master/extensions/applicationinsights-angularplugin-js)