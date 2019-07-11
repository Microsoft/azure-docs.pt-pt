---
title: 'Tutorial: Inicie o leitor envolventes com node. js'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, irá criar uma aplicação node. js que inicia o leitor envolvente e experimental.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: f8697042ed46e0ff333f736454346908d76cf039
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718370"
---
# <a name="tutorial-launch-the-immersive-reader-nodejs"></a>Tutorial: Iniciar a Leitura Avançada (Node.js)

Na [descrição geral](./overview.md), ficou a conhecer o que é o leitor envolventes e como ele implementa técnicas comprovadas para melhorar a compreensão de leitura para os aprendizes de linguagem, os leitores emergentes e estudantes com diferenças de aprendizado. Este tutorial abrange como criar uma aplicação web node. js que inicia o leitor envolvente e experimental. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma aplicação web do node. js Express
> * Adquirir um token de acesso
> * Inicie o leitor envolventes com conteúdo de exemplo
> * Especificar o idioma do seu conteúdo
> * Especificar o idioma da interface do leitor de imersão
> * Inicie o leitor envolventes com conteúdo de matemática

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Uma chave de subscrição para o leitor envolvente. Obter uma ao seguir [estas instruções](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).
* [NODE. js](https://nodejs.org/) e [Yarn](https://yarnpkg.com)
* Um IDE como [Visual Studio Code](https://code.visualstudio.com/)

## <a name="create-a-nodejs-web-app-with-express"></a>Criar uma aplicação web do node. js Express

Criar uma aplicação web do node. js com o `express-generator` ferramenta.

```bash
npm install express-generator -g
express --view=pug myapp
cd myapp
```

Instalar as dependências do yarn e adicionar as dependências `request` e `dotenv`, que será utilizado mais tarde no tutorial.

```bash
yarn
yarn add request
yarn add dotenv
```

## <a name="acquire-an-access-token"></a>Adquirir um token de acesso

Em seguida, escreva um API para obter um token de acesso utilizando a sua chave de subscrição de back-end. Precisa de sua chave de subscrição e o ponto final para a próxima etapa. Pode encontrar a chave de subscrição na página chaves do seu recurso de leitor de imersão no portal do Azure. Pode encontrar o ponto final na página de descrição geral.

Assim que tiver a sua chave de subscrição e o ponto de extremidade, crie um novo ficheiro chamado _. env_e cole o seguinte código para o mesmo, substituindo `{YOUR_SUBSCRIPTION_KEY}` e `{YOUR_ENDPOINT}` com a sua chave de subscrição e o ponto de extremidade, respectivamente.

```text
SUBSCRIPTION_KEY={YOUR_SUBSCRIPTION_KEY}
ENDPOINT={YOUR_ENDPOINT}
```

Não se esqueça de não consolidar este ficheiro no controle de fonte, porque contém segredos que não devem ser efetuados públicos.

Em seguida, abra _App. js_ e adicione o seguinte na parte superior do ficheiro. Isso carrega a chave de subscrição e o ponto de extremidade como variáveis de ambiente no nó.

```javascript
require('dotenv').config();
```

Abra o _routes\index.js_ importar de ficheiro e o seguinte na parte superior do ficheiro:

```javascript
var request = require('request');
```

Em seguida, adicione o seguinte código diretamente abaixo dessa linha. Este código cria um ponto de final de API que adquire um token de acesso utilizando a sua chave de subscrição e, em seguida, devolve esse token.

```javascript
router.get('/token', function(req, res, next) {
  request.post({
    headers: {
        'Ocp-Apim-Subscription-Key': process.env.SUBSCRIPTION_KEY,
        'content-type': 'application/x-www-form-urlencoded'
    },
    url: process.env.ENDPOINT
  },
  function(err, resp, token) {
    return res.send(token);
  });
});
```

Este ponto final de API deve ser protegido por trás de algum tipo de autenticação (por exemplo, [OAuth](https://oauth.net/2/)); que trabalho está além do escopo deste tutorial.

## <a name="launch-the-immersive-reader-with-sample-content"></a>Inicie o leitor envolventes com conteúdo de exemplo

1. Aberto _views\layout.pug_e adicione o seguinte código sob o `head` marcar, antes o `body` marca. Estes `script` etiquetas carregar o [envolventes de leitor de SDK](https://github.com/Microsoft/immersive-reader-sdk) e o jQuery.

    ```pug
    script(src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.0.0.1.js')
    script(src='https://code.jquery.com/jquery-3.3.1.min.js')
    ```

2. Open _views\index.pug_e substituir seu conteúdo com o código a seguir. Esse código preenche a página com algum conteúdo de exemplo e adiciona um botão que inicia o leitor envolvente e experimental.

    ```pug
    extends layout

    block content
      h2(id='title') Geography
      p(id='content') The study of Earth's landforms is called physical geography. Landforms can be mountains and valleys. They can also be glaciers, lakes or rivers.
      div(class='immersive-reader-button' data-button-style='iconAndText' data-locale='en-US' onclick='launchImmersiveReader()')
      script.
        function launchImmersiveReader() {
          // First, get a token using our /token endpoint
          $.ajax('/token', { success: token => {
            // Second, grab the content from the page
            const content = {
              title: document.getElementById('title').innerText,
              chunks: [ {
                content: document.getElementById('content').innerText + '\n\n',
                lang: 'en'
              } ]
            };

            // Third, launch the Immersive Reader
            ImmersiveReader.launchAsync(token, content);
          }});
        }
    ```

3. A aplicação web está pronta. Inicie a aplicação executando:

    ```bash
    npm start
    ```

4. Abra o browser e navegue para _http://localhost:3000_ . Deverá ver o conteúdo acima na página. Clique nas **leitor envolventes** botão para iniciar o leitor envolventes com o seu conteúdo.

## <a name="specify-the-language-of-your-content"></a>Especificar o idioma do seu conteúdo

O leitor de imersão tem suporte para várias linguagens diferentes. Pode especificar o idioma do seu conteúdo ao seguir os passos abaixo.

1. Open _views\index.pug_ e adicione o seguinte código abaixo o `p(id=content)` etiqueta que adicionou no passo anterior. Este código adiciona algum conteúdo Espanhol conteúdo à sua página.

    ```pug
    p(id='content-spanish') El estudio de las formas terrestres de la Tierra se llama geografía física. Los accidentes geográficos pueden ser montañas y valles. También pueden ser glaciares, lagos o ríos.
    ```

2. No código JavaScript, adicione o seguinte acima da chamada para `ImmersiveReader.launchAsync`. Este código passa o conteúdo de espanhol para o leitor envolvente e experimental.

    ```pug
    content.chunks.push({
      content: document.getElementById('content-spanish').innerText + '\n\n',
      lang: 'es'
    });
    ```

3. Navegue para _http://localhost:3000_ novamente. Deverá ver o texto de espanhol na página e quando clica em **leitor envolventes**, que será apresentado no leitor envolventes também.

## <a name="specify-the-language-of-the-immersive-reader-interface"></a>Especificar o idioma da interface do leitor de imersão

Por predefinição, o idioma da interface do leitor de imersão corresponde a definições de idioma do navegador. Também pode especificar o idioma da interface do leitor envolventes com o código a seguir.

1. Na _views\index.pug_, substitua a chamada para `ImmersiveReader.launchAsync(token, content)` com o código abaixo.

    ```javascript
    const options = {
        uiLang: 'fr',
    }
    ImmersiveReader.launchAsync(token, content, options);
    ```

2. Navegue para _http://localhost:3000_ . Quando inicia o leitor Imersivos, será apresentada a interface em francês.

## <a name="launch-the-immersive-reader-with-math-content"></a>Inicie o leitor envolventes com conteúdo de matemática

Pode incluir conteúdo matemática no leitor de imersão usando [MathML](https://developer.mozilla.org/en-US/docs/Web/MathML).

1. Modificar _views\index.pug_ para incluir o seguinte código acima a chamada para `ImmersiveReader.launchAsync`:

    ```javascript
    const mathML = '<math xmlns="https://www.w3.org/1998/Math/MathML" display="block"> \
      <munderover> \
        <mo>∫</mo> \
        <mn>0</mn> \
        <mn>1</mn> \
      </munderover> \
      <mrow> \
        <msup> \
          <mi>x</mi> \
          <mn>2</mn> \
        </msup> \
        <mo>ⅆ</mo> \
        <mi>x</mi> \
      </mrow> \
    </math>';

    content.chunks.push({
      content: mathML,
      mimeType: 'application/mathml+xml'
    });
    ```

2. Navegue para _http://localhost:3000_ . Quando inicia o leitor envolventes e desloque-se para a parte inferior, verá a fórmula de matemática.

## <a name="next-steps"></a>Passos seguintes

* Explorar o [leitor envolvente SDK](https://github.com/Microsoft/immersive-reader-sdk) e o [envolventes leitor a referência do SDK](./reference.md)
* Ver exemplos de código no [GitHub](https://github.com/microsoft/immersive-reader-sdk/samples/advanced-csharp)