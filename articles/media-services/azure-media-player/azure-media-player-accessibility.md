---
title: Acessibilidade do Azure Media Player
description: Saiba mais sobre as definições de acessibilidade do Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/05/2021
ms.openlocfilehash: 26e25f5391524020ef7968ba627997ea16a80921
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449907"
---
# <a name="accessibility"></a>Acessibilidade #

O Azure Media Player trabalha com capacidades de leitor de ecrãs como Windows Narrator e Apple OSX/iOS VoiceOver. Estão disponíveis tags alternativas para os botões de UI, e o leitor de ecrã é capaz de ler estas tags alternativas quando o utilizador navega para eles. Configurações adicionais podem ser definidas ao nível do Sistema Operativo.

## <a name="captions-and-subtitles"></a>Legendas e legendas ##

Neste momento, o Azure Media Player suporta atualmente legendas para apenas eventos on-demand com formato WebVTT e eventos ao vivo usando CEA 708. O formato TTML não é suportado atualmente. As legendas permitem ao leitor atender e capacitar um público mais amplo, incluindo pessoas com incapacidades auditivas ou pessoas que querem ler num idioma diferente. As legendas também aumentam a interação com o vídeo, melhoram a compreensão e tornam possível ver o vídeo em ambientes sensíveis a som, como um local de trabalho.  

## <a name="high-contrast-mode"></a>Modo de alto contraste ##

O UI predefinido no Azure Media Player está em conformidade com a maioria dos modos de visualização de alto contraste do dispositivo/navegador. As configurações podem ser definidas ao nível do Sistema Operativo.

## <a name="mobility-options"></a>Opções de mobilidade ##

### <a name="tabbing-focus"></a>Foco de tabbing ###

O foco de tabbing, fornecido pelas normas gerais html, está disponível no Azure Media Player. Para ativar a focagem do separador, deve adicionar `tabindex=0` (ou outro valor se entender como a encomenda do separador é afetada em HTML) ao HTML `<video>` assim: `<video ... tabindex=0>...</video>` . Em algumas plataformas, o foco para os controlos só pode estar presente se os controlos forem visíveis e se a plataforma suportar estas capacidades.

Uma vez ativado o foco de tabbing, o utilizador final pode navegar e controlar eficazmente o leitor de vídeo sem depender do rato. Cada menu de contexto ou elemento controlável pode ser navegado através do botão do separador e selecionado com entrada ou barra espacial. Ao entrar ou à barra de espaço num menu de contexto irá expandi-lo para que o utilizador final possa continuar a fazer a sua escolha para selecionar um item de menu. Assim que tiver o contexto do item que pretende selecionar, prima enter ou a barra de espaço novamente para concluir a seleção.

### <a name="hotkeys"></a>HotKeys ###

O Azure Media Player suporta o controlo através da tecla quente do teclado. Num navegador web, a única forma de controlar o elemento de vídeo subjacente é focando-se no jogador. Uma vez que haja foco no leitor, a chave quente pode controlar a funcionalidade do jogador.  A tabela abaixo descreve as várias chaves de acesso e o comportamento associado:

| Chave de acesso              | Comportamento                                                                |
|----------------------|-------------------------------------------------------------------------|
| F/f                  | O leitor irá entrar/sair do modo FullScreen                                  |
| M/m                  | O volume de leitor irá ativar/desativar o som                                          |
| Seta Para Cima e Para Baixo    | O volume do leitor irá aumentar/diminuir                                    |
| Seta Para a Esquerda e Para a Direita | O progresso do vídeo irá aumentar/diminuir                                  |
| 0,1,2,3,4,5,6,7,8,9  | O progresso do vídeo será alterado para 0% \- 90% dependendo da tecla pressionada |
| Clique em Ação         | O vídeo irá reproduzir/pausar                                                   |

## <a name="next-steps"></a>Passos seguintes

<!---Some context for the following links goes here--->
- [Azure Media Player Quickstart](azure-media-player-quickstart.md)