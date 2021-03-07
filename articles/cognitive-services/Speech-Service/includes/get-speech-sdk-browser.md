---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.custom: devx-track-js
ms.openlocfilehash: 252f2e6275b01522b83e846201d190b39d2bbf21
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102434514"
---
:::row:::
    :::column span="3":::
        O Speech SDK para JavaScript está disponível como um pacote npm, ver <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">microsoft-cognitiveservices-speech-sdk </a> e seu companheiro GitHub repositório <a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank">cognitivo-serviços-discurso-sdk-js </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="JavaScript" src="https://docs.microsoft.com/media/logos/logo_js.svg"  width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Embora o SDK de Discurso para JavaScript esteja disponível como um pacote npm, assim tanto os navegadores web do cliente como Node.js podem consumi-lo - considere as várias implicações arquitetónicas de cada ambiente. Por exemplo, o <a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">modelo de objeto de documento (DOM) </a> não está disponível para aplicações do lado do servidor, tal como o sistema de <a href="https://nodejs.org/api/fs.html" target="_blank">ficheiros </a> não está disponível para aplicações do lado do cliente.

### <a name="nodejs-package-manager-npm"></a>Node.js Package Manager (NPM)

Para instalar o SDK de voz para JavaScript, execute o seguinte `npm install` comando abaixo.

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

### <a name="html-script-tag"></a>Etiqueta de script HTML

Em alternativa, pode incluir diretamente uma `<script>` etiqueta no elemento HTMLs, `<head>` contando com o sindicato <a href="https://www.jsdelivr.com/package/npm/microsoft-cognitiveservices-speech-sdk" target="_blank"> **JSDelivr** NPM</a>.

```html
<script src="https://cdn.jsdelivr.net/npm/microsoft-cognitiveservices-speech-sdk@latest/distrib/browser/microsoft.cognitiveservices.speech.sdk.bundle-min.js">
</script>
```

Para obter mais informações, consulte o <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser" target="_blank">quickstart SDK do Discurso do Navegador Web </a>.
