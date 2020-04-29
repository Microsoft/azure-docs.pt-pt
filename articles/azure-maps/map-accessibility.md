---
title: Criar uma aplicação de mapa acessível com o Azure Maps [ Microsoft Azure Maps
description: Neste artigo, você vai aprender a construir uma aplicação com funcionalidades de acessibilidade usando o Microsoft Azure Maps.
services: azure-maps
author: rbrundritt
ms.author: richbrun
ms.date: 12/10/2019
ms.topic: conceptual
ms.service: azure-maps
manager: cpendleton
ms.openlocfilehash: 5fa9e159fa0ac76ce8c585936059fb7f3151c7c4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79473324"
---
# <a name="building-an-accessible-application"></a>Construção de uma aplicação acessível

Mais de 20% dos internautas têm necessidade de aplicações web acessíveis. Como tal, é importante certificar-se de que a sua aplicação foi concebida de modo a que qualquer utilizador possa utilizá-la facilmente. Em vez de pensar na acessibilidade como um conjunto de tarefas para completar, pense nela como parte da sua experiência geral de utilizador. Quanto mais acessível for a sua aplicação, mais pessoas podem usá-la. 

Quando se trata de conteúdo interativo rico como um mapa, algumas considerações comuns de acessibilidade são:
- Apoie o leitor de ecrã para utilizadores que tenham dificuldade em ver a aplicação web.
- Tenha vários métodos para interagir e navegar na aplicação web, como rato, toque e teclado.
- Certifique-se de que o contraste de cores é tal que as cores não se misturam e se tornam difíceis de distinguir umas das outras. 

O Azure Maps Web SDK vem pré-construído com muitas funcionalidades de acessibilidade tais como:
- Verime as descrições do leitor quando o mapa se move e quando o utilizador se concentra num controlo ou pop-up.
- Rato, toque e suporte ao teclado.
- Suporte de contraste de cor acessível no estilo do roteiro.

Detalhes completos de conformidade de acessibilidade para todos os produtos da Microsoft podem ser encontrados [aqui](https://cloudblogs.microsoft.com/industry-blog/government/2018/09/11/accessibility-conformance-reports/). Procure "Azure Maps web" para encontrar o documento especificamente para o Azure Maps Web SDK. 

## <a name="navigating-the-map"></a>Navegar no mapa

Existem várias maneiras diferentes em que o mapa pode ser zoomed, panned, rodado e arremessado. Os seguintes detalhes são todas as diferentes formas de navegar no mapa.

**Zoom o mapa**

- Utilizando um rato, clique duas vezes no mapa para ampliar num só nível.
- Usando um rato, percorra a roda para ampliar o mapa.
- Utilizando um ecrã tátil, toque no mapa com dois dedos e belisque-o para ampliar ou espalhar os dedos para ampliar.
- Utilizando um ecrã tátil, toque duas vezes no mapa para ampliar num só nível.
- Com o mapa focado, utilize`+`o sinal Plus`=`( ) ou igual a igual para ampliar num só nível.
- Com o mapa focado, use o sinal`-`De Sem, Hyphen ( ou Sublinhado )`_`para ampliar um nível.
- Utilizando o controlo de zoom com um separador de rato, toque ou teclado/introduza teclas.
- Pressione e `Shift` segure o botão e pressione o botão do rato esquerdo para baixo no mapa e arraste para desenhar uma área para ampliar o mapa.

**Pan o mapa**

- Utilizando um rato, pressione para baixo com o botão do rato esquerdo no mapa e arraste em qualquer direção.
- Utilizando um ecrã tátil, toque no mapa e arraste em qualquer direção.
- Com o mapa focado, use as teclas de seta para mover o mapa.

**Rode o mapa**

- Utilizando um rato, pressione para baixo com o botão do rato direito no mapa e arraste para a esquerda ou para a direita. 
- Utilizando um ecrã tátil, toque no mapa com dois dedos e rode.
- Com o mapa focado, utilize a chave de mudança e as teclas de seta esquerda ou direita.
- Utilizando o controlo de rotação com um separador de rato, toque ou teclado/introduza teclas.

**Lançar o mapa**

- Utilizando o rato, pressione para baixo com o botão do rato certo no mapa e arraste para cima ou para baixo. 
- Utilizando um ecrã tátil, toque no mapa com dois dedos e arraste-os juntos para cima ou para baixo.
- Com o mapa focado, utilize a tecla de mudança mais as teclas de seta para cima ou para baixo. 
- Utilizando o controlo de arremesso com um rato, toque ou teclado/teclas de entrada.

## <a name="change-the-map-style"></a>Alterar o estilo do mapa

Nem todos os desenvolvedores querem que todos os estilos de mapas possíveis estejam disponíveis na sua aplicação. Se o desenvolvedor apresentar o controlo do apanhador de estilo do mapa, o utilizador poderá alterar o estilo do mapa utilizando o rato, um toque ou o teclado com o separador ou a tecla de entrada. O desenvolvedor pode especificar quais os estilos de mapa que querem disponibilizar no controlo do picker de estilo do mapa. Além disso, o desenvolvedor pode definir programáticamente e alterar o estilo do mapa.

**Utilizar alto contraste**

- Quando o controlo do mapa é carregado, verifica se o alto contraste está ativado e o navegador suporta-o.
- O controlo do mapa não monitoriza o modo de contraste elevado do dispositivo. Se o modo de dispositivo mudar, o mapa não. Assim, o utilizador terá de recarregar o mapa refrescando a página.
- Quando for detetado um contraste elevado, o estilo do mapa mudará automaticamente para um contraste elevado, e todos os controlos incorporados usarão um estilo de alto contraste. Por exemplo, zoomControl, PitchControl, CompassControl, StyleControl e outros controlos incorporados, usarão um estilo de alto contraste.
- Há dois tipos de alto contraste, luz e escuro. Se o tipo de alto contraste pode ser detetado pelos controlos do mapa, então o comportamento do mapa irá ajustar-se em conformidade. Se for leve, o grayscale_light estilo mapa será carregado. Se o tipo não puder ser detetado ou estiver escuro, o estilo high_contrast_dark será carregado.
- Se criar controlos personalizados, é útil saber se os controlos incorporados estão usando um estilo de alto contraste. Os desenvolvedores podem adicionar uma classe css no div de contentores do mapa para verificar. As classes css que `high-contrast-dark` seriam `high-contrast-light`adicionadas são e . Para verificar a utilização do JavaScript, utilize:

```javascript
map.getMapContainer().classList.contains("high-contrast-dark")
```

ou, use:

```javascript
map.getMapContainer().classList.contains("high-contrast-light")
```

## <a name="keyboard-shortcuts"></a>Atalhos de teclado

O mapa tem uma série de atalhos de teclado incorporados que facilitam a utilização do mapa. Estes atalhos de teclado funcionam quando o mapa tem foco.

| Chave      | Ação                            |
|----------|-----------------------------------|
| `Tab` | Navegue pelos controlos e popups no mapa. |
| `ESC` | Mova o foco de qualquer elemento no mapa para o elemento de mapa de nível superior. |
| `Ctrl` + `Shift` + `D` | Alternar o nível de detalhe do leitor de ecrã.  |
| Tecla Seta Para a Esquerda | Pan o mapa deixou 100 pixels |
| Tecla Seta Para a Direita | Pan o mapa direito 100 pixels |
| Tecla de seta para baixo | Pan o mapa para baixo 100 pixels |
| Chave de seta para cima | Pan o mapa até 100 pixels |
| `Shift`+ seta para cima | Aumentar o tom do mapa em 10 graus |
| `Shift`+ seta para baixo | Diminuir o tom do mapa em 10 graus |
| `Shift`+ seta direita | Rode o mapa 15 graus no sentido horário |
| `Shift`+ seta esquerda | Rode o mapa 15 graus no sentido contrário ao dos ponteiros do relógio |
| Sinal mais`+`() ou <sup>*</sup>`=`sinal igual ( ) | Ampliar |
| Menos sinal, Hífen (),`-`ou <sup>*</sup>Sublinhado (`_`) | Reduzir | 
| `Shift`+ arrasto de rato no mapa para desenhar área | Zoom para a área |

<sup>*</sup>Estes atalhos principais geralmente partilham a mesma chave num teclado. Estes atalhos foram adicionados para melhorar a experiência do utilizador. Também não importa se o utilizador usa a chave de turno ou não para estes atalhos.

## <a name="screen-reader-support"></a>Suporte do Leitor de Ecrã

Os utilizadores podem navegar no mapa usando o teclado. Se um leitor de ecrã estiver a funcionar, o mapa notificará o utilizador das alterações ao seu estado. Por exemplo, os utilizadores são notificados das alterações do mapa quando o mapa é panned ou zoomed. Por padrão, o mapa fornece descrições simplificadas que excluem o nível de zoom e as coordenadas do centro do mapa. O utilizador pode alternar o nível de detalhe `Ctrl`  +  `Shift`  + destas descrições utilizando o atalho `D`do teclado .

Qualquer informação adicional que seja colocada no mapa base deve ter informações texuais correspondentes para os utilizadores de leitores de ecrã. Certifique-se de adicionar aplicações de [Internet ricas acessíveis (ARIA),](https://www.w3.org/WAI/standards-guidelines/aria/)alt e atributos de título, sempre que apropriado. 

## <a name="make-popups-keyboard-accessible"></a>Tornar o teclado popups acessível

Um marcador ou símbolo é frequentemente usado para representar uma localização no mapa. Informações adicionais sobre a localização são normalmente exibidas num popup quando o utilizador interage com o marcador. Na maioria das aplicações, surgem popups quando um utilizador clica ou toca num marcador. No entanto, clicar e tocar requer que o utilizador utilize um rato e um ecrã tátil, respectivamente. Uma boa prática é tornar os popups acessíveis quando se usa um teclado. Esta funcionalidade pode ser conseguida através da criação de um pop-up para cada ponto de dados e adicionando-a ao mapa. 

O exemplo seguinte carrega pontos de interesse no mapa usando uma camada de símbolo e adiciona um popup ao mapa para cada ponto de interesse. Uma referência a cada popup é armazenada nas propriedades de cada ponto de dados. Também pode ser recuperado para um marcador, como quando um marcador é clicado. Quando focado no mapa, premir a tecla do separador permitirá ao utilizador passar por cada pop-up no mapa.

<br/>

<iframe height='500' scrolling='no' title='Faça uma aplicação acessível' src='//codepen.io/azuremaps/embed/ZoVyZQ/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Ver a caneta <a href='https://codepen.io/azuremaps/pen/ZoVyZQ/'>Faça uma aplicação acessível</a> pelo Azure Maps ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>no <a href='https://codepen.io'>CodePen</a>. </iframe>

<br/>

## <a name="additional-accessibility-tips"></a>Dicas adicionais de acessibilidade

Aqui ficam algumas dicas adicionais para tornar a sua aplicação de web mapping mais acessível.

- Se apresentar muitos dados de pontos interativos no mapa, considere reduzir a desordem e use o agrupamento. 
- Certifique-se de que a relação de contraste de cor entre texto/símbolos e cores de fundo é de 4.5:1 ou mais.
- Mantenha as mensagens curtas, descritivas e significativas do leitor de ecrã (ARIA, alt e title). Evite jargão e siglas desnecessárias.
- Tente otimizar as mensagens enviadas ao leitor de ecrã para fornecer informações curtas e significativas que são fáceis de digerir para o utilizador. Por exemplo, se quiser atualizar o leitor de ecrã a uma alta frequência, como quando o mapa está em movimento, considere fazer os seguintes pontos:
    - Espere até que o mapa termine de se mover para atualizar o leitor de ecrã.
    - Acelere as atualizações para uma vez a cada poucos segundos. 
    - Combine mensagens de uma forma lógica. 
- Evite utilizar cores como a única forma de transmitir informações. Utilize texto, ícones ou padrões para complementar ou substituir a cor. Algumas considerações:
    - Se utilizar uma camada de bolha para mostrar o valor relativo entre os pontos de dados, considere escalar o raio de cada bolha, colorindo a bolha, ou ambos. 
    - Considere usar uma camada de símbolo com diferentes ícones para diferentes categorias métricas, tais como triângulos, estrelas e quadrados. A camada de símbolo também suporta a escala do tamanho do ícone. Também pode ser apresentada uma etiqueta de texto.
    - Se apresentar dados da linha, a largura pode ser utilizada para representar peso ou tamanho. Um padrão de matriz de traços pode ser usado para representar diferentes categorias de linhas. Uma camada de símbolo pode ser usada em combinação com uma linha para sobrepor ícones ao longo da linha. A utilização de um ícone de seta é útil para mostrar o fluxo ou direção da linha.
    - Se exibir dados de polígono, um padrão, como listras, pode ser usado como alternativa à cor. 
- Algumas visualizações, como mapas de calor, camadas de azulejos e camadas de imagem não são acessíveis para utilizadores com deficiências visuais. Algumas considerações:
    - O leitor de ecrã descreve o que a camada está a mostrar quando adicionada ao mapa. Por exemplo, se for apresentada uma camada de azulejos de radar meteorológico, então peça ao leitor de ecrã "Os dados do radar meteorológico estão sobrepostos no mapa."
- Limite a quantidade de funcionalidade que requer um paira do rato. Estas funcionalidades serão inacessíveis aos utilizadores que estejam a utilizar um teclado ou dispositivo touch para interagir com a sua aplicação. Note-se que ainda é uma boa prática ter um estilo hover para conteúdo interativo, como ícones, links e botões clicáveis.
- Tente navegar na sua aplicação utilizando o teclado. Certifique-se de que a encomenda do separador é lógica.
- Se criar atalhos de teclado, tente limitá-lo a duas teclas ou menos. 

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a acessibilidade nos módulos Web SDK.

> [!div class="nextstepaction"]
> [Acessibilidade de ferramentas de desenho](drawing-tools-interactions-keyboard-shortcuts.md)

Saiba mais sobre o desenvolvimento de aplicações acessíveis com o Microsoft Learn:

> [!div class="nextstepaction"]
> [Acessibilidade no Caminho de Aprendizagem de Crachá Digital de Ação](https://ready.azurewebsites.net/learning/track/2940)

Veja estas ferramentas de acessibilidade úteis:
> [!div class="nextstepaction"]
> [Desenvolvimento de aplicações acessíveis](https://developer.microsoft.com/windows/accessible-apps)

> [!div class="nextstepaction"]
> [Visão geral da WAI-ARIA](https://www.w3.org/WAI/standards-guidelines/aria/)

> [!div class="nextstepaction"]
> [Ferramenta de Avaliação de Acessibilidade Web (WAVE)](https://wave.webaim.org/)

> [!div class="nextstepaction"]
> [Verificador de contraste de cor WebAim](https://webaim.org/resources/contrastchecker/)

> [!div class="nextstepaction"]
> [Sem simulador de visão de café](https://chrome.google.com/webstore/detail/nocoffee/jjeeggmbnhckmgdhmgdckeigabjfbddl?hl=en-US)
