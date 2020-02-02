---
title: Criar um aplicativo de mapa acessível com o Azure Maps | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá a criar um aplicativo com recursos de acessibilidade usando mapas de Microsoft Azure.
services: azure-maps
author: rbrundritt
ms.author: richbrun
ms.date: 12/10/2019
ms.topic: conceptual
ms.service: azure-maps
manager: cpendleton
ms.openlocfilehash: e298aad6dc43f85f5b6c344eec56f5d8b37980d4
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76933310"
---
# <a name="building-an-accessible-application"></a>Criando um aplicativo acessível

Mais de 20% dos internautas têm necessidade de aplicações web acessíveis. Como tal, é importante certificar-se de que a sua aplicação foi concebida de modo a que qualquer utilizador possa utilizá-la facilmente. Em vez de pensar na acessibilidade como um conjunto de tarefas a serem concluídas, pense nisso como parte da experiência geral do usuário. Quanto mais acessível for seu aplicativo, mais pessoas poderão usá-lo. 

Quando se trata de conteúdo interativo avançado como um mapa, algumas considerações de acessibilidade comuns são:
- Dê suporte ao leitor de tela para usuários que têm dificuldade em ver o aplicativo Web.
- Ter vários métodos para interagir com e navegar pelo aplicativo Web, como mouse, toque e teclado.
- Verifique se o contraste de cores é que as cores não se misturam e se tornam difíceis de distinguir umas das outras. 

O SDK da Web do Azure Maps vem precompilado com muitos recursos de acessibilidade, como:
- Descrições do leitor de tela quando o mapa se move e quando o usuário se concentra em um controle ou pop-up.
- Suporte ao mouse, ao toque e ao teclado.
- Suporte de contraste de cores acessível no estilo de mapa de estrada.

