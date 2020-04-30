---
title: Acessibilidade do jogador azure media
description: Saiba mais sobre as definições de acessibilidade do Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 2231c2969bbfce1668002ad4f5f719e0b8e13de5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81726558"
---
# <a name="accessibility"></a>Acessibilidade #

O Azure Media Player trabalha com capacidades de leitor de ecrã, como windows narrador e Apple OSX/iOS VoiceOver. As etiquetas alternativas estão disponíveis para os botões UI, e o leitor de ecrã é capaz de ler estas etiquetas alternativas quando o utilizador navega para eles. Configurações adicionais podem ser definidas ao nível do Sistema Operativo.

## <a name="captions-and-subtitles"></a>Legendas e legendas ##

Neste momento, o Azure Media Player suporta atualmente legendas para apenas eventos On-Demand com formato WebVTT e eventos ao vivo usando CEA 708. O formato TTML não é suportado atualmente. As legendas permitem ao leitor atender e capacitar um público mais amplo, incluindo pessoas com incapacidades auditivas ou pessoas que querem ler num idioma diferente. As legendas também aumentam a interação com o vídeo, melhoram a compreensão e tornam possível ver o vídeo em ambientes sensíveis a som, como um local de trabalho.  

## <a name="high-contrast-mode"></a>Modo de alto contraste ##

O UI padrão no Azure Media Player está em conformidade com a maioria dos modos de visão de alto contraste de dispositivo/navegador. As configurações podem ser definidas ao nível do Sistema Operativo.

## <a name="mobility-options"></a>Opções de mobilidade ##

### <a name="tabbing-focus"></a>Foco de tabbing ###

O foco tabbing, fornecido pelos padrões gerais html, está disponível no Azure Media Player. Para ativar a focalação do `tabindex=0` separador, deve adicionar (ou outro valor se `<video>` entender como `<video ... tabindex=0>...</video>`a encomenda de separadores é afetada em HTML) ao HTML como o mesmo: . Em algumas plataformas, o foco para os controlos só pode estar presente se os controlos estiverem visíveis e se a plataforma suportar essas capacidades.

Uma vez ativado o foco de tabbing, o utilizador final pode navegar e controlar eficazmente o leitor de vídeo sem depender do rato. Cada menu de contexto ou elemento controlável pode ser navegado ao premir o botão do separador e selecionado com a barra de entrada ou espaço. Bater no enter ou na barra espacial num menu de contexto irá expandi-lo para que o utilizador final possa continuar a mexer para selecionar um item de menu. Assim que tiver o contexto do item que pretende selecionar, prima enter ou a barra de espaço novamente para concluir a seleção.

### <a name="hotkeys"></a>HotKeys ###

O Azure Media Player suporta o controlo através da tecla quente do teclado. Num navegador web, a única forma de controlar o elemento de vídeo subjacente é focando-se no leitor. Uma vez que há foco no leitor, a chave quente pode controlar a funcionalidade do jogador.  A tabela abaixo descreve as várias chaves de acesso e o comportamento associado:

| Chave de acesso              | Comportamento                                                                |
|----------------------|-------------------------------------------------------------------------|
| F/f                  | O leitor irá entrar/sair do modo FullScreen                                  |
| M/m                  | O volume de leitor irá ativar/desativar o som                                          |
| Seta Para Cima e Para Baixo    | O volume do leitor irá aumentar/diminuir                                    |
| Seta Para a Esquerda e Para a Direita | O progresso do vídeo irá aumentar/diminuir                                  |
| 0,1,2,3,4,5,6,7,8,9  | O progresso do vídeo\- será alterado para 0% 90% dependendo da chave pressionada |
| Clique em Ação         | O vídeo irá reproduzir/pausar                                                   |

## <a name="next-steps"></a>Passos seguintes

<!---Some context for the following links goes here--->
- [Azure Media Player Quickstart](azure-media-player-quickstart.md)