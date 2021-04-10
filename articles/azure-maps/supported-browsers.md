---
title: Web SDK suporta navegadores | Microsoft Azure Maps
description: Descubra como verificar se o Azure Maps Web SDK suporta um navegador. Veja uma lista de navegadores suportados. Saiba como usar os serviços de mapas com navegadores antigos.
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 6321b96cb3db570102f138dcfd949d9c32daedbc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100384474"
---
# <a name="web-sdk-supported-browsers"></a>Browsers suportados por SDK web

O Azure Maps Web SDK fornece uma função de ajudante chamada [atlas.isSupported](/javascript/api/azure-maps-control/atlas#issupported-boolean-). Esta função deteta se um navegador web tem o conjunto mínimo de funcionalidades WebGL necessárias para suportar o carregamento e a prestação do controlo do mapa. Aqui está um exemplo de como usar a função:

```JavaScript
if (!atlas.isSupported()) {
    alert('Your browser is not supported by Azure Maps');
} else if (!atlas.isSupported(true)) {
    alert('Your browser is supported by Azure Maps, but may have major performance caveats.');
} else {
    // Your browser is supported. Add your map code here.
}
```

## <a name="desktop"></a>Ambiente de Trabalho

O Azure Maps Web SDK suporta os seguintes navegadores de ambiente de trabalho:

- Microsoft Edge (versão atual e anterior)
- Google Chrome (versão atual e anterior)
- Mozilla Firefox (versão atual e anterior)
- Apple Safari (macOS X) (versão atual e anterior)

Consulte também [navegadores legados target](#Target-Legacy-Browsers) mais tarde neste artigo.

## <a name="mobile"></a>Móvel

O Azure Maps Web SDK suporta os seguintes navegadores móveis:

- Android
  - Versão atual do Chrome no Android 6.0 e mais tarde
  - Chrome WebView no Android 6.0 e mais tarde
- iOS
  - Mobile Safari na versão principal atual e anterior do iOS
  - UIWebView e WKWebView na versão principal atual e anterior do iOS
  - Versão atual do Chrome para iOS

> [!TIP]
> Se estiver a incorporar um mapa dentro de uma aplicação móvel utilizando um controlo WebView, talvez prefira utilizar o [pacote npm do Azure Maps Web SDK](https://www.npmjs.com/package/azure-maps-control) em vez de fazer referência à versão do SDK que está hospedado na Rede de Entrega de Conteúdos Azure. Esta abordagem reduz o tempo de carregamento porque o SDK já se encontra no dispositivo do utilizador e não precisa de ser descarregado no tempo de execução.

## <a name="nodejs"></a>Node.js

Os seguintes módulos Web SDK também são suportados em Node.js:

- Módulo de serviços[(módulo npm documentação)](how-to-use-services-module.md)  |  [](https://www.npmjs.com/package/azure-maps-rest)

## <a name="target-legacy-browsers"></a><a name="Target-Legacy-Browsers"></a>Navegadores legados alvo

Você pode querer direcionar navegadores mais antigos que não suportam WebGL ou que tenham apenas suporte limitado para ele. Nesses casos, recomendamos que utilize os serviços Azure Maps juntamente com um controlo de mapas de código aberto como [o Folheto](https://leafletjs.com/). Aqui está um exemplo que faz uso do plugin de [folheto azure Maps de](https://github.com/azure-samples/azure-maps-leaflet)código aberto.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mapas Azure + Folheto" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte os Mapas Pen <a href='https://codepen.io/azuremaps/pen/GeLgyx/'>Azure + Folheto</a> by Azure Maps ( ) no <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>CodePen</a>.
</iframe>

Podem ser [encontradas](https://azuremapscodesamples.azurewebsites.net/?search=leaflet)aqui amostras de código adicionais utilizando mapas Azure em Folheto.

[Aqui](open-source-projects.md#third-part-map-control-plugins) estão alguns populares controlos de mapas abertos para os que a equipa do Azure Maps criou plugin's.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o Azure Maps Web SDK:

[Controlo de mapa](how-to-use-map-control.md)

[Módulo de serviços](how-to-use-services-module.md)
