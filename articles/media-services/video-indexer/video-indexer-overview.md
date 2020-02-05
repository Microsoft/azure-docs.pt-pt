---
title: O que é o Video Indexer?
titleSuffix: Azure Media Services
description: Este artigo dá uma visão geral do serviço Azure Media Services Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 02/02/2020
ms.author: juliako
ms.openlocfilehash: aee4390d9b892eddbd7dc59887f5268e4c35d3f7
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989795"
---
# <a name="what-is-video-indexer"></a>O que é o Video Indexer?

O Video Indexer (VI) é a solução Azure Media Services AI e faz parte da marca Azure Cognitive Services. O Indexer de vídeo fornece capacidade de extrair insights profundos (sem necessidade de análise de dados ou habilidades de codificação) utilizando modelos de machine learning baseados em vários canais (voz, voz, visual). Pode ainda personalizar e treinar os modelos. O serviço permite uma pesquisa profunda, reduz os custos operacionais, permite novas oportunidades de rentabilização e cria novas experiências de utilizador em grandes arquivos de vídeos (com barreiras de entrada baixas).

Para começar a extrair insights com o Indexer de Vídeo, precisa de criar uma conta e fazer upload de vídeos. Ao fazer o upload dos seus vídeos para o Video Indexer, analisa tanto os visuais como o áudio executando diferentes modelos de IA. À medida que o Indexer de Vídeo analisa o seu vídeo, as ideias que são extraídas pelos modelos de IA.

O diagrama a seguir é uma ilustração e não uma explicação técnica de como o Indexer de Vídeo funciona no backend.

![Diagrama de fluxo de fluxo de vídeo de serviços de mídia azure](./media/video-indexer-overview/model-chart.png)

## <a name="compliance-privacy-and-security"></a>Conformidade, Privacidade e Segurança
 
