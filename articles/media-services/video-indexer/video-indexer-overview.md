---
title: O que é o Video Indexer dos Serviços de Multimédia do Azure?
titleSuffix: Azure Media Services
description: Este tópico fornece uma visão geral do serviço de Video Indexer de serviços de mídia do Azure.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/06/2019
ms.author: juliako
ms.openlocfilehash: e3f60b5fb0693e40c9db040f7b14f487fce8f68e
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860251"
---
# <a name="what-is-video-indexer"></a>O que é o Video Indexer?

O Video Indexer dos Serviços de Multimédia do Azure é uma aplicação da cloud criada com base na Análise de Multimédia do Azure, no Azure Search e nos Serviços Cognitivos (como a API Face, o Microsoft Translator, a API de Imagem Digitalizada e o Serviço de Voz Personalizada). Ele permite que você extraia as informações de seus vídeos usando os modelos de áudio e vídeo Video Indexer descritos abaixo:
  
## <a name="video-insights"></a>Informações de vídeo

- **Detecção facial**: Detecta e agrupa faces que aparecem no vídeo.
- **Identificação de celebridade**: Video Indexer identifica automaticamente mais de 1 milhão celebridades – como líderes do mundo, atores, actresses, atletas, pesquisadores, negócios e líderes de tecnologia em todo o mundo. Os dados sobre estas celebridades também podem ser encontrados em vários sites conhecidos como, por exemplo, o IMDB e a Wikipédia.
- **Identificação facial baseada em conta**: Video Indexer treina um modelo para uma conta específica. Em seguida, ele reconhece os rostos no vídeo com base no modelo treinado. Para obter mais informações, consulte [Personalizar um modelo Person no site video indexer](customize-person-model-with-website.md) e [Personalizar um modelo Person com a API Video indexer](customize-person-model-with-api.md).
- **Extração de miniatura para rostos** ("melhor face"): Identifica automaticamente a melhor aparência capturada em cada grupo de faces (com base na qualidade, no tamanho e na posição frontais) e a extrai como um ativo de imagem.
- **Reconhecimento de texto visual** (OCR): Extrai o texto que é exibido visualmente no vídeo.
- **Moderação do conteúdo Visual**: Detecta os elementos visuais adulto e/ou erótico.
- **Identificação de rótulos**: Identifica objetos visuais e ações exibidas.
- **Segmentação de cena**: determina quando uma cena é alterada em vídeo com base em indicações visuais. Uma cena descreve um único evento e é composta por uma série de capturas consecutivas, que são relacionadas semanticamente. 
- **Detecção de captura**: determina quando uma captura é alterada em vídeo com base em indicações visuais. Uma captura é uma série de quadros tirados da mesma câmera de imagem de movimento. Para obter mais informações, consulte [cenas, capturas e quadros-chave](scenes-shots-keyframes.md).
- **Detecção de quadro preto**: Identifica quadros pretos apresentados no vídeo.
- **Extração de quadro-chave**: Detecta quadros-chave estáveis em um vídeo.
- **Créditos sem interrupção**: identifique o início e o fim dos créditos contínuos no final de filmes e apresentações de TV.
- **Detecção de caracteres animados** (visualização): detecção, agrupamento e reconhecimento de caracteres em conteúdo animado por meio da integração com a [visão personalizada dos serviços cognitivas](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/). Para obter mais informações, consulte a [detecção de caracteres animados](animated-characters-recognition.md).
- **Detecção de tipo editorial shot**: marcação de capturas com base em seu tipo (como ampla captura, imagem média, fechamento, extremo, superior, duas telas, várias pessoas, externamente e em interno, etc.). Para obter mais informações, consulte [detecção do tipo editorial shot](scenes-shots-keyframes.md#editorial-shot-type-detection).

## <a name="audio-insights"></a>Informações de áudio

- **Detecção automática de idioma**: Identifica automaticamente a linguagem falada dominante. Os idiomas com suporte incluem Inglês, espanhol, francês, alemão, italiano, chinês (simplificado), japonês, russo e português brasileiro. Se o idioma não puder ser identificado com confiança, o Video Indexer assumirá que o idioma falado é o inglês. Para obter mais informações, consulte [modelo de identificação de idioma](language-identification-model.md).
- **Identificação e transcrição de fala em vários idiomas** (visualização): Identifica automaticamente o idioma falado em diferentes segmentos do áudio, enviando cada segmento do arquivo de mídia a ser transcrita e combinando a transcrição de volta a uma transcrição unificada. Para obter mais informações, consulte [identificar e transcrever automaticamente o conteúdo em vários idiomas](multi-language-identification-transcription.md).
- **Transcrição de áudio**: Converte a fala em texto em 12 idiomas e permite extensões. Os idiomas com suporte incluem Inglês, espanhol, francês, alemão, italiano, chinês (simplificado), japonês, árabe, russo, Português do Brasil, híndi e coreano.
- **Legendagem oculta**: Cria legendas ocultas em três formatos: VTT, TTML, SRT.
- **Processamento de dois canais**: Detecta automaticamente, separa a transcrição e mescla em uma única linha do tempo.
- **Redução de ruído**: Limpa o áudio de telefonia ou gravações com ruídos (com base em filtros do Skype).
- **Personalização de transcrição** (CRIS): Treina a fala personalizada em modelos de texto para criar transcrições específicas do setor. Para obter mais informações, consulte [Personalizar um modelo de linguagem no site video indexer](customize-language-model-with-website.md) e [Personalizar um modelo de linguagem com as APIs de video indexer](customize-language-model-with-api.md).
- **Enumeração do palestrante**: Mapeia e entende qual palestrante fala quais palavras e quando.
- **Estatísticas do palestrante**: Fornece estatísticas para taxas de fala dos alto-falantes.
- **Moderação de conteúdo textual**: Detecta texto explícito na transcrição de áudio.
- **Efeitos de áudio**: Identifica os efeitos de áudio como mão Claps, fala e silêncio.
- **Detecção de emoções**: Identifica emoções com base na fala (o que está sendo dito) e a tonalidade de voz (como ele está sendo dito).  As emoções podem ser alegria, tristeza, raiva ou medo.
- **Tradução**: Cria traduções da transcrição de áudio para 54 idiomas diferentes.

## <a name="audio-and-video-insights-multi-channels"></a>Informações de áudio e vídeo (vários canais)

Ao indexar um resultado parcial do canal para esses modelos estará disponível

- **Extração de palavras-chave**: Extrai palavras-chave de fala e texto visual.
- **Extração de entidades nomeadas**: Extrai marcas, locais e pessoas de fala e texto visual por meio do NLP (processamento de idioma natural).
- **Inferência de tópico**: Faz a inferência de tópicos principais de transcrições. A taxonomia IPTC de 1º nível está incluída.
- **Artefatos**: Extrai um conjunto avançado de artefatos de "próximo nível de detalhes" para cada um dos modelos.
- **Análise de sentimentos**: Identifica as opiniões positivas, negativas e neutras de fala e texto visual.
 
Assim que o Video Indexer terminar o processamento e a análise, pode rever, organizar, pesquisar e publicar as informações do vídeo.

Se a sua função é ser gestor de conteúdos ou programador, o serviço Video Indexer pode atender as suas necessidades. Os gestores de conteúdo podem utilizar o portal Web do Video Indexer para consumirem o serviço, sem terem de escrever uma única linha de código. Veja [Introdução ao site do Video Indexer](video-indexer-get-started.md). Os programadores podem tirar partido das APIs para processar conteúdo em escala, veja [Utilizar a API REST do Video Indexer](video-indexer-use-apis.md). O serviço também permite aos clientes utilizarem widgets para publicarem transmissões de vídeo e informações extraídas nas suas próprias aplicações, veja [Incorporar widgets visuais na sua aplicação](video-indexer-embed-widgets.md).

Pode inscrever-se no serviço com a sua conta existente do AAD, LinkedIn, Facebook, Google ou MSA. Para obter mais informações, veja [introdução](video-indexer-get-started.md).

## <a name="scenarios"></a>Cenários

Seguem-se alguns cenários em que o Video Indexer pode ser útil

- Pesquisa – as informações extraídas de vídeo podem servir para melhorar a experiência de pesquisa numa biblioteca de vídeos. Por exemplo, a indexação de palavras faladas e rostos pode ativar a experiência de pesquisa de encontrar momentos num vídeo em que uma determinada pessoa falou determinadas palavras ou quando duas pessoas foram vistas juntas. A pesquisa com base nessas informações dos vídeos é aplicável a agências de notícias, institutos educacionais, emissoras, proprietários de conteúdo de entretenimento, aplicações LOB empresariais e, em geral, a qualquer setor que tenha uma biblioteca de vídeos que os utilizadores precisam para pesquisar.
- Criação de conteúdo – ideias extraídas de vídeos e ajudam a criar conteúdo com eficiência, como trailers, conteúdo de mídia social, clipes de notícias etc. de conteúdo existente no arquivo da organização 
- Monetização – o Video Indexer pode ajudar a melhorar o valor dos vídeos. Por exemplo, os setores que se baseiam na receita de anúncios (por exemplo, imprensa, redes sociais, etc.), podem apresentar anúncios mais relevantes através das informações extraídas como sinais adicionais para o servidor do anúncio (apresentar um anúncio de calçado de desporto é mais relevante no meio de um jogo de futebol do que numa competição de natação).
- Cativação – as informações de vídeo podem ser utilizadas para melhorar a cativação ao posicionar os momentos de vídeo relevantes para os utilizadores. Por exemplo, considere um vídeo educacional que fala sobre esferas durante os primeiros 30 minutos e pirâmides nos 30 minutos seguintes. Um estudante que lê sobre pirâmides iria beneficiar mais se o vídeo estivesse posicionado a partir do marcador dos 30 minutos.

## <a name="next-steps"></a>Passos seguintes

Está pronto para começar a utilizar o Video Indexer. Para obter mais informações, veja os artigos seguintes:

- [Introdução ao site do Video Indexer](video-indexer-get-started.md)
- [Processar conteúdo com a API REST do Video Indexer](video-indexer-use-apis.md)
- [Incorporar widgets visuais na sua aplicação](video-indexer-embed-widgets.md)
