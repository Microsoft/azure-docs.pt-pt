---
title: Crie uma aplicação de mapa acessível com Azure Maps | Microsoft Azure Maps
description: Conheça as considerações de acessibilidade no Azure Maps. Veja quais as funcionalidades disponíveis para tornar as aplicações do mapa acessíveis e veja as dicas de acessibilidade.
services: azure-maps
author: rbrundritt
ms.author: richbrun
ms.date: 12/10/2019
ms.topic: conceptual
ms.service: azure-maps
manager: cpendleton
ms.openlocfilehash: ec88437a0fad3a6bd94a67a5ef5c75b3e506f9e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88006218"
---
# <a name="building-an-accessible-application"></a>Construção de uma aplicação acessível

Mais de 20% dos utilizadores de internet têm necessidade de aplicações web acessíveis. Como tal, é importante garantir que a sua aplicação foi desenhada de modo a que qualquer utilizador possa facilmente usá-la. Em vez de pensar na acessibilidade como um conjunto de tarefas para completar, pense nela como parte da sua experiência geral de utilizador. Quanto mais acessível for a sua aplicação, mais pessoas a podem usar. 

Quando se trata de conteúdos interativos ricos como um mapa, algumas considerações comuns de acessibilidade são:
- Suporte o leitor de ecrã para utilizadores com dificuldades em ver a aplicação web.
- Tenha vários métodos para interagir e navegar na aplicação web, como rato, toque e teclado.
- Certifique-se de que o contraste de cor é tal que as cores não se misturam e se tornam difíceis de distinguir umas das outras. 

O Azure Maps Web SDK vem pré-construído com muitas funcionalidades de acessibilidade, tais como:
- As descrições do leitor de ecrã quando o mapa se move e quando o utilizador se concentra num controlo ou pop-up.
- Suporte ao rato, toque e teclado.
- Suporte de contraste de cor acessível no estilo de roteiro.
- Suporte de alto contraste.

