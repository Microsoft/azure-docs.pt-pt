---
title: 'Tutorial: Lançar o Leitor Imersivo usando Node.js'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, irá criar uma aplicação Node.js que lança o Leitor Imersivo.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 01/14/2020
ms.author: metan
ms.custom: devx-track-js
ms.openlocfilehash: 1ac23ad66cadc553095ff869b665a6bba2fba6f3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91262285"
---
# <a name="tutorial-launch-the-immersive-reader-nodejs"></a>Tutorial: Lançar o Leitor Imersivo (Node.js)

Na [visão geral,](./overview.md)aprendeu sobre o que é o Leitor Imersivo e como implementa técnicas comprovadas para melhorar a compreensão da leitura para os alunos de línguas, leitores emergentes e alunos com diferenças de aprendizagem. Este tutorial abrange como criar uma aplicação web Node.js que lança o Leitor Imersivo. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma Node.js web app com o Express
> * Adquirir um token de acesso
> * Lançar o Leitor Imersivo com conteúdo de amostra
> * Especifique o idioma do seu conteúdo
> * Especificar o idioma da interface imersiva do leitor
> * Lançar o Leitor Imersivo com conteúdo matemático

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/cognitive-services/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Um recurso imersivo do Leitor configurado para a autenticação do Azure Ative Directory. Siga [estas instruções](./how-to-create-immersive-reader.md) para se preparar. Você precisará de alguns dos valores criados aqui ao configurar as propriedades ambientais. Guarde a saída da sua sessão num ficheiro de texto para referência futura.
* [Node.js](https://nodejs.org/) e [Fios](https://yarnpkg.com)
* Um IDE como [Código de Estúdio Visual](https://code.visualstudio.com/)

## <a name="create-a-nodejs-web-app-with-express"></a>Criar uma Node.js web app com o Express

Crie uma aplicação web Node.js com a `express-generator` ferramenta.

```bash
npm install express-generator -g
express --view=pug myapp
cd myapp
```

Instale dependências de fios e adicione dependências `request` e `dotenv` , que serão usadas mais tarde no tutorial.

```bash
yarn
yarn add request
yarn add dotenv
```

## <a name="acquire-an-azure-ad-authentication-token"></a>Adquirir um token de autenticação AD AZure

Em seguida, escreva uma API backend para recuperar um token de autenticação AD Azure.

Você precisa de alguns valores da configuração auth Ad Azure pré-requisito acima para esta parte. Consulte de volta o ficheiro de texto que guardou nessa sessão.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

Assim que tiver estes valores, crie um novo ficheiro chamado _.env_e cole-o o seguinte código, fornecendo os valores de propriedade personalizados de cima. Não inclua aspas ou os caracteres "{" e "}".

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

Certifique-se de que não compromete este ficheiro em controlo de fontes, pois contém segredos que não devem ser tornados públicos.

Em seguida, abra _app.js_ e adicione o seguinte ao topo do ficheiro. Isto carrega as propriedades definidas no ficheiro .env como variáveis ambientais em Nó.

```javascript
require('dotenv').config();
```

Abra o ficheiro _routes\index.js_ e substitua o seu conteúdo pelo seguinte código.

Este código cria um ponto final da API que adquire um token de autenticação AD Azure usando a sua senha principal de serviço. Também recupera o subdomínio. Em seguida, devolve um objeto contendo o símbolo e o subdomínio.

```javascript
var request = require('request');
var express = require('express');
var router = express.Router();

router.get('/getimmersivereaderlaunchparams', function(req, res) {
    request.post ({
                headers: {
                    'content-type': 'application/x-www-form-urlencoded'
                },
                url: `https://login.windows.net/${process.env.TENANT_ID}/oauth2/token`,
                form: {
                    grant_type: 'client_credentials',
                    client_id: process.env.CLIENT_ID,
                    client_secret: process.env.CLIENT_SECRET,
                    resource: 'https://cognitiveservices.azure.com/'
                }
        },
        function(err, resp, tokenResponse) {
                if (err) {
                    return res.status(500).send('CogSvcs IssueToken error');
                }

                const token = JSON.parse(tokenResponse).access_token;
                const subdomain = process.env.SUBDOMAIN;
                return res.send({token: token, subdomain: subdomain});
        }
  );
});

/* GET home page. */
router.get('/', function(req, res, next) {
  res.render('index', { title: 'Express' });
});

module.exports = router;

