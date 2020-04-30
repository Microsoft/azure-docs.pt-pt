---
title: Problemas conhecidos do jogador de mídia Azure
description: O lançamento atual tem as seguintes questões conhecidas.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: ff8dc58b9122e5173a9a6065e2efdbc5697be0d7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81727219"
---
# <a name="known-issues"></a>Problemas Conhecidos #

O lançamento atual tem as seguintes questões conhecidas:

## <a name="azure-media-player"></a>Media Player do Azure ##

- Codificadores configurados incorretamente podem causar problemas com reprodução
- Os fluxos com selos temporais superiores a 2^53 podem ter problemas de reprodução.
  - Mitigação: Utilize vídeos de 90 kHz e escalas de tempo de áudio de 44,1 kHz
- Sem reprodução automática em dispositivos móveis sem interação do utilizador. Está bloqueado pela plataforma.
- Procurar quase descontinuidades pode causar falha na reprodução.
- O download de grandes apresentações pode fazer com que a UI bloqueie.
- Não se pode reproduzir automaticamente a mesma fonte depois de a apresentação ter terminado.
  - Para reproduzir uma fonte depois de ter terminado, é necessário definir a fonte novamente.
- Manifestos vazios podem causar problemas com o jogador.
  - Esta questão pode ocorrer quando se inicia um fluxo ao vivo e não se encontram pedaços suficientes no manifesto.
- Posição de reprodução talvez fora da UI seekbar.
- A encomenda de eventos não é consistente em todos os técnicos.
- Propriedade tampão não é consistente entre os técnicos.
- nativeControlsForTouch deve ser falso (padrão) para evitar "O objeto não suporta a propriedade ou o método 'setControls'"
- Os cartazes devem agora ser urls absolutos
- Problemas estéticos menores podem ocorrer na UI quando se utiliza o modo de contraste elevado do dispositivo
- UrLs que contenham "%" ou "+" na cadeia totalmente descodificada podem ter problemas em definir a fonte

## <a name="ad-insertion"></a>Inserção de anúncios ##

- Os anúncios podem ter problemas sendo inseridos (a pedido ou ao vivo) quando um bloqueador de anúncios é instalado no navegador
- Os dispositivos móveis podem ter problemas em reproduzir anúncios.

## <a name="azurehtml5js"></a>AzureHtml5JS ##

