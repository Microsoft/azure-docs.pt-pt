---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 2a4f0cfbdc6a88c2f32b60e8ac3178ef5bd4f8dd
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86035905"
---
:::row:::
    :::column span="3":::
        O Speech SDK para JavaScript está disponível como um pacote npm, ver <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">microsoft-cognitiveservices-speech-sdk <span class="docon docon-navigate-external x-hidden-focus"></span> </a> e seu companheiro GitHub repositório <a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank">cognitivo-serviços-discurso-sdk-js <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="JavaScript" src="https://docs.microsoft.com/media/logos/logo_js.svg"  width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Embora o SDK de Discurso para JavaScript esteja disponível como um pacote npm, assim tanto os navegadores web do cliente como Node.js podem consumi-lo - considere as várias implicações arquitetónicas de cada ambiente. Por exemplo, o <a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">modelo de <span class="docon docon-navigate-external x-hidden-focus"></span> objeto de documento (DOM)</a> não está disponível para aplicações do lado do servidor, tal como o sistema <a href="https://nodejs.org/api/fs.html" target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> </a> de ficheiros não está disponível para aplicações do lado do cliente.

### <a name="nodejs-package-manager-npm"></a>Node.js Package Manager (NPM)

Para instalar o SDK de voz para JavaScript, execute o seguinte `npm install` comando abaixo.

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

### <a name="html-script-tag"></a>Etiqueta de script HTML

Em alternativa, pode incluir diretamente uma `<script>` etiqueta no elemento HTMLs, `<head>` contando com o <a href="https://www.jsdelivr.com/package/npm/microsoft-cognitiveservices-speech-sdk" target="_blank">sindicato <span class="docon docon-navigate-external x-hidden-focus"></span> **JSDelivr** NPM </a>.

```html
<script src="https://cdn.jsdelivr.net/npm/microsoft-cognitiveservices-speech-sdk@latest/distrib/lib/microsoft.cognitiveservices.speech.sdk.min.js">
</script>
```

Para obter mais informações, consulte o <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser" target="_blank">quickstart <span class="docon docon-navigate-external x-hidden-focus"></span> SDK do Discurso do Navegador Web </a>.
