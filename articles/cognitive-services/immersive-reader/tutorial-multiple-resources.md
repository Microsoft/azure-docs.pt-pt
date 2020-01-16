---
title: 'Tutorial: integrar vários recursos de leitor de imersão'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, você criará um aplicativo node. js que inicia o leitor de imersão usando vários recursos de leitura de imersão.
author: skamal
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 01/14/2020
ms.author: skamal
ms.openlocfilehash: 3912d55b13f3977818e8d898efa651ffeb1a798a
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76046276"
---
# <a name="tutorial-integrate-multiple-immersive-reader-resources"></a>Tutorial: integrar vários recursos de leitor de imersão

Na [visão geral](./overview.md), você aprendeu sobre o que é o leitor de imersão e como ele implementa técnicas comprovadas para melhorar a compreensão da leitura para aprendizes de idioma, leitores emergentes e estudantes com diferenças de aprendizado. No guia de [início rápido do node. js](./quickstart-nodejs.md), você aprendeu a usar o leitor de imersão com um único recurso. Este tutorial aborda como integrar vários recursos de leitor de imersão no mesmo aplicativo. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar vários recursos de leitor de imersão em um grupo de recursos existente
> * Iniciar o leitor de imersão usando vários recursos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Siga o guia de [início rápido](./quickstart-nodejs.md) para criar um aplicativo Web que inicia o leitor de imersão com NodeJS. Nesse início rápido, você configura um único recurso de leitor de imersão. Nós vamos criar sobre isso neste tutorial.

## <a name="create-the-immersive-reader-resources"></a>Criar os recursos do leitor de imersão

Siga [estas instruções](./how-to-create-immersive-reader.md) para criar cada recurso de leitor de imersão. O script **Create-ImmersiveReaderResource** tem `ResourceName`, `ResourceSubdomain`e `ResourceLocation` como parâmetros. Eles devem ser exclusivos para cada recurso que está sendo criado. Os parâmetros restantes devem ser iguais aos que você usou ao configurar seu primeiro recurso de leitor de imersão. Dessa forma, cada recurso pode ser vinculado ao mesmo grupo de recursos do Azure e ao aplicativo do Azure AD.

O exemplo a seguir mostra como criar dois recursos, um na Westus e outro no Lesteus. Observe os valores exclusivos para `ResourceName`, `ResourceSubdomain`e `ResourceLocation`.

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

## <a name="add-resources-to-environment-configuration"></a>Adicionar recursos à configuração de ambiente

No guia de início rápido, você criou um arquivo de configuração de ambiente que contém os parâmetros `TenantId`, `ClientId`, `ClientSecret`e `Subdomain`. Como todos os seus recursos usam o mesmo aplicativo do Azure AD, podemos usar os mesmos valores para o `TenantId`, `ClientId`e `ClientSecret`. A única alteração que precisa ser feita é listar cada subdomínio para cada recurso.

O novo arquivo __. env__ agora deve ser semelhante ao seguinte:

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN_WUS={YOUR_WESTUS_SUBDOMAIN}
SUBDOMAIN_EUS={YOUR_EASTUS_SUBDOMAIN}
```

Certifique-se de não confirmar esse arquivo no controle do código-fonte, pois ele contém segredos que não devem ser tornados públicos.

Em seguida, vamos modificar o arquivo _routes\index.js_ que criamos para dar suporte a nossos vários recursos. Substitua seu conteúdo pelo código a seguir.

Como antes, esse código cria um ponto de extremidade de API que adquire um token de autenticação do Azure AD usando sua senha de entidade de serviço. Desta vez, ele permite que o usuário especifique um local de recurso e o passe como um parâmetro de consulta. Em seguida, ele retorna um objeto que contém o token e o subdomínio correspondente.

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

O ponto de extremidade da API do **getimmersivereaderlaunchparams** deve ser protegido por parte de alguma forma de autenticação (por exemplo, [OAuth](https://oauth.net/2/)) para impedir que usuários não autorizados obtenham tokens para uso no seu serviço de leitor de imersão e cobrança; Esse trabalho está além do escopo deste tutorial.

## <a name="launch-the-immersive-reader-with-sample-content"></a>Iniciar o leitor de imersão com conteúdo de exemplo

1. Abra _views\index.Pug_e substitua seu conteúdo pelo código a seguir. Esse código popula a página com algum conteúdo de exemplo e adiciona dois botões que iniciam o leitor de imersão. Um para iniciar o leitor de imersão para o recurso Lesteus e outro para o recurso Westus.

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

3. Nosso aplicativo Web agora está pronto. Inicie o aplicativo executando:

    ```bash
    npm start
    ```

4. Abra o navegador e navegue até [http://localhost:3000](http://localhost:3000). Você deve ver o conteúdo acima na página. Clique no botão **leitor de imersão do lesteus** ou no botão **leitor de imersão do oesteus** para iniciar o leitor de imersão usando esses recursos respectivos.

## <a name="next-steps"></a>Passos seguintes

* Explore o [SDK do leitor de imersão](https://github.com/microsoft/immersive-reader-sdk) e a [referência do SDK do leitor de imersão](./reference.md)
* Exibir exemplos de código no [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/advanced-csharp)