---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 6dd4dfd0edd334005cc2af51a46f9ca2e634272f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399957"
---
:::row:::
    :::column span="3":::
        O JavaScript Speech SDK está disponível como um pacote npm, ver <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">microsoft-cognitiveservices-speech-sdk <span class="docon docon-navigate-external x-hidden-focus"></span> </a> e seu companheiro gitHub repositório <a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank">cognitivo-serviços-discurso-sdk-js <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Node.js" src="https://docs.microsoft.com/media/logos/logo_nodejs.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Embora o JavaScript Speech SDK esteja disponível como um pacote npm, assim tanto o Node.js como os navegadores web do cliente podem consumi-lo - considere as várias implicações arquitetônicas de cada ambiente. Por exemplo, o modelo de objeto de <a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">documento <span class="docon docon-navigate-external x-hidden-focus"></span> (DOM)</a> não está disponível para aplicações do lado do servidor, damesma forma que o sistema de <a href="https://nodejs.org/api/fs.html" target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> ficheiros</a> não está disponível para aplicações do lado do cliente.

### <a name="nodejs-package-manager-npm"></a>Gestor de pacotes node.js (NPM)

Para instalar o JavaScript Speech SDK, execute o seguinte `npm install` comando abaixo.

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

Para mais informações, consulte o <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node" target="_blank">SDK <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>de Discurso do Nó.
