---
title: Suporte a navegadores da web - Azure Maps | Documentos da Microsoft
description: Saiba mais sobre browsers suportados para o SDK Web do Azure Maps
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.openlocfilehash: 2678fa7c9290c7ec0a27288e7739fde4bb0870fd
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58501275"
---
# <a name="supported-web-browsers"></a>Browsers suportados

O SDK de Web do Azure Maps fornece uma função auxiliar [atlas.isSupported](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest#issupported-boolean-) detetar se um navegador da web tem o mínimo necessário a recursos de WebGL para oferecer suporte a carregar e renderizar o controlo do mapa. 

```
if(!atlas.isSupported()) {
    alert('Your browser is not supported by Azure Maps');
} else if(!atlas.isSupported(true)) {
    alert('Your browser is supported by Azure Maps, but may have major performance caveats.');
} else {
    //Add your map code here.
}
```

O SDK de Web de mapas do Azure suporta os seguintes browsers.

## <a name="desktop"></a>Ambiente de trabalho

- A versão atual e anterior do Microsoft Edge 
- A versão atual e anterior do Chrome 
- A versão atual e anterior do Firefox 
- A versão atual e anterior do Safari (Mac OS X) 

Consulte também [direcionar os navegadores herdados](#Target-Legacy-Browsers).

## <a name="mobile"></a>Telemóvel

-  Android
    * Versão atual do Chrome no Android 6.0 +
    * Chrome WebView no Android 6.0 +
- iOS
    * Mobile Safari na versão principal atual e anterior do iOS
    * UIWebView e WKWebView sobre a versão principal atual e anterior do iOS
    * Versão atual do Chrome para iOS

> [!TIP]
> Se estiver a incorporar um mapa dentro de uma vista de aplicações móveis um controle WebView, pode achar a utilizar o [pacote de npm do SDK de Web do Azure Maps](https://www.npmjs.com/package/azure-maps-control) preferível para referenciar a versão hospedada em CDN do SDK. Isto irá reduzir o tempo de carregamento como o SDK irá já estar no dispositivo do utilizador e não tem de ser transferido em tempo de execução.

## <a name="nodejs"></a>Node.js

Os seguintes módulos de Web SDK também são suportados em node. js:

- Módulo de serviços ([documentação](how-to-use-services-module.md) | [módulo de npm](https://www.npmjs.com/package/azure-maps-rest))

## <a name="Target-Legacy-Browsers"></a>Navegadores herdados de destino

Se precisar de atingir a navegadores mais antigos que podem não suportar ou um suporte de WebGL limitado, é recomendável usar os serviços de mapas do Azure em combinação com um controle de mapa de código-fonte aberto, como [leaflet](https://leafletjs.com/). 

<iframe height="500" style="width: 100%;" scrolling="no" title="O Azure Maps + Leaflet" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a caneta <a href='https://codepen.io/azuremaps/pen/GeLgyx/'>do Azure Maps + Leaflet</a> através do Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>