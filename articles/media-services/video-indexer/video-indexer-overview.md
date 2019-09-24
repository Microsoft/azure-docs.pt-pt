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
ms.date: 09/17/2019
ms.author: juliako
ms.openlocfilehash: d346f68534a9fdbc286cd5521c00d81c2a5fec78
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71203552"
---
# <a name="what-is-video-indexer"></a>O que é o Video Indexer?

Os serviços de mídia do Azure Video Indexer é uma solução de ia criada nos serviços cognitivas do Azure. O Video Indexer fornece a capacidade de extrair informações aprofundadas (sem necessidade de análise de dados ou habilidades de codificação) usando modelos de aprendizado de máquina com base em vários canais (voz, vozes, Visual). Você pode personalizar e treinar os modelos. O serviço permite a pesquisa profunda, reduz os custos operacionais, permite novas oportunidades de monetização, novas experiências de usuário em grandes arquivos de vídeos (com barreiras de baixa entrada). 

Para começar a extrair informações com Video Indexer, você precisa criar uma conta e carregar vídeos. Quando você carrega seus vídeos para Video Indexer, ele analisa os elementos visuais e áudio executando modelos de ia diferentes. Como Video Indexer analisa seu vídeo, as informações que são extraídas pelos modelos.

![Diagrama de fluxo](./media/video-indexer-overview/model-chart.png)

## <a name="what-can-i-do-with-video-indexer"></a>O que posso fazer com Video Indexer?

As informações de Video Indexer podem ser aplicadas a vários cenários, entre elas:

* *Pesquisa profunda* – use as informações extraídas do vídeo para aprimorar a experiência de pesquisa em uma biblioteca de vídeos. Por exemplo, a indexação de palavras faladas e rostos pode permitir a experiência de pesquisa de encontrar momentos em um vídeo em que uma pessoa falou determinadas palavras ou quando duas pessoas foram vistas juntas. A pesquisa com base nessas informações dos vídeos é aplicável a agências de notícias, institutos educacionais, emissoras, proprietários de conteúdo de entretenimento, aplicações LOB empresariais e, em geral, a qualquer setor que tenha uma biblioteca de vídeos que os utilizadores precisam para pesquisar.
* *Criação de conteúdo*: criar trailers, realçar rolos, conteúdo de mídia social ou clipes de notícias com base nas informações Video indexer extraídos do seu conteúdo. Os quadros-chave, os marcadores de cenas e os carimbos de hora para as pessoas e as aparências dos rótulos tornam o processo de criação muito mais suave e fácil, além de permitir que você tenha direito às partes interessantes do vídeo que você precisa para o conteúdo que está criando.
* *Acessibilidade*: se você deseja disponibilizar seu conteúdo para pessoas com deficiências ou se deseja que seu conteúdo seja distribuído para regiões diferentes usando idiomas diferentes, você pode usar a transcrição e a tradução fornecidos pelo vídeo indexador em vários idiomas.
* *Monetização*: Video Indexer pode ajudar a aumentar o valor dos vídeos. Por exemplo, os setores que dependem da receita do AD (por exemplo, mídia de notícias, mídia social etc.) podem fornecer anúncios relevantes usando as informações extraídas como sinais adicionais para o servidor do AD.
* *Moderação de conteúdo*: use modelos de moderação de conteúdo visual e textual para manter seus usuários protegidos contra conteúdo inadequado e validar que o conteúdo publicado corresponde aos valores da sua organização. Você pode bloquear automaticamente determinados vídeos ou alertar seus usuários sobre o conteúdo. 
* *Recomendações*: As informações de vídeo podem ser usadas para melhorar o envolvimento do usuário, destacando o tempo de vídeo relevante para os usuários. Ao marcar cada vídeo com metadados adicionais, você pode recomendar aos usuários os vídeos mais relevantes e realçar a parte do vídeo que atenderá às suas necessidades. 

