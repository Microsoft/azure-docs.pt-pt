---
title: Adicionar um painel para a solução de monitorização remota da interface do Usuário - Azure | Documentos da Microsoft
description: Este artigo mostra-lhe como adicionar um novo painel ao dashboard de monitorização remota solution accelerator da IU da web.
author: dominicbetts
manager: timlt
ms.author: v-yiso
ms.service: iot-accelerators
services: iot-accelerators
origin.date: 10/05/2018
ms.date: 11/26/2018
ms.topic: conceptual
ms.openlocfilehash: 3b855c3bed75945f44b55463bdacd049b7930aa7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61447068"
---
# <a name="add-a-custom-panel-to-the-dashboard-in-the-remote-monitoring-solution-accelerator-web-ui"></a>Adicionar um painel personalizado para o dashboard de monitorização remota solution accelerator da IU da web

Este artigo mostra-lhe como adicionar um novo painel numa página de dashboard de monitorização remota solution accelerator da IU da web. O artigo descreve:

- Como preparar um ambiente de desenvolvimento local.
- Como adicionar um novo painel para uma página de dashboard na IU da web.

O painel de exemplo neste artigo mostra-se na página do dashboard existente.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir os passos neste guia de procedimentos, terá o seguinte software instalado no seu computador de desenvolvimento local:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Antes de começar

Deverá concluir os passos a [adicionar uma página personalizada para monitorização remota solution accelerator da IU da web](iot-accelerators-remote-monitoring-customize-page.md) artigo antes de continuar.

## <a name="add-a-panel"></a>Adicionar um painel

Para adicionar um painel para a IU da web, terá de adicionar os ficheiros de origem que definem o painel e, em seguida, modifique o dashboard para apresentar o painel.

### <a name="add-the-new-files-that-define-the-panel"></a>Adicionar os novos ficheiros que definem o painel

Para começar, o **src/instruções/componentes/páginas/dashboard/painéis/examplePanel** pasta contém os ficheiros que definem um painel, incluindo:

**examplePanel.js**


Copiar o **src/instruções/componentes/páginas/dashboard/painéis/examplePanel** pasta para o **src/componentes/páginas/dashboard/painéis** pasta.

Adicione a seguinte exportação para o **src/walkthrough/components/pages/dashboard/panels/index.js** ficheiro:

```js
export * from './examplePanel';
```

### <a name="add-the-panel-to-the-dashboard"></a>Adicionar o painel ao dashboard

Modificar a **src/components/pages/dashboard/dashboard.js** para adicionar o painel.

Adicione o painel de exemplo para a lista de importações de painéis:

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

Adicione a seguinte definição de célula para a grade no conteúdo da página:

```js
          <Cell className="col-2">
            <ExamplePanel t={t} />
          </Cell>
```

## <a name="test-the-flyout"></a>Testar a lista de opções

Se o web interface do Usuário não está já em execução localmente, execute o seguinte comando na raiz da sua cópia local do repositório:

```cmd/sh
npm start
```

O comando anterior é a interface do Usuário localmente em executado [ http://localhost:3000/dashboard ](http://localhost:3000/dashboard). Navegue para o **Dashboard** página para ver o novo painel.

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu sobre os recursos disponíveis para o ajudar a adicionar ou personalizar dashboards na IU da web no solution accelerator monitorização remota.

Para obter mais informações concetuais sobre o acelerador de solução de monitorização remota, consulte [arquitetura de monitorização remota](iot-accelerators-remote-monitoring-sample-walkthrough.md).
