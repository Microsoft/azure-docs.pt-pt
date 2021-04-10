---
title: Azure Maps Web SDK boas práticas | Microsoft Azure Maps
description: Aprenda dicas & truques para otimizar o seu uso do Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 3/22/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: ba351053c876c31d945ec7e4127a5caebd6a71ce
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878349"
---
# <a name="azure-maps-web-sdk-best-practices"></a>Azure Maps Web SDK boas práticas

Este documento centra-se nas melhores práticas para o Azure Maps Web SDK, no entanto, muitas das melhores práticas e otimizações delineadas podem ser aplicadas a todos os outros SDKs Azure Maps.

O Azure Maps Web SDK fornece uma tela poderosa para renderizar grandes conjuntos de dados espaciais de muitas maneiras diferentes. Em alguns casos, existem múltiplas formas de tornar os dados da mesma forma, mas dependendo do tamanho do conjunto de dados e da funcionalidade desejada, um método pode ter um desempenho melhor do que outros. Este artigo destaca as melhores práticas e dicas e truques para maximizar o desempenho e criar uma experiência suave do utilizador.

Geralmente, quando se procura melhorar o desempenho do mapa, procure formas de reduzir o número de camadas e fontes, e a complexidade dos conjuntos de dados e estilos de renderização que estão a ser utilizados.

## <a name="security-basics"></a>Fundamentos de segurança