## <a name="features"></a>Funcionalidades

A seguir está a lista de informações que você pode recuperar de seus vídeos usando Video Indexer modelos de vídeo e áudio:

### <a name="video-insights"></a>Informações de vídeo

* **Detecção facial**: Detecta e agrupa faces que aparecem no vídeo.
* **Identificação de celebridade**: Video Indexer identifica automaticamente mais de 1 milhão celebridades – como líderes do mundo, atores, actresses, atletas, pesquisadores, negócios e líderes de tecnologia em todo o mundo. Os dados sobre estas celebridades também podem ser encontrados em vários sites conhecidos como, por exemplo, o IMDB e a Wikipédia.
* **Identificação facial baseada em conta**: Video Indexer treina um modelo para uma conta específica. Em seguida, ele reconhece os rostos no vídeo com base no modelo treinado. Para obter mais informações, consulte [Personalizar um modelo Person no site video indexer](customize-person-model-with-website.md) e [Personalizar um modelo Person com a API Video indexer](customize-person-model-with-api.md).
* **Extração de miniatura para rostos** ("melhor face"): Identifica automaticamente a melhor aparência capturada em cada grupo de faces (com base na qualidade, no tamanho e na posição frontais) e a extrai como um ativo de imagem.
* **Reconhecimento de texto visual** (OCR): Extrai o texto que é exibido visualmente no vídeo.
* **Moderação do conteúdo Visual**: Detecta os elementos visuais adulto e/ou erótico.
* **Identificação de rótulos**: Identifica objetos visuais e ações exibidas.
* **Segmentação de cena**: determina quando uma cena é alterada em vídeo com base em indicações visuais. Uma cena descreve um único evento e é composta por uma série de capturas consecutivas, que são relacionadas semanticamente.
* **Detecção de captura**: determina quando uma captura é alterada em vídeo com base em indicações visuais. Uma captura é uma série de quadros tirados da mesma câmera de imagem de movimento. Para obter mais informações, consulte cenas, capturas e quadros-chave.
* **Detecção de quadro preto**: Identifica quadros pretos apresentados no vídeo.
* **Extração de quadro-chave**: Detecta quadros-chave estáveis em um vídeo.
* **Créditos sem interrupção**: identifique o início e o fim dos créditos contínuos no final de filmes e apresentações de TV.
* **Detecção de caracteres animados** (visualização): detecção, agrupamento e reconhecimento de caracteres em conteúdo animado por meio da integração com a [visão personalizada dos serviços cognitivas](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/). Para obter mais informações, consulte a [detecção de caracteres animados](animated-characters-recognition.md).
* **Detecção de tipo editorial shot**: marcação de capturas com base em seu tipo (como ampla captura, imagem média, fechamento, extremo, superior, duas telas, várias pessoas, externamente e em interno, etc.). Para obter mais informações, consulte [detecção do tipo editorial shot](scenes-shots-keyframes.md#editorial-shot-type-detection).

### <a name="audio-insights"></a>Informações de áudio

* **Detecção automática de idioma**: Identifica automaticamente a linguagem falada dominante. Os idiomas com suporte incluem Inglês, espanhol, francês, alemão, italiano, chinês (simplificado), japonês, russo e português brasileiro. Se o idioma não puder ser identificado com confiança, o Video Indexer assumirá que o idioma falado é o inglês. Para obter mais informações, consulte [modelo de identificação de idioma](language-identification-model.md).
* **Identificação e transcrição de fala em vários idiomas** (visualização): Identifica automaticamente o idioma falado em diferentes segmentos do áudio, enviando cada segmento do arquivo de mídia a ser transcrita e combinando a transcrição de volta a uma transcrição unificada. Para obter mais informações, consulte [identificar e transcrever automaticamente o conteúdo em vários idiomas](multi-language-identification-transcription.md).
* **Transcrição de áudio**: Converte a fala em texto em 12 idiomas e permite extensões. Os idiomas com suporte incluem Inglês, espanhol, francês, alemão, italiano, chinês (simplificado), japonês, árabe, russo, Português do Brasil, híndi e coreano.
* **Legendagem oculta**: Cria legendas ocultas em três formatos: VTT, TTML, SRT.
* **Processamento de dois canais**: Detecta automaticamente, separa a transcrição e mescla em uma única linha do tempo.
* **Redução de ruído**: Limpa o áudio de telefonia ou gravações com ruídos (com base em filtros do Skype).
* **Personalização de transcrição** (CRIS): Treina a fala personalizada em modelos de texto para criar transcrições específicas do setor. Para obter mais informações, consulte [Personalizar um modelo de linguagem no site video indexer](customize-language-model-with-website.md) e [Personalizar um modelo de linguagem com as APIs de video indexer](customize-language-model-with-api.md).
* **Enumeração do palestrante**: Mapeia e entende qual palestrante fala quais palavras e quando.
* **Estatísticas do palestrante**: Fornece estatísticas para taxas de fala dos alto-falantes.
* **Moderação de conteúdo textual**: Detecta texto explícito na transcrição de áudio.
* **Efeitos de áudio**: Identifica os efeitos de áudio como mão Claps, fala e silêncio.
* **Detecção de emoções**: Identifica emoções com base na fala (o que está sendo dito) e a tonalidade de voz (como ele está sendo dito). A emoção poderia ser a alegria, tristeza, raiva ou medo.
* **Tradução**: Cria traduções da transcrição de áudio para 54 idiomas diferentes.

### <a name="audio-and-video-insights-multi-channels"></a>Informações de áudio e vídeo (vários canais)

Ao indexar um resultado parcial do canal para esses modelos estará disponível

* **Extração de palavras-chave**: Extrai palavras-chave de fala e texto visual.
* **Extração de entidades nomeadas**: Extrai marcas, locais e pessoas de fala e texto visual por meio do NLP (processamento de idioma natural).
* **Inferência de tópico**: Faz a inferência de tópicos principais de transcrições. A taxonomia IPTC de 1º nível está incluída.
* **Artefatos**: Extrai um conjunto avançado de artefatos de "próximo nível de detalhes" para cada um dos modelos.
* **Análise de sentimentos**: Identifica as opiniões positivas, negativas e neutras de fala e texto visual.

## <a name="how-can-i-get-started-with-video-indexer"></a>Como posso começar a usar o Video Indexer?

Você pode acessar recursos de Video Indexer usando três maneiras diferentes: 

* Video Indexer portal – uma solução fácil de usar que permite avaliar o produto, gerenciar a conta e personalizar modelos. 

    Para obter mais informações sobre o portal, consulte Introdução [ao site video indexer](video-indexer-get-started.md).  
* Integração de API – todos os recursos de Video Indexer estão disponíveis por meio de uma API REST para permitir que você integre a solução em seus aplicativos e infraestrutura. 

    Para começar como desenvolvedor, consulte [usar Video INDEXER API REST](video-indexer-use-apis.md). 
* Widget Emendable – permite que você incorpore as experiências do indexador de vídeo, do Player e do editor em seu aplicativo. 

    Para obter mais informações, consulte [Inserir widgets visuais em seu aplicativo](video-indexer-embed-widgets.md). 

Se você estiver usando o site, as informações serão adicionadas como metadados e visíveis no Portal. Se você estiver usando APIs, as informações estarão disponíveis como um arquivo JSON. 

## <a name="next-steps"></a>Passos seguintes

Está pronto para começar a utilizar o Video Indexer. Para obter mais informações, veja os artigos seguintes:

- [Introdução ao site do Video Indexer](video-indexer-get-started.md)
- [Processar conteúdo com a API REST do Video Indexer](video-indexer-use-apis.md)
- [Incorporar widgets visuais na sua aplicação](video-indexer-embed-widgets.md)