- Na janela DVR do conteúdo ao vivo, quando o conteúdo terminar a linha do tempo continuará a crescer até procurar a área ou chegar ao fim da apresentação.
- Apresentações ao vivo no Firefox com MSE ativadas tem alguns problemas
- Os ativos que são áudio ou vídeo só não reprodução ão através da tecnologia AzureHtml5JS.
  - Se quiser reproduzir ativos sem áudio ou vídeo, pode fazê-lo inserindo áudio ou vídeo em branco utilizando a [ferramenta Azure Media Services Explorer](https://aka.ms/amse)
    - Instruções sobre como inserir áudio silencioso podem ser encontradas [aqui](https://azure.microsoft.com/documentation/articles/media-services-advanced-encoding-with-mes/#silent_audio)

## <a name="flash"></a>Flash ##

- O conteúdo da AES não rejoga na versão Flash 30.0.0.134 devido a um bug na lógica de cache da Adobe. A Adobe fixou a emissão e lançou-a em 30.0.0.154
- Falhas tecnológicas e http (como 404 intervalos de rede), o jogador demorará mais tempo a recuperar do que outras tecnologias.
- Clique na área de vídeo com a tecnologia flashSS não jogará/pausa o leitor.
- Se o utilizador tiver o Flash instalado mas não autorizar a carregá-lo no site, pode ocorrer uma rotação infinita. Isto porque o leitor pensa que o plugin está instalado e disponível e acha que o plugin está a executar o conteúdo. O código JavaScript foi enviado, mas as definições do navegador bloquearam a execução do plugin até que o utilizador aceite o pedido para permitir o plugin. Isto pode ocorrer em todos os navegadores.  

## <a name="silverlight"></a>Silverlight ##

- Características em falta
- Falhas tecnológicas e http (como 404 intervalos de rede), o jogador demorará mais tempo a recuperar do que outras tecnologias.
- Safari e Firefox na reprodução de Mac com `"http://` `https://` Silverlight requer explicitamente definir ou para a fonte.
- Se faltar uma API para esta tecnologia, geralmente devolverá o nulo.
- Se o utilizador tiver o Flash instalado mas não autorizar a carregá-lo no site, pode ocorrer uma rotação infinita. Isto porque o leitor pensa que o plugin está instalado e disponível e acha que o plugin está a executar o conteúdo. O código JavaScript foi enviado, mas as definições do navegador bloquearam a execução do plugin até que o utilizador aceite o pedido para permitir o plugin. Isto pode ocorrer em todos os navegadores.  

## <a name="native-html5"></a>Nativo HTML5 ##

- A tecnologia Html5 está apenas a enviar canplaythrough através de evento para a primeira fonte de set.
- Esta tecnologia apenas suporta o que o navegador implementou.  Algumas funcionalidades podem estar faltando nesta tecnologia.  
- Se faltar uma API para esta tecnologia, geralmente devolverá o nulo.
- Há problemas com legendas e legendas nesta tecnologia. Podem ou não estar disponíveis ou vervê-lo nesta tecnologia.
- Ter largura de banda limitada no cenário tecnológico HLS/Html5 resulta em reprodução áudio sem vídeo.

### <a name="microsoft"></a>Microsoft ###

- A reprodução iE8 não funciona atualmente devido à incompatibilidade com o ECMAScript 5
- No IE e em algumas versões de Edge, o ecrã completo não pode ser introduzido tocando no botão e selecionando-o ou utilizando a chave f/f.

## <a name="google"></a>Google ##

- Vários perfis de codificação no mesmo manifesto têm alguns problemas de reprodução no Chrome e não são recomendados.
- O Chrome não pode reproduzir o HE-AAC com o AzureHtml5JS. Siga os detalhes no localizador de [erros](https://bugs.chromium.org/p/chromium/issues/detail?id=534301).
- A partir do Chrome v58, o conteúdo de grande videira deve ser carregado/reproduzido através do protocolo https:// caso contrário a reprodução falhará.

## <a name="mozilla"></a>Rio Mozilla ##

- O interruptor de fluxo de áudio requer que os streams de áudio tenham os mesmos dados privados de codec ao utilizar o AzureHtml5JS. A plataforma Firefox requer isto.

## <a name="apple"></a>Apple ##

- O Safari no Mac permite frequentemente o modo Power Saver por padrão com a definição "Stop plug-ins to save power", que bloqueia plugins como Flash e Silverlight quando acreditam que não é a favor do utilizador. Este bloco não bloqueia as capacidades existentes do plugin. Dada a tecnologia padrãoOrder, isto pode causar problemas ao tentar reproduzir
  - Mitigação 1: Se o leitor de vídeo estiver 'frontal e central' (dentro de um limite de 3000 x 3000 pixels a partir do canto superior esquerdo do documento), deve ainda ser reprodução.
  - Mitigação 2: Alterar a techOrder para o Safari ser ["azureHtml5JS", "html5"]. Esta mitigação tem implicado não obter todas as funcionalidades disponíveis na tecnologia FlashSS.
- O conteúdo da PlayReady via Silverlight pode ter problemas em voltar a jogar no Safari.
- A ESE e conteúdo simbólico restrito não reprodução utilizando dispositivos Android iOS e android mais antigos.
  - Para alcançar este cenário, deve ser adicionado um representante ao seu serviço.
- A pele padrão para iOS O telefone mostra.
- A reprodução do iPhone ocorre sempre no ecrã completo do jogador nativo.
  - As funcionalidades, incluindo legendas, podem não persistir nesta reprodução não inline.
- Quando a apresentação ao vivo terminar, os dispositivos iOS não terminarão adequadamente a apresentação.
- O iOS não permite capacidades de DVR.
- O interruptor de fluxo de áudio iOS só pode ser feito apesar do iOS native player UI e requer que os fluxos de áudio tenham os mesmos dados privados codec
- Versões mais antigas do Safari podem potencialmente ter problemas em reproduzir streams ao vivo.

## <a name="older-android"></a>Android mais antigo ##

- A ESE e conteúdo simbólico restrito não reprodução utilizando dispositivos Android iOS e android mais antigos.
  - Para alcançar este cenário, deve ser adicionado um representante ao seu serviço.

## <a name="next-steps"></a>Passos seguintes ##

- [Azure Media Player Quickstart](azure-media-player-quickstart.md)