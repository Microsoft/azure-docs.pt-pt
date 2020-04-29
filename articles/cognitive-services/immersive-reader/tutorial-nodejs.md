---
title: 'Tutorial: Lance o Leitor Imersivo usando node.js'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, você vai criar uma aplicação Node.js que lança o Leitor Imersivo.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: 139dd2ebdabbc91a6de3b0a1eb921b110d47c3f3
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76842032"
---
# <a name="tutorial-launch-the-immersive-reader-nodejs"></a>Tutorial: Lance o Leitor Imersivo (Node.js)

Na [visão geral,](./overview.md)aprendeu sobre o que é o Leitor Imersivo e como implementa técnicas comprovadas para melhorar a compreensão da leitura para os alunos de línguas, leitores emergentes e alunos com diferenças de aprendizagem. Este tutorial cobre como criar uma aplicação web Node.js que lança o Leitor Imersivo. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie uma aplicação web Node.js com express
> * Adquirir um token de acesso
> * Lançar o Leitor Imersivo com conteúdo de amostra
> * Especifique a linguagem do seu conteúdo
> * Especifique a linguagem da interface imersiva do leitor
> * Lançar o Leitor Imersivo com conteúdo matemático

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Um recurso Imersivo do Leitor configurado para autenticação de Diretório Ativo Azure. Siga [estas instruções](./how-to-create-immersive-reader.md) para ser configurado. Você precisará de alguns dos valores criados aqui ao configurar as propriedades do ambiente. Guarde a saída da sua sessão num ficheiro de texto para referência futura.
* [Nó.js](https://nodejs.org/) e [Fios](https://yarnpkg.com)
* Um IDE como [Visual Studio Code](https://code.visualstudio.com/)

## <a name="create-a-nodejs-web-app-with-express"></a>Crie uma aplicação web Node.js com express

Crie uma aplicação web Node.js com a `express-generator` ferramenta.

```bash
npm install express-generator -g
express --view=pug myapp
cd myapp
```

Instale dependências de fios, `request` `dotenv`adicione dependências e, que serão usados mais tarde no tutorial.

```bash
yarn
yarn add request
yarn add dotenv
```

## <a name="acquire-an-azure-ad-authentication-token"></a>Adquirir um símbolo de autenticação Azure AD

Em seguida, escreva uma API de backend para recuperar um símbolo de autenticação Azure AD.

Você precisa de alguns valores da configuração auth AUth Azure para esta parte. Volte a consultar o ficheiro de texto que guardou dessa sessão.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

Assim que tiver estes valores, crie um novo ficheiro chamado _.env_, e colhe o seguinte código nele, fornecendo os valores de propriedade personalizados de cima. Não inclua aspas ou os caracteres "{" e "}".

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

Certifique-se de não comprometer este ficheiro no controlo de fontes, uma vez que contém segredos que não devem ser tornados públicos.

Em seguida, abra _app.js_ e adicione o seguinte ao topo do ficheiro. Isto carrega as propriedades definidas no ficheiro .env como variáveis ambientais em Nó.

```javascript
require('dotenv').config();
```

Abra o ficheiro _routes\index.js_ e substitua o seu conteúdo pelo seguinte código.

Este código cria um ponto final da API que adquire um símbolo de autenticação Azure AD utilizando a sua senha principal de serviço. Também recupera o subdomínio. Em seguida, devolve um objeto contendo o símbolo e o subdomínio.

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

Os **pontos finais getimmersivereaderlaunchparams** API devem ser protegidos por detrás de alguma forma de autenticação (por exemplo, [OAuth](https://oauth.net/2/)) para impedir que utilizadores não autorizados obtenham fichas para usar contra o seu serviço de Leitor Imersivo e faturação; que o trabalho está fora do âmbito deste tutorial.

## <a name="launch-the-immersive-reader-with-sample-content"></a>Lançar o Leitor Imersivo com conteúdo de amostra

1. Vista _aberta\layout.pug,_ e adicione o `head` seguinte código `body` sob a etiqueta, antes da etiqueta. Estas `script` etiquetas carregam o SDK e o jQuery do [Leitor Imersivo.](https://github.com/microsoft/immersive-reader-sdk)

    ```pug
    script(src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.0.0.2.js')
    script(src='https://code.jquery.com/jquery-3.3.1.min.js')
    ```

2. Abra _views\index.pug,_ e substitua o seu conteúdo pelo seguinte código. Este código povoa a página com algum conteúdo de amostra, e adiciona um botão que lança o Leitor Imersivo.

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

3. A nossa aplicação está pronta. Inicie a aplicação executando:

    ```bash
    npm start
    ```

4. Abra o seu _http://localhost:3000_navegador e navegue para . Deve ver o conteúdo acima na página. Clique no botão **Leitor Imersivo** para lançar o Leitor Imersivo com o seu conteúdo.

## <a name="specify-the-language-of-your-content"></a>Especifique a linguagem do seu conteúdo

O Leitor Imersivo tem suporte para muitas línguas diferentes. Pode especificar a linguagem do seu conteúdo seguindo os passos abaixo.

1. Abra _views\index.pug_ e adicione o `p(id=content)` seguinte código abaixo da etiqueta que adicionou no passo anterior. Este código adiciona alguns conteúdos espanhóis à sua página.

    ```pug
    p(id='content-spanish') El estudio de las formas terrestres de la Tierra se llama geografía física. Los accidentes geográficos pueden ser montañas y valles. También pueden ser glaciares, lagos o ríos.
    ```

2. No código JavaScript, adicione o seguinte `ImmersiveReader.launchAsync`acima da chamada para . Este código transmite o conteúdo espanhol para o Leitor Imersivo.

    ```pug
    content.chunks.push({
      content: document.getElementById('content-spanish').innerText + '\n\n',
      lang: 'es'
    });
    ```

3. Navegue _http://localhost:3000_ de novo. Você deve ver o texto espanhol na página, e quando clicar no **Leitor Imersivo,** ele também aparecerá no Leitor Imersivo.

## <a name="specify-the-language-of-the-immersive-reader-interface"></a>Especifique a linguagem da interface imersiva do leitor

Por padrão, o idioma da interface Imersiva do Leitor corresponde às definições de idioma do navegador. Também pode especificar o idioma da interface Imersiva do Leitor com o seguinte código.

1. Em _views\index.pug,_ substitua `ImmersiveReader.launchAsync(token, subdomain, content)` a chamada com o código abaixo.

    ```javascript
    const options = {
        uiLang: 'fr',
    }
    ImmersiveReader.launchAsync(token, subdomain, content, options);
    ```

2. Navegar _http://localhost:3000_para. Quando lançar o Leitor Imersivo, a interface será mostrada em francês.

## <a name="launch-the-immersive-reader-with-math-content"></a>Lançar o Leitor Imersivo com conteúdo matemático

Pode incluir conteúdo matemático no Leitor Imersivo utilizando [mathML](https://developer.mozilla.org/en-US/docs/Web/MathML).

1. Modificar pontos de _vista\index.pug_ para `ImmersiveReader.launchAsync`incluir o seguinte código acima da chamada para:

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

2. Navegar _http://localhost:3000_para. Quando lançar o Leitor Imersivo e rolar para o fundo, verá a fórmula de matemática.

## <a name="next-steps"></a>Passos seguintes

* Explore o [SDK imersivo do leitor](https://github.com/microsoft/immersive-reader-sdk) e a [referência SDK do leitor imersivo](./reference.md)
* Ver amostras de código no [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/advanced-csharp)
