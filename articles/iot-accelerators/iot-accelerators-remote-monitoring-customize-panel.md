---
title: Adicione um painel à solução de monitorização remota UI - Azure / Microsoft Docs
description: Este artigo mostra-lhe como adicionar um novo painel ao painel no sistema de monitorização remota uI do acelerador de solução de monitorização remota.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/05/2018
ms.topic: conceptual
ms.custom: devx-track-js
ms.openlocfilehash: 1dcca8409022ba4cf1f988b7c777e3a1fa511060
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006069"
---
# <a name="add-a-custom-panel-to-the-dashboard-in-the-remote-monitoring-solution-accelerator-web-ui"></a>Adicione um painel personalizado ao painel no painel de instrumentos na UI do acelerador de solução de monitorização remota

Este artigo mostra-lhe como adicionar um novo painel numa página de dashboard na UI do acelerador de solução de monitorização remota. O artigo descreve:

- Como preparar um ambiente de desenvolvimento local.
- Como adicionar um novo painel a uma página de dashboard na UI web.

O painel de exemplo deste artigo apresenta-se na página do painel de instrumentos existente.

## <a name="prerequisites"></a>Pré-requisitos

Para completar os passos neste guia de como fazer, precisa do seguinte software instalado na sua máquina de desenvolvimento local:

- [Rio Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Antes de começar

Deve completar os passos na página Adicionar uma página personalizada ao artigo de UI do [acelerador de solução de monitorização remota](iot-accelerators-remote-monitoring-customize-page.md) antes de continuar.

## <a name="add-a-panel"></a>Adicionar um painel

Para adicionar um painel à UI web, é necessário adicionar os ficheiros de origem que definem o painel e, em seguida, modificar o painel para exibir o painel.

### <a name="add-the-new-files-that-define-the-panel"></a>Adicione os novos ficheiros que definem o painel

Para começar, a **src/walkthrough/componentes/pages/dashboard/panel/exemploPanel** contém os ficheiros que definem um painel, incluindo:

**examplePanel.js**

[!code-javascript[Example panel](~/remote-monitoring-webui/src/walkthrough/components/pages/dashboard/panels/examplePanel/examplePanel.js?name=panel "Example panel")]

Copie a pasta **src/walkthrough/components/pages/dashboard/panel/exemploPanel** para a pasta **src/componentes/pages/dashboard/panels.**

Adicione a seguinte exportação para o **src/walkthrough/componentes/pages/dashboard/panels/index.js** ficheiro:

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

## <a name="test-the-flyout"></a>Teste o flyout

Se a UI web ainda não estiver a funcionar localmente, executa o seguinte comando na raiz da sua cópia local do repositório:

```cmd/sh
npm start
```

O comando anterior dirige a UI localmente em `http://localhost:3000/dashboard` . Navegue na página **dashboard** para ver o novo painel.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu sobre os recursos disponíveis para ajudá-lo a adicionar ou personalizar dashboards na UI web no acelerador de solução de monitorização remota.

Para obter mais informações conceptuais sobre o acelerador de solução de monitorização remota, consulte [a arquitetura de monitorização remota.](iot-accelerators-remote-monitoring-sample-walkthrough.md)
