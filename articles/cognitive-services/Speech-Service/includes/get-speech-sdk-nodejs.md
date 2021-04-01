---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.custom: devx-track-js
ms.openlocfilehash: 99be483f67bf5e3f9b27c63a2318df8761c16eff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102434532"
---
:::row:::
    :::column span="3":::
        O Speech SDK para JavaScript está disponível como um pacote npm, ver <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">microsoft-cognitiveservices-speech-sdk </a> e seu companheiro GitHub repositório <a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank">cognitivo-serviços-discurso-sdk-js </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Node.js" src="https://docs.microsoft.com/media/logos/logo_nodejs.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Embora o SDK de Discurso para JavaScript esteja disponível como um pacote npm, assim tanto Node.js como os navegadores web do cliente podem consumi-lo - considere as várias implicações arquitetónicas de cada ambiente. Por exemplo, o <a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">modelo de objeto de documento (DOM) </a> não está disponível para aplicações do lado do servidor, tal como o sistema de <a href="https://nodejs.org/api/fs.html" target="_blank">ficheiros </a> não está disponível para aplicações do lado do cliente.

### <a name="nodejs-package-manager-npm"></a>Node.js Package Manager (NPM)

Para instalar o SDK de voz para JavaScript, execute o seguinte `npm install` comando abaixo.

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

Para mais informações, consulte o <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node" target="_blank">Node.js Speech SDK quickstart </a>.
