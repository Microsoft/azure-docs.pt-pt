---
title: Adicionar um serviço para a solução de monitorização remota da interface do Usuário - Azure | Documentos da Microsoft
description: Este artigo mostra-lhe como adicionar um novo serviço para monitorização remota solution accelerator da IU da web.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/02/2018
ms.topic: conceptual
ms.openlocfilehash: e44aa8ade512a6005959e795cb1d4ad861da1338
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2018
ms.locfileid: "49094606"
---
# <a name="add-a-custom-service-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Adicionar um serviço personalizado para monitorização remota solution accelerator da IU da web

Este artigo mostra-lhe como adicionar um novo serviço para monitorização remota solution accelerator da IU da web. O artigo descreve:

- Como preparar um ambiente de desenvolvimento local.
- Como adicionar um novo serviço para a IU da web.

O serviço de exemplo neste artigo fornece os dados para uma grade que o [adicionar uma grade personalizada para monitorização remota solution accelerator da IU da web](iot-accelerators-remote-monitoring-customize-grid.md) procedimento artigo mostra-lhe como adicionar.

Num aplicativo do React, um serviço normalmente interage com um serviço de back-end. No solution accelerator monitorização remota, os exemplos incluem serviços que interagem com o Gestor do hub IoT e a configuração de microsserviços.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir os passos neste guia de procedimentos, terá o seguinte software instalado no seu computador de desenvolvimento local:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Antes de começar

Deverá concluir os passos a [adicionar uma página personalizada para monitorização remota solution accelerator da IU da web](iot-accelerators-remote-monitoring-customize-page.md) artigo que mostra como antes de continuar.

## <a name="add-a-service"></a>Adicionar um serviço

Para adicionar um serviço para a IU da web, terá de adicionar os ficheiros de origem que definem o serviço e modificar alguns ficheiros existentes conscientizá da IU da web do novo serviço.

### <a name="add-the-new-files-that-define-the-service"></a>Adicionar os novos ficheiros que definem o serviço

Para começar, o **passo a passo/src/serviços** pasta contém os ficheiros que definem um serviço simples:

**exampleService.js**

[!code-javascript[Example service](~/remote-monitoring-webui/src/walkthrough/services/exampleService.js?name=service "Example service")]

Para saber mais sobre como os serviços são implementados, veja [a introdução à programação reativa estiver em falta](https://gist.github.com/staltz/868e7e9bc2a7b8c1f754).

**model/exampleModels.js**

[!code-javascript[Example model](~/remote-monitoring-webui/src/walkthrough/services/models/exampleModels.js?name=models "Example model")]

Cópia **exampleService.js** para o **src/serviços** pastas e copia **exampleModels.js** para o **serviços/src/modelos** pasta.

Atualização do **Index** de ficheiros a **src/serviços** pasta para exportar o novo serviço:

```js
export * from './exampleService';
```

Atualização do **Index** de ficheiros a **serviços/src/modelos** pasta para exportar o novo modelo de:

```js
export * from './exampleModels';
```

### <a name="set-up-the-calls-to-the-service-from-the-store"></a>Configurar as chamadas para o serviço da loja

Para começar, o **src/instruções/store/redutores** pasta contém um reducer de exemplo:

**exampleReducer.js**

[!code-javascript[Example reducer](~/remote-monitoring-webui/src/walkthrough/store/reducers/exampleReducer.js?name=reducer "Example reducer")]

Cópia **exampleReducer.js** para o **src/store/redutores** pasta.

Para saber mais sobre o reducer e **Epopéias**, consulte [, o retorno observable](https://redux-observable.js.org/).

### <a name="configure-the-middleware"></a>Configurar o middleware

Para configurar o middleware, adicione o reducer para o **rootReducer.js** de ficheiros a **src/loja** pasta:

```js
import { reducer as exampleReducer } from './reducers/exampleReducer';

const rootReducer = combineReducers({
  ...appReducer,
  ...devicesReducer,
  ...rulesReducer,
  ...simulationReducer,
  ...exampleReducer
});
```

Adicionar as epopéias para o **rootEpics.js** de ficheiros a **src/loja** pasta:

```js
import { epics as exampleEpics } from './reducers/exampleReducer';

// Extract the epic function from each property object
const epics = [
  ...appEpics.getEpics(),
  ...devicesEpics.getEpics(),
  ...rulesEpics.getEpics(),
  ...simulationEpics.getEpics(),
  ...exampleEpics.getEpics()
];
```

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu sobre os recursos disponíveis para o ajudar a adicionar ou personalizar os serviços da IU da web no solution accelerator monitorização remota.

Agora que definiu um serviço, a próxima etapa é [adicionar uma grade personalizada para monitorização remota solution accelerator da IU da web](iot-accelerators-remote-monitoring-customize-grid.md) que apresenta os dados retornados pelo serviço.

Para obter mais informações concetuais sobre o acelerador de solução de monitorização remota, consulte [arquitetura de monitorização remota](iot-accelerators-remote-monitoring-sample-walkthrough.md).