Como um lembrete importante, você deve cumprir todas as leis aplicáveis na sua utilização do Indexer de Vídeo, e você não pode usar O Indexer de Vídeo ou qualquer outro serviço Azure de uma forma que viole os direitos de outros ou pode ser prejudicial para os outros. Antes de enviar quaisquer vídeos, incluindo quaisquer dados biométricos, para o serviço de Indexer de Vídeo para processamento e armazenamento, deve ter todos os direitos adequados, incluindo todos os consentimentos adequados, do(s) indivíduo(s) no vídeo. Para conhecer a conformidade, privacidade e segurança no Indexer de Vídeo, os [Termos dos Serviços Cognitivos](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/)da Microsoft . Para as obrigações de privacidade da Microsoft e o tratamento dos seus dados, por favor, consulte a Declaração de [Privacidade](https://privacy.microsoft.com/PrivacyStatement)da Microsoft, os Termos dos [Serviços Online](https://www.microsoft.com/licensing/product-licensing/products) ("OST") e a [Adenda](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) de Processamento de Dados ("DPA"). Informações adicionais sobre a privacidade, incluindo sobre a retenção de dados, eliminação/destruição, estão disponíveis no OST e [aqui](faq.md). Ao utilizar o Indexer de Vídeo, concorda em ficar vinculado aos Termos dos Serviços Cognitivos, ao OST, dPA e à Declaração de Privacidade.

## <a name="what-can-i-do-with-video-indexer"></a>O que posso fazer com o Indexer de Vídeo?

Os insights do Indexer de vídeo podem ser aplicados a muitos cenários, entre eles:

* *Pesquisa profunda*: Utilize as informações extraídas do vídeo para melhorar a experiência de pesquisa através de uma biblioteca de vídeo. Por exemplo, indexar palavras e rostos falados pode permitir a experiência de pesquisa de encontrar momentos num vídeo onde uma pessoa falou certas palavras ou quando duas pessoas foram vistas juntas. A pesquisa baseada em tais insights de vídeos é aplicável a agências noticiosas, institutos educativos, emissores, proprietários de conteúdos de entretenimento, aplicações LOB empresariais e, em geral, a qualquer indústria que tenha uma biblioteca de vídeo que os utilizadores precisam de procurar.
* *Criação de conteúdos*: Crie trailers, destaque bobinas, conteúdos de redes sociais ou clips de notícias com base nos insights extratos do Indexer de Vídeo do seu conteúdo. Quadros-chave, marcadores de cenas e carimbos de tempo para as pessoas e as aparências de etiqueta saem do processo de criação muito mais suave e fácil, e permite-lhe chegar às partes do vídeo que precisa para o conteúdo que está a criar.
* *Acessibilidade*: Quer pretenda disponibilizar o seu conteúdo para pessoas com deficiência ou se pretende que o seu conteúdo seja distribuído por diferentes regiões utilizando diferentes línguas, pode utilizar a transcrição e tradução fornecidas pelo indexador de vídeo em várias línguas.
* *Monetização*: O Indexante de Vídeo pode ajudar a aumentar o valor dos vídeos. Por exemplo, as indústrias que dependem da receita de anúncios (meios de comunicação social, redes sociais, e assim por diante) podem entregar anúncios relevantes usando os insights extraídos como sinais adicionais para o servidor de anúncios.
* *Moderação de conteúdo*: Utilize modelos de moderação de conteúdo textual e visual para manter os seus utilizadores seguros de conteúdos inadequados e validar que o conteúdo que publica corresponde aos valores da sua organização. Pode bloquear automaticamente determinados vídeos ou alertar os seus utilizadores sobre o conteúdo.
* *Recomendações*: As informações de vídeo podem ser usadas para melhorar o envolvimento do utilizador, destacando os momentos de vídeo relevantes para os utilizadores. Ao marcar cada vídeo com metadados adicionais, pode recomendar aos utilizadores os vídeos mais relevantes e destacar as partes do vídeo que irão corresponder às suas necessidades.

## <a name="features"></a>Funcionalidades

A lista que se segue mostra as ideias que pode obter dos seus vídeos utilizando modelos de vídeo e áudio do Indexer de Vídeo:

### <a name="video-insights"></a>Informações de vídeo

* **Face detection** (Deteção facial): deteta e agrupa os rostos que aparecem no vídeo.
* **Identificação de celebridades**: O Video Indexer identifica automaticamente mais de 1 milhão de celebridades — como líderes mundiais, atores, atrizes, atletas, investigadores, empresas e líderes tecnológicos em todo o mundo. Os dados sobre estas celebridades também podem ser encontrados em vários sites (IMDB, Wikipédia, e assim por diante).
* **Identificação facial com base na conta**: o Video Indexer prepara um modelo para uma conta específica. Em seguida, reconhece rostos no vídeo com base no modelo treinado. Para mais informações, consulte [Personalizar um modelo pessoa do site do Indexer de Vídeo](customize-person-model-with-website.md) e personalizar um modelo pessoa com a [API do Indexer de Vídeo](customize-person-model-with-api.md).
* **Extração** de miniaturas para rostos ("melhor rosto"): Identifica automaticamente o melhor rosto capturado em cada grupo de rostos (com base na qualidade, tamanho e posição frontal) e extrai-o como um ativo de imagem.
* **Reconhecimento** visual de texto (OCR): Extrai texto que é exibido visualmente no vídeo.
* **Visual content moderation** (Moderação de conteúdo visual): deteta elementos visuais para adultos.
* **Labels identification** (Identificação através de etiquetas): identifica as ações e os objetos visuais apresentados.
* **Segmentação da cena**: Determina quando uma cena muda em vídeo com base em pistas visuais. Uma cena retrata um único evento e é composta por uma série de fotos consecutivas, que são semânticamente relacionadas.
* **Deteção de tiro**: Determina quando um tiro muda em vídeo com base em pistas visuais. Um tiro é uma série de quadros tirados da mesma câmara de filme. Para mais informações, consulte [Cenas, fotos e quadros](scenes-shots-keyframes.md)de chaves.
* **Black frame detection** (Deteção de fotogramas pretos): identifica os fotogramas pretos apresentados no vídeo.
* **Keyframe extraction** (Extração de keyframes): deteta keyframes estáveis num vídeo.
* **Créditos rolantes**: Identifica o início e o fim dos créditos rolantes no final de séries e filmes.
* **Deteção de caracteres animados** (pré-visualização): Deteção, agrupamento e reconhecimento de personagens em conteúdo animado através da integração com [visão personalizada dos Serviços Cognitivos.](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) Para obter mais informações, consulte a [detecção de caracteres animados](animated-characters-recognition.md).
* **Deteção do tipo**de tiro editorial: Tiro de marcação com base no seu tipo (como tiro largo, tiro médio, close-up, close-up extremo, dois tiros, várias pessoas, ao ar livre e interior, e assim por diante). Para mais informações, consulte a deteção do [tipo editorial de tiro.](scenes-shots-keyframes.md#editorial-shot-type-detection)

### <a name="audio-insights"></a>Insights áudio

* **Automatic language detection** (Deteção automática do idioma): identifica automaticamente o idioma falado dominante. Os idiomas com suporte incluem Inglês, espanhol, francês, alemão, italiano, chinês (simplificado), japonês, russo e português (Brasil). Se a língua não pode ser identificada com confiança, o Indexer de vídeo assume que a língua falada é inglês. Para mais informações, consulte o modelo de [identificação da língua.](language-identification-model.md)
* **Identificação e transcrição da fala multi-idioma (pré-visualização):** Identifica automaticamente a língua falada em diferentes segmentos a partir do áudio. Envia cada segmento do ficheiro mediático para ser transcrito e, em seguida, combina a transcrição de volta a uma transcrição unificada. Para obter mais informações, consulte [identificar e transcrever automaticamente o conteúdo em vários idiomas](multi-language-identification-transcription.md).
* **Transcrição áudio**: Converte a fala em texto em 12 idiomas e permite extensões. As línguas apoiadas incluem inglês, espanhol, francês, alemão, italiano, chinês (simplificado), japonês, árabe, russo, português brasileiro, hindi e coreano.
* **Closed captioning** (Legendas): cria legendas em três formatos: VTT, TTML e SRT.
* **Processamento de dois canais:** O Auto deteta transcrição separada e funde-se numa única linha temporal.
* **Redução do ruído**: Limpa gravações áudio de telefonia ou ruidosas (com base em filtros Skype).
* **Personalização da transcrição** (CRIS): Treina a fala personalizada para modelos de texto para criar transcrições específicas da indústria. Para mais informações, consulte [Personalizar um modelo de Idioma do website do Indexer de Vídeo](customize-language-model-with-website.md) e personalizar um modelo de [idioma com as APIs do Indexer de Vídeo](customize-language-model-with-api.md).
* **Enumeração do orador**: Mapas e compreende que orador falou quais palavras e quando.
* **Estatísticas dos oradores**: Fornece estatísticas sobre os rácios de fala dos oradores.
* **Textual content moderation** (Moderação de conteúdo textual): deteta texto explícito na transcrição de áudio.
* **Efeitos áudio**: Identifica efeitos áudio como palmas das mãos, discurso e silêncio.
* **Deteção de emoções**: Identifica emoções baseadas na fala (o que está a ser dito) e na tonalidade da voz (como está a ser dito). A emoção pode ser alegria, tristeza, raiva ou medo.
* **Translation** (Tradução): cria traduções da transcrição de áudio para 54 idiomas diferentes.

### <a name="audio-and-video-insights-multi-channels"></a>Insights de áudio e vídeo (multi-canais)

Ao indexar por um canal, o resultado parcial desses modelos estará disponível.

* **Extração de palavras-chave**: Extrai palavras-chave da fala e do texto visual.
* **Extração de entidades nomeadas**: Extrai marcas, locais e pessoas da fala e texto visual através do processamento de linguagem natural (NLP).
* **Topic inference** (Inferência de tópicos): faz inferências relativamente aos principais tópicos a partir de transcrições. A taxonomia iPTC de 2º nível está incluída.
* **Artifacts** (Artefactos): extrai um conjunto avançado de artefactos mais detalhados para cada um dos modelos.
* **Sentiment analysis** (Análise de sentimentos): identifica sentimentos positivos, negativos e neutros a partir do discurso e de texto visual.

## <a name="how-can-i-get-started-with-video-indexer"></a>Como posso começar com o Indexer de Vídeo?

Pode aceder às capacidades do Indexer de Vídeo de três formas:

* Portal do Indexante de Vídeo: Uma solução fácil de usar que permite avaliar o produto, gerir a conta e personalizar modelos.

    Para mais informações sobre o portal, consulte [Começar com o site do Indexer de Vídeo](video-indexer-get-started.md).  

* Integração API: Todas as capacidades do Indexer de Vídeo estão disponíveis através de uma API REST, que permite integrar a solução nas suas apps e infraestruturas.

    Para começar como desenvolvedor, consulte [Use Video Indexer REST API](video-indexer-use-apis.md).

* Widget incorporado: Permite incorporar as experiências do Indexer de Vídeo, jogador e editor na sua aplicação.

    Para mais informações, consulte [widgets visuais Incorporados na sua aplicação](video-indexer-embed-widgets.md).

Se estiver a usar o site, os insights são adicionados como metadados e são visíveis no portal. Se estiver a utilizar APIs, os insights estão disponíveis como ficheiro JSON.

## <a name="next-steps"></a>Passos seguintes

Está pronto para começar a utilizar o Video Indexer. Para obter mais informações, veja os artigos seguintes:

- [Começar com o site do Indexer de Vídeo.](video-indexer-get-started.md)
- [Processe o conteúdo com o Indexer de Vídeo REST API](video-indexer-use-apis.md).
- [Inbed widgets visuais na sua aplicação](video-indexer-embed-widgets.md).
