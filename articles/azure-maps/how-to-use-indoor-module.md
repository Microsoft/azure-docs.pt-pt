---
title: Utilize o módulo Mapas Indoor Do Mapa Azure
description: Saiba como utilizar o módulo Microsoft Azure Maps Indoor Maps para renderizar mapas incorporando as bibliotecas JavaScript do módulo.
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 9b2a47cde4d79671aada7c280c2bffd9bb8fe759
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83596975"
---
# <a name="use-the-azure-maps-indoor-maps-module"></a>Utilize o módulo Mapas Indoor Do Mapa Azure

O Azure Maps Web SDK inclui o módulo *Azure Maps Indoor.* O módulo *Azure Maps Indoor* permite-lhe renderizar mapas interiores criados no Criador do Azure Maps.

## <a name="prerequisites"></a>Pré-requisitos

1. [Faça uma conta Azure Maps](quick-demo-map-app.md#create-an-account-with-azure-maps)
2. [Criar um recurso Criador](how-to-manage-creator.md)
3. [Obtenha uma chave de subscrição primária,](quick-demo-map-app.md#get-the-primary-key-for-your-account)também conhecida como a chave principal ou a chave de subscrição.
4. Obtenha um `tilesetId` e a `statesetId` completando o tutorial para a [criação de mapas interiores.](tutorial-creator-indoor-maps.md)
 Você precisará usar estes identificadores para renderizar mapas interiores com o módulo Azure Maps Indoor Maps.

## <a name="embed-the-indoor-maps-module"></a>Incorporar o módulo Indoor Maps

Pode instalar e incorporar o módulo *Azure Maps Indoor* de uma de duas maneiras.

Para utilizar a versão da Rede de Entrega de Conteúdos Azure, globalmente hospedada, do módulo *Azure Maps Indoor,* faça referências ao JavaScript e à Folha de Estilo no `<head>` elemento do ficheiro HTML:

  ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
    <script src="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.js"></script>
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.css" type="text/css"/>
  ```

 Ou, pode descarregar o módulo *Azure Maps Indoor.* O módulo *Azure Maps Indoor* contém uma biblioteca de clientes para aceder aos serviços do Azure Maps. Siga os passos abaixo para instalar e carregar o módulo *Interior* na sua aplicação web.  
  
  1. Descarregue o [pacote azul-maps-indoor](https://www.npmjs.com/package/azure-maps-indoor).
  
  2. Instale o pacote NPM. Certifique-se de que utiliza privilégios de administrador na consola:

      ```powershell
        >npm install azure-maps-control
        >npm install azure-maps-indoor
      ```

  3. Referência ao módulo *Interior Do Azure Maps* JavaScript e folha de estilo no elemento do ficheiro `<head>` HTML:

      ```html
      <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
      <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.css" type="text/css"/>
      ```

## <a name="instantiate-the-map-object"></a>Instantiar o objeto do mapa

Primeiro, crie um *objeto de mapa.* O *objeto Map* será utilizado no próximo passo para instantaneamente o objeto Indoor *Manager.*  O código abaixo mostra-lhe como instantaneaizar o *objeto do Mapa:*

```javascript
  const subscriptionKey = "<your Azure Maps Primary Subscription Key>";

  const map = new atlas.Map("map-id", {
    //use your facility's location
    center: [-122.13315, 47.63637],
    //or, you can use bounds: [#,#,#,#] and replace # with your map's bounds
    style: "blank",
    subscriptionKey,
    zoom: 19,
  });
```

## <a name="instantiate-the-indoor-manager"></a>Instantiate o Gerente Indoor

Para carregar os azulejos interiores e o estilo do mapa dos azulejos, deve instantaneamente o *Gerente Interior*. Instantie o *Gerente Interior* fornecendo o *objeto mapa* e o correspondente `tilesetId` . Se desejar suportar o estilo dinâmico do [mapa,](indoor-map-dynamic-styling.md)tem de passar pelo `statesetId` . O `statesetId` nome variável é sensível ao caso. O seu código deve gostar do JavaScript abaixo.

```javascript
const tilesetId = "";
const statesetId = "";

const indoorManager = new atlas.indoor.IndoorManager(map, {
    tilesetId: "<tilesetId>",
    statesetId: "<statesetId>" // Optional
});
```

Para permitir a sondagem dos dados do Estado que fornece, deve fornecer o `statesetId` e chamar `indoorManager.setDynamicStyling(true)` . Os dados do estado de sondagem permitem atualizar dinamicamente o estado das propriedades ou *estados dinâmicos.* Por exemplo, uma característica como o quarto pode ter uma propriedade dinâmica *(estado*) chamada `occupancy` . A sua aplicação pode desejar fazer sondagens para quaisquer alterações de *Estado* que reflitam a mudança dentro do mapa visual. O código abaixo mostra-lhe como permitir as sondagens estatais:

```javascript

const tilesetId = "";
const statesetId = "";

const indoorManager = new atlas.indoor.IndoorManager(map, {
    tilesetId: "<tilesetId>",
    statesetId: "<statesetId>" // Optional
});

if (statesetId.length > 0) {
    indoorManager.setDynamicStyling(true);
}

```

## <a name="indoor-level-picker-control"></a>Controlo de picker de nível interior

 O comando *do Picker de Nível Interior* permite alterar o nível do mapa renderizado. Pode inicializar opcionalmente o controlo do Picker de *Nível Interior* através do *Indoor Manager*. Aqui está o código para inicializar o apanhador de controlo de nível:

```javascript
const levelControl = new atlas.control.LevelControl({ position: "top-right" });
indoorManager.setOptions({ levelControl });
```

## <a name="indoor-events"></a>Eventos Indoor

 O módulo *Azure Maps Indoor* suporta eventos de *objetos* mapeados. Os ouvintes do evento *do objeto do Mapa* são invocados quando um nível ou instalação mudou. Se quiser executar código quando um nível ou uma instalação tiverem mudado, coloque o seu código dentro do ouvinte do evento. O código abaixo mostra como os ouvintes do evento podem ser adicionados ao *objeto do Mapa*.

```javascript
map.events.add("levelchanged", indoorManager, (eventData) => {

    //code that you want to run after a level has been changed
    console.log("The level has changed: ", eventData);

});
map.events.add("facilitychanged", indoorManager, (eventData) => {

    //code that you want to run after a facility has been changed
    console.log("The facility has changed: ", eventData);
});
```

A `eventData` variável contém informações sobre o nível ou instalação que invocou o `levelchanged` ou `facilitychanged` evento, respectivamente. Quando um nível muda, o `eventData` objeto conterá os `facilityId` , os novos e outros `levelNumber` metadados. Quando uma instalação muda, o `eventData` objeto conterá os novos `facilityId` , os novos , e outros `levelNumber` metadados.

## <a name="example-use-the-indoor-maps-module"></a>Exemplo: Utilize o Módulo de Mapas Interiores

Este exemplo mostra-lhe como utilizar o módulo *Azure Maps Indoor* na sua aplicação web. Embora o exemplo seja limitado no âmbito, cobre o básico do que precisa para começar a usar o módulo *Azure Maps Indoor.* O código HTML completo está abaixo destes passos.

1. Utilize a [opção](#embed-the-indoor-maps-module) Rede de Entrega de Conteúdo Sintetizador para instalar o módulo *Azure Maps Indoor.*

2. Criar um novo ficheiro HTML

3. No cabeçalho HTML, refira-se ao módulo *Indoor Do Azure Maps* JavaScript e estilos de folha de estilo.

4. Inicialize um *objeto de mapa.* O *objeto do Mapa* suporta as seguintes opções:
    - `Subscription key`é a sua chave de subscrição primária do Azure Maps.
    - `center`define uma latitude e longitude para a sua localização no centro do mapa interior. Forneça um valor para `center` se não quiser fornecer um valor para `bounds` . O formato deve aparecer `center` como: [-122.13315, 47.63637].
    - `bounds`é a menor forma retangular que encerra os dados do mapa de azulejos. Detete um valor para `bounds` se não quiser definir um valor para `center` . Você pode encontrar os seus limites de mapa chamando a [Lista tileset API](https://docs.microsoft.com/rest/api/maps/tileset/listpreview). A API da Lista tileset devolve a `bbox` , a que pode analisar e atribuir a `bounds` . O formato deve aparecer `bounds` como: [#####].
    - `style`permite definir a cor do fundo. Para mostrar um fundo branco, defina `style` como "em branco".
    - `zoom`permite especificar os níveis de zoom min e max para o seu mapa.

5. Em seguida, crie o módulo *Indoor Manager.* Atribuir os *Mapas Azure Indoor,* `tilesetId` e opcionalmente adicionar o `statesetId` .

6. Instantie o controlo *do picker de nível interior.*

7. Adicione os ouvintes do evento do *objeto do mapa.*  

O seu ficheiro deve agora parecer semelhante ao HTML abaixo.

  ```html
  <!DOCTYPE html>
  <html lang="en">
    <head>
      <meta charset="utf-8" />
      <meta name="viewport" content="width=device-width, user-scalable=no" />
      <title>Indoor Maps App</title>
       <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
        <script src="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.js"></script>
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.css" type="text/css"/>
      <style>
        html,
        body {
          width: 100%;
          height: 100%;
          padding: 0;
          margin: 0;
        }

        #map-id {
          width: 100%;
          height: 100%;
        }
      </style>
    </head>

    <body>
      <div id="map-id"></div>
      <script>
        const subscriptionKey = "<your Azure Maps Primary Subscription Key>";
        const tilesetId = "<your tilesetId>";
        const statesetId = "<your statesetId>";

        const map = new atlas.Map("map-id", {
          //use your facility's location
          center: [-122.13315, 47.63637],
          //or, you can use bounds: [ # , # , # , # ] and replace # with your Map bounds
          style: "blank",
          subscriptionKey,
          zoom: 19,
        });

        const levelControl = new atlas.control.LevelControl({
          position: "top-right",
        });

        const indoorManager = new atlas.indoor.IndoorManager(map, {
          levelControl, //level picker
          tilesetId,
          statesetId, //optional
        });

        if (statesetId.length > 0) {
          indoorManager.setDynamicStyling(true);
        }

        map.events.add("levelchanged", indoorManager, (eventData) => {
          //put code that runs after a level has been changed
          console.log("The level has changed:", eventData);
        });

        map.events.add("facilitychanged", indoorManager, (eventData) => {
          //put code that runs after a facility has been changed
          console.log("The facility has changed:", eventData);
        });
      </script>
    </body>
  </html>
  ```

Para ver o seu mapa interior, carregue-o num navegador web. Deve parecer a imagem abaixo. Se clicar na função das escadas, o apanhador de *nível* aparecerá no canto superior direito.

  ![imagem do mapa interior](media/how-to-use-indoor-module/indoor-map-graphic.png)

## <a name="next-steps"></a>Passos seguintes

Leia sobre as APIs que estão relacionadas com o módulo *Azure Maps Indoor:*

> [!div class="nextstepaction"]
> [Requisitos de pacote de desenho](drawing-requirements.md)

>[!div class="nextstepaction"]
> [Criador de mapas interiores](creator-indoor-maps.md)

Saiba mais sobre como adicionar mais dados ao seu mapa:

> [!div class="nextstepaction"]
> [Estilo dinâmico do Mapa Interior](indoor-map-dynamic-styling.md)

> [!div class="nextstepaction"]
> [Amostras de código](https://docs.microsoft.com/samples/browse/?products=azure-maps)