---
title: Adicione um pop-up a um ponto num mapa | Microsoft Azure Maps
description: Saiba mais sobre popups, modelos popup e eventos pop-up no Azure Maps. Veja como adicionar um popup a um ponto num mapa e como reutilizar e personalizar popups.
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 903574a8c8696f10d9903a685bab8d12b3e6395f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92891041"
---
# <a name="add-a-popup-to-the-map"></a>Adicione um pop-up ao mapa

Este artigo mostra-lhe como adicionar um pop-up a um ponto num mapa.

## <a name="understand-the-code"></a>Compreender o código

O código a seguir adiciona uma característica de ponto, que tem `name` e `description` propriedades, ao mapa usando uma camada de símbolo. Um exemplo da [classe Popup](/javascript/api/azure-maps-control/atlas.popup) é criado, mas não exibido. Os eventos do rato são adicionados à camada do símbolo para acionar a abertura e o fecho do pop-up. Quando o símbolo do marcador está sobressaía, a propriedade do popup `position` é atualizada com a posição do marcador, e a `content` opção é atualizada com alguns HTML que envolvem as  `name` propriedades e propriedades da `description` função ponto a ser pairada. O popup é então exibido no mapa utilizando a sua `open` função.

```javascript
//Define an HTML template for a custom popup content laypout.
var popupTemplate = '<div class="customInfobox"><div class="name">{name}</div>{description}</div>';

//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

dataSource.add(new atlas.data.Feature(new atlas.data.Point([-122.1333, 47.63]), {
  name: 'Microsoft Building 41', 
  description: '15571 NE 31st St, Redmond, WA 98052'
}));

//Create a layer to render point data.
var symbolLayer = new atlas.layer.SymbolLayer(dataSource);

//Add the polygon and line the symbol layer to the map.
map.layers.add(symbolLayer);

//Create a popup but leave it closed so we can update it and display it later.
popup = new atlas.Popup({
  pixelOffset: [0, -18],
  closeButton: false
});

//Add a hover event to the symbol layer.
map.events.add('mouseover', symbolLayer, function (e) {
  //Make sure that the point exists.
  if (e.shapes && e.shapes.length > 0) {
    var content, coordinate;
    var properties = e.shapes[0].getProperties();
    content = popupTemplate.replace(/{name}/g, properties.name).replace(/{description}/g, properties.description);
    coordinate = e.shapes[0].getCoordinates();

    popup.setOptions({
      //Update the content of the popup.
      content: content,

      //Update the popup's position with the symbol's coordinate.
      position: coordinate

    });
    //Open the popup.
    popup.open(map);
  }
});

map.events.add('mouseleave', symbolLayer, function (){
  popup.close();
});
```

Abaixo está a amostra completa do código de execução da funcionalidade acima.

<br/>

<iframe height='500' scrolling='no' title='Adicione um pop-up usando Azure Maps' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Ver a Pen <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>Adicionar um pop-up usando Azure Maps</a> by Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="reusing-a-popup-with-multiple-points"></a>Reutilizar um pop-up com vários pontos

Há casos em que a melhor abordagem é criar um pop-up e reutilizá-lo. Por exemplo, pode ter um grande número de pontos e quer mostrar apenas um pop-up de cada vez. Ao reutilizar o popup, o número de elementos DOM criados pela aplicação é muito reduzido, o que pode proporcionar um melhor desempenho. A amostra a seguir cria características de 3 pontos. Se clicar em qualquer um deles, será exibido um pop-up com o conteúdo para essa função de ponto.

<br/>

<iframe height='500' scrolling='no' title='Reutilizar Popup com múltiplos pinos' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte o <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>Popup de Reutilização de Canetas com Múltiplos Pinos</a> by Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customizing-a-popup"></a>Personalizar um popup

Por predefinição, o popup tem um fundo branco, uma seta de ponteiro na parte inferior e um botão de fecho no canto superior direito. A amostra a seguir altera a cor de fundo para preto utilizando a `fillColor` opção do pop-up. O botão de fecho é removido definindo a `CloseButton` opção para falso. O conteúdo HTML do popup utiliza acolchoado de 10 pixels das bordas do popup. O texto é feito de branco, por isso aparece bem no fundo preto.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Popup personalizado" src="//codepen.io/azuremaps/embed/ymKgdg/?height=500&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Consulte o <a href='https://codepen.io/azuremaps/pen/ymKgdg/'>Popup Personalizado</a> pen by Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-popup-templates-to-the-map"></a>Adicione modelos pop-up ao mapa

Os modelos popup facilitam a criação de layouts orientados por dados para popups. As secções abaixo demonstram o uso de vários modelos popup para gerar conteúdo formatado usando propriedades de funcionalidades.