A parte mais importante da sua aplicação é a sua segurança. Se a sua aplicação não for segura, um hacker pode arruinar qualquer aplicação, por muito boa que seja a experiência do utilizador. Seguem-se algumas dicas para manter a sua aplicação Azure Maps segura. Ao utilizar o Azure, não se esqueça de se familiarizar com as ferramentas de segurança disponíveis. Consulte este documento para obter uma [introdução à segurança da Azure](https://docs.microsoft.com/azure/security/fundamentals/overview).

> [!IMPORTANT]
> O Azure Maps fornece dois métodos de autenticação.
> * Autenticação baseada em chaves de subscrição
> * Autenticação do Azure Ative Directory Use Azure Ative Directy em todas as aplicações de produção.
> A autenticação baseada em chaves de subscrição é simples e o que a maioria das plataformas de mapeamento usam como método de forma leve para medir o seu uso da plataforma para fins de faturação. No entanto, esta não é uma forma segura de autenticação e só deve ser usada localmente no desenvolvimento de apps. Algumas plataformas fornecem a capacidade de restringir quais endereços IP e/ou remetente HTTP está em pedidos, no entanto, esta informação pode facilmente ser falsificada. Se utilizar as teclas de subscrição, [certifique-se](how-to-manage-authentication.md#manage-and-rotate-shared-keys)de que as roda regularmente .
> O Azure Ative Directory é um serviço de identidade empresarial que tem uma grande seleção de funcionalidades e configurações de segurança para todo o tipo de cenários de aplicação. A Microsoft recomenda que todas as aplicações de produção que utilizem o Azure Maps utilizem o Azure Ative Directory para autenticação.
> Saiba mais sobre [como gerir a autenticação no Azure Maps](how-to-manage-authentication.md) neste documento.

### <a name="secure-your-private-data"></a>Proteja os seus dados privados

Quando os dados são adicionados ao mapa interativo Azure Maps SDKs, este é renderizado localmente no dispositivo do utilizador final e nunca é enviado de volta para a internet por qualquer motivo.

Se a sua aplicação estiver a carregar dados que não devem ser acessíveis ao público, certifique-se de que os dados são armazenados num local seguro, que são acedidos de forma segura e que a aplicação em si está bloqueada e apenas disponível para os utilizadores pretendidos. Se algum destes passos for ignorado, uma pessoa não autorizada tem o potencial de aceder a estes dados. O Azure Ative Directory pode ajudá-lo a bloquear isto.

Consulte este tutorial sobre [a adição de autenticação à sua aplicação web em execução no Azure App Service](https://docs.microsoft.com/azure/app-service/scenario-secure-app-authentication-app-service)

### <a name="use-the-latest-versions-of-azure-maps"></a>Use as versões mais recentes do Azure Maps

Os SDKs Azure Maps passam por testes de segurança regulares, juntamente com quaisquer bibliotecas de dependência externa que possam ser usadas pelos SDKs. Qualquer problema de segurança conhecido é corrigido em tempo útil e libertado para a produção. Se a sua aplicação apontar para a versão mais recente da versão hospedada do Azure Maps Web SDK, receberá automaticamente todas as atualizações de versão menores que incluirão correções relacionadas com a segurança.

Se se hospedar o Azure Maps Web SDK através do módulo NPM, certifique-se de que utiliza o símbolo caret (^) para em combinação com o número de versão do pacote Azure Maps NPM no seu ficheiro, de modo a que ele `package.json` sempre aponte para a versão menor mais recente.

```json
"dependencies": {
  "azure-maps-control": "^2.0.30"
}
```

## <a name="optimize-initial-map-load"></a>Otimizar a carga inicial do mapa

Quando uma página web está a carregar, uma das primeiras coisas que pretende fazer é começar a renderizar algo o mais rapidamente possível para que o utilizador não esteja a olhar para um ecrã em branco.

### <a name="watch-the-maps-ready-event"></a>Assista ao evento pronto para mapas

Da mesma forma, quando o mapa inicialmente carrega frequentemente, é desejado carregar dados sobre ele o mais rapidamente possível, de modo que o utilizador não está a olhar para um mapa vazio. Uma vez que o mapa carrega os recursos assíncronos, tem de esperar até que o mapa esteja pronto para ser interagido antes de tentar fazer os seus próprios dados sobre ele. Há dois eventos que pode esperar, um `load` evento e um `ready` evento. O evento de carga disparará depois de o mapa ter terminado completamente a visualização do mapa inicial e todos os azulejos do mapa terem sido carregados. O evento pronto disparará quando os recursos mínimos do mapa necessários para começar a interagir com o mapa. O evento pronto pode muitas vezes disparar em metade do tempo do evento de carga e assim permitir-lhe começar a carregar os seus dados no mapa mais cedo.

### <a name="lazy-load-the-azure-maps-web-sdk"></a>Carga preguiçosa do Azure Maps Web SDK

Se o mapa não for necessário imediatamente, preguiçoso carregue o Azure Maps Web SDK até que seja necessário. Isto atrasará o carregamento dos ficheiros JavaScript e CSS utilizados pelo Azure Maps Web SDK até ser necessário. Um cenário comum onde isto ocorre é quando o mapa é carregado em um separador ou painel de voo que não é exibido na carga da página.
A seguinte amostra de código mostra como atrasar o carregamento do Azure Maps Web SDK até que um botão seja premido.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Preguiçoso carregar o mapa" src="https://codepen.io/azuremaps/embed/vYEeyOv?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Veja o Pen <a href='https://codepen.io/azuremaps/pen/vYEeyOv'>Lazy carregar o mapa</a> por Azure Maps ( ) no <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="add-a-placeholder-for-the-map"></a>Adicione um espaço reservado para o mapa

Se o mapa demorar algum tempo a carregar devido a limitações de rede ou outras prioridades dentro da sua aplicação, considere adicionar uma pequena imagem de fundo ao mapa `div` como um espaço reservado para o mapa. Isto preencherá o vazio do mapa `div` enquanto está a carregar.

### <a name="set-initial-map-style-and-camera-options-on-initialization"></a>Definir o estilo do mapa inicial e as opções de câmera na inicialização

Muitas vezes as aplicações querem carregar o mapa para uma localização ou estilo específico. Por vezes, os desenvolvedores esperam até que o mapa tenha carregado (ou espere pelo `ready` evento) e, em seguida, use as `setCemer` funções ou `setStyle` funções do mapa. Isto levará muitas vezes mais tempo a chegar à visão do mapa inicial desejado, uma vez que muitos recursos acabam por ser carregados por defeito antes de serem carregados os recursos necessários para a vista do mapa desejado. Uma melhor abordagem é passar na câmara de mapa desejada e opções de estilo no mapa ao inicializá-la.

## <a name="optimize-data-sources"></a>Otimizar fontes de dados

O Web SDK tem duas fontes de dados,

* **GeoJSON fonte**: Conhecida como a `DataSource` classe, gere dados de localização bruta em formato GeoJSON localmente. Bom para pequenos e médios conjuntos de dados (mais de centenas de milhares de funcionalidades).
* **Fonte de azulejos vetoriais**: Conhecidos na `VectorTileSource` classe, carrega dados formatados como azulejos vetoriais para a visão atual do mapa, com base no sistema de inclinação de mapas. Ideal para grandes e maciços conjuntos de dados (milhões ou biliões de funcionalidades).

### <a name="use-tile-based-solutions-for-large-datasets"></a>Utilize soluções baseadas em azulejos para grandes conjuntos de dados

Se trabalhar com conjuntos de dados maiores que contenham milhões de funcionalidades, a forma recomendada de obter o melhor desempenho é expor os dados usando uma solução do lado do servidor, como o serviço de azulejos de vetor ou raster.  
Os azulejos vetoriais são otimizados para carregar apenas os dados que estão à vista com as geometrias cortadas à área de foco do azulejo e generalizadas para corresponder à resolução do mapa para o nível de zoom do azulejo.

A [plataforma Azure Maps Creator](creator-indoor-maps.md) fornece a capacidade de recuperar dados em formato de azulejo vetorial. Outros formatos de dados podem estar a usar ferramentas como [o Tippecanoe](https://github.com/mapbox/tippecanoe) ou uma das muitas listas de [recursos nesta página.](https://github.com/mapbox/awesome-vector-tiles)

Também é possível criar um serviço personalizado que torne os conjuntos de dados como telhas de imagem rasterizadas no lado do servidor e carregue os dados usando a classe TileLayer no mapa SDK. Isto proporciona um desempenho excecional, uma vez que o mapa só precisa de carregar e gerir algumas dezenas de imagens no máximo. No entanto, existem algumas limitações na utilização de azulejos rasteres, uma vez que os dados brutos não estão disponíveis localmente. Um serviço secundário é frequentemente necessário para alimentar qualquer tipo de experiência de interação, por exemplo, descobrir em que forma um utilizador clicou. Além disso, o tamanho do ficheiro de um azulejo raster é muitas vezes maior do que um azulejo vetorial comprimido que contém geometrias otimizadas de nível generalizado e zoom otimizado.

Saiba mais sobre fontes de dados no [Documento de Origem de Dados.](create-data-source-web-sdk.md)

### <a name="combine-multiple-datasets-into-a-single-vector-tile-source"></a>Combine vários conjuntos de dados numa única fonte de azulejos de vetor

Quanto menos fontes de dados o mapa tiver de gerir, mais rápido poderá processar todas as funcionalidades a serem exibidas. Em particular, quando se trata de fontes de azulejos, combinar duas fontes de azulejos de vetores em conjunto reduz o número de pedidos HTTP para recuperar os azulejos ao meio, e a quantidade total de dados seria ligeiramente menor uma vez que existe apenas um cabeçalho de ficheiro.

Combinar vários conjuntos de dados numa única fonte de azulejos pode ser alcançado usando uma ferramenta como [Tippecanoe](https://github.com/mapbox/tippecanoe). Os conjuntos de dados podem ser combinados numa única recolha de características ou separados em camadas separadas dentro do azulejo vetorial conhecido como camadas de origem. Ao ligar uma fonte de azulejo vetorial a uma camada de renderização, especificaria a camada de origem que contém os dados que pretende renderizar com a camada.

### <a name="reduce-the-number-of-canvas-refreshes-due-to-data-updates"></a>Reduzir o número de atualizações de tela devido a atualizações de dados

Existem várias formas de adicionar ou atualizar dados numa `DataSource` aula. Abaixo estão listados os diferentes métodos e algumas considerações para garantir um bom desempenho.

* A função de fontes de dados `add` pode ser usada para adicionar uma ou mais funcionalidades a uma fonte de dados. Cada vez que esta função é chamada, irá desencadear uma atualização de tela de mapa. Se adicionar muitas funcionalidades, combine-as numa matriz ou recolha de recursos e passe-as para esta função uma vez, em vez de dar a volta a um conjunto de dados e chamar esta função para cada funcionalidade.
* A função de fontes de dados `setShapes` pode ser usada para substituir todas as formas numa fonte de dados. Sob o capot, combina as fontes de dados `clear` e `add` funções juntas e faz uma única tela de mapa refrescante em vez de duas, o que é muito mais rápido. Certifique-se de que utiliza isto quando pretende atualizar todos os dados numa fonte de dados.
* A função de fontes de dados `importDataFromUrl` pode ser usada para carregar um ficheiro GeoJSON através de um URL numa fonte de dados. Uma vez descarregados os dados, é transmitido para a função de fontes de `add` dados. Se o ficheiro GeoJSON estiver alojado num domínio diferente, certifique-se de que o outro domínio suporta pedidos de domínio cruzado (CORs). Se não considerar copiar os dados para um ficheiro local no seu domínio ou criar um serviço de procuração que tenha CORs ativados. Se o ficheiro for grande, considere convertê-lo numa fonte de azulejos vetoriais.
* Se as funcionalidades estiverem embrulhadas com a `Shape` classe, o `addProperty` , e `setCoordinates` as `setProperties` funções da forma irão desencadear uma atualização na fonte de dados e uma atualização de tela de mapa. Todas as funcionalidades devolvidas pelas fontes de dados `getShapes` e `getShapeById` funções são automaticamente embrulhadas com a `Shape` classe. Se pretender atualizar várias formas, é mais rápido convertê-las em JSON utilizando a função de fontes de `toJson` dados, editando o GeoJSON e, em seguida, passando estes dados para a função fontes de `setShapes` dados.

### <a name="avoid-calling-the-data-sources-clear-function-unnecessarily"></a>Evite chamar as fontes de dados de uma função clara desnecessariamente

Chamar a função clara da `DataSource` classe causa uma atualização de tela de mapa. Se a `clear` função for chamada várias vezes seguidas, pode ocorrer um atraso enquanto o mapa aguarda que cada atualização ocorra.

Um cenário comum em que isto aparece frequentemente nas aplicações é quando uma aplicação limpa a fonte de dados, descarrega novos dados, limpa novamente a fonte de dados e adiciona os novos dados à fonte de dados. Dependendo da experiência desejada pelo utilizador, as seguintes alternativas seriam melhores.

* Limpe os dados antes de descarregar os novos dados e, em seguida, passe os novos dados para as fontes de dados `add` ou `setShapes` função. Se este for o único conjunto de dados no mapa, o mapa estará vazio enquanto os novos dados estiverem a ser descarregados.
* Faça o download dos novos dados e, em seguida, passe-os para a função de fontes de `setShapes` dados. Isto substituirá todos os dados no mapa.

### <a name="remove-unused-features-and-properties"></a>Remover funcionalidades e propriedades não usudas

Se o seu conjunto de dados contiver funcionalidades que não serão utilizadas na sua aplicação, remova-as. Da mesma forma, remova quaisquer propriedades em funcionalidades que não sejam necessárias. Isto tem vários benefícios:

* Reduz a quantidade de dados que tem de ser descarregado.
* Reduz o número de funcionalidades que precisam de ser enroladas ao renderizar os dados.
* Por vezes, pode ajudar a simplificar ou remover expressões e filtros orientados a dados, o que significa menos processamento necessário no momento de renderização.

Quando as funcionalidades têm muitas propriedades ou conteúdos, é muito mais performante limitar o que é adicionado à fonte de dados apenas aos necessários para renderização e ter um método ou serviço separado para recuperar a propriedade ou conteúdo adicional quando necessário. Por exemplo, se tiver um mapa simples a exibir localizações num mapa quando clicado, é apresentado um monte de conteúdo detalhado. Se pretender utilizar o estilo orientado por dados para personalizar a forma como as localizações são renderizadas no mapa, carregue apenas as propriedades necessárias na fonte de dados. Quando pretender apresentar o conteúdo detalhado, utilize o ID da função para recuperar separadamente o conteúdo adicional. Se o conteúdo for armazenado no lado do servidor, pode ser utilizado um serviço para o recuperar assíncronos, o que reduziria drasticamente a quantidade de dados que precisa de ser descarregado quando o mapa é inicialmente carregado.

Além disso, a redução do número de dígitos significativos nas coordenadas das funcionalidades também pode reduzir significativamente o tamanho dos dados. Não é incomum que as coordenadas contenham 12 ou mais casas decimais; no entanto, seis casas decimais têm uma precisão de cerca de 0,1 metros, o que é muitas vezes mais preciso do que a localização que a coordenada representa (seis casas decimais são recomendadas quando se trabalha com pequenos dados de localização, como layouts de edifícios interiores). Ter mais de seis casas decimais provavelmente não fará diferença na forma como os dados são renderizados e apenas exigirá que o utilizador descarregue mais dados sem qualquer benefício adicional.

Aqui está uma lista de [ferramentas úteis para trabalhar com dados da GeoJSON.](https://github.com/tmcw/awesome-geojson)

### <a name="use-a-separate-data-source-for-rapidly-changing-data"></a>Utilize uma fonte de dados separada para alterar rapidamente os dados

Por vezes, é necessário atualizar rapidamente os dados no mapa para coisas como mostrar atualizações ao vivo de dados de streaming ou funcionalidades de animação. Quando uma fonte de dados é atualizada, o motor de renderização irá circular e renderizar todas as funcionalidades na fonte de dados. Separar dados estáticos de alterar rapidamente os dados em diferentes fontes de dados pode reduzir significativamente o número de funcionalidades que são re-renderizadas em cada atualização para a fonte de dados e melhorar o desempenho geral.

Se utilizar telhas de vetor com dados ao vivo, uma maneira fácil de suportar atualizações é usar o `expires` cabeçalho de resposta. Por padrão, qualquer fonte de azulejos vetoriais ou camada de azulejos rasterizador recarregará automaticamente os azulejos quando a `expires` data. O fluxo de tráfego e os azulejos incidentes no mapa utilizam esta funcionalidade para garantir que os dados de tráfego em tempo real frescos são apresentados no mapa. Esta funcionalidade pode ser desativada definindo a opção de serviço de mapas `refreshExpiredTiles` para `false` .

### <a name="adjust-the-buffer-and-tolerance-options-in-geojson-data-sources"></a>Ajuste as opções de tampão e tolerância nas fontes de dados da GeoJSON

A `DataSource` classe converte dados de localização bruta em azulejos vetoriais locais para renderização on-the-fly. Estes azulejos vetoriais locais prendem os dados brutos aos limites da área do azulejo com um pouco de tampão para garantir a renderização suave entre azulejos. Quanto menor for a `buffer` opção, menos dados sobrepostos são armazenados nos vetores locais e melhor desempenho, no entanto, maior a mudança de artefactos de renderização ocorre. Experimente ajustar esta opção para obter a mistura certa de desempenho com artefactos de renderização mínimos.

A `DataSource` classe também tem uma `tolerance` opção que é usada com o algoritmo de simplificação Douglas-Peucker ao reduzir a resolução de geometrias para efeitos de renderização. Aumentar este valor de tolerância reduzirá a resolução de geometrias e, por sua vez, melhorará o desempenho. Ajuste esta opção para obter a mistura certa de resolução de geometria e desempenho para o seu conjunto de dados.

### <a name="set-the-max-zoom-option-of-geojson-data-sources"></a>Desaprote a opção de zoom máximo das fontes de dados da GeoJSON

A `DataSource` classe converte dados de localização bruta em azulejos vetoriais locais para renderização on-the-fly. Por predefinição, fá-lo-á até ao nível de zoom 18, altura em que, quando ampliado mais perto, irá recolher dados dos azulejos gerados para o nível de zoom 18. Isto funciona bem para a maioria dos conjuntos de dados que precisam de ter alta resolução quando ampliados a estes níveis. No entanto, ao trabalhar com conjuntos de dados que são mais propensos a serem vistos quando ampliados mais, como quando se vê polígonos estatais ou de província, definir `minZoom` a opção da fonte de dados para um valor menor, como `12` reduzirá a quantidade de cálculo, a geração de azulejos locais que ocorre e a memória usada pela fonte de dados e aumenta o desempenho.

### <a name="minimize-geojson-response"></a>Minimizar a resposta da GeoJSON

Ao carregar dados da GeoJSON a partir de um servidor, seja através de um serviço ou carregando um ficheiro plano, certifique-se de que os dados são minimizados para remover caracteres espaciais não necessários que tornam o tamanho do download maior do que o necessário.

### <a name="access-raw-geojson-using-a-url"></a>Aceda a GeoJSON em bruto usando um URL

É possível armazenar objetos GeoJSON dentro do JavaScript, no entanto isto irá usar muita memória como cópias do mesmo serão armazenadas em toda a variável que criou para este objeto e a instância de origem de dados, que o gere dentro de um web worker separado. Exponha o GeoJSON à sua aplicação utilizando um URL e a fonte de dados carregará uma única cópia de dados diretamente no web worker de fontes de dados.  

## <a name="optimize-rendering-layers"></a>Otimizar camadas de renderização

Os mapas Azure fornecem várias camadas diferentes para renderizar dados num mapa. Existem muitas otimizações que pode aproveitar para adaptar estas camadas ao seu cenário, aumentando as performances e a experiência geral do utilizador.

### <a name="create-layers-once-and-reuse-them"></a>Crie camadas uma vez e reutilizá-las

O Azure Maps Web SDK é decidido ser orientado por dados. Os dados vão para fontes de dados, que são então ligadas a camadas de renderização. Se pretender alterar os dados no mapa, atualize os dados na fonte de dados ou altere as opções de estilo numa camada. Isto é muitas vezes muito mais rápido do que remover e, em seguida, recriar camadas sempre que há uma mudança.

### <a name="consider-bubble-layer-over-symbol-layer"></a>Considere a camada de bolha sobre a camada de símbolo

A camada de bolha torna os pontos como círculos no mapa e pode facilmente ter o seu raio e cor modelados usando uma expressão baseada em dados. Uma vez que o círculo é uma forma simples para o WebGL desenhar, o motor de renderização será capaz de torná-los muito mais rápido do que uma camada de símbolo, que tem que carregar e renderizar uma imagem. A diferença de desempenho destas duas camadas de renderização é percetível ao renderizar dezenas de milhares de pontos.

### <a name="use-html-markers-and-popups-sparingly"></a>Use marcadores HTML e Popups com moderação

Ao contrário da maioria das camadas no controlo Web Azure Maps que utilizam WebGL para renderização, marcadores HTML e Popups usam elementos dom tradicionais para renderização. Como tal, quanto mais marcadores HTML e Popups adicionam uma página, mais elementos DOM existem. O desempenho pode degradar-se depois de adicionar algumas centenas de marcadores HTML ou popups. Para conjuntos de dados maiores, considere o agrupamento dos seus dados ou a utilização de um símbolo ou camada de bolha. Para os popups, uma estratégia comum é criar um único pop-up e reutilizá-lo atualizando o seu conteúdo e posição como mostrado no exemplo abaixo:

<br/>

<iframe height='500' scrolling='no' title='Reutilizar Popup com múltiplos pinos' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte o <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>Popup de Reutilização de Canetas com Múltiplos Pinos</a> by Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

Dito isto, se tiver apenas alguns pontos a renderizar no mapa, a simplicidade dos marcadores HTML pode ser preferível. Além disso, os marcadores HTML podem ser facilmente arrastados se necessário.

### <a name="combine-layers"></a>Combine camadas

O mapa é capaz de renderizar centenas de camadas, no entanto, quanto mais camadas existem, mais tempo demora a fazer uma cena. Uma estratégia para reduzir o número de camadas é combinar camadas que tenham estilos semelhantes ou que possam ser modeladas usando [estilos orientados por dados.](data-driven-style-expressions-web-sdk.md)

Por exemplo, considere um conjunto de dados onde todas as funcionalidades têm um `isHealthy` imóvel que pode ter um valor de `true` ou `false` . Se criar uma camada de bolha que torne diferentes bolhas coloridas com base nesta propriedade, existem várias maneiras de fazê-lo como listado abaixo, de menos performante a mais performante.

* Divida os dados em duas fontes de dados com base no `isHealthy` valor e anexe uma camada de bolha com uma opção de cor codificada por código rígido a cada fonte de dados.
* Coloque todos os dados numa única fonte de dados e crie duas camadas de bolha com uma opção de cor codificada e um filtro baseado na `isHealthy` propriedade. 
* Coloque todos os dados numa única fonte de dados, crie uma única camada de bolha com uma `case` expressão de estilo para a opção de cor baseada na `isHealthy` propriedade. Aqui está uma amostra de código que demonstra isto.

```javascript
var layer = new atlas.layer.BubbleLayer(source, null, {
    color: [
        'case',

        //Get the 'isHealthy' property from the feature.
        ['get', 'isHealthy'],

        //If true, make the color 'green'. 
        'green',

        //If false, make the color red.
        'red'
    ]
});
```

### <a name="create-smooth-symbol-layer-animations"></a>Crie animações de camada de símbolo suave

As camadas de símbolo têm deteção de colisão ativada por predefinição. Esta deteção de colisão visa garantir que não se sobreponham dois símbolos. As opções de ícone e texto de uma camada de símbolo têm duas opções,

* `allowOverlap` - especifica se o símbolo será visível se colidir com outros símbolos.
* `ignorePlacement` - especifica se os outros símbolos podem colidir com o símbolo.

Ambas as opções são definidas `false` por padrão. Ao animar um símbolo, os cálculos de deteção de colisão serão executados em cada quadro da animação, o que pode abrandar a animação e fazê-la parecer menos fluida. Para suavizar a animação, desa estalem estas opções para `true` .

O código seguinte amostra uma forma simples de animar uma camada de símbolo.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Animação da camada de símbolo" src="https://codepen.io/azuremaps/embed/oNgGzRd?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a <a href='https://codepen.io/azuremaps/pen/oNgGzRd'>animação</a> da camada de símbolo de caneta por Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="specify-zoom-level-range"></a>Especifique a gama de nível de zoom

Se os seus dados satisfaçam um dos seguintes critérios, certifique-se de especificar o nível de zoom min e máximo da camada para que o motor de renderização possa ignorá-lo quando estiver fora da gama de nível de zoom.

* Se os dados são provenientes de uma fonte de azulejos vetoriais, muitas vezes camadas de origem para diferentes tipos de dados só estão disponíveis através de uma gama de níveis de zoom.
* Se usar uma camada de azulejos que não tenha azulejos para todos os níveis de zoom 0 a 24 e você quer que ele só renderiza nos níveis que tem azulejos, e não tente preencher azulejos em falta com azulejos de outros níveis de zoom.
* Se quiser apenas fazer uma camada em certos níveis de zoom.
Todas as camadas têm uma `minZoom` e `maxZoom` opção onde a camada será renderizada quando entre estes níveis de zoom com base nesta ` maxZoom > zoom >= minZoom` lógica.

**Exemplo**

```javascript
//Only render this layer between zoom levels 1 and 9. 
var layer = new atlas.layer.BubbleLayer(dataSource, null, {
    minZoom: 1,
    maxZoom: 10
});
```

### <a name="specify-tile-layer-bounds-and-source-zoom-range"></a>Especifique os limites da camada de azulejos e a gama de zoom de fonte

Por padrão, as camadas de azulejos carregarão azulejos em todo o mundo. No entanto, se o serviço de azulejos tiver apenas azulejos para uma determinada área, o mapa tentará carregar azulejos quando estiver fora desta área. Quando isso acontece, um pedido para cada azulejo será feito e aguarda-se uma resposta que possa bloquear outros pedidos que sejam feitos pelo mapa e assim abrandar a renderização de outras camadas. Especificar os limites de uma camada de azulejo resultará no mapa apenas solicitando azulejos que estão dentro dessa caixa de delimitação. Além disso, se a camada de azulejos estiver disponível apenas entre certos níveis de zoom, especifique o zoom min e max source pela mesma razão.

**Exemplo**

```javascript
var tileLayer = new atlas.layer.TileLayer({
    tileUrl: 'myTileServer/{z}/{y}/{x}.png',
    bounds: [-101.065, 14.01, -80.538, 35.176],
    minSourceZoom: 1,
    maxSourceZoom: 10
});
```

### <a name="use-a-blank-map-style-when-base-map-not-visible"></a>Use um estilo de mapa em branco quando o mapa base não é visível

Se uma camada estiver a ser sobreposta no mapa que cobrirá completamente o mapa base, considere definir o estilo do mapa para `blank` ou para que o mapa base não seja `blank_accessible` renderizado. Um cenário comum para fazer isso é quando sobrepor um azulejo de globo completo não tem opacidade ou área transparente acima do mapa base.

### <a name="smoothly-animate-image-or-tile-layers"></a>Imagem suavemente animada ou camadas de azulejos

Se quiser animar através de uma série de camadas de imagem ou azulejos no mapa. É frequentemente mais rápido criar uma camada para cada imagem ou camada de azulejos e alterar a opacidade do que atualizar a fonte de uma única camada em cada quadro de animação. Esconder uma camada definindo a opacidade para zero e mostrando uma nova camada, definindo a sua opacidade para um valor superior a zero é muito mais rápido do que atualizar a fonte na camada. Alternativamente, a visibilidade das camadas pode ser alternada, mas certifique-se de que define a duração da camada para zero, caso contrário irá animar a camada ao exibi-la, o que causará um efeito cintilante uma vez que a camada anterior teria sido escondida antes da nova camada ser visível.

### <a name="tweak-symbol-layer-collision-detection-logic"></a>Lógica de deteção de colisão de camada de símbolo de ajuste

A camada de símbolo tem duas opções que existem tanto para o ícone como para o texto chamado `allowOverlap` e `ignorePlacement` . Estas duas opções especificam se o ícone ou texto de um símbolo pode sobrepor-se ou ser sobreposto. Quando estes estiverem `false` definidos, a camada de símbolo fará cálculos ao renderizar cada ponto para ver se colide com qualquer outro símbolo já renderizado na camada, e se o fizer, não tornará o símbolo de colisão. Isto é bom em reduzir a desordem no mapa e reduzir o número de objetos renderizados. Ao definir estas opções `false` para , esta lógica de deteção de colisão será ignorada, e todos os símbolos serão renderizados no mapa. Ajuste esta opção para obter a melhor combinação de desempenho e experiência do utilizador.

### <a name="cluster-large-point-data-sets"></a>Conjuntos de dados de pontos grandes do cluster

Ao trabalhar com grandes conjuntos de pontos de dados, poderá descobrir que, quando renderizados a determinados níveis de zoom, muitos dos pontos se sobrepõem e são apenas parcialmente visíveis, se é que são. O agrupamento é um processo de agrupamento de pontos que estão próximos e representando-os como um único ponto agrupado. À medida que o utilizador faz zoom no mapa, os clusters vão separar-se nos seus pontos individuais. Isto pode reduzir significativamente a quantidade de dados que precisa de ser prestado, fazer com que o mapa se sinta menos desordenado e melhorar o desempenho. A `DataSource` classe tem opções para agrupar dados localmente. Além disso, muitas ferramentas que geram azulejos vetoriais também têm opções de clustering.

Adicionalmente, aumente o tamanho do raio do cluster para melhorar o desempenho. Quanto maior for o raio do cluster, menos pontos agrupados há para acompanhar e renderizar.
Saiba mais no [documento de dados do ponto de agrupamento](clustering-point-data-web-sdk.md)

### <a name="use-weighted-clustered-heat-maps"></a>Use mapas de calor agrupados ponderados

A camada de mapa de calor pode tornar dezenas de milhares de pontos de dados facilmente. Para conjuntos de dados maiores, considere permitir o agrupamento na fonte de dados e usar um pequeno raio de cluster e usar a propriedade dos clusters `point_count` como um peso para o mapa de altura. Quando o raio do cluster é apenas alguns pixels de tamanho, haverá pouca diferença visual no mapa de calor renderizado. A utilização de um raio de aglomerado maior melhorará mais o desempenho, mas pode reduzir a resolução do mapa de calor renderizado.

```javascript
var layer = new atlas.layer.HeatMapLayer(source, null, {
   weight: ['get', 'point_count']
});
```

Saiba mais nos [mapas de Clustering e calor neste documento](clustering-point-data-web-sdk.md #clustering-and-the-heat-maps-layer)

### <a name="keep-image-resources-small"></a>Mantenha os recursos de imagem pequenos

As imagens podem ser adicionadas ao sprite de imagem de mapas para renderizar ícones em uma camada de símbolo ou padrões em uma camada de polígono. Mantenha estas imagens pequenas para minimizar a quantidade de dados que tem de ser descarregado e a quantidade de espaço que assumem no sprite de imagem dos mapas. Quando utilizar uma camada de símbolo que escala o ícone usando a `size` opção, utilize uma imagem que seja o tamanho máximo que o seu plano possa apresentar no mapa e não maior. Isto garantirá que o ícone é renderizado com alta resolução, minimizando os recursos que utiliza. Além disso, sVG's também pode ser usado como um formato de arquivo menor para imagens de ícones simples.

## <a name="optimize-expressions"></a>Otimizar expressões

[Expressões de estilo orientadas por dados](data-driven-style-expressions-web-sdk.md) proporcionam muita flexibilidade e poder para filtrar e modelar dados no mapa. Há muitas maneiras de as expressões poderem ser otimizadas. Eis algumas sugestões.

### <a name="reduce-the-complexity-of-filters"></a>Reduzir a complexidade dos filtros

Os filtros ligam todos os dados numa fonte de dados e verificam se cada filtro corresponde à lógica do filtro. Se os filtros se tornarem complexos, isto pode causar problemas de desempenho. Algumas estratégias possíveis para abordar esta questão incluem as seguintes.

* Se utilizar telhas vetoriais, decompo os dados em diferentes camadas de origem.
* Se utilizar a `DataSource` classe, parta esses dados em fontes de dados separadas. Tente equilibrar o número de fontes de dados com a complexidade do filtro. Muitas fontes de dados também podem causar problemas de desempenho, por isso pode precisar de fazer alguns testes para descobrir o que funciona melhor para o seu cenário.
* Ao utilizar um filtro complexo sobre uma camada, considere usar várias camadas com expressões de estilo para reduzir a complexidade do filtro. Evite criar um monte de camadas com estilos codificados quando expressões de estilo podem ser usadas como um grande número de camadas também podem causar problemas de desempenho.

### <a name="make-sure-expressions-dont-produce-errors"></a>Certifique-se de que expressões não produzem erros

As expressões são frequentemente usadas para gerar código para realizar cálculos ou operações lógicas no momento de renderização. Tal como o código no resto da sua aplicação, certifique-se de que os cálculos e lógicas fazem sentido e não são propensos a erros. Erros nas expressões causarão problemas na avaliação da expressão, o que pode resultar em problemas de desempenho e renderização reduzidos.

Um erro comum a ter em conta é ter uma expressão que se baseia numa propriedade de recurso que pode não existir em todas as funcionalidades. Por exemplo, o seguinte código usa uma expressão para definir a propriedade colorida de uma camada de bolha para a `myColor` propriedade de uma característica.

```javascript
var layer = new atlas.layer.BubbleLayer(source, null, {
    color: ['get', 'myColor']
});
```

O código acima funcionará bem se todas as funcionalidades na fonte de dados tiverem um `myColor` imóvel, e o valor dessa propriedade for uma cor. Isto pode não ser um problema se tiver o controlo total dos dados na fonte de dados e se souber com certeza que todas as funcionalidades terão uma cor válida numa `myColor` propriedade. Dito isto, para tornar este código seguro de erros, uma `case` expressão pode ser usada com a expressão para verificar se a funcionalidade tem a `has` `myColor` propriedade. Se o fizer, a `to-color` expressão tipo pode então ser usada para tentar converter o valor dessa propriedade em uma cor. Se a cor for inválida, pode ser utilizada uma cor de recuo. O código a seguir demonstra como fazê-lo e define a cor do recuo para verde.

```javascript
var layer = new atlas.layer.BubbleLayer(source, null, {
    color: [
        'case',

        //Check to see if the feature has a 'myColor' property.
        ['has', 'myColor'],

        //If true, try validating that 'myColor' value is a color, or fallback to 'green'. 
        ['to-color', ['get', 'myColor'], 'green'],

        //If false, return a fallback value.
        'green'
    ]
});
```

### <a name="order-boolean-expressions-from-most-specific-to-least-specific"></a>Encomende expressões booleanas da maioria específica para menos específica

Quando utilizar expressões booleanas que contenham múltiplos testes condicional, encomende os testes condicionalis da mais específica para a menos específica. Ao fazê-lo, a primeira condição deve reduzir a quantidade de dados contra os que a segunda condição tem de ser testada, reduzindo assim o número total de testes condicional que precisam de ser realizados.

### <a name="simplify-expressions"></a>Simplificar expressões

Expressões podem ser poderosas e às vezes complexas. Quanto mais simples for uma expressão, mais rápido será avaliado. Por exemplo, se for necessária uma simples comparação, uma expressão como `['==', ['get', 'category'], 'restaurant']` seria melhor do que usar uma expressão de correspondência como `['match', ['get', 'category'], 'restaurant', true, false]` . Neste caso, se a propriedade que está a ser verificada for um valor boolean, uma `get` expressão seria ainda mais `['get','isRestaurant']` simples.

## <a name="web-sdk-troubleshooting"></a>Resolução de problemas web SDK

Seguem-se algumas dicas para depurar algumas das questões comuns encontradas ao desenvolver-se com o Azure Maps Web SDK.

**Porque é que o mapa não é exibido quando carrego o controlo da web?**

Faça o seguinte:

* Certifique-se de que adicionou as suas opções de autenticação adicionadas ao mapa. Se isso não for adicionado, o mapa será carregado com uma tela em branco, uma vez que não pode aceder aos dados do mapa base sem autenticação e 401 erros aparecerão no separador de rede das ferramentas de desenvolvimento do navegador.
* Certifique-se de que tem uma ligação à internet.
* Verifique se a consola tem erros nas ferramentas de desenvolvimento do navegador. Alguns erros podem fazer com que o mapa não se torne. Depurar a sua candidatura.
* Certifique-se de que está a utilizar um [browser suportado.](supported-browsers.md)

**Todos os meus dados estão a aparecer do outro lado do mundo, o que se passa?**
As coordenadas, também referidas como posições, nos SDKs Azure Maps alinham-se com o formato padrão da indústria geoespacial de `[longitude, latitude]` . Este mesmo formato é também como as coordenadas são definidas no esquema geoJSON; os dados fundamentais formatados utilizados nos SDKs Azure Maps. Se os seus dados estiverem a aparecer no lado oposto do mundo, é mais provável que os valores de longitude e latitude sejam invertidos na sua informação de coordenadas/posição.

**Porque é que os marcadores HTML aparecem no local errado no controlo web?**

Coisas a verificar:

* Se utilizar conteúdo personalizado para o marcador, certifique-se de que as `anchor` opções e `pixelOffset` as opções estão corretas. Por predefinição, o centro inferior do conteúdo está alinhado com a posição no mapa.
* Certifique-se de que o ficheiro CSS para a Azure Maps foi carregado.
* Inspecione o elemento DOM do marcador HTML para ver se algum CSS da sua aplicação se aprodicou ao marcador e está a afetar a sua posição.

**Por que os ícones ou texto na camada de símbolo aparecem no lugar errado?**
Verifique se `anchor` as opções e as `offset` opções estão corretamente configuradas para se alinhar com a parte da sua imagem ou texto que deseja ter alinhado com a coordenada no mapa.
Se o símbolo só estiver fora do lugar quando o mapa estiver rodado, verifique a `rotationAlignment` opção. Por predefinição, os símbolos rodaremos com o display dos mapas para que apareçam na vertical para o utilizador. No entanto, dependendo do seu cenário, pode ser desejável bloquear o símbolo à orientação do mapa. Desateia a `rotationAlignment` opção `’map’` de o fazer.
Se o símbolo só estiver fora do lugar quando o mapa estiver inclinado/inclinado, verifique a `pitchAlignment` opção. Por predefinição, os símbolos ficaremos na vertical com o viewport dos mapas à medida que o mapa é lançado ou inclinado. No entanto, dependendo do seu cenário, pode ser desejável bloquear o símbolo no tom do mapa. Desateia a `pitchAlignment` opção `’map’` de o fazer.

**Porque é que os meus dados não aparecem no mapa?**

Coisas a verificar:

* Verifique se a consola nas ferramentas de desenvolvimento do navegador é para obter erros.
* Certifique-se de que uma fonte de dados foi criada e adicionada ao mapa, e que a fonte de dados foi ligada a uma camada de renderização que também foi adicionada ao mapa.
* Adicione pontos de rutura no seu código e passe através dele para garantir que os dados estão a ser adicionados à fonte de dados e a fonte de dados e camadas estão a ser adicionadas ao mapa sem que ocorram erros.
* Tente remover expressões baseadas em dados da sua camada de renderização. É possível que um deles possa ter um erro que está a causar o problema.

**Posso usar o Azure Maps Web SDK num iframe de sandboxed?**

Sim. Note que o [Safari tem um bug](https://bugs.webkit.org/show_bug.cgi?id=170075) que impede que os iframes de sandboxed de executar trabalhadores web, o que é requisito do Azure Maps Web SDK. A solução é adicionar a `"allow-same-origin"` etiqueta à propriedade sandbox do iframe.

## <a name="get-support"></a>Obter suporte

Seguem-se as diferentes formas de obter apoio para o Azure Maps, dependendo do seu problema.

**Como reporto um problema de dados ou um problema com uma morada?**

O Azure Maps tem uma ferramenta de feedback de dados onde os problemas de dados podem ser reportados e rastreados. [https://feedback.azuremaps.com/](https://feedback.azuremaps.com/) Cada emissão submetida gera um URL único que pode usar para acompanhar o progresso da questão dos dados. O tempo que leva para resolver um problema de dados varia consoante o tipo de problema e a facilidade com que a alteração está correta. Uma vez corrigido, o serviço de prestação verá a atualização na atualização semanal, enquanto outros serviços como geocoding e encaminhamento verão a atualização na atualização mensal. Neste [documento](how-to-use-feedback-tool.md)são fornecidas instruções detalhadas sobre como comunicar uma questão de dados .

**Como reporto um bug num serviço ou API?**

https://azure.com/support

**Onde consigo ajuda técnica para o Azure Maps?**

Se relacionado com o visual do Azure Maps no Power BI: https://powerbi.microsoft.com/support/ Para todos os outros serviços da Azure Maps: https://azure.com/support ou os fóruns de desenvolvedores: https://docs.microsoft.com/answers/topics/azure-maps.html

**Como faço um pedido de recurso?**

Faça um pedido de funcionalidade no nosso site de voz do utilizador: https://feedback.azure.com/forums/909172-azure-maps

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos para obter mais dicas sobre como melhorar a experiência do utilizador na sua aplicação.

> [!div class="nextstepaction"]
> [Tornar a sua aplicação acessível](map-accessibility.md)

Saiba mais sobre a terminologia utilizada pelo Azure Maps e pela indústria geoespacial.

> [!div class="nextstepaction"]
> [Glossário do Azure Maps](glossary.md)