Os detalhes completos da conformidade de acessibilidade para todos os produtos da Microsoft podem ser [consultados aqui.](https://cloudblogs.microsoft.com/industry-blog/government/2018/09/11/accessibility-conformance-reports/) Procure "Azure Maps web" para encontrar o documento especificamente para o Azure Maps Web SDK. 

## <a name="navigating-the-map"></a>Navegando no mapa

Existem várias maneiras diferentes em que o mapa pode ser ampliado, panned, girado e lançado. Os seguintes detalhes são todas as diferentes formas de navegar no mapa.

**Zoom o mapa**

- Usando um rato, clique duas vezes no mapa para ampliar em um nível.
- Usando um rato, percorra a roda para ampliar o mapa.
- Utilizando um ecrã táctil, toque no mapa com dois dedos e belisque-os juntos para ampliar ou espalhar os dedos para fazer zoom.
- Utilizando um ecrã táctil, toque duas vezes no mapa para ampliar num nível.
- Com o mapa focado, utilize o sinal Plus `+` () ou sinal Equals ( `=` ) para ampliar num nível.
- Com o mapa focado, use o sinal Minus, Hyphen `-` () ou Underscore `_` () para ampliar um nível.
- Utilizando o controlo de zoom com um separador/teclas de teclado/introdução de ratos.
- Pressione e segure `Shift` o botão e pressione o botão do rato esquerdo para baixo no mapa e arraste para desenhar uma área para ampliar o mapa.
- Usando algumas almofadas multi-toque, arrastando dois dedos para cima para ampliar, ou para baixo para ampliar dentro

**Pan o mapa**

- Utilizando um rato, pressione para baixo com o botão do rato esquerdo no mapa e arraste em qualquer direção.
- Utilizando um ecrã táctil, toque no mapa e arraste em qualquer direção.
- Com o mapa focado, use as setas para mover o mapa.

**Rode o mapa**

- Utilizando um rato, pressione para baixo com o botão do rato direito no mapa e arraste para a esquerda ou para a direita. 
- Utilizando um ecrã táctil, toque no mapa com dois dedos e rode.
- Com o mapa focado, use a chave de mudança e as teclas de seta esquerda ou direita.
- Utilizando o controlo de rotação com o separador/inscamo do rato, do toque ou do teclado.

**Pitch o mapa**

- Utilizando o rato, pressione para baixo com o botão do rato direito no mapa e arraste para cima ou para baixo. 
- Utilizando um ecrã táctil, toque no mapa com dois dedos e arraste-os para cima ou para baixo juntos.
- Com o mapa focado, use a chave de mudança mais as teclas de seta para cima ou para baixo. 
- Utilizando o controlo de altura com um separador de rato, toque ou teclado/teclas de entrada.

## <a name="change-the-map-style"></a>Alterar o estilo do mapa

Nem todos os desenvolvedores querem que todos os estilos de mapa possíveis estejam disponíveis na sua aplicação. Se o desenvolvedor apresentar o controlo de picker de estilo do mapa, então o utilizador pode alterar o estilo do mapa usando o rato, um toque ou o teclado com o separador ou introduzir a tecla. O desenvolvedor pode especificar quais os estilos de mapa que querem disponibilizar no controlo do picker de estilo de mapa. Além disso, o desenvolvedor pode definir programáticamente e alterar o estilo do mapa.

**Utilizar alto contraste**

- Quando o controlo do mapa é carregado, verifica se o alto contraste está ativado e o navegador o suporta.
- O controlo do mapa não monitoriza o modo de contraste elevado do dispositivo. Se o modo do dispositivo mudar, o mapa não. Assim, o utilizador terá de recarregar o mapa refrescando a página.
- Quando o alto contraste é detetado, o estilo do mapa mudará automaticamente para um contraste elevado, e todos os controlos incorporados usarão um estilo de contraste elevado. Por exemplo, ZoomControl, PitchControl, CompassControl, StyleControl e outros controlos incorporados utilizarão um estilo de contraste elevado.
- Há dois tipos de contraste alto, luz e escuridão. Se o tipo de contraste elevado puder ser detetado pelos controlos do mapa, então o comportamento do mapa ajustar-se-á em conformidade. Se a luz, então o estilo grayscale_light mapa será carregado. Se o tipo não puder ser detetado ou estiver escuro, o estilo high_contrast_dark será carregado.
- Se criar controlos personalizados, é útil saber se os controlos incorporados estão a usar um estilo de contraste elevado. Os desenvolvedores podem adicionar uma classe css no mapa de mergulho para verificar. As aulas de css que seriam adicionadas são `high-contrast-dark` `high-contrast-light` e. Para verificar a utilização do JavaScript, utilize:

```javascript
map.getMapContainer().classList.contains("high-contrast-dark")
```

ou utilizar:

```javascript
map.getMapContainer().classList.contains("high-contrast-light")
```

## <a name="keyboard-shortcuts"></a>Atalhos de teclado

O mapa tem uma série de atalhos de teclado construídos que facilitam a utilização do mapa. Estes atalhos de teclado funcionam quando o mapa tem foco.

| Chave      | Ação                            |
|----------|-----------------------------------|
| `Tab` | Navegue através dos controlos e popups no mapa. |
| `ESC` | Mova o foco de qualquer elemento do mapa para o elemento de mapa de nível superior. |
| `Ctrl` + `Shift` + `D` | Alterne o nível de detalhe do leitor de ecrã.  |
| Tecla Seta Para a Esquerda | Pan o mapa deixou 100 pixels |
| Tecla Seta Para a Direita | Pan o mapa direito 100 pixels |
| Tecla de seta para baixo | Panorâmica do mapa para baixo 100 pixels |
| Tecla de seta para cima | Pan o mapa até 100 pixels |
| `Shift` + seta para cima | Aumente o tom do mapa em 10 graus |
| `Shift` + seta para baixo | Diminuir o tom do mapa em 10 graus |
| `Shift` + seta direita | Rode o mapa 15 graus no sentido horário |
| `Shift` + seta esquerda | Rode o mapa 15 graus no sentido contrário ao dos ponteiros do relógio |
| Sinal de `+` mais () ou <sup>*</sup> sinal de Iguais `=` () | Ampliar |
| Menos sinal, Hífen `-` (), ou <sup>*</sup> Sublinhado `_` () | Reduzir | 
| `Shift` + arrasto de rato no mapa para desenhar área | Zoom na área |

<sup>*</sup> Estes atalhos-chave geralmente partilham a mesma chave num teclado. Estes atalhos foram adicionados para melhorar a experiência do utilizador. Também não importa se o utilizador usa a chave de mudança ou não para estes atalhos.

## <a name="screen-reader-support"></a>Suporte do Leitor de Ecrã

Os utilizadores podem navegar no mapa utilizando o teclado. Se um leitor de ecrã estiver em execução, o mapa notificará o utilizador de alterações ao seu estado. Por exemplo, os utilizadores são notificados das alterações do mapa quando o mapa é analisado ou ampliado. Por padrão, o mapa fornece descrições simplificadas que excluem o nível de zoom e as coordenadas do centro do mapa. O utilizador pode alternar o nível de detalhe destas descrições utilizando o atalho do teclado `Ctrl`  +  `Shift`  +  `D` .

Qualquer informação adicional colocada no mapa base deve ter informações textuais correspondentes para os utilizadores de leitores de ecrã. Certifique-se de adicionar [aplicações de Internet ricas acessíveis (ARIA)](https://www.w3.org/WAI/standards-guidelines/aria/), alt e atributos de título sempre que apropriado. 

## <a name="make-popups-keyboard-accessible"></a>Tornar o teclado popups acessível

Um marcador ou símbolo é frequentemente usado para representar uma localização no mapa. Informações adicionais sobre a localização são normalmente exibidas num pop-up quando o utilizador interage com o marcador. Na maioria das aplicações, surgem popups quando um utilizador clica ou toca num marcador. No entanto, clicar e tocar requer que o utilizador utilize um rato e um ecrã tátil, respectivamente. Uma boa prática é tornar os pop-ups acessíveis quando se utiliza um teclado. Esta funcionalidade pode ser alcançada criando um popup para cada ponto de dados e adicionando-a ao mapa. 

O exemplo a seguir carrega pontos de interesse no mapa usando uma camada de símbolo e adiciona um pop-up ao mapa para cada ponto de interesse. Uma referência a cada pop-up é armazenada nas propriedades de cada ponto de dados. Também pode ser recuperado para um marcador, como quando um marcador é clicado. Quando focado no mapa, premir a tecla do separador permitirá ao utilizador passar por cada pop-up no mapa.

<br/>

<iframe height='500' scrolling='no' title='Faça uma aplicação acessível' src='//codepen.io/azuremaps/embed/ZoVyZQ/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Pen <a href='https://codepen.io/azuremaps/pen/ZoVyZQ/'>Faça uma aplicação acessível</a> por Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>. </iframe>

<br/>

## <a name="additional-accessibility-tips"></a>Dicas de acessibilidade adicionais

Aqui ficam algumas dicas adicionais para tornar a sua aplicação de mapeamento web mais acessível.

- Se apresentar muitos dados de pontos interativos no mapa, considere reduzir a desordem e utilizar o agrupamento. 
- Certifique-se de que a relação de contraste de cor entre texto/símbolos e cores de fundo é de 4,5:1 ou mais.
- Mantenha as mensagens curtas, descritivas e significativas do leitor de ecrã (ARIA, alt e title). Evite jargão e siglas desnecessárias.
- Tente otimizar as mensagens enviadas ao leitor de ecrã para fornecer informações curtas e significativas que são fáceis de digerir para o utilizador. Por exemplo, se pretender atualizar o leitor de ecrã numa frequência elevada, como quando o mapa está em movimento, considere fazer os seguintes pontos:
    - Aguarde até que o mapa termine de se mover para atualizar o leitor de ecrã.
    - Acelerar as atualizações de uma vez a cada poucos segundos. 
    - Combine mensagens de uma forma lógica. 
- Evite utilizar cores como a única forma de transmitir informações. Utilize texto, ícones ou padrões para complementar ou substituir a cor. Algumas considerações:
    - Se utilizar uma camada de bolha para mostrar o valor relativo entre pontos de dados, considere escalar o raio de cada bolha, colorir a bolha, ou ambos. 
    - Considere usar uma camada de símbolo com diferentes ícones para diferentes categorias métricas, tais como triângulos, estrelas e quadrados. A camada de símbolo também suporta o dimensionamento do tamanho do ícone. Uma etiqueta de texto também pode ser exibida.
    - Se apresentar dados de linha, a largura pode ser utilizada para representar peso ou tamanho. Um padrão de traço-matriz pode ser usado para representar diferentes categorias de linhas. Uma camada de símbolo pode ser usada em combinação com uma linha para sobrepor ícones ao longo da linha. A utilização de um ícone de seta é útil para mostrar o fluxo ou a direção da linha.
    - Se exibir dados de polígono, um padrão, como listras, pode ser usado como uma alternativa à cor. 
- Algumas visualizações como máscaras de calor, camadas de azulejos e camadas de imagem não são acessíveis para utilizadores com deficiências visuais. Algumas considerações:
    - O leitor de ecrã descreve o que a camada está a exibir quando adicionada ao mapa. Por exemplo, se uma camada de azulejos de radar meteorológico for exibida, então o leitor de ecrã diga "Os dados do radar meteorológico estão sobrepostos no mapa."
- Limite a quantidade de funcionalidade que requer um pairar de rato. Estas funcionalidades serão inacessíveis aos utilizadores que estejam a utilizar um teclado ou dispositivo tátil para interagir com a sua aplicação. Note que ainda é uma boa prática ter um estilo hover para conteúdo interativo, como ícones, links e botões clicáveis.
- Tente navegar na sua aplicação utilizando o teclado. Certifique-se de que o pedido de separador é lógico.
- Se criar atalhos de teclado, tente limitá-lo a duas teclas ou menos. 

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a acessibilidade nos módulos Web SDK.

> [!div class="nextstepaction"]
> [Acessibilidade de ferramentas de desenho](drawing-tools-interactions-keyboard-shortcuts.md)

Saiba como desenvolver aplicações acessíveis com o Microsoft Learn:

> [!div class="nextstepaction"]
> [Acessibilidade no Caminho de Aprendizagem do Crachá Digital de Ação](https://ready.azurewebsites.net/learning/track/2940)

Veja estas ferramentas de acessibilidade úteis:
> [!div class="nextstepaction"]
> [Desenvolvimento de aplicativos acessíveis](https://developer.microsoft.com/windows/accessible-apps)

> [!div class="nextstepaction"]
> [Visão geral da WAI-ARIA](https://www.w3.org/WAI/standards-guidelines/aria/)

> [!div class="nextstepaction"]
> [Ferramenta de avaliação de acessibilidade web (WAVE)](https://wave.webaim.org/)

> [!div class="nextstepaction"]
> [Verificador de contraste de cores WebAim](https://webaim.org/resources/contrastchecker/)

> [!div class="nextstepaction"]
> [Sem Simulador de Visão de Café](https://chrome.google.com/webstore/detail/nocoffee/jjeeggmbnhckmgdhmgdckeigabjfbddl?hl=en-US)
