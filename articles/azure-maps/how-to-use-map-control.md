---
title: Introdução ao controle de mapa da Web no Azure Maps | Microsoft Docs
description: Saiba como usar a biblioteca JavaScript do lado do cliente do controle de mapeamento do Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: d70d0e1107a6ee1b53b178d8912c1b808472b142
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75432904"
---
# <a name="use-the-azure-maps-map-control"></a>Usar o controle de mapeamento do Azure Maps

O Controle de Mapeamento biblioteca JavaScript do lado do cliente permite renderizar mapas e a funcionalidade do Azure Maps incorporada em seu aplicativo Web ou móvel.

## <a name="create-a-new-map-in-a-web-page"></a>Criar um novo mapa em uma página da Web

Você pode inserir um mapa em uma página da Web usando o Controle de Mapeamento biblioteca JavaScript do lado do cliente.

1. Crie um novo arquivo HTML.

2. Carregue no SDK da Web do Azure Maps. Isso pode ser feito usando uma das duas opções;

    a. Use a versão CDN hospedada globalmente do SDK da Web do Azure Maps adicionando os pontos de extremidade de URL à folha de estilo e às referências de script no elemento `<head>` do arquivo:

    ```HTML
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
    ```

    b. Como alternativa, carregue o código-fonte do SDK da Web do Azure Maps localmente usando o pacote [Azure-Maps-Control](https://www.npmjs.com/package/azure-maps-control) NPM e hospede-o com seu aplicativo. Esse pacote também inclui definições de TypeScript.

    > **NPM instalar Azure-Maps-Control**

    Em seguida, adicione referências à folha de estilos do Azure Maps e às referências de origem do script para o elemento `<head>` do arquivo:

    ```HTML
    <link rel="stylesheet" href="node_modules/azure-maps-control/dist/atlas.min.css" type="text/css"> 
    <script src="node_modules/azure-maps-control/dist/atlas.min.js"></script>
    ```

    >[!Note]
    > As definições do typescript podem ser importadas para seu aplicativo adicionando:
    > ```Javascript
    > import * as atlas from 'azure-maps-control';
    > ```

3. Para renderizar o mapa para que ele preencha o corpo completo da página, adicione o seguinte elemento `<style>` ao elemento `<head>`.

    ```HTML
    <style>
        html, body {
            margin: 0;
        }

        #myMap {
            height: 100vh;
            width: 100vw;
        }
    </style>
    ```

4. No corpo da página, adicione um elemento `<div>` e dê a ele um `id` de **myMap**.

    ```HTML
    <body>
        <div id="myMap"></div>
    </body>
    ```

5. Para inicializar o controle de mapa, defina uma nova seção no corpo do HTML e crie um script. Passe o `id` do `<div>` de mapa ou um `HTMLElement` (por exemplo, `document.getElementById('myMap')`) como o primeiro parâmetro ao criar uma instância da classe `Map`. Use suas próprias credenciais de chave de conta do Azure Maps ou Azure Active Directory (AAD) para autenticar o mapa usando [as opções de autenticação](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.authenticationoptions). Se você precisar criar uma conta ou encontrar sua chave, siga as instruções em [criar uma conta](quick-demo-map-app.md#create-an-account-with-azure-maps) para criar uma assinatura de conta do Azure Maps e siga as etapas em [obter chave primária](quick-demo-map-app.md#get-the-primary-key-for-your-account) para obter a chave primária para sua conta. A opção **Language** especifica o idioma a ser usado para os rótulos e controles de mapa. Para obter mais informações sobre os idiomas com suporte, consulte [idiomas com suporte](supported-languages.md). Se estiver usando uma chave de assinatura para autenticação.

    ```HTML
    <script type="text/javascript">
        var map = new atlas.Map('myMap', {
            center: [-122.33, 47.6],
            zoom: 12,
            language: 'en-US',
            authOptions: {
                authType: 'subscriptionKey',
                subscriptionKey: '<Your Azure Maps Key>'
            }
        });
    </script>
    ```

    Se estiver usando Azure Active Directory (AAD) para autenticação:

    ```HTML
    <script type="text/javascript">
        var map = new atlas.Map('myMap', {
            center: [-122.33, 47.6],
            zoom: 12,
            language: 'en-US',
            authOptions: {
                authType: 'aad',
                clientId: '<Your AAD Client Id>',
                aadAppId: '<Your AAD App Id>',
                aadTenant: '<Your AAD Tenant Id>'
            }
        });
    </script>
    ```

    Uma lista de exemplos que mostram como integrar Azure Active Directory (AAD) com mapas do Azure pode ser encontrada [aqui](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples). 
    
    Para obter mais informações, consulte o documento [autenticação com o Azure Maps](azure-maps-authentication.md) e também os [exemplos de autenticação](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)do Azure AD do Azure Maps.

6. Opcionalmente, você pode achar interessante adicionar os seguintes elementos meta tag ao início da sua página:

    ```HTML
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge">

    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    ```

7. Colocar tudo em conjunto em seu arquivo HTML deve ser semelhante ao seguinte código:

    ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title></title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

        <style>
            html, body {
                margin: 0;
            }

            #myMap {
                height: 100vh;
                width: 100vw;
            }
        </style>
    </head>
    <body>
        <div id="myMap"></div>

        <script type="text/javascript">
            //Create an instance of the map control and set some options.
            var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });
        </script>
    </body>
    </html>
    ```

8. Abra o arquivo no navegador da Web e exiba o mapa renderizado. Ele deve ser semelhante ao seguinte código:

    <iframe height="700" style="width: 100%;" scrolling="no" title="Como usar o controle de mapa" src="//codepen.io/azuremaps/embed/yZpEYL/?height=557&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">Consulte a caneta <a href='https://codepen.io/azuremaps/pen/yZpEYL/'>como usar o controle de mapa</a> pelo mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
    </iframe>

## <a name="localizing-the-map"></a>Localizando o mapa

O mapas do Azure fornece duas maneiras diferentes de definir o idioma e a exibição regional do mapa. A primeira opção é adicionar essas informações ao namespace global `atlas`, o que resultará em todas as instâncias de controle de mapa em seu aplicativo padronizando essas configurações. O seguinte define o idioma como francês ("fr-FR") e a exibição regional como "auto":

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('Auto');
```