```

O ponto final da API **getimmersivereaderlaunchparams** deve ser protegido por trás de alguma forma de autenticação (por exemplo, [OAuth)](https://oauth.net/2/)para impedir que utilizadores não autorizados obtenham fichas para usar contra o seu serviço de Leitor imersivo e faturação; que o trabalho está fora do âmbito deste tutorial.

## <a name="launch-the-immersive-reader-with-sample-content"></a>Lançar o Leitor Imersivo com conteúdo de amostra

1. Abra _as vistas\layout.pug_, e adicione o seguinte código na `head` etiqueta, antes da `body` etiqueta. Estas `script` etiquetas carregam o SDK e jQuery [do Leitor Imersivo.](https://github.com/microsoft/immersive-reader-sdk)

    ```pug
    script(src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.0.0.2.js')
    script(src='https://code.jquery.com/jquery-3.3.1.min.js')
    ```

2. Vistas _abertas\index.pug_, e substitua o seu conteúdo pelo seguinte código. Este código povoa a página com algum conteúdo da amostra e adiciona um botão que lança o Leitor Imersivo.

    ```pug
    extends layout

    block content
          h2(id='title') Geography
          p(id='content') The study of Earth's landforms is called physical geography. Landforms can be mountains and valleys. They can also be glaciers, lakes or rivers.
          div(class='immersive-reader-button' data-button-style='iconAndText' data-locale='en-US' onclick='launchImmersiveReader()')
          script.

            function getImmersiveReaderLaunchParamsAsync() {
                    return new Promise((resolve, reject) => {
                        $.ajax({
                                url: '/getimmersivereaderlaunchparams',
                                type: 'GET',
                                success: data => {
                                        resolve(data);
                                },
                                error: err => {
                                        console.log('Error in getting token and subdomain!', err);
                                        reject(err);
                                }
                        });
                    });
            }

            async function launchImmersiveReader() {
                    const content = {
                            title: document.getElementById('title').innerText,
                            chunks: [{
                                    content: document.getElementById('content').innerText + '\n\n',
                                    lang: 'en'
                            }]
                    };

                    const launchParams = await getImmersiveReaderLaunchParamsAsync();
                    const token = launchParams.token;
                    const subdomain = launchParams.subdomain;

                    ImmersiveReader.launchAsync(token, subdomain, content);
            }
    ```

3. A nossa aplicação web está agora pronta. Inicie a aplicação executando:

    ```bash
    npm start
    ```

4. Abra o seu navegador e navegue para _http://localhost:3000_ . Deve ver o conteúdo acima na página. Clique no botão **Leitor Imersivo** para lançar o Leitor Imersivo com o seu conteúdo.

## <a name="specify-the-language-of-your-content"></a>Especifique o idioma do seu conteúdo

O Leitor Imersivo tem suporte para muitas línguas diferentes. Pode especificar o idioma do seu conteúdo seguindo os passos abaixo.

1. Abra _as vistas\index.pug_ e adicione o seguinte código abaixo da `p(id=content)` etiqueta que adicionou no passo anterior. Este código adiciona alguns conteúdos em espanhol à sua página.

    ```pug
    p(id='content-spanish') El estudio de las formas terrestres de la Tierra se llama geografía física. Los accidentes geográficos pueden ser montañas y valles. También pueden ser glaciares, lagos o ríos.
    ```

2. No código JavaScript, adicione o seguinte acima da chamada para `ImmersiveReader.launchAsync` . Este código transmite o conteúdo espanhol para o Leitor Imersivo.

    ```pug
    content.chunks.push({
      content: document.getElementById('content-spanish').innerText + '\n\n',
      lang: 'es'
    });
    ```

3. Navegue para _http://localhost:3000_ novamente. Deve ver o texto espanhol na página e, quando clicar no **Leitor Imersivo,** também aparecerá no Leitor Imersivo.

## <a name="specify-the-language-of-the-immersive-reader-interface"></a>Especificar o idioma da interface imersiva do leitor

Por padrão, o idioma da interface Imersiva do Leitor corresponde às definições de idioma do navegador. Também pode especificar o idioma da interface Imersiva do Leitor com o seguinte código.

1. Em _pontos de vista\index.pug,_ substitua a chamada `ImmersiveReader.launchAsync(token, subdomain, content)` pelo código abaixo.

    ```javascript
    const options = {
        uiLang: 'fr',
    }
    ImmersiveReader.launchAsync(token, subdomain, content, options);
    ```

2. Navegue para _http://localhost:3000_ . Quando lançar o Leitor Imersivo, a interface será mostrada em francês.

## <a name="launch-the-immersive-reader-with-math-content"></a>Lançar o Leitor Imersivo com conteúdo matemático

Pode incluir conteúdo matemático no Leitor Imersivo utilizando [MathML](https://developer.mozilla.org/en-US/docs/Web/MathML).

1. Modificar _pontos de vista\index.pug_ para incluir o seguinte código acima da chamada `ImmersiveReader.launchAsync` para:

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

2. Navegue para _http://localhost:3000_ . Quando lançar o Leitor Imersivo e rolar para o fundo, verá a fórmula matemática.

## <a name="next-steps"></a>Passos seguintes

* Explore o [SDK do leitor imersivo](https://github.com/microsoft/immersive-reader-sdk) e a [referência SDK do leitor imersivo](./reference.md)
* Ver amostras de código no [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/advanced-csharp)