> [!NOTE]
> Por predefinição, todos os conteúdos renderizados utilizam o modelo popup será sandboxed dentro de um iframe como uma funcionalidade de segurança. No entanto, existem limitações:
>
> - Todos os scripts, formulários, bloqueio de ponteiro e funcionalidade de navegação superior estão desativadas. As ligações podem abrir-se num novo separador quando clicados. 
> - Os navegadores mais antigos que não suportam o `srcdoc` parâmetro em iframes serão limitados a renderizar uma pequena quantidade de conteúdo.
> 
> Se confia nos dados que estão a ser carregados nos popups e potencialmente pretende que estes scripts carregados em popups possam aceder à sua aplicação, pode desativá-lo definindo a opção de modelos pop-up `sandboxContent` para falso. 

### <a name="string-template"></a>Modelo de corda

O modelo de corda substitui os espaços reservados por valores das propriedades da funcionalidade. As propriedades da funcionalidade não têm de ser atribuídas a um valor do tipo String. Por exemplo, `value1` detém um inteiro. Estes valores são então passados para a propriedade de conteúdo do `popupTemplate` . 

A `numberFormat` opção especifica o formato do número a exibir. Se o `numberFormat` não for especificado, o código utilizará o formato de data dos modelos pop-up. Os `numberFormat` números de formatos de opção utilizando a função [Number.toLocaleString.](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) Para formatar grandes números, considere utilizar a `numberFormat` opção com funções a partir de [NumberFormat.formato](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/NumberFormat/format). Por exemplo, o corte de código abaixo usa `maximumFractionDigits` para limitar o número de dígitos de fração a dois.

> [!Note]
> Só há uma maneira de o modelo de corda renderizar imagens. Primeiro, o modelo de corda precisa de ter uma etiqueta de imagem. O valor que está a ser passado para a etiqueta de imagem deve ser um URL para uma imagem. Em seguida, o modelo de corda precisa de ter `isImage` definido para ser verdadeiro no `HyperLinkFormatOptions` . A `isImage` opção especifica que a hiperligação é para uma imagem, e a hiperligação será carregada numa etiqueta de imagem. Quando a hiperligação é clicada, a imagem abre-se.

```javascript
var templateOptions = {
  content: 'This template uses a string template with placeholders.<br/><br/> - Value 1 = {value1}<br/> - Value 2 = {value2/subValue}<br/> - Array value [2] = {arrayValue/2}',
  numberFormat: {
    maximumFractionDigits: 2
  }
};

var feature = new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 1 - String template',
    value1: 1.2345678,
    value2: {
        subValue: 'Pizza'
    },
    arrayValue: [3, 4, 5, 6]
});

var popup = new atlas.Popup({
  content: atlas.PopupTemplate.applyTemplate(feature.properties, templateOptions),
  position: feature.geometry.coordinates
});
```

### <a name="propertyinfo-template"></a>Modelo PropertyInfo

O modelo PropertyInfo exibe propriedades disponíveis da funcionalidade. A `label` opção especifica o texto a apresentar ao utilizador. Se `label` não for especificado, a hiperligação será exibida. E, se a hiperligação for uma imagem, o valor atribuído à etiqueta "alt" será exibido. O `dateFormat` especia o formato da data, e se o formato da data não for especificado, então a data renderá como uma cadeia. A `hyperlinkFormat` opção torna os links clicáveis, da mesma forma, a `email` opção pode ser usada para tornar os endereços de e-mail clicáveis.

Antes do modelo PropertyInfo exibir as propriedades ao utilizador final, verifica novamente se as propriedades estão realmente definidas para essa funcionalidade. Também ignora a exibição de propriedades de estilo e título. Por exemplo, não vai exibir `color` , `size` , e `anchor` `strokeOpacity` `visibility` . Assim, uma vez que a verificação do caminho da propriedade está completa no back-end, o modelo PropertyInfo mostra o conteúdo em um formato de tabela.

```javascript
var templateOptions = {
  content: [
    {
        propertyPath: 'createDate',
        label: 'Created Date'
    },
    {
        propertyPath: 'dateNumber',
        label: 'Formatted date from number',
        dateFormat: {
          weekday: 'long',
          year: 'numeric',
          month: 'long',
          day: 'numeric',
          timeZone: 'UTC',
          timeZoneName: 'short'
        }
    },
    {
        propertyPath: 'url',
        label: 'Code samples',
        hideLabel: true,
        hyperlinkFormat: {
          lable: 'Go to code samples!',
          target: '_blank'
        }
    },
    {
        propertyPath: 'email',
        label: 'Email us',
        hideLabel: true,
        hyperlinkFormat: {
          target: '_blank',
          scheme: 'mailto:'
        }
    }
  ]
};

var feature = new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 2 - PropertyInfo',
    createDate: new Date(),
    dateNumber: 1569880860542,
    url: 'https://aka.ms/AzureMapsSamples',
    email: 'info@microsoft.com'
}),

var popup = new atlas.Popup({
  content: atlas.PopupTemplate.applyTemplate(feature.properties, templateOptions),
  position: feature.geometry.coordinates
});
```

