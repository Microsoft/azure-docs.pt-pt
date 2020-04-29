---
title: Adicione um popup a um ponto num mapa Microsoft Azure Maps
description: Neste artigo, você aprenderá a adicionar um popup a um ponto usando o Microsoft Azure Maps Web SDK.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 02/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: cf6424d2a6cbcfb7c5052201b5a9190c81fddaff
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80055959"
---
# <a name="add-a-popup-to-the-map"></a>Adicione um popup ao mapa

Este artigo mostra-lhe como adicionar um popup a um ponto num mapa.

## <a name="understand-the-code"></a>Compreender o código

O código seguinte adiciona uma `name` característica `description` de ponto, que tem e propriedades, ao mapa usando uma camada de símbolo. Uma instância da [classe Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup) é criada, mas não exibida. Os eventos do rato são adicionados à camada de símbolo para desencadear a abertura e o fecho do popup. Quando `position` o símbolo do marcador é pairado, a propriedade do popup é atualizada com a posição do marcador, e a opção `content` é atualizada com algum HTML que envolve as `name` propriedades e `description` propriedades da característica do ponto sendo pairada. O popup é então exibido `open` no mapa utilizando a sua função.

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

Abaixo está a amostra completa de código de execução da funcionalidade acima referida.

<br/>

<iframe height='500' scrolling='no' title='Adicione um pop-up usando o Azure Maps' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a caneta <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>Adicione um pop up usando O Mapa Azure</a> por Azure Maps ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="reusing-a-popup-with-multiple-points"></a>Reutilizar um popup com vários pontos

Há casos em que a melhor abordagem é criar um pop-up e reutilizá-lo. Por exemplo, você pode ter um grande número de pontos e quer mostrar apenas um popup de cada vez. Ao reutilizar o popup, o número de elementos DOM criados pela aplicação é muito reduzido, o que pode proporcionar um melhor desempenho. A amostra seguinte cria características de 3 pontos. Se clicar em qualquer um deles, será exibido um popup com o conteúdo para essa funcionalidade de ponto.

<br/>

<iframe height='500' scrolling='no' title='Reutilizar popup com pinos múltiplos' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a Caneta <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>Reusing Popup com Pinos Múltiplos</a> de Azure Maps ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customizing-a-popup"></a>Personalizando um pop-up

Por padrão, o popup tem um fundo branco, uma seta de ponteiro na parte inferior, e um botão de fecho no canto superior direito. A amostra seguinte muda a cor `fillColor` de fundo para preto utilizando a opção do popup. O botão de fecho `CloseButton` é removido definindo a opção para falso. O conteúdo HTML do popup utiliza acolchoados 10 pixels das bordas do popup. O texto é feito branco, por isso aparece bem no fundo preto.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Popup personalizado" src="//codepen.io/azuremaps/embed/ymKgdg/?height=500&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Veja o <a href='https://codepen.io/azuremaps/pen/ymKgdg/'>Popup Personalizado</a> pen por<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-popup-templates-to-the-map"></a>Adicione modelos popup ao mapa

Os modelos popup facilitam a criação de layouts orientados por dados para popups. As secções abaixo demonstram o uso de vários modelos popup para gerar conteúdo formado usando propriedades de funcionalidades.

> [!NOTE]
> Por predefinição, todos os conteúdos renderizados utilizam o modelo popup será encaixotado de areia dentro de um iframe como uma funcionalidade de segurança. No entanto, existem limitações:
>
> - Todos os scripts, formulários, bloqueio de ponteiro e funcionalidade de navegação superior estão desativados. Os links podem abrir-se num novo separador quando clicados. 
> - Os navegadores mais antigos `srcdoc` que não suportam o parâmetro nos iframes limitar-se-ão a renderizar uma pequena quantidade de conteúdo.
> 
> Se confiar nos dados que estão a ser carregados nos popups e potencialmente pretender que estes scripts carregados em popups possam aceder à sua aplicação, pode desativar isso definindo a opção de modelos `sandboxContent` popup para falso. 

### <a name="string-template"></a>Modelo de corda

O modelo de string substitui os espaços reservados por valores das propriedades da funcionalidade. As propriedades da funcionalidade não têm de ser atribuídas um valor de tipo String. Por exemplo, `value1` tem uma inteiro. Estes valores são então passados para a propriedade de conteúdo do `popupTemplate`. 

A `numberFormat` opção especifica o formato do número a apresentar. Se `numberFormat` o não for especificado, então o código utilizará o formato de data de data dos modelos popup. A `numberFormat` opção formata os números utilizando a função [Número.toLocaleString.](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) Para formatar grandes números, considere utilizar a opção `numberFormat` com funções do [formato NumberFormat.formato](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/NumberFormat/format). Por exemplo, o código abaixo `maximumFractionDigits` usa para limitar o número de dígitos de fração a dois.

