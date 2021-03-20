---
title: Azure Media Player Problemas Conhecidos
description: O lançamento atual tem os seguintes problemas conhecidos.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 05/11/2020
ms.openlocfilehash: a31c089971b7e70e70c5906480deb2b17c197b9f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87043617"
---
# <a name="known-issues"></a>Problemas Conhecidos #

O lançamento atual tem as seguintes questões conhecidas:

## <a name="azure-media-player"></a>Media Player do Azure ##

- Codificadores configurados incorretamente podem causar problemas com reprodução
- Os streams com tempotamps superiores a 2^53 podem ter problemas de reprodução.
  - Mitigação: Use vídeo de 90 kHz e escalas de tempo de áudio de 44,1 kHz
- Sem reprodução automática em dispositivos móveis sem interação do utilizador. Está bloqueada pela plataforma.
- Procurar perto de descontinuidades pode causar falhas de reprodução.
- O download de grandes apresentações pode fazer com que a UI seja bloqueada.
- Não pode reproduzir automaticamente a mesma fonte novamente após a apresentação terminada.
  - Para reproduzir uma fonte depois de ter terminado, é necessário definir a fonte novamente.
- Manifestos vazios podem causar problemas com o jogador.
  - Esta questão pode ocorrer quando se inicia um fluxo de transmissão ao vivo e não são encontrados pedaços suficientes no manifesto.
- Posição de reprodução talvez fora da barra de busca da UI.
- A ordem de eventos não é consistente em todos os técnicos.
- A propriedade tamponada não é consistente entre os técnicos.
- nativeControlsForTouch deve ser falso (padrão) para evitar "O objeto não suporta propriedade ou método 'setControls'"
- Os cartazes devem agora ser urls absolutos
- Pequenos problemas estéticos podem ocorrer na UI quando se utiliza o modo de alto contraste do dispositivo
- URLs que contenham "%" ou "+" na cadeia totalmente descodificada podem ter problemas em definir a fonte

## <a name="ad-insertion"></a>Inserção de anúncios ##

- Os anúncios podem ter problemas sendo inseridos (a pedido ou ao vivo) quando um bloqueador de anúncios é instalado no navegador
- Os dispositivos móveis podem ter problemas em reproduzir anúncios.
- Os anúncios MP4 Midroll não são atualmente suportados pelo Azure Media Player.

## <a name="azurehtml5js"></a>AzureHtml5JS ##

- Na janela DVR de conteúdos Live, quando o conteúdo terminar, a linha do tempo continuará a crescer até procurar a área ou chegar ao final da apresentação.
- Apresentações ao vivo no Firefox com MSE ativadas tem alguns problemas

