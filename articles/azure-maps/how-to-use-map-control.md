---
title: Como usar o controle de mapa do Azure Maps | Documentos da Microsoft
description: Saiba como utilizar a biblioteca de Javascript do lado do cliente de controlo de mapas do Azure Maps.
author: dsk-2015
ms.author: dkshir
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 57850f67b56113036cb6cc37e9f1f2694ba9eb8f
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56672734"
---
# <a name="use-the-azure-maps-map-control"></a>Utilizar o controlo de mapas do Azure Maps

A biblioteca de Javascript do lado do cliente de controlo de mapas permite-lhe compor mapas e funcionalidade do Azure Maps embedded nas suas aplicações móveis ou web.

## <a name="create-a-new-map-in-a-web-page"></a>Criar um novo mapa numa página da web

Para incorporar um mapa numa página da web, usando a biblioteca de Javascript do lado do cliente de controlo do mapa.

1. Crie um novo ficheiro HTML.

2. Carregar na Web do Azure Maps SDK. Isso pode ser feito usando uma das duas opções;
    
    a. Utilizar a versão CDN globalmente alojada do Azure Maps Web SDK ao adicionar os pontos finais de URL para as referências de folha de estilo e script no `<head>` elemento do arquivo:

    ```html
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=2" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=2"></script>
    ```

    b. Em alternativa, carregar o código de origem do SDK de Web de mapas do Azure localmente, utilizando o [controlo de mapas do azure](https://www.npmjs.com/package/azure-maps-control) NPM empacotar e hospedá-lo com a sua aplicação. Este pacote também inclui definições de TypeScript.

    > npm install azure-maps-control

    Em seguida, adicione referências para as referências de origem de Azure Maps folha de estilo e script, para o `<head>` elemento do arquivo:

    ```html
    <link rel="stylesheet" href="node_modules/azure-maps-control/dist/css/atlas.min.css" type="text/css" />
    <script src="node_modules/azure-maps-control/dist/js/atlas.min.js"></script>
    ```

3. Para compor o mapa, de modo que ela preenche o corpo completo da página, adicione as seguintes `<style>` elemento para a `<head>` elemento.

    ```html
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

4. No corpo da página, adicione uma `<div>` elemento e conceda-lhe uma `id` dos **myMap**. 

    ```html
    <body>
        <div id="myMap"></div>
    </body>
    ```

5. Para inicializar o controlo do mapa, definir uma nova seção no corpo do html e criar um script. Utilize a sua própria chave de conta do Azure Maps ou as credenciais do Azure Active Directory (AAD) para autenticar com o mapa [opções de autenticação](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.authenticationoptions). Se precisar de criar uma conta ou encontrar a sua chave, consulte [como gerir a sua conta do Azure Maps e as chaves](how-to-manage-account-keys.md). O **linguagem** opção especifica o idioma a ser utilizada para etiquetas de mapa e controlos. Para obter mais informações sobre idiomas suportados, consulte [idiomas suportados](supported-languages.md). Se utilizar uma chave de subscrição para a autenticação.

    ```html
    <script type='text/javascript'>
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

    Se utilizar o Azure Active Directory (AAD) para autenticação:

    ```html
    <script type='text/javascript'>
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

    Ver [autenticação com o Azure Maps](azure-maps-authentication.md) para obter mais detalhes.
6. Opcionalmente, poderá considerar adicionar os seguintes elementos de marca meta para o cabeçalho da página úteis:

    ```html
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    
    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />
    ```

7. Juntando as peças seu arquivo HTML deve ter um aspeto semelhante ao seguinte:

    ```html
    <!DOCTYPE html>
    <html>
    <head>
        <title></title>
    
        <meta charset="utf-8" />
        
        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    
        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />
    
        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=2" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=2"></script>
    
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
        
        <script type='text/javascript'>
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

8. Abra o ficheiro no seu navegador da web e ver o mapa processado. Ele deve ter um aspeto semelhante ao seguinte:

    <iframe height="700" style="width: 100%;" scrolling="no" title="Como usar o controle de mapa" src="//codepen.io/azuremaps/embed/yZpEYL/?height=557&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a caneta <a href='https://codepen.io/azuremaps/pen/yZpEYL/'>como usar o controle de mapa</a> ao Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
    </iframe>

## <a name="next-steps"></a>Passos Seguintes

Saiba como criar e interagir com um mapa:

> [!div class="nextstepaction"]
> [Criar um mapa](map-create.md)

Saiba como um mapa de estilo:

> [!div class="nextstepaction"]
> [Escolha um estilo de mapa](choose-map-style.md)
