---
title: Adicione um serviço à solução de monitorização remota UI - Azure [ Microsoft Docs
description: Este artigo mostra-lhe como adicionar um novo serviço à uI do acelerador de soluções de monitorização remota.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/02/2018
ms.topic: conceptual
ms.openlocfilehash: e44aa8ade512a6005959e795cb1d4ad861da1338
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "61447051"
---
# <a name="add-a-custom-service-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Adicione um serviço personalizado ao uI web de solução de monitorização remota

Este artigo mostra-lhe como adicionar um novo serviço à uI do acelerador de soluções de monitorização remota. O artigo descreve:

- Como preparar um ambiente de desenvolvimento local.
- Como adicionar um novo serviço à UI web.

O serviço de exemplo neste artigo fornece os dados para uma grelha que o Adicionar uma grelha personalizada ao acelerador de [soluções de monitorização remota ui](iot-accelerators-remote-monitoring-customize-grid.md) como fazer mostra como adicionar.

Numa aplicação React, um serviço normalmente interage com um serviço back-end. Exemplos no acelerador de soluções de monitorização remota incluem serviços que interagem com o gestor do hub IoT e microserviços de configuração.

## <a name="prerequisites"></a>Pré-requisitos

Para completar os passos neste guia de como orientar, precisa do seguinte software instalado na sua máquina de desenvolvimento local:

- [Git](https://git-scm.com/downloads)
- [Nó.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Antes de começar

Deve completar os passos na página Personalizada para o acelerador de [soluções de monitorização remota uI](iot-accelerators-remote-monitoring-customize-page.md) artigo antes de continuar.

## <a name="add-a-service"></a>Adicionar um serviço

Para adicionar um serviço à Web UI, é necessário adicionar os ficheiros de origem que definem o serviço e modificar alguns ficheiros existentes para sensibilizar a Web UI para o novo serviço.

### <a name="add-the-new-files-that-define-the-service"></a>Adicione os novos ficheiros que definem o serviço

Para começar, a pasta **src/walkthrough/services** contém os ficheiros que definem um serviço simples:

**exemploService.js**

[!code-javascript[Example service](~/remote-monitoring-webui/src/walkthrough/services/exampleService.js?name=service "Example service")]

Para saber mais sobre como os serviços são implementados, consulte [a introdução à Programação Reativa que tem estado ausente.](https://gist.github.com/staltz/868e7e9bc2a7b8c1f754)

**modelo/exemploModels.js**

[!code-javascript[Example model](~/remote-monitoring-webui/src/walkthrough/services/models/exampleModels.js?name=models "Example model")]

Copiar **exemploSService.js** para a pasta **src/serviços** e copiar **exemploModels.js** para a pasta **src/serviços/modelos.**

Atualizar o ficheiro **index.js** na pasta **sRC/serviços** para exportar o novo serviço:

```js
export * from './exampleService';
```

Atualizar o ficheiro **index.js** na pasta **sRC/serviços/modelos** para exportar o novo modelo:

```js
export * from './exampleModels';
```

### <a name="set-up-the-calls-to-the-service-from-the-store"></a>Configurar as chamadas para o serviço a partir da loja

Para começar, a pasta **src/walkthrough/store/redutor** contém um redutor de amostras:

**exemploReducer.js**

[!code-javascript[Example reducer](~/remote-monitoring-webui/src/walkthrough/store/reducers/exampleReducer.js?name=reducer "Example reducer")]

Exemplos **de cópiaReducer.js** para a pasta **src/store/redutores.**

Para saber mais sobre o redutor e **épicos,** consulte [redux-observável](https://redux-observable.js.org/).

### <a name="configure-the-middleware"></a>Configure o middleware

Para configurar o middleware, adicione o redutor ao ficheiro **rootReducer.js** na pasta **src/store:**

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

Adicione os épicos ao ficheiro **rootEpics.js** na pasta **src/store:**

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

Neste artigo, aprendeu sobre os recursos disponíveis para o ajudar a adicionar ou personalizar serviços na UI web no acelerador de soluções de Monitorização Remota.

Agora que definiu um serviço, o próximo passo é [adicionar uma grelha personalizada ao acelerador de soluções de monitorização remota UI](iot-accelerators-remote-monitoring-customize-grid.md) que exibe dados devolvidos pelo serviço.

Para obter informações mais conceptuais sobre o acelerador de soluções de monitorização remota, consulte [a arquitetura de Monitorização Remota.](iot-accelerators-remote-monitoring-sample-walkthrough.md)
