---
title: Web SDK suportado navegadores Microsoft Azure Maps
description: Descubra como verificar se o Azure Maps Web SDK suporta um navegador. Veja uma lista de navegadores suportados. Saiba como usar os serviços de mapas com navegadores antigos.
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 5b186901551e421ba365b97a6de6a55ff791e5e9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91310191"
---
# <a name="web-sdk-supported-browsers"></a>Browsers suportados por SDK web

O Azure Maps Web SDK fornece uma função de ajudante chamada [atlas.isSupported](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas#issupported-boolean-). Esta função deteta se um navegador web tem o conjunto mínimo de funcionalidades WebGL necessárias para suportar o carregamento e a prestação do controlo do mapa. Aqui está um exemplo de como usar a função:

```JavaScript
if (!atlas.isSupported()) {
    alert('Your browser is not supported by Azure Maps');
} else if (!atlas.isSupported(true)) {
    alert('Your browser is supported by Azure Maps, but may have major performance caveats.');
} else {
    // Your browser is supported. Add your map code here.
}
```

## <a name="desktop"></a>Ambiente de trabalho

O Azure Maps Web SDK suporta os seguintes navegadores de ambiente de trabalho:

- Microsoft Edge (versão atual e anterior)
- Google Chrome (versão atual e anterior)
- Mozilla Firefox (versão atual e anterior)
- Apple Safari (Mac OS X) (versão atual e anterior)

Consulte também [navegadores legados target](#Target-Legacy-Browsers) mais tarde neste artigo.

## <a name="mobile"></a>Telemóvel

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

- Módulo de serviços[(módulo npm documentação)](how-to-use-services-module.md)  |  [npm module](https://www.npmjs.com/package/azure-maps-rest)

## <a name="target-legacy-browsers"></a><a name="Target-Legacy-Browsers"></a>Navegadores legados alvo

Você pode querer direcionar navegadores mais antigos que não suportam WebGL ou que tenham apenas suporte limitado para ele. Nesses casos, recomendamos que utilize os serviços Azure Maps juntamente com um controlo de mapas de código aberto como [o Folheto](https://leafletjs.com/). Eis um exemplo:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mapas Azure + Folheto" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte os Mapas Pen <a href='https://codepen.io/azuremaps/pen/GeLgyx/'>Azure + Folheto</a> by Azure Maps ( ) no <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o Azure Maps Web SDK:

[Controlo de mapa](how-to-use-map-control.md)

[Módulo de serviços](how-to-use-services-module.md)