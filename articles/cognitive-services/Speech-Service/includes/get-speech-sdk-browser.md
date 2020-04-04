---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: dapine
ms.openlocfilehash: 1f1512fbd766faed3922e4bdf8a47dba0de69864
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656537"
---
:::row:::
    :::column span="3":::
        O JavaScript Speech SDK está disponível como um pacote npm, ver <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">microsoft-cognitiveservices-speech-sdk <span class="docon docon-navigate-external x-hidden-focus"></span> </a> e seu companheiro gitHub repositório <a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank">cognitivo-serviços-discurso-sdk-js <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="JavaScript" src="https://docs.microsoft.com/media/logos/logo_js.svg"  width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Embora o JavaScript Speech SDK esteja disponível como um pacote npm, assim tanto os navegadores web do cliente como o Node.js podem consumi-lo - considere as várias implicações arquitetónicas de cada ambiente. Por exemplo, o modelo de objeto de <a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">documento <span class="docon docon-navigate-external x-hidden-focus"></span> (DOM)</a> não está disponível para aplicações do lado do servidor, damesma forma que o sistema de <a href="https://nodejs.org/api/fs.html" target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> ficheiros</a> não está disponível para aplicações do lado do cliente.

### <a name="nodejs-package-manager-npm"></a>Gestor de pacotes node.js (NPM)

Para instalar o JavaScript Speech SDK, execute o seguinte `npm install` comando abaixo.

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

### <a name="html-script-tag"></a>Etiqueta de script HTML

Em alternativa, pode incluir `<script>` diretamente uma etiqueta no `<head>` elemento HTMLs, contando com o <a href="https://www.jsdelivr.com/package/npm/microsoft-cognitiveservices-speech-sdk" target="_blank">sindicato <span class="docon docon-navigate-external x-hidden-focus"> </span> **JSDelivr** NPM </a>.

```html
<script src="https://cdn.jsdelivr.net/npm/microsoft-cognitiveservices-speech-sdk@1.10.1/distrib/lib/microsoft.cognitiveservices.speech.sdk.min.js">
</script>
```

Para mais informações, consulte o <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser" target="_blank">SDK <span class="docon docon-navigate-external x-hidden-focus"> </span>de discurso </a>do navegador web .
