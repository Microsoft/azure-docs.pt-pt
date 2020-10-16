---
title: Plugin de reação nativa para insights de aplicação JavaScript SDK
description: Como instalar e utilizar o plugin Native React para Informações JavaScript SDK.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 3dac47aa7cf93ca882c4c1d0d191dabf0eb7178c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88227436"
---
# <a name="native-react-plugin-for-application-insights-javascript-sdk"></a>Plugin de reação nativa para insights de aplicação JavaScript SDK

O plugin Native React para Informações JavaScript SDK recolhe informações do dispositivo, por padrão este plugin recolhe automaticamente:

- **ID exclusivo do dispositivo** (também conhecido como ID de instalação.)
- **Nome do modelo do dispositivo** (como iPhone X, Samsung Galaxy Fold, Huawei P30 Pro, etc.)
- **Tipo de Dispositivo** (por exemplo, auscultador, tablet, etc.)

## <a name="requirements"></a>Requisitos

Deve estar a utilizar uma versão >= 2.0.0 de `@microsoft/applicationinsights-web` . Este plugin só funcionará em aplicações nativas de reagem. Não funcionará com [aplicações que utilizem o quadro expo,](https://docs.expo.io/)pelo que não funcionará com a Create React Native App.

## <a name="getting-started"></a>Introdução

Instale e ligue o pacote [react-native-device-info.](https://www.npmjs.com/package/react-native-device-info) Mantenha o `react-native-device-info` pacote atualizado para recolher os nomes mais recentes do dispositivo usando a sua aplicação.

```zsh

npm install --save @microsoft/applicationinsights-react-native @microsoft/applicationinsights-web
npm install --save react-native-device-info
react-native link react-native-device-info

```

## <a name="initializing-the-plugin"></a>Inicialização do plugin

Para utilizar este plugin, é necessário construir o plugin e adicioná-lo como uma `extension` instância de Insights de Aplicação existente.

```typescript
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ReactNativePlugin } from '@microsoft/applicationinsights-react-native';

var RNPlugin = new ReactNativePlugin();
var appInsights = new ApplicationInsights({
    config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [RNPlugin]
    }
});
appInsights.loadAppInsights();

```

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre o JavaScript SDK, consulte a [documentação SDK javaScript da aplicação.](javascript.md)
- Para saber mais sobre a linguagem de consulta de Kusto e consultando dados em Log Analytics, consulte a [visão geral](../../azure-monitor/log-query/log-query-overview.md)da consulta de log .