Detalhes completos de conformidade de acessibilidade para todos os produtos da Microsoft podem ser encontrados [aqui](https://cloudblogs.microsoft.com/industry-blog/government/2018/09/11/accessibility-conformance-reports/). Pesquise "Azure Maps Web" para localizar o documento especificamente para o SDK da Web do Azure Maps. 

## <a name="navigating-the-map"></a>Navegando no mapa
Há várias maneiras diferentes pelas quais o mapa pode ser ampliado, panorâmico, girado e com densidade. Os detalhes a seguir são todas as diferentes maneiras de navegar no mapa.

**Aplicar zoom ao mapa**
- Usando um mouse, clique duas vezes no mapa para aplicar zoom em um nível.
- Usando um mouse, role a roda para aplicar zoom no mapa.
- Usando uma tela sensível ao toque, toque o mapa com dois dedos e aperte-o para reduzir ou espalhar os dedos para ampliar.
- Usando uma tela sensível ao toque, toque duas vezes no mapa para ampliar um nível.
- Com o mapa focalizado, use o sinal de adição (`+`) ou * sinal de igualdade (`=`) para aplicar zoom em um nível.
- Com o mapa focado, use o sinal De Sem, Hyphen (`-`) ou Sublinhar (`_`) para ampliar um nível.
- Usando o controle de zoom com uma tecla de mouse, toque ou teclado/Insira as teclas.
- Pressione e segure o botão `Shift` e pressione o botão esquerdo do mouse para baixo no mapa e arraste para desenhar uma área para aplicar zoom no mapa.

**Deslocar o mapa**
- Usando um mouse, pressione o botão esquerdo do mouse no mapa e arraste em qualquer direção.
- Usando uma tela sensível ao toque, toque no mapa e arraste em qualquer direção.
- Com o mapa focalizado, use as teclas de direção para mover o mapa.

**Girar o mapa**
- Usando um mouse, pressione o botão direito do mouse no mapa e arraste para a esquerda ou para a direita. 
- Usando uma tela sensível ao toque, toque no mapa com dois dedos e girar.
- Com o mapa focalizado, use a tecla Shift e as teclas de seta para a esquerda ou para a direita.
- Usando o controle de rotação com uma tecla de mouse, toque ou teclado/insira chaves.

**Pitch do mapa**
- Usando o mouse, pressione o botão direito do mouse no mapa e arraste para cima ou para baixo. 
- Usando uma tela sensível ao toque, toque o mapa com dois dedos e arraste-os para cima ou para baixo juntos.
- Com o mapa focalizado, use a tecla Shift mais as teclas de seta para cima ou para baixo. 
- Usando o controle de densidade com uma tecla de mouse, toque ou teclado/insira chaves.

**Alterar o estilo do mapa** Nem todos os desenvolvedores desejarão que todos os estilos de mapa possíveis estejam disponíveis em seu aplicativo. O desenvolvedor pode definir e alterar de forma programática o estilo de mapa conforme desejado. Se o desenvolvedor exibir o controle do seletor de estilo de mapa, o usuário poderá alterar o estilo de mapa usando o mouse, toque ou o teclado usando as teclas Tab/Enter. O desenvolvedor pode especificar quais estilos de mapa eles desejam disponibilizar no controle do seletor de estilo do mapa. 

## <a name="keyboard-shortcuts"></a>Atalhos de teclado

O mapa tem vários atalhos de teclado internos que facilitam o uso do mapa. Esses atalhos de teclado funcionam quando o mapa tem foco.

| Chave      | Ação                            |
|----------|-----------------------------------|
| `Tab` | Navegue pelos controles e pop-ups no mapa. |
| `ESC` | Mova o foco de qualquer elemento no mapa para o elemento de mapa de nível superior. |
| `Ctrl` + `Shift` + `D` | Alternar nível de detalhe do leitor de tela.  |
| Chave de seta esquerda | Deslocar o mapa para a esquerda 100 pixels |
| Chave de seta direita | Deslocar o mapa para a direita 100 pixels |
| Tecla de seta para baixo | Deslocar o mapa para baixo 100 pixels |
| Tecla de seta para cima | Deslocar o mapa para cima em 100 pixels |
| `Shift` + seta para cima | Aumentar a densidade do mapa em 10 graus |
| `Shift` + seta para baixo | Diminuir o tom do mapa por 10 graus |
| `Shift` + seta para a direita | Girar o mapa 15 graus no sentido horário |
| `Shift` + seta para a esquerda | Girar o mapa 15 graus no sentido anti-horário |
| Sinal de adição (`+`) ou <sup>*</sup>sinal de igual (`=`) | Zoom dentro |
| Menos sinal, Hífen (`-`) ou <sup>sublinhado*</sup>(`_`) | Zoom para fora | 
| arrastar `Shift` + mouse sobre o mapa para a área de desenho | Ampliar área |

<sup>*</sup> Esses atalhos-chave geralmente compartilham a mesma chave em um teclado. Estes atalhos foram adicionados para melhorar a experiência do utilizador. Também não importa se o utilizador usa a chave de turno ou não para estes atalhos.

## <a name="screen-reader-support"></a>Suporte ao leitor de tela

Os usuários podem navegar pelo mapa usando o teclado. Se um leitor de tela estiver em execução, o mapa notificará o usuário sobre as alterações em seu estado. Por exemplo, os usuários são notificados sobre alterações de mapa quando o mapa é panorâmico ou ampliado. Por padrão, o mapa fornece descrições simplificadas que excluem o nível de zoom e as coordenadas do centro do mapa. O usuário pode ativar/desativar o nível de detalhe dessas descrições usando o corte rápido do teclado `Ctrl` + `Shift` + `D`.

Todas as informações adicionais que são colocadas no mapa base devem ter informações textuais correspondentes para os usuários do leitor de tela. Certifique-se de adicionar os atributos de [Aria (aplicativos de Internet avançados)](https://www.w3.org/WAI/standards-guidelines/aria/), ALT e título acessíveis quando apropriado. 

## <a name="make-popups-keyboard-accessible"></a>Tornar o teclado de pop-ups acessível

Um marcador ou símbolo geralmente é usado para representar um local no mapa. Informações adicionais sobre o local normalmente são exibidas em um pop-up quando o usuário interage com o marcador. Na maioria das aplicações surgem popups quando um utilizador clica ou toca num marcador, no entanto este evento requer que o utilizador utilize um rato ou um ecrã tátil. Uma boa prática é tornar os pop-ups acessíveis ao usar um teclado. Esta funcionalidade pode ser conseguida através da criação de um pop-up para cada ponto de dados e adicionando-a ao mapa. 

O exemplo a seguir carrega pontos de interesse no mapa usando uma camada de símbolo e adiciona um pop-up ao mapa para cada ponto de interesse. Uma referência a cada popup é armazenada nas propriedades de cada ponto de dados. Também pode ser recuperado para um marcador, como quando um marcador é clicado. Quando focalizado no mapa, pressionar a tecla Tab permitirá que o usuário percorra cada pop-up no mapa.

<br/>

<iframe height='500' scrolling='no' title='Tornar um aplicativo acessível' src='//codepen.io/azuremaps/embed/ZoVyZQ/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a caneta <a href='https://codepen.io/azuremaps/pen/ZoVyZQ/'>tornar um aplicativo acessível</a> pelo mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>. </iframe>

<br/>

## <a name="additional-accessibility-tips"></a>Dicas adicionais de acessibilidade

Aqui ficam algumas dicas adicionais para tornar a sua aplicação de web mapping mais acessível.

- Se apresentar muitos dados de pontos interativos no mapa, considere reduzir a desordem e use o agrupamento. 
- Verifique se a taxa de contraste de cores entre texto/símbolos e cores de plano de fundo é 4.5:1 ou mais.
- Mantenha suas mensagens de leitor de tela (atributos do ARIA, ALT e title) curtas, descritivas e significativas. Evite um jargão e acrônimos desnecessários.
- Tente otimizar as mensagens enviadas para o leitor de tela para fornecer informações significativas que sejam fáceis para o usuário resumir. Por exemplo, se quiser atualizar o leitor de ecrã a uma alta frequência, como quando o mapa está em movimento, considere fazer os seguintes pontos:
    - Aguarde até que o mapa termine de migrar para atualizar o leitor de tela.
    - Limite as atualizações para uma vez a cada segundos. 
    - Combine mensagens de maneira lógica. 
- Evite utilizar a cor como único meio de transmitir informação. Use texto, ícones ou padrões para complementar ou substituir a cor. Algumas considerações:
    - Se estiver usando uma camada de bolha para mostrar o valor relativo entre os pontos de dados, considere dimensionar o raio de cada bolha, além de ou como uma alternativa para Colorá-los. 
    - Considere usar uma camada de símbolo com ícones diferentes para categorias de métrica diferentes, como triângulos, estrelas e quadrados. A camada de símbolo também dá suporte ao dimensionamento do tamanho do ícone. Um rótulo de texto também pode ser exibido.
    - Ao exibir dados de linha, a largura pode ser usada para representar peso ou tamanho. Um padrão de matriz Dash pode ser usado para representar diferentes categorias de linhas. Uma camada de símbolo pode ser usada em combinação com uma linha para sobrepor ícones ao longo da linha. O uso de um ícone de seta é útil para mostrar o fluxo ou a direção da linha.
    - Se exibir dados de polígono, um padrão, como faixas, pode ser usado como uma alternativa à cor. 
- Algumas visualizações, como mapas de calor, camadas de azulejos e camadas de imagem não são acessíveis para utilizadores com deficiências visuais. Algumas considerações:
    - Faça com que o leitor de tela Descreva o que a camada está exibindo quando adicionada ao mapa. Por exemplo, se uma camada de bloco de radar de clima for exibida, faça com que o leitor de tela diga algo como "dados de radar de clima sobrepostos no mapa".
- Limite a quantidade de funcionalidade que exige um foco do mouse. Estas funcionalidades serão inacessíveis aos utilizadores que estejam a utilizar um teclado ou dispositivo touch para interagir com a sua aplicação. Note-se que ainda é uma boa prática ter um estilo hover para conteúdo interativo, como ícones, links e botões clicáveis.
- Tente navegar pelo seu aplicativo usando o teclado. Verifique se a ordem de tabulação é lógica.
- Se estiver criando atalhos de teclado, Tente limitá-lo para duas chaves ou menos. 

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre acessibilidade nos módulos do SDK da Web.

> [!div class="nextstepaction"]
> [Acessibilidade das ferramentas de desenho](drawing-tools-interactions-keyboard-shortcuts.md)

Saiba mais sobre como desenvolver aplicativos acessíveis com o Microsoft Learn:

> [!div class="nextstepaction"]
> [Caminho de aprendizado de notificação digital de acessibilidade em ação](https://ready.azurewebsites.net/learning/track/2940)

Dê uma olhada nessas ferramentas de acessibilidade úteis:
> [!div class="nextstepaction"]
> [Desenvolvendo aplicativos acessíveis](https://developer.microsoft.com/windows/accessible-apps)

> [!div class="nextstepaction"]
> [WAI-visão geral do ARIA](https://www.w3.org/WAI/standards-guidelines/aria/)

> [!div class="nextstepaction"]
> [ONDA (ferramenta de avaliação de acessibilidade da Web)](https://wave.webaim.org/)

> [!div class="nextstepaction"]
> [Verificador de contraste de cores WebAim](https://webaim.org/resources/contrastchecker/)

> [!div class="nextstepaction"]
> [Não há simulador de visão de café](https://chrome.google.com/webstore/detail/nocoffee/jjeeggmbnhckmgdhmgdckeigabjfbddl?hl=en-US)