> [!Note]
> Só há uma maneira de o modelo de corda sondar imagens. Primeiro, o modelo de corda precisa de ter uma etiqueta de imagem nele. O valor que está a ser passado para a etiqueta de imagem deve ser um URL para uma imagem. Em seguida, o modelo `isImage` string precisa `HyperLinkFormatOptions`ter definido para verdade no . A `isImage` opção especifica que a hiperligação é para uma imagem, e a hiperligação será carregada numa etiqueta de imagem. Quando o hiperlink é clicado, a imagem abre-se.

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

O modelo PropertyInfo exibe propriedades disponíveis da funcionalidade. A `label` opção especifica o texto para exibir ao utilizador. Se `label` não for especificado, a hiperligação será exibida. E, se a hiperligação for uma imagem, o valor atribuído à etiqueta "alt" será exibido. O `dateFormat` especifica o formato da data, e se o formato da data não for especificado, então a data será renderizada como uma corda. A `hyperlinkFormat` opção torna links clicáveis, `email` da mesma forma, a opção pode ser usada para renderizar endereços de e-mail clicáveis.

Antes do modelo PropertyInfo apresentar as propriedades ao utilizador final, verifica recursivamente que as propriedades estão de facto definidas para essa funcionalidade. Também ignora exibindo propriedades de estilo e título. Por exemplo, não vai `color` `size` `anchor`exibir, `strokeOpacity`, `visibility`e . Assim, uma vez que a verificação do caminho da propriedade é completa na parte de trás, o modelo PropertyInfo mostra o conteúdo em formato de tabela.

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

### <a name="multiple-content-templates"></a>Múltiplos modelos de conteúdo

Uma funcionalidade também pode exibir conteúdo usando uma combinação do modelo string e do modelo PropertyInfo. Neste caso, o modelo string torna os suportes reservados valores em um fundo branco.  E, o modelo PropertyInfo torna uma imagem de largura completa dentro de uma mesa. As propriedades desta amostra são semelhantes às propriedades que explicamos nas amostras anteriores.

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

Quando o modelo Popup não é definido como um modelo de String, um modelo PropertyInfo, ou uma combinação de ambos, então ele usa as definições padrão. Quando `title` as `description` propriedades e são as únicas propriedades atribuídas, o modelo popup mostra um fundo branco, um botão de perto no canto superior direito. E, nos ecrãs pequenos e médios, mostra uma seta na parte inferior. As definições predefinidas mostram dentro `title` de `description`uma tabela para todas as propriedades que não a e a . Mesmo quando recua para as definições predefinidas, o modelo popup ainda pode ser manipulado programáticamente. Por exemplo, os utilizadores podem desligar a deteção de hiperligações e as definições predefinidas continuariam a aplicar-se a outras propriedades.

Clique nos pontos do mapa no CodePen. Há um ponto no mapa para cada um dos seguintes modelos popup: modelo de string, modelo PropertyInfo e modelo de conteúdo múltiplo. Existem também três pontos para mostrar como os modelos são renderizados usando as definições predefinidas.

<br/>

<iframe height='500' scrolling='no' title='Modelos pop-up' src='//codepen.io/azuremaps/embed/dyovrzL/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte os <a href='https://codepen.io/azuremaps/pen/dyovrzL/'>Modelos Popup</a> Pen<a href='https://codepen.io/azuremaps'>@azuremaps</a>por Azure Maps () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="reuse-popup-template"></a>Reutilizar o modelo popup

Semelhante a reutilizar popup, você pode reutilizar modelos popup. Esta abordagem é útil quando você só quer mostrar um modelo popup de cada vez, para vários pontos. Ao reutilizar o modelo popup, o número de elementos DOM criados pela aplicação é reduzido, o que melhora o desempenho da sua aplicação. A amostra seguinte usa o mesmo modelo popup para três pontos. Se clicar em qualquer um deles, será exibido um popup com o conteúdo para essa funcionalidade de ponto.

<br/>

<iframe height='500' scrolling='no' title='Modelo de popup reutiluse' src='//codepen.io/azuremaps/embed/WNvjxGw/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte o Modelo De <a href='https://codepen.io/azuremaps/pen/WNvjxGw/'>Reutilização</a> de<a href='https://codepen.io/azuremaps'>@azuremaps</a>Canetas Popup por Azure Maps () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="popup-events"></a>Eventos pop-up

Popups podem ser abertos, fechados e arrastados. A classe popup fornece eventos para ajudar os desenvolvedores a reagir a estes eventos. A amostra seguinte realça quais os eventos que disparam quando o utilizador abre, fecha ou arrasta o popup. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Eventos pop-up" src="//codepen.io/azuremaps/embed/BXrpvB/?height=500&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Veja os <a href='https://codepen.io/azuremaps/pen/BXrpvB/'>eventos</a> Pen Popup<a href='https://codepen.io/azuremaps'>@azuremaps</a>da Azure Maps () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as aulas e métodos utilizados neste artigo:

> [!div class="nextstepaction"]
> [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup)

> [!div class="nextstepaction"]
> [PopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions)

> [!div class="nextstepaction"]
> [Modelo popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popuptemplate)

Consulte os seguintes grandes artigos para amostras completas de código:

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Adicione um marcador HTML](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de linhas](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de polígonos](map-add-shape.md)
