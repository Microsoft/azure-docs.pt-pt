---
title: Adicione um serviço à solução de Monitorização Remota UI - Azure / Microsoft Docs
description: Este artigo mostra-lhe como adicionar um novo serviço na UI do acelerador de solução de monitorização remota.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/02/2018
ms.topic: conceptual
ms.custom: devx-track-js
ms.openlocfilehash: 6689a6d8a19dd690aa19aeecd29ead35ef6c2db1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91318352"
---
# <a name="add-a-custom-service-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Adicione um serviço personalizado à UI do acelerador de solução de monitorização remota

Este artigo mostra-lhe como adicionar um novo serviço na UI do acelerador de solução de monitorização remota. O artigo descreve:

- Como preparar um ambiente de desenvolvimento local.
- Como adicionar um novo serviço à UI web.

O serviço de exemplo neste artigo fornece os dados para uma grelha que a grelha personalizada adicionar à web ui do [acelerador de solução de monitorização remota](iot-accelerators-remote-monitoring-customize-grid.md) web ui como-a-artigo mostra-lhe como adicionar.

Numa aplicação React, um serviço normalmente interage com um serviço back-end. Exemplos no acelerador de solução de monitorização remota incluem serviços que interagem com o hub manager IoT e microserviços de configuração.

## <a name="prerequisites"></a>Pré-requisitos

Para completar os passos neste guia de como fazer, precisa do seguinte software instalado na sua máquina de desenvolvimento local:

- [Rio Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Antes de começar

Deve completar os passos na página Adicionar uma página personalizada ao artigo web ui da solução de [monitorização remota](iot-accelerators-remote-monitoring-customize-page.md) antes de continuar.

## <a name="add-a-service"></a>Adicionar um serviço

Para adicionar um serviço à UI web, é necessário adicionar os ficheiros de origem que definem o serviço e modificar alguns ficheiros existentes para sensibilizar a UI web para o novo serviço.

### <a name="add-the-new-files-that-define-the-service"></a>Adicione os novos ficheiros que definem o serviço

Para começar, a pasta **src/walkthrough/services** contém os ficheiros que definem um serviço simples:

**exampleService.js**

[!code-javascript[Example service](~/remote-monitoring-webui/src/walkthrough/services/exampleService.js?name=service "Example service")]

Para saber mais sobre como os serviços são implementados, consulte [a introdução à Programação Reativa que tem faltado.](https://gist.github.com/staltz/868e7e9bc2a7b8c1f754)

**modelo/exampleModels.js**

[!code-javascript[Example model](~/remote-monitoring-webui/src/walkthrough/services/models/exampleModels.js?name=models "Example model")]

Copie **exampleService.js** para a pasta **src/serviços** e copie **exampleModels.js** para a pasta **src/serviços/modelos.**

Atualizar o ficheiro **index.js** na pasta **src/serviços** para exportar o novo serviço:

```js
export * from './exampleService';
```

Atualizar o ficheiro **index.js** na pasta **src/serviços/modelos** para exportar o novo modelo:

```js
export * from './exampleModels';
```

### <a name="set-up-the-calls-to-the-service-from-the-store"></a>Configurar as chamadas para o serviço da loja

Para começar, a pasta **src/walkthrough/store/redudores** contém um redutor de amostras:

**exampleReducer.js**

[!code-javascript[Example reducer](~/remote-monitoring-webui/src/walkthrough/store/reducers/exampleReducer.js?name=reducer "Example reducer")]

Copie **exampleReducer.js** para a pasta **src/loja/redutores.**

Para saber mais sobre o redutor e **os épicos,** consulte [redux-observável](https://redux-observable.js.org/).

### <a name="configure-the-middleware"></a>Configure o middleware

Para configurar o middleware, adicione o redutor ao ficheiro **rootReducer.js** na pasta **src/loja:**

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

Adicione os épicos ao ficheiro **rootEpics.js** na pasta **src/loja:**

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

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu sobre os recursos disponíveis para ajudá-lo a adicionar ou personalizar serviços na UI web no acelerador de solução de Monitorização Remota.

Agora que definiu um serviço, o próximo passo é [adicionar uma grelha personalizada à UI do acelerador de solução de monitorização remota](iot-accelerators-remote-monitoring-customize-grid.md) que exibe dados devolvidos pelo serviço.

Para obter mais informações conceptuais sobre o acelerador de solução de monitorização remota, consulte [a arquitetura de monitorização remota.](iot-accelerators-remote-monitoring-sample-walkthrough.md)
