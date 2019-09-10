---
title: Navegadores com suporte do SDK Web – mapas do Azure | Microsoft Docs
description: Saiba mais sobre os navegadores com suporte para o SDK da Web do Azure Maps
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.openlocfilehash: d867e9a1afcb495aee7e8b0e0b3b001104f48a4c
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70844818"
---
# <a name="web-sdk-supported-browsers"></a>Browsers suportados por SDK web

O SDK da Web do Azure Maps fornece uma função auxiliar chamada [Atlas. IsSupported](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest#issupported-boolean-). Essa função detecta se um navegador da Web tem o conjunto mínimo de recursos de WebGL necessários para dar suporte ao carregamento e renderização do controle de mapa. Aqui está um exemplo de como usar a função:

```JavaScript
if (!atlas.isSupported()) {
    alert('Your browser is not supported by Azure Maps');
} else if (!atlas.isSupported(true)) {
    alert('Your browser is supported by Azure Maps, but may have major performance caveats.');
} else {
    // Your browser is supported. Add your map code here.
}
```

## <a name="desktop"></a>Desktop

O SDK da Web do Azure Maps dá suporte aos seguintes navegadores da área de trabalho:

- Microsoft Edge (versão atual e anterior)
- Google Chrome (versão atual e anterior)
- Mozilla Firefox (versão atual e anterior)
- Apple Safari (Mac OS X) (versão atual e anterior)

Consulte também [direcionar navegadores herdados](#Target-Legacy-Browsers) mais adiante neste artigo.

## <a name="mobile"></a>móvel

O SDK da Web do Azure Maps dá suporte aos seguintes navegadores móveis:

- Android
  - Versão atual do Chrome no Android 6,0 e posterior
  - Chrome WebView no Android 6,0 e posterior
- iOS
  - Mobile Safari na versão principal atual e anterior do iOS
  - UIWebView e WKWebView na versão principal atual e anterior do iOS
  - Versão atual do Chrome para iOS

> [!TIP]
> Se você estiver inserindo um mapa dentro de um aplicativo móvel usando um controle WebView, talvez prefira usar o [pacote NPM do SDK da Web do Azure Maps](https://www.npmjs.com/package/azure-maps-control) em vez de fazer referência à versão do SDK hospedado na rede de distribuição de conteúdo do Azure. Essa abordagem reduz o tempo de carregamento porque o SDK já está no dispositivo do usuário e não precisa ser baixado em tempo de execução.

## <a name="nodejs"></a>Node.js

Os seguintes módulos do SDK da Web também têm suporte no node. js:

- Módulo de serviços ([módulo NPM](https://www.npmjs.com/package/azure-maps-rest)de[documentação](how-to-use-services-module.md) | )

## <a name="Target-Legacy-Browsers"></a>Direcionar navegadores herdados

Talvez você queira direcionar para navegadores mais antigos que não dão suporte a WebGL ou que tenham apenas suporte limitado para ele. Nesses casos, recomendamos que você use os serviços do Azure Maps junto com um controle de mapa de código-fonte aberto como [Leaflet](https://leafletjs.com/). Segue-se um exemplo:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mapas do Azure + Leaflet" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a caneta <a href='https://codepen.io/azuremaps/pen/GeLgyx/'>Azure Maps + Leaflet</a> pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre o SDK da Web do Azure Maps:

> [!div class="nextstepaction"]
> [Controlo de mapa](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Módulo de serviços](how-to-use-services-module.md)
