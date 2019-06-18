---
title: Web SDK suportado navegadores - Azure Maps | Documentos da Microsoft
description: Saiba mais sobre browsers suportados para o SDK Web do Azure Maps
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.openlocfilehash: 84c5dbcf5073ba8c0ae662af019cde590a9adf10
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64686652"
---
# <a name="web-sdk-supported-browsers"></a>Browsers suportados por SDK web

O SDK de Web do Azure Maps fornece uma função auxiliar chamada [atlas.isSupported](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest#issupported-boolean-). Esta função Deteta se um navegador da web tem o conjunto mínimo de recursos de WebGL necessário para suportar a carregar e renderizar o controlo do mapa. Eis um exemplo de como usar a função:

```
if(!atlas.isSupported()) {
    alert('Your browser is not supported by Azure Maps');
} else if(!atlas.isSupported(true)) {
    alert('Your browser is supported by Azure Maps, but may have major performance caveats.');
} else {
    // Your browser is supported. Add your map code here.
}
```

## <a name="desktop"></a>Desktop

O SDK de Web de mapas do Azure suporta os seguintes browsers de ambiente de trabalho:

- Microsoft Edge (versão atual e anterior)
- Google Chrome (versão atual e anterior)
- Mozilla Firefox (versão atual e anterior)
- Apple Safari (Mac OS X) (versão atual e anterior)

Consulte também [direcionar os navegadores herdados](#Target-Legacy-Browsers) mais adiante neste artigo.

## <a name="mobile"></a>móvel

O SDK de Web de mapas do Azure suporta os seguintes browsers para dispositivos móveis:

- Android
  - Versão atual do Chrome no Android 6.0 e posterior
  - Chrome WebView no Android 6.0 e posterior
- iOS
  - Mobile Safari na versão principal atual e anterior do iOS
  - UIWebView e WKWebView sobre a versão principal atual e anterior do iOS
  - Versão atual do Chrome para iOS

> [!TIP]
> Se estiver a incorporar um mapa dentro de um aplicativo móvel usando um controle WebView, poderá preferir utilizar o [pacote de npm do SDK de Web do Azure Maps](https://www.npmjs.com/package/azure-maps-control) em vez de fazer referência a versão do SDK que está alojado na entrega de conteúdos do Azure Rede. Essa abordagem reduz o tempo de carregamento, uma vez que o SDK está a ser já no dispositivo do utilizador e não tem de ser transferido em tempo de execução.

## <a name="nodejs"></a>Node.js

Os seguintes módulos de Web SDK também são suportados em node. js:

- Módulo de serviços ([documentação](how-to-use-services-module.md) | [módulo de npm](https://www.npmjs.com/package/azure-maps-rest))

## <a name="Target-Legacy-Browsers"></a>Navegadores herdados de destino

Pode querer direcionar os navegadores mais antigos que não suportam WebGL ou que têm apenas suporte limitado para o mesmo. Nesses casos, recomendamos que utilize serviços do Azure Maps, juntamente com um controle de mapa de código-fonte aberto, como [Leaflet](https://leafletjs.com/). Segue-se um exemplo:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="O Azure Maps + Leaflet" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a caneta <a href='https://codepen.io/azuremaps/pen/GeLgyx/'>do Azure Maps + Leaflet</a> através do Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre o SDK do Azure Maps Web:

> [!div class="nextstepaction"]
> [Controlo de mapa](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Módulo de serviços](how-to-use-services-module.md)