### <a name="multiple-content-templates"></a>Vários modelos de conteúdo

Uma funcionalidade também pode exibir conteúdo usando uma combinação do modelo string e do modelo PropertyInfo. Neste caso, o modelo de corda torna os espaços reservados num fundo branco.  E, o modelo PropertyInfo torna uma imagem de largura total dentro de uma mesa. As propriedades desta amostra são semelhantes às propriedades que explicamos nas amostras anteriores.

```javascript
var templateOptions = {
  content: [
    'This template has two pieces of content; a string template with placeholders and a array of property info which renders a full width image.<br/><br/> - Value 1 = {value1}<br/> - Value 2 = {value2/subValue}<br/> - Array value [2] = {arrayValue/2}',
    [{
      propertyPath: 'imageLink',
      label: 'Image',
      hideImageLabel: true,
      hyperlinkFormat: {
        isImage: true
      }
    }]
  ],
  numberFormat: {
    maximumFractionDigits: 2
  }
};

var feature = new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 3 - Multiple content template',
    value1: 1.2345678,
    value2: {
    subValue: 'Pizza'
    },
    arrayValue: [3, 4, 5, 6],
    imageLink: 'https://azuremapscodesamples.azurewebsites.net/common/images/Pike_Market.jpg'
});

var popup = new atlas.Popup({
  content: atlas.PopupTemplate.applyTemplate(feature.properties, templateOptions),
  position: feature.geometry.coordinates
});
```

### <a name="points-without-a-defined-template"></a>Pontos sem um modelo definido

Quando o modelo Popup não é definido como um modelo de string, um modelo PropertyInfo, ou uma combinação de ambos, então ele usa as definições padrão. Quando as `title` propriedades e são as `description` únicas propriedades atribuídas, o modelo popup mostra um fundo branco, um botão de fecho no canto superior direito. E, em ecrãs pequenos e médios, mostra uma flecha na parte inferior. As definições predefinidos mostram dentro de uma tabela para todas as propriedades que não a `title` . e a `description` . Mesmo quando recua para as definições predefinidas, o modelo popup ainda pode ser manipulado programáticamente. Por exemplo, os utilizadores podem desativar a deteção de hiperligação e as definições predefinidas ainda se aplicam a outras propriedades.

Clique nos pontos no mapa no CodePen. Há um ponto no mapa para cada um dos seguintes modelos popup: modelo de corda, modelo PropertyInfo e modelo de conteúdo múltiplo. Existem também três pontos para mostrar como os modelos tornam os modelos usando as definições de predefinição.

<br/>

<iframe height='500' scrolling='no' title='PopupTemplates' src='//codepen.io/azuremaps/embed/dyovrzL/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte os <a href='https://codepen.io/azuremaps/pen/dyovrzL/'>PopupTemplates</a> pen by Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="reuse-popup-template"></a>Modelo popup de reutilização

Semelhante ao popup reutilizado, você pode reutilizar modelos popup. Esta abordagem é útil quando você só quer mostrar um modelo popup de cada vez, para vários pontos. Ao reutilizar o modelo popup, o número de elementos DOM criados pela aplicação é reduzido, o que melhora o desempenho da sua aplicação. A amostra a seguir utiliza o mesmo modelo popup para três pontos. Se clicar em qualquer um deles, será exibido um pop-up com o conteúdo para essa função de ponto.

<br/>

<iframe height='500' scrolling='no' title='ReutilarPopupTemplate' src='//codepen.io/azuremaps/embed/WNvjxGw/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Placa <a href='https://codepen.io/azuremaps/pen/WNvjxGw/'>De ReusePopupTemplate</a> pen by Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="popup-events"></a>Eventos popup

Popups podem ser abertos, fechados e arrastados. A classe popup fornece eventos para ajudar os desenvolvedores a reagir a estes eventos. A amostra que se segue realça quais os eventos que disparam quando o utilizador abre, fecha ou arrasta o pop-up. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Eventos popup" src="//codepen.io/azuremaps/embed/BXrpvB/?height=500&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Veja os <a href='https://codepen.io/azuremaps/pen/BXrpvB/'>eventos</a> Pen Popup da Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as aulas e métodos utilizados neste artigo:

> [!div class="nextstepaction"]
> [Popup](/javascript/api/azure-maps-control/atlas.popup)

> [!div class="nextstepaction"]
> [PopupOptions](/javascript/api/azure-maps-control/atlas.popupoptions)

> [!div class="nextstepaction"]
> [PopupTemplate](/javascript/api/azure-maps-control/atlas.popuptemplate)

Consulte os seguintes artigos para amostras de código completo:

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Adicione um marcador HTML](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de linhas](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de polígonos](map-add-shape.md)