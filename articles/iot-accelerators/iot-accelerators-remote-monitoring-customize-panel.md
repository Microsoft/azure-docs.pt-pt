---
title: Adicione um painel à solução de monitorização remota UI - Azure [ Microsoft Docs
description: Este artigo mostra-lhe como adicionar um novo painel ao painel no acelerador de solução de monitorização remota UI.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/05/2018
ms.topic: conceptual
ms.openlocfilehash: 3b855c3bed75945f44b55463bdacd049b7930aa7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "61447068"
---
# <a name="add-a-custom-panel-to-the-dashboard-in-the-remote-monitoring-solution-accelerator-web-ui"></a>Adicione um painel personalizado ao painel no UI web de solução de monitorização remota

Este artigo mostra-lhe como adicionar um novo painel a uma página de painel na UI web de solução de monitorização remota. O artigo descreve:

- Como preparar um ambiente de desenvolvimento local.
- Como adicionar um novo painel a uma página de painel na UI web.

O painel de exemplo neste artigo apresenta na página do painel existente.

## <a name="prerequisites"></a>Pré-requisitos

Para completar os passos neste guia de como orientar, precisa do seguinte software instalado na sua máquina de desenvolvimento local:

- [Git](https://git-scm.com/downloads)
- [Nó.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Antes de começar

Deve completar os passos na página Personalizada do acelerador de [soluções de monitorização remota](iot-accelerators-remote-monitoring-customize-page.md) antes de continuar.

## <a name="add-a-panel"></a>Adicionar um painel

Para adicionar um painel à UI web, é necessário adicionar os ficheiros de origem que definem o painel e, em seguida, modificar o painel para exibir o painel.

### <a name="add-the-new-files-that-define-the-panel"></a>Adicione os novos ficheiros que definem o painel

Para começar, o **src/walkthrough/componentes/pages/dashboard/panels/examplePanel** contém os ficheiros que definem um painel, incluindo:

**exemploPainel.js**

[!code-javascript[Example panel](~/remote-monitoring-webui/src/walkthrough/components/pages/dashboard/panels/examplePanel/examplePanel.js?name=panel "Example panel")]

Copie a pasta **src/walkthrough/components/pages/dashboard/panel/examplePanel** para a pasta **src/components/pages/dashboard/panel.**

Adicione a seguinte exportação ao ficheiro **src/walkthrough/components/pages/dashboard/panels/index.js:**

```js
export * from './examplePanel';
```

### <a name="add-the-panel-to-the-dashboard"></a>Adicione o painel ao painel

Modifique o **src/componentes/páginas/dashboard/dashboard.js** para adicionar o painel.

Adicione o painel de exemplo à lista de importações de painéis:

```js
import {
  OverviewPanel,
  AlertsPanel,
  TelemetryPanel,
  AnalyticsPanel,
  MapPanel,
  transformTelemetryResponse,
  chartColorObjects,
  ExamplePanel
} from './panels';
```

Adicione a seguinte definição de célula à grelha no conteúdo da página:

```js
          <Cell className="col-2">
            <ExamplePanel t={t} />
          </Cell>
```

## <a name="test-the-flyout"></a>Teste o voo para fora

Se a UI web ainda não estiver a funcionar localmente, execute o seguinte comando na raiz da sua cópia local do repositório:

```cmd/sh
npm start
```

O comando anterior executa [http://localhost:3000/dashboard](http://localhost:3000/dashboard)a UI localmente em . Navegue na página **do Dashboard** para ver o novo painel.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu sobre os recursos disponíveis para ajudá-lo a adicionar ou personalizar dashboards na UI web no acelerador de soluções de monitorização remota.

Para obter informações mais conceptuais sobre o acelerador de soluções de monitorização remota, consulte [a arquitetura de Monitorização Remota.](iot-accelerators-remote-monitoring-sample-walkthrough.md)
