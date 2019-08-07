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
ms.openlocfilehash: 5fdbd8092abcc51fc03e8b00106b7e25ec4be905
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68839398"
---
# <a name="use-the-azure-maps-map-control"></a>Usar o controle de mapeamento do Azure Maps

O Controle de Mapeamento biblioteca JavaScript do lado do cliente permite renderizar mapas e a funcionalidade do Azure Maps incorporada em seu aplicativo Web ou móvel.

## <a name="create-a-new-map-in-a-web-page"></a>Criar um novo mapa em uma página da Web

Você pode inserir um mapa em uma página da Web usando o Controle de Mapeamento biblioteca JavaScript do lado do cliente.

1. Crie um novo arquivo HTML.

2. Carregue no SDK da Web do Azure Maps. Isso pode ser feito usando uma das duas opções;

    a. Use a versão CDN hospedada globalmente do SDK da Web do Azure Maps adicionando os pontos de extremidade de URL à folha de estilo e às `<head>` referências de script no elemento do arquivo:

    ```HTML
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
    ```

    b. Como alternativa, carregue o código-fonte do SDK da Web do Azure Maps localmente usando o pacote [Azure-Maps-Control](https://www.npmjs.com/package/azure-maps-control) NPM e hospede-o com seu aplicativo. Esse pacote também inclui definições de TypeScript.

    > NPM instalar Azure-Maps-Control

    Em seguida, adicione referências à folha de estilos do Azure Maps e às `<head>` referências de origem do script ao elemento do arquivo:

    ```HTML
    <link rel="stylesheet" href="node_modules/azure-maps-control/dist/css/atlas.min.css" type="text/css">
    <script src="node_modules/azure-maps-control/dist/js/atlas.min.js"></script>
    ```

3. Para renderizar o mapa para que ele preencha o corpo completo da página, adicione o elemento `<style>` a seguir `<head>` ao elemento.

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

4. No corpo da página, adicione um `<div>` elemento e dê a ele um `id` de **myMap**.

    ```HTML
    <body>
        <div id="myMap"></div>
    </body>
    ```

5. Para inicializar o controle de mapa, defina uma nova seção no corpo do HTML e crie um script. `id` Passe o `Map` do mapa `<div>` ou um `HTMLElement` (por exemplo, `document.getElementById('myMap')`) como o primeiro parâmetro ao criar uma instância da classe. Use suas próprias credenciais de chave de conta do Azure Maps ou Azure Active Directory (AAD) para autenticar o mapa usando [as opções de autenticação](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.authenticationoptions). Se você precisar criar uma conta ou encontrar sua chave, consulte [como gerenciar sua conta e chaves do Azure Maps](how-to-manage-account-keys.md). A opção **Language** especifica o idioma a ser usado para os rótulos e controles de mapa. Para obter mais informações sobre os idiomas com suporte, consulte [idiomas com suporte](supported-languages.md). Se estiver usando uma chave de assinatura para autenticação.

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
                aadTenant: 'msft.ccsctp.net'
            }
        });
    </script>
    ```

    Para obter mais informações, consulte [autenticação com o Azure Maps](azure-maps-authentication.md) para obter mais detalhes.

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

    <iframe height="700" style="width: 100%;" scrolling="no" title="Como usar o controle de mapa" src="//codepen.io/azuremaps/embed/yZpEYL/?height=557&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">Consulte a caneta <a href='https://codepen.io/azuremaps/pen/yZpEYL/'>como usar o controle de mapa</a> pelo mapas do Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>() em <a href='https://codepen.io'>CodePen</a>.
    </iframe>

## <a name="localizing-the-map"></a>Localizando o mapa

O mapas do Azure fornece duas maneiras diferentes de definir o idioma e a exibição regional do mapa. A primeira opção é adicionar essas informações ao namespace global `atlas` , o que resultará em todas as instâncias de controle de mapa em seu aplicativo padronizando essas configurações. O seguinte define o idioma como francês ("fr-FR") e a exibição regional como "auto":

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('auto');
```

A segunda opção é passar essas informações para as opções de mapa ao carregar o mapa, como:

```javascript
map = new atlas.Map('myMap', {
    language: 'fr-FR',
    view: 'auto',

    authOptions: {
        authType: 'aad',
        clientId: '<Your AAD Client Id>',
        aadAppId: '<Your AAD App Id>',
        aadTenant: 'msft.ccsctp.net'
    }
});
```

> [!Note]
> Com o SDK da Web, é possível carregar várias instâncias de mapa na mesma página com diferentes configurações de idioma e região. Além disso, essas configurações podem ser atualizadas depois que o mapa é carregado `setStyle` usando a função do mapa. 

Aqui está um exemplo de mapas do Azure com o idioma definido como "fr-FR" e a exibição regional definida como "auto".

![Imagem do mapa mostrando rótulos em francês](./media/how-to-use-map-control/websdk-localization.png)

Uma lista completa de idiomas com suporte e exibições regionais está documentada [aqui](supported-languages.md).

## <a name="next-steps"></a>Passos Seguintes

Saiba como criar e interagir com um mapa:

> [!div class="nextstepaction"]
> [Criar um mapa](map-create.md)

Saiba como estilizar um mapa:

> [!div class="nextstepaction"]
> [Escolher um estilo de mapa](choose-map-style.md)
