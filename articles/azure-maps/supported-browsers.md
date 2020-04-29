---
title: Navegadores suportados pela Web SDK / Microsoft Azure Maps
description: Neste artigo, você vai aprender sobre navegadores suportados para Microsoft Azure Maps Web SDK e como verificar se um navegador é um navegador suportado.
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: e81b15b974469d319384a67b08512130b7876a30
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76988792"
---
# <a name="web-sdk-supported-browsers"></a>Browsers suportados por SDK web

O Azure Maps Web SDK fornece uma função de ajudante chamada [atlas.isSupported](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest#issupported-boolean-). Esta função deteta se um navegador web tem o conjunto mínimo de funcionalidades Do WebGL necessárias para suportar o carregamento e renderização do controlo do mapa. Aqui está um exemplo de como usar a função:

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

Consulte também os [navegadores legados Target](#Target-Legacy-Browsers) mais tarde neste artigo.

## <a name="mobile"></a>Telemóvel

O Azure Maps Web SDK suporta os seguintes navegadores móveis:

- Android
  - Versão atual do Chrome no Android 6.0 e mais tarde
  - Chrome WebView no Android 6.0 e mais tarde
- iOS
  - Mobile Safari na versão atual e anterior do iOS
  - UIWebView e WKWebView sobre a versão principal atual e anterior do iOS
  - Versão atual do Chrome para iOS

> [!TIP]
> Se estiver a incorporar um mapa dentro de uma aplicação móvel utilizando um controlo WebView, poderá preferir utilizar o [pacote nPM do Azure Maps Web SDK](https://www.npmjs.com/package/azure-maps-control) em vez de se referir à versão do SDK que está alojada na Rede de Entrega de Conteúdos Azure. Esta abordagem reduz o tempo de carregamento porque o SDK já está no dispositivo do utilizador e não precisa de ser descarregado no momento de funcionar.

## <a name="nodejs"></a>Node.js

Os seguintes módulos Web SDK também são suportados no Node.js:

- Módulo de serviços[(módulo de documentação](how-to-use-services-module.md) | [npm)](https://www.npmjs.com/package/azure-maps-rest)

## <a name="target-legacy-browsers"></a><a name="Target-Legacy-Browsers"></a>Navegadores legados-alvo

Você pode querer direcionar navegadores mais antigos que não suportam WebGL ou que têm apenas suporte limitado para o mesmo. Nestes casos, recomendamos que utilize os serviços do Azure Maps juntamente com um controlo de mapas de código aberto como [o Folheto](https://leafletjs.com/). Segue-se um exemplo:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mapas Azure + Folheto" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte o Pen <a href='https://codepen.io/azuremaps/pen/GeLgyx/'>Azure Maps +</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>Folheto de Azure Maps () em <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o Azure Maps Web SDK:

> [!div class="nextstepaction"]
> [Controlo de mapa](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Módulo de serviços](how-to-use-services-module.md)