- Os ativos que são áudio só não serão reproduzidos através da tecnologia AzureHtml5JS.
  - Se quiser reproduzir ativos sem áudio, pode fazê-lo inserindo áudio em branco utilizando a [ferramenta Azure Media Services Explorer](https://aka.ms/amse)
  - Instruções sobre como inserir áudio silencioso podem ser encontradas [aqui](../previous/media-services-advanced-encoding-with-mes.md#silent_audio)

## <a name="flash"></a>Flash ##

- O conteúdo da AES não reproduz na versão Flash 30.0.0.134 devido a um bug na lógica de caching da Adobe. A Adobe corrigiu o problema e lançou-o em 30.0.0.154
- Falhas tecnológicas e http (como 404 intervalos de rede), o jogador demorará mais tempo a recuperar do que outros técnicos.
- Clique na área de vídeo com a tecnologia flashSS não reproduz/pausa o leitor.
- Se o utilizador tiver o Flash instalado mas não autorizar a sua carga no site, pode ocorrer uma rotação infinita. Isto porque o jogador pensa que o plugin está instalado e disponível e pensa que o plugin está a executar o conteúdo. O código JavaScript foi enviado, mas as configurações do navegador bloquearam a execução do plugin até que o utilizador aceite o pedido para permitir o plugin. Isto pode ocorrer em todos os navegadores.  

## <a name="silverlight"></a>Silverlight ##

- Características em falta
- Falhas tecnológicas e http (como 404 intervalos de rede), o jogador demorará mais tempo a recuperar do que outros técnicos.
- Safari e Firefox na reprodução de Mac com Silverlight requer explicitamente definir `"http://` ou `https://` para a fonte.
- Se faltar uma API para esta tecnologia, geralmente devolverá nulo.
- Se o utilizador tiver o Flash instalado mas não autorizar a sua carga no site, pode ocorrer uma rotação infinita. Isto porque o jogador pensa que o plugin está instalado e disponível e pensa que o plugin está a executar o conteúdo. O código JavaScript foi enviado, mas as configurações do navegador bloquearam a execução do plugin até que o utilizador aceite o pedido para permitir o plugin. Isto pode ocorrer em todos os navegadores.  

## <a name="native-html5"></a>NATIVO HTML5 ##

- A tecnologia Html5 está apenas a enviar o evento canplaythrough para a primeira origem definida.
- Esta tecnologia apenas suporta o que o navegador implementou.  Algumas funcionalidades podem estar a faltar nesta tecnologia.  
- Se faltar uma API para esta tecnologia, geralmente devolverá nulo.
- Há problemas com legendas e legendas nesta tecnologia. Podem ou não estar disponíveis ou visualizadas nesta tecnologia.
- Ter uma largura de banda limitada no cenário tecnológico HLS/Html5 resulta em reprodução de áudio sem vídeo.

### <a name="microsoft"></a>Microsoft ###

- A reprodução do IE8 não funciona atualmente devido à incompatibilidade com o ECMAScript 5
- No IE e em algumas versões de Edge, o ecrã completo não pode ser introduzido batendo no botão e selecionando-o ou utilizando o hotkey F/f.

## <a name="google"></a>Google ##

- Vários perfis de codificação no mesmo manifesto têm alguns problemas de reprodução no Chrome e não são recomendados.
- O Chrome não pode reproduzir o HE-AAC com o AzureHtml5JS. Siga os detalhes no [localizador de erros](https://bugs.chromium.org/p/chromium/issues/detail?id=534301).
- A partir do Chrome v58, o conteúdo de grande variedade deve ser carregado/reproduzido através do protocolo https:// caso contrário, a reprodução falhará.

## <a name="mozilla"></a>Mozilla ##

- O interruptor de fluxo de áudio requer que os fluxos de áudio tenham os mesmos dados privados codificadores quando utilizarem O AzureHtml5JS. A plataforma Firefox requer isto.

## <a name="apple"></a>Apple ##

- O Safari no Mac permite frequentemente o modo Power Saver por padrão com a definição "Stop plug-ins para poupar energia", que bloqueia plugins como Flash e Silverlight quando acreditam que não é a favor do utilizador. Este bloco não bloqueia as capacidades existentes do plugin, apenas. Dado o padrão techOrder, isto pode causar problemas ao tentar reproduzir
  - Mitigação 1: Se o leitor de vídeo for 'frontal e central' (dentro de um limite de 3000 x 3000 pixels a partir do canto superior esquerdo do documento), ele ainda deve ser reproduzido.
  - Mitigação 2: Mude o techOrder para o Safari para ser ["azureHtml5JS", "html5"]. Esta mitigação tem implicação em não obter todas as funcionalidades que estão disponíveis na tecnologia FlashSS.
- O conteúdo do PlayReady via Silverlight pode ter problemas em reproduzir-se no Safari.
- O conteúdo de fichas AES e restrito não reproduz usando dispositivos Android iOS e mais antigos.
  - Para alcançar este cenário, deve ser adicionado um representante ao seu serviço.
- A pele padrão para iOS Telefone aparece através.
- A reprodução do iPhone ocorre sempre no ecrã completo do jogador nativo.
  - As funcionalidades, incluindo as legendas, podem não persistir nesta reprodução não-inline.
- Quando a apresentação ao vivo terminar, os dispositivos iOS não terminarão corretamente a apresentação.
- O iOS não permite capacidades de DVR.
- O switch de fluxo de áudio iOS só pode ser feito embora o iOS jogador nativo UI e exija que os streams de áudio tenham os mesmos dados privados codec
- Versões mais antigas do Safari podem potencialmente ter problemas em reproduzir streams ao vivo.

## <a name="older-android"></a>Android mais antigo ##

- O conteúdo de fichas AES e restrito não reproduz usando dispositivos Android iOS e mais antigos.
  - Para alcançar este cenário, deve ser adicionado um representante ao seu serviço.

## <a name="next-steps"></a>Passos seguintes ##

- [Azure Media Player Quickstart](azure-media-player-quickstart.md)
