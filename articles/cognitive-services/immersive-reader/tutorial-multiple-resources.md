---
title: 'Tutorial: Integrar múltiplos recursos imersivos do leitor'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, você vai criar uma aplicação Node.js que lança o Leitor Imersivo usando vários recursos imersivos do Leitor.
author: skamal
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 01/14/2020
ms.author: skamal
ms.openlocfilehash: f68112095bc8a8fd9bcc1bd67ff77827d6d00fd7
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82195626"
---
# <a name="tutorial-integrate-multiple-immersive-reader-resources"></a>Tutorial: Integrar múltiplos recursos imersivos do leitor

Na [visão geral,](./overview.md)aprendeu sobre o que é o Leitor Imersivo e como implementa técnicas comprovadas para melhorar a compreensão da leitura para os alunos de línguas, leitores emergentes e alunos com diferenças de aprendizagem. No início rápido do [Node.js,](./quickstart-nodejs.md)aprendeu a usar o Leitor Imersivo com um único recurso. Este tutorial abrange como integrar vários recursos imersivos do Leitor na mesma aplicação. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar múltiplos recursos imersivos do Leitor sob um grupo de recursos existente
> * Lançar o Leitor Imersivo usando vários recursos

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Siga o [quickstart](./quickstart-nodejs.md) para criar uma aplicação web que lança o Leitor Imersivo com NodeJS. Nesse arranque rápido, configura um único recurso Imersivo do Leitor. Vamos construir em cima disso neste tutorial.

## <a name="create-the-immersive-reader-resources"></a>Criar os recursos imersivos do leitor

Siga [estas instruções](./how-to-create-immersive-reader.md) para criar cada recurso imersivo do Leitor. O script **Create-ImmersiveReaderResource** tem `ResourceName`, `ResourceSubdomain`e `ResourceLocation` como parâmetros. Estes devem ser únicos para cada recurso que está a ser criado. Os parâmetros restantes devem ser os mesmos que usou na configuração do seu primeiro recurso Imersivo leitor. Desta forma, cada recurso pode ser ligado ao mesmo grupo de recursos Azure e à aplicação Azure AD.

O exemplo abaixo mostra como criar dois recursos, um no WestUS, e outro no EastUS. Reparenos valores `ResourceName` `ResourceSubdomain`únicos `ResourceLocation`para, e .

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

No arranque rápido, criou-se um ficheiro `TenantId` `ClientId`de `ClientSecret`configuração ambiental que contém os parâmetros e `Subdomain` parâmetros. Uma vez que todos os seus recursos utilizam a mesma aplicação `TenantId` `ClientId`Azure `ClientSecret`AD, podemos usar os mesmos valores para o , e . A única mudança que precisa de ser feita é listar cada subdomínio para cada recurso.

O seu novo ficheiro __.env__ deve agora parecer algo como o seguinte:

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN_WUS={YOUR_WESTUS_SUBDOMAIN}
SUBDOMAIN_EUS={YOUR_EASTUS_SUBDOMAIN}
```

Certifique-se de não comprometer este ficheiro no controlo de fontes, uma vez que contém segredos que não devem ser tornados públicos.

Em seguida, vamos modificar o ficheiro _routes\index.js_ que criamos para apoiar os nossos múltiplos recursos. Substitua o seu conteúdo pelo seguinte código.

Como antes, este código cria um ponto final da API que adquire um símbolo de autenticação Azure AD utilizando a sua senha principal de serviço. Desta vez, permite ao utilizador especificar uma localização de recursos e passá-la como parâmetro de consulta. Em seguida, devolve um objeto que contém o símbolo e o subdomínio correspondente.

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

Os **pontos finais getimmersivereaderlaunchparams** API devem ser protegidos por detrás de alguma forma de autenticação (por exemplo, [OAuth](https://oauth.net/2/)) para impedir que utilizadores não autorizados obtenham fichas para usar contra o seu serviço de Leitor Imersivo e faturação; que o trabalho está fora do âmbito deste tutorial.

## <a name="launch-the-immersive-reader-with-sample-content"></a>Lançar o Leitor Imersivo com conteúdo de amostra

1. Abra _views\index.pug,_ e substitua o seu conteúdo pelo seguinte código. Este código povoa a página com algum conteúdo de amostra, e adiciona dois botões que lançam o Leitor Imersivo. Um para o lançamento do Leitor Imersivo para o recurso EastUS, e outro para o recurso WestUS.

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

3. A nossa aplicação está pronta. Inicie a aplicação executando:

    ```bash
    npm start
    ```

4. Abra o seu `http://localhost:3000`navegador e navegue para . Deve ver o conteúdo acima na página. Clique no botão **EastUS Immersive Reader** ou no botão **WestUS Immersive Reader** para lançar o Leitor Imersivo utilizando esses respetivos recursos.

## <a name="next-steps"></a>Passos seguintes

* Explore o [SDK imersivo do leitor](https://github.com/microsoft/immersive-reader-sdk) e a [referência SDK do leitor imersivo](./reference.md)
* Ver amostras de código no [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/advanced-csharp)