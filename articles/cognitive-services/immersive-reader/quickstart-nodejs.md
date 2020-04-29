---
title: 'Quickstart: Criar uma aplicação web que lança o Leitor Imersivo com Node.js'
titleSuffix: Azure Cognitive Services
description: Neste arranque rápido, você constrói uma aplicação web de raiz e adiciona a funcionalidade API imersiva do leitor.
author: pasta
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: quickstart
ms.date: 01/14/2020
ms.author: pasta
ms.openlocfilehash: 749e75fed409632c613713a49154e4cd8dc265b3
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75946328"
---
# <a name="quickstart-create-a-web-app-that-launches-the-immersive-reader-nodejs"></a>Quickstart: Criar uma aplicação web que lança o Leitor Imersivo (Node.js)

O [Leitor Imersivo](https://www.onenote.com/learningtools) é uma ferramenta inclusivamente projetada que implementa técnicas comprovadas para melhorar a compreensão da leitura.

Neste arranque rápido, você constrói uma aplicação web de raiz e integra o Leitor Imersivo utilizando o SDK de leitor imersivo. Uma amostra completa deste arranque rápido está disponível [aqui.](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/quickstart-nodejs)

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Um recurso Imersivo do Leitor configurado para autenticação de Diretório Ativo Azure. Siga [estas instruções](./how-to-create-immersive-reader.md) para ser configurado. Você precisará de alguns dos valores criados aqui ao configurar as propriedades do ambiente. Guarde a saída da sua sessão num ficheiro de texto para referência futura.
* [Nó.js](https://nodejs.org/) e [Fios](https://yarnpkg.com)
* Um IDE como [Visual Studio Code](https://code.visualstudio.com/)

## <a name="create-a-nodejs-web-app-with-express"></a>Crie uma aplicação web Node.js com express

Crie uma aplicação web Node.js com a `express-generator` ferramenta.

```bash
npm install express-generator -g
express --view=pug quickstart-nodejs
cd quickstart-nodejs
```

Instale dependências de fios, `request` `dotenv`adicione dependências e, que serão usados mais tarde no arranque rápido.

```bash
yarn
yarn add request
yarn add dotenv
```

## <a name="set-up-authentication"></a>Configurar autenticação

### <a name="configure-authentication-values"></a>Configurar valores de autenticação

Crie um novo ficheiro chamado _.env_ na raiz do seu projeto. Colhe o seguinte código nele, fornecendo os valores dados quando criou o seu recurso Imersivo leitor.
Não inclua aspas ou os caracteres "{" e "}".

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

### <a name="update-the-router-to-acquire-the-token"></a>Atualize o router para adquirir o símbolo
Abra o ficheiro _routes\index.js_ e substitua o código gerado automaticamente pelo seguinte código.

Este código cria um ponto final da API que adquire um símbolo de autenticação Azure AD utilizando a sua senha principal de serviço. Também recupera o subdomínio. Em seguida, devolve um objeto contendo o símbolo e o subdomínio.

```javascript
var express = require('express');
var router = express.Router();
var request = require('request');

/* GET home page. */
router.get('/', function(req, res, next) {
  res.render('index', { title: 'Express' });
});

router.get('/GetTokenAndSubdomain', function(req, res) {
    try {
        request.post({
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
        function(err, resp, tokenResult) {
            if (err) {
                console.log(err);
                return res.status(500).send('CogSvcs IssueToken error');
            }

            var tokenResultParsed = JSON.parse(tokenResult);

            if (tokenResultParsed.error) {
                console.log(tokenResult);
                return res.send({error :  "Unable to acquire Azure AD token. Check the debugger for more information."})
            }

            var token = tokenResultParsed.access_token;
            var subdomain = process.env.SUBDOMAIN;
            return res.send({token, subdomain});
        });
    } catch (err) {
        console.log(err);
        return res.status(500).send('CogSvcs IssueToken error');
    }
});

module.exports = router;
```

O ponto final da API **GetTokenAndSubdomain** deve ser protegido por detrás de alguma forma de autenticação (por exemplo, [OAuth](https://oauth.net/2/)) para impedir que utilizadores não autorizados obtenham fichas para usar contra o seu serviço de leitor imersivo e faturação; que o trabalho está fora do âmbito deste arranque rápido.

## <a name="add-sample-content"></a>Adicionar conteúdo da amostra

Agora, vamos adicionar conteúdo de amostra a esta aplicação web. Abra _vistas\index.pug_ e substitua o código gerado automaticamente por esta amostra:

```pug
doctype html
html
   head
      title Immersive Reader Quickstart Node.js

      link(rel='stylesheet', href='https://stackpath.bootstrapcdn.com/bootstrap/3.4.1/css/bootstrap.min.css')

      // A polyfill for Promise is needed for IE11 support.
      script(src='https://cdn.jsdelivr.net/npm/promise-polyfill@8/dist/polyfill.min.js')

      script(src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.1.0.0.js')
      script(src='https://code.jquery.com/jquery-3.3.1.min.js')

      style(type="text/css").
        .immersive-reader-button {
          background-color: white;
          margin-top: 5px;
          border: 1px solid black;
          float: right;
        }
   body
      div(class="container")
        button(class="immersive-reader-button" data-button-style="iconAndText" data-locale="en")

        h1(id="ir-title") About Immersive Reader
        div(id="ir-content" lang="en-us")
          p Immersive Reader is a tool that implements proven techniques to improve reading comprehension for emerging readers, language learners, and people with learning differences. The Immersive Reader is designed to make reading more accessible for everyone. The Immersive Reader

            ul
                li Shows content in a minimal reading view
                li Displays pictures of commonly used words
                li Highlights nouns, verbs, adjectives, and adverbs
                li Reads your content out loud to you
                li Translates your content into another language
                li Breaks down words into syllables

          h3 The Immersive Reader is available in many languages.

          p(lang="es-es") El Lector inmersivo está disponible en varios idiomas.
          p(lang="zh-cn") 沉浸式阅读器支持许多语言
          p(lang="de-de") Der plastische Reader ist in vielen Sprachen verfügbar.
          p(lang="ar-eg" dir="rtl" style="text-align:right") يتوفر \"القارئ الشامل\" في العديد من اللغات.

script(type="text/javascript").
  function getTokenAndSubdomainAsync() {
        return new Promise(function (resolve, reject) {
            $.ajax({
                url: "/GetTokenAndSubdomain",
                type: "GET",
                success: function (data) {
                    if (data.error) {
                        reject(data.error);
                    } else {
                        resolve(data);
                    }
                },
                error: function (err) {
                    reject(err);
                }
            });
        });
    }

    $(".immersive-reader-button").click(function () {
        handleLaunchImmersiveReader();
    });

    function handleLaunchImmersiveReader() {
        getTokenAndSubdomainAsync()
            .then(function (response) {
                const token = response["token"];
                const subdomain = response["subdomain"];
                // Learn more about chunk usage and supported MIME types https://docs.microsoft.com/azure/cognitive-services/immersive-reader/reference#chunk
                const data = {
                    title: $("#ir-title").text(),
                    chunks: [{
                        content: $("#ir-content").html(),
                        mimeType: "text/html"
                    }]
                };
                // Learn more about options https://docs.microsoft.com/azure/cognitive-services/immersive-reader/reference#options
                const options = {
                    "onExit": exitCallback,
                    "uiZIndex": 2000
                };
                ImmersiveReader.launchAsync(token, subdomain, data, options)
                    .catch(function (error) {
                        alert("Error in launching the Immersive Reader. Check the console.");
                        console.log(error);
                    });
            })
            .catch(function (error) {
                alert("Error in getting the Immersive Reader token and subdomain. Check the console.");
                console.log(error);
            });
    }

    function exitCallback() {
        console.log("This is the callback function. It is executed when the Immersive Reader closes.");
    }
```


Note que todo o texto tem um atributo **de lang,** que descreve as línguas do texto. Este atributo ajuda o Leitor Imersivo a fornecer características linguísticas e gramaticais relevantes.

## <a name="build-and-run-the-app"></a>Compilar e executar a aplicação

A nossa aplicação está pronta. Inicie a aplicação executando:

```bash
npm start
```

Abra o seu _http://localhost:3000_navegador e navegue para . Deverá ver o seguinte:

![App de amostras](./media/quickstart-nodejs/1-buildapp.png)

## <a name="launch-the-immersive-reader"></a>Lançar o Leitor Imersivo

Quando clicar no botão "Leitor Imersivo", verá o Leitor Imersivo lançado com o conteúdo na página.

![Leitura Avançada](./media/quickstart-nodejs/2-viewimmersivereader.png)

## <a name="next-steps"></a>Passos seguintes

* Explore o [SDK imersivo do leitor](https://github.com/microsoft/immersive-reader-sdk) e a [referência SDK do leitor imersivo](./reference.md)