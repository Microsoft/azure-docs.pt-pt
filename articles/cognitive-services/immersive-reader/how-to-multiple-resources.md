---
title: Integrar múltiplos recursos imersivos do Leitor
titleSuffix: Azure Cognitive Services
description: Neste tutorial, irá criar uma aplicação Node.js que lança o Leitor Imersivo utilizando vários recursos imersivos do Leitor.
author: skamal
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: how-to
ms.date: 01/14/2020
ms.author: skamal
ms.custom: devx-track-js
ms.openlocfilehash: dc6c7bd9e38aa21ab10f55fca2500b0c12194d43
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102620171"
---
# <a name="integrate-multiple-immersive-reader-resources"></a>Integrar múltiplos recursos imersivos do Leitor

Na [visão geral,](./overview.md)aprendeu sobre o que é o Leitor Imersivo e como implementa técnicas comprovadas para melhorar a compreensão da leitura para os alunos de línguas, leitores emergentes e alunos com diferenças de aprendizagem. No [arranque rápido,](./quickstarts/client-libraries.md)aprendeu a usar o Leitor Imersivo com um único recurso. Este tutorial abrange como integrar múltiplos recursos imersivos do Leitor na mesma aplicação. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar vários recursos imersivos do Leitor sob um grupo de recursos existente
> * Lançar o Leitor Imersivo usando vários recursos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/cognitive-services/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Siga o [quickstart](./quickstarts/client-libraries.md?pivots=programming-language-nodejs) para criar uma aplicação web que lança o Leitor Imersivo com NodeJS. Nesse arranque rápido, configura-se um único recurso imersivo do Leitor. Vamos construir ainda mais neste tutorial.

## <a name="create-the-immersive-reader-resources"></a>Criar os recursos imersivos do Leitor

Siga estas instruções para criar cada recurso [Imersivo](./how-to-create-immersive-reader.md) Reader. O script **Create-ImersiveReaderResource** `ResourceName` `ResourceSubdomain` tem, e `ResourceLocation` como parâmetros. Estes devem ser únicos para cada recurso que está sendo criado. Os parâmetros restantes devem ser os mesmos que usou ao configurar o seu primeiro recurso Leitor Imersivo. Desta forma, cada recurso pode ser ligado ao mesmo grupo de recursos Azure e aplicação AD AZure.

O exemplo abaixo mostra como criar dois recursos, um no WestUS, e outro em EastUS. Note os valores únicos para `ResourceName` `ResourceSubdomain` , e `ResourceLocation` .

```azurepowershell-interactive
Create-ImmersiveReaderResource
  -SubscriptionName <SUBSCRIPTION_NAME> `
  -ResourceName Resource_name_wus `
  -ResourceSubdomain resource-subdomain-wus `
  -ResourceSKU <RESOURCE_SKU> `
  -ResourceLocation westus `
  -ResourceGroupName <RESOURCE_GROUP_NAME> `
  -ResourceGroupLocation <RESOURCE_GROUP_LOCATION> `
  -AADAppDisplayName <AAD_APP_DISPLAY_NAME> `
  -AADAppIdentifierUri <AAD_APP_IDENTIFIER_URI> `
  -AADAppClientSecret <AAD_APP_CLIENT_SECRET>

Create-ImmersiveReaderResource
  -SubscriptionName <SUBSCRIPTION_NAME> `
  -ResourceName Resource_name_eus `
  -ResourceSubdomain resource-subdomain-eus `
  -ResourceSKU <RESOURCE_SKU> `
  -ResourceLocation eastus `
  -ResourceGroupName <RESOURCE_GROUP_NAME> `
  -ResourceGroupLocation <RESOURCE_GROUP_LOCATION> `
  -AADAppDisplayName <AAD_APP_DISPLAY_NAME> `
  -AADAppIdentifierUri <AAD_APP_IDENTIFIER_URI> `
  -AADAppClientSecret <AAD_APP_CLIENT_SECRET>
```

## <a name="add-resources-to-environment-configuration"></a>Adicionar recursos à configuração do ambiente

No arranque rápido, criou um ficheiro de configuração ambiental que contém os `TenantId` `ClientId` , e `ClientSecret` `Subdomain` parâmetros. Uma vez que todos os seus recursos usam a mesma aplicação AD Azure, podemos usar os mesmos valores `TenantId` `ClientId` para, e `ClientSecret` . A única mudança que precisa de ser feita é listar cada subdomínio para cada recurso.

O seu novo ficheiro __.env__ deve agora parecer algo como o seguinte:

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN_WUS={YOUR_WESTUS_SUBDOMAIN}
SUBDOMAIN_EUS={YOUR_EASTUS_SUBDOMAIN}
```

Certifique-se de que não compromete este ficheiro em controlo de fontes, pois contém segredos que não devem ser tornados públicos.

Em seguida, vamos modificar o ficheiro  _routes\index.js_ que criamos para suportar os nossos múltiplos recursos. Substitua o seu conteúdo pelo seguinte código.

Como antes, este código cria um ponto final da API que adquire um token de autenticação AD Azure usando a sua senha principal de serviço. Desta vez, permite ao utilizador especificar uma localização de recursos e passá-la como parâmetro de consulta. Em seguida, devolve um objeto que contém o símbolo e o subdomínio correspondente.

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

            var subdomain = "";
            var region = req.query && req.query.region;
            switch (region) {
                case "eus":
                    subdomain = process.env.SUBDOMAIN_EUS
                    break;
                case "wus":
                default:
                    subdomain = process.env.SUBDOMAIN_WUS
            }

            return res.send({token, subdomain});
        });
    } catch (err) {
        console.log(err);
        return res.status(500).send('CogSvcs IssueToken error');
    }
});

module.exports = router;
```

O ponto final da API **getimmersivereaderlaunchparams** deve ser protegido por trás de alguma forma de autenticação (por exemplo, [OAuth)](https://oauth.net/2/)para impedir que utilizadores não autorizados obtenham fichas para usar contra o seu serviço de Leitor imersivo e faturação; que o trabalho está fora do âmbito deste tutorial.

## <a name="launch-the-immersive-reader-with-sample-content"></a>Lançar o Leitor Imersivo com conteúdo de amostra

1. Vistas _abertas\index.pug_, e substitua o seu conteúdo pelo seguinte código. Este código povoa a página com algum conteúdo da amostra e adiciona dois botões que lançam o Leitor Imersivo. Um para o lançamento do Leitor Imersivo para o recurso EastUS, e outro para o recurso WestUS.

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
                button(class="immersive-reader-button" data-button-style="icon" data-locale="en" onclick='handleLaunchImmersiveReader("wus")') WestUS Immersive Reader
                button(class="immersive-reader-button" data-button-style="icon" data-locale="en" onclick='handleLaunchImmersiveReader("eus")') EastUS Immersive Reader

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
    function getTokenAndSubdomainAsync(region) {
            return new Promise(function (resolve, reject) {
                $.ajax({
                    url: "/GetTokenAndSubdomain",
                    type: "GET",
                    data: {
                        region: region
                    },
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

        function handleLaunchImmersiveReader(region) {
            getTokenAndSubdomainAsync(region)
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

3. A nossa aplicação web está agora pronta. Inicie a aplicação executando:

    ```bash
    npm start
    ```

4. Abra o seu navegador e navegue para `http://localhost:3000` . Deve ver o conteúdo acima na página. Clique no botão **EastUS Immersive Reader** ou no botão **WestUS Immersive Reader** para lançar o Leitor Imersivo utilizando esses respetivos recursos.

## <a name="next-steps"></a>Passos seguintes

* Explore o [SDK do leitor imersivo](https://github.com/microsoft/immersive-reader-sdk) e a [referência SDK do leitor imersivo](./reference.md)
* Ver amostras de código no [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/advanced-csharp)