A segunda opção é passar essas informações para as opções de mapa ao carregar o mapa, como:

```javascript
map = new atlas.Map('myMap', {
    language: 'fr-FR',
    view: 'Auto',

    authOptions: {
        authType: 'aad',
        clientId: '<Your AAD Client Id>',
        aadAppId: '<Your AAD App Id>',
        aadTenant: '<Your AAD Tenant Id>'
    }
});
```

> [!Note]
> Com o SDK da Web, é possível carregar várias instâncias de mapa na mesma página com diferentes configurações de idioma e região. Além disso, essas configurações podem ser atualizadas depois que o mapa é carregado usando a função `setStyle` do mapa. 

Aqui está um exemplo de mapas do Azure com o idioma definido como "fr-FR" e a exibição regional definida como "auto".

![Imagem do mapa mostrando rótulos em francês](./media/how-to-use-map-control/websdk-localization.png)

Uma lista completa de idiomas com suporte e exibições regionais está documentada [aqui](supported-languages.md).

## <a name="azure-government-cloud-support"></a>Suporte de nuvem do Azure governamental

O SDK da Web do Azure Maps dá suporte à nuvem do Azure governamental. Todas as URLs de JavaScript e CSS usadas para acessar o SDK da Web do Azure Maps permanecem as mesmas, no entanto, as tarefas a seguir precisarão ser feitas para se conectar à versão de nuvem do Azure governamental da plataforma Azure Maps.

Ao usar o controle de mapa interativo, adicione a seguinte linha de código antes de criar uma instância da classe `Map`. 

```javascript
atlas.setDomain('atlas.azure.us');
```

Certifique-se de usar os detalhes de autenticação do Azure Maps da plataforma de nuvem do Azure governamental ao autenticar o mapa e os serviços.

Ao usar o módulo de serviços, o domínio para os serviços precisa ser definido ao criar uma instância de um ponto de extremidade de URL de API. Por exemplo, o código a seguir cria uma instância da classe `SearchURL` e aponta o domínio para a nuvem do Azure governamental.

```javascript
var searchURL = new atlas.service.SearchURL(pipeline, 'atlas.azure.us');
```

Se acessar diretamente os serviços REST do Azure Maps, altere o domínio da URL para `atlas.azure.us`. Por exemplo, se estiver usando o serviço de API de pesquisa, altere o domínio da URL de `https://atlas.microsoft.com/search/` para `https://atlas.azure.us/search/`.

## <a name="next-steps"></a>Passos seguintes

Saiba como criar e interagir com um mapa:

> [!div class="nextstepaction"]
> [Criar um mapa](map-create.md)

Saiba como estilizar um mapa:

> [!div class="nextstepaction"]
> [Escolher um estilo de mapa](choose-map-style.md)

Para adicionar mais dados ao seu mapa:

> [!div class="nextstepaction"]
> [Criar um mapa](map-create.md)

> [!div class="nextstepaction"]
> [Exemplos de código](https://docs.microsoft.com/samples/browse/?products=azure-maps)

Para obter uma lista de exemplos que mostram como integrar o Azure Active Directory (AAD) com o Azure Maps, consulte:

> [!div class="nextstepaction"]
> [Exemplos de autenticação do Azure AD](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)