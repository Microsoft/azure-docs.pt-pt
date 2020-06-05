---
title: Utilize o módulo Azure Maps Indoor Maps
description: Aprenda a utilizar o módulo Microsoft Azure Maps Indoor Maps para renderizar mapas incorporando as bibliotecas JavaScript do módulo.
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: c0b043bdb20cad508950a11853403958340acadf
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2020
ms.locfileid: "84434210"
---
# <a name="use-the-azure-maps-indoor-maps-module"></a>Utilize o módulo Azure Maps Indoor Maps

O Azure Maps Web SDK inclui o módulo *Interior Azure Maps.* O módulo *Azure Maps Indoor* permite-lhe renderizar mapas interiores criados no Azure Maps Creator.

## <a name="prerequisites"></a>Pré-requisitos

1. [Faça uma conta Azure Maps](quick-demo-map-app.md#create-an-account-with-azure-maps)
2. [Criar um recurso Criador](how-to-manage-creator.md)
3. [Obtenha uma chave de subscrição primária,](quick-demo-map-app.md#get-the-primary-key-for-your-account)também conhecida como a chave primária ou a chave de subscrição.
4. Obtenha um `tilesetId` e um `statesetId` completando o tutorial para a [criação de mapas interiores.](tutorial-creator-indoor-maps.md)
 Você precisará usar estes identificadores para renderizar mapas interiores com o módulo Azure Maps Indoor Maps.

## <a name="embed-the-indoor-maps-module"></a>Incorporar o módulo De Mapas Interiores

Pode instalar e incorporar o módulo *Azure Maps Indoor* de duas maneiras.

Para utilizar a versão global da Rede de Entrega de Conteúdos Azure do módulo *Azure Maps Indoor,* consulte as seguintes referências JavaScript e Style Sheet no `<head>` elemento do ficheiro HTML:

```html
<script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
<script src="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.js"></script>
<link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
<link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.css" type="text/css"/>
```

 Ou, você pode baixar o módulo *Azure Maps Indoor.* O módulo *Azure Maps Indoor* contém uma biblioteca de clientes para aceder aos serviços do Azure Maps. Siga os passos abaixo para instalar e carregar o módulo *Interior* na sua aplicação web.  
  
  1. Descarregue o [pacote azure-maps-indoor](https://www.npmjs.com/package/azure-maps-indoor).
  
  2. Instale o pacote NPM. Certifique-se de que utiliza privilégios de administrador na consola:

      ```powershell
      >npm install azure-maps-control
      >npm install azure-maps-indoor
      ```

  3. Faça referência ao módulo *Interior Azure Maps* JavaScript e Style Sheet no `<head>` elemento do ficheiro HTML:

      ```html
      <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
      <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.css" type="text/css"/>
      ```

## <a name="instantiate-the-map-object"></a>Instantaneamente o objeto do mapa

Primeiro, criar um *objeto mapa.* O *objeto Mapa* será usado no próximo passo para instantaneaizar o objeto do Gerente *Interior.*  O código abaixo mostra-lhe como instantaneaizar o *objeto do mapa:*

```javascript
const subscriptionKey = "<Your Azure Maps Primary Subscription Key>";

const map = new atlas.Map("map-id", {
  //use your facility's location
  center: [-122.13315, 47.63637],
  //or, you can use bounds: [# west, # south, # east, # north] and replace # with your map's bounds
  style: "blank",
  view: 'Auto',
  authOptions: { 
      authType: 'subscriptionKey',
      subscriptionKey: subscriptionKey
  },
  zoom: 19,
});
```

## <a name="instantiate-the-indoor-manager"></a>Instantaneamente o Gerente Interior

Para carregar os azulejos interiores e o estilo de mapa dos azulejos, deve instantaneamente o *Gerente Interior*. Instantânear o *Gestor Interior* fornecendo o objeto *mapa* e o correspondente `tilesetId` . Se deseja apoiar o [estilo dinâmico do mapa,](indoor-map-dynamic-styling.md)tem de passar o `statesetId` . O `statesetId` nome variável é sensível a casos. O seu código deve gostar do JavaScript abaixo.

```javascript
const tilesetId = "";
const statesetId = "";

const indoorManager = new atlas.indoor.IndoorManager(map, {
    tilesetId: "<tilesetId>",
    statesetId: "<statesetId>" // Optional
});
```

Para ativar as sondagens dos dados estatais que fornece, deve fornecer `statesetId` o e ligar `indoorManager.setDynamicStyling(true)` . Os dados do estado das sondagens permitem-lhe atualizar dinamicamente o estado das propriedades dinâmicas ou *estados.* Por exemplo, uma característica como o quarto pode ter uma propriedade dinâmica *(estado)* chamada `occupancy` . A sua aplicação pode querer sondar quaisquer alterações *de estado* para refletir a mudança dentro do mapa visual. O código abaixo mostra-lhe como permitir a votação do estado:

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

## <a name="indoor-level-picker-control"></a>Controlo de picker de nível interno

 O controlo *indoor Level Picker* permite-lhe alterar o nível do mapa renderizado. Pode inicializar opcionalmente o controlo *do selecionador de nível interior* através do Gestor *Interior*. Aqui está o código para inicializar o selecionador de controlo de nível:

```javascript
const levelControl = new atlas.control.LevelControl({ position: "top-right" });
indoorManager.setOptions({ levelControl });
```

## <a name="indoor-events"></a>Eventos Indoor

 O módulo *Azure Maps Indoor* suporta eventos *de objetos de mapa.* Os ouvintes do evento *do objeto mapa* são invocados quando um nível ou instalação mudou. Se pretender executar o código quando um nível ou instalação tiver mudado, coloque o seu código dentro do ouvinte do evento. O código abaixo mostra como os ouvintes do evento podem ser adicionados ao *objeto Mapa*.

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

A `eventData` variável contém informações sobre o nível ou facilidade que invocou o `levelchanged` ou `facilitychanged` evento, respectivamente. Quando um nível muda, o `eventData` objeto conterá o `facilityId` , o novo , e `levelNumber` outros metadados. Quando uma instalação muda, o `eventData` objeto conterá o novo `facilityId` , o novo , e `levelNumber` outros metadados.

## <a name="example-use-the-indoor-maps-module"></a>Exemplo: Utilize o módulo de mapas interiores

Este exemplo mostra-lhe como utilizar o módulo *Azure Maps Indoor* na sua aplicação web. Embora o exemplo seja limitado no âmbito, abrange o básico do que precisa para começar a usar o módulo *Azure Maps Indoor.* O código HTML completo está abaixo destes passos.

1. Utilize a [opção](#embed-the-indoor-maps-module) Azure Content Delivery Network para instalar o módulo *Interior Azure Maps.*

2. Criar um novo ficheiro HTML

3. No cabeçalho HTML, refira-se aos estilos de placa de javaScript do módulo *Azure Maps e* de chapa de estilo.

4. Inicialize um *objeto de mapa.* O *objeto Mapa* suporta as seguintes opções:
    - `Subscription key`é a sua chave de subscrição primária do Azure Maps.
    - `center`define uma latitude e longitude para a sua localização do centro do mapa interior. Forneça um valor para `center` se não quiser fornecer um valor para `bounds` . O formato deve aparecer como `center` : [-122.13315, 47.63637].
    - `bounds`é a menor forma retangular que encerra os dados do mapa de azulejos. Desa estada um valor para `bounds` se não quiser definir um valor para `center` . Pode encontrar os limites do seu mapa ligando para a [API da Lista tileset.](https://docs.microsoft.com/rest/api/maps/tileset/listpreview) A Lista de Tileset API devolve o `bbox` , que você pode analisar e atribuir a `bounds` . O formato deve aparecer como `bounds` : [# oeste, # sul, # leste, # norte].
    - `style`permite definir a cor do fundo. Para exibir um fundo branco, defina `style` como "em branco".
    - `zoom`permite especificar os níveis de zoom min e max para o seu mapa.

5. Em seguida, crie o módulo *Gerente Interior.* Atribua o *Azure Maps Indoor,* `tilesetId` e opcionalmente adicione o `statesetId` .

6. Instantaneamente o controlo *do selecionador de nível interior.*

7. Adicione os ouvintes do evento *do objeto do mapa.*  

O seu ficheiro deve agora ser semelhante ao HTML abaixo.

  ```html
  <!DOCTYPE html>
  <html lang="en">
    <head>
      <meta charset="utf-8" />
      <meta name="viewport" content="width=device-width, user-scalable=no" />
      <title>Indoor Maps App</title>
      
      <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
      <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.css" type="text/css"/>

      <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
      <script src="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.js"></script>
        
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
        const subscriptionKey = "<Your Azure Maps Primary Subscription Key>";
        const tilesetId = "<your tilesetId>";
        const statesetId = "<your statesetId>";

        const map = new atlas.Map("map-id", {
          //use your facility's location
          center: [-122.13315, 47.63637],
          //or, you can use bounds: [# west, # south, # east, # north] and replace # with your Map bounds
          style: "blank",
          view: 'Auto',
          authOptions: { 
              authType: 'subscriptionKey',
              subscriptionKey: subscriptionKey
          },
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

Para ver o seu mapa interior, carregue-o num navegador web. Deve parecer a imagem abaixo. Se clicar na função de escada, o *selecionador de nível* aparecerá no canto superior direito.

  ![imagem de mapa interior](media/how-to-use-indoor-module/indoor-map-graphic.png)

## <a name="next-steps"></a>Passos seguintes

Leia sobre as APIs que estão relacionadas com o módulo *Interior Azure Maps:*

> [!div class="nextstepaction"]
> [Requisitos de pacote do desenho](drawing-requirements.md)

>[!div class="nextstepaction"]
> [Criador para mapas interiores](creator-indoor-maps.md)

Saiba mais sobre como adicionar mais dados ao seu mapa:

> [!div class="nextstepaction"]
> [Estilo dinâmico de Mapas Interiores](indoor-map-dynamic-styling.md)

> [!div class="nextstepaction"]
> [Amostras de código](https://docs.microsoft.com/samples/browse/?products=azure-maps)