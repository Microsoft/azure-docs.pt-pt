---
title: O que é o Video Indexer dos Serviços de Multimédia do Azure?
titleSuffix: Azure Media Services
description: Este artigo apresenta uma visão geral do serviço Azure Media Services Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/11/2020
ms.author: juliako
ms.openlocfilehash: 6b5a228c12cfc874673dda854de6d4dcc25f28c1
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98014540"
---
# <a name="what-is-azure-media-services-video-indexer"></a>O que é o Video Indexer dos Serviços de Multimédia do Azure?

[!INCLUDE [regulation](./includes/regulation.md)]

O Video Indexer (VI) é a solução Azure Media Services AI e parte da marca Azure Cognitive Services. O Video Indexer fornece a capacidade de extrair insights profundos (sem necessidade de análise de dados ou habilidades de codificação) utilizando modelos de aprendizagem automática baseados em múltiplos canais (voz, vocal, visual). Pode personalizar e treinar os modelos. O serviço permite uma pesquisa profunda, reduz os custos operacionais, permite novas oportunidades de rentabilização e cria novas experiências de utilização em grandes arquivos de vídeos (com baixas barreiras de entrada).

Para começar a extrair insights com o Video Indexer, é necessário criar uma conta e carregar vídeos. Ao fazer o upload dos seus vídeos para o Video Indexer, analisa tanto os visuais como o áudio, executando diferentes modelos de IA. À medida que o Video Indexer analisa o seu vídeo, as insights que são extraídas pelos modelos de IA.

Quando cria uma conta de Indexer de Vídeo e a liga aos Serviços de Media, os ficheiros de mídia e metadados são armazenados na conta de armazenamento Azure associada a essa conta de Serviços de Mídia. Para obter mais informações, consulte [Criar uma conta de Indexer de Vídeo ligada ao Azure](connect-to-azure.md).

O diagrama seguinte é uma ilustração e não uma explicação técnica de como o Indexer de Vídeo funciona no backend.

![Diagrama de fluxo de indexante de vídeo Azure Media Services](./media/video-indexer-overview/model-chart.png)


## <a name="compliance-privacy-and-security"></a>Conformidade, Privacidade e Segurança

Como um importante lembrete, deve cumprir todas as leis aplicáveis na sua utilização do Video Indexer, e não poderá utilizar o Video Indexer ou qualquer serviço Azure de uma forma que viole os direitos dos outros, ou que possa ser prejudicial para os outros.

Antes de enviar qualquer vídeo/imagem para o Video Indexer, deve ter todos os direitos adequados de utilização do vídeo/imagem, incluindo, sempre que tal seja exigido por lei, todos os consentimentos necessários de indivíduos (se houver) no vídeo/imagem, para a utilização, processamento e armazenamento dos seus dados no Video Indexer e no Azure. Algumas jurisdições podem impor requisitos legais especiais para a recolha, tratamento e armazenamento online de determinadas categorias de dados, tais como dados biométricos. Antes de utilizar o Video Indexer e o Azure para o processamento e armazenamento de quaisquer dados sujeitos a requisitos legais especiais, deve garantir o cumprimento de quaisquer requisitos legais que possam ser aplicáveis a Si.

Para saber mais sobre a conformidade, privacidade e segurança no Video Indexer, visite o Microsoft [Trust Center](https://www.microsoft.com/TrustCenter/CloudServices/Azure/default.aspx). Para as obrigações de privacidade da Microsoft, práticas de tratamento e retenção de dados, incluindo como eliminar os seus dados, por favor reveja a Declaração de [Privacidade](https://privacy.microsoft.com/PrivacyStatement)da Microsoft, [os Termos dos Serviços Online](https://www.microsoft.com/licensing/product-licensing/products?rtc=1) ("OST") e a [adenda de processamento de dados](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA"). Ao utilizar o Video Indexer, concorda em ficar vinculado pela OST, DPA e pela Declaração de Privacidade.

## <a name="what-can-i-do-with-video-indexer"></a>O que posso fazer com o Indexer de Vídeo?

Os insights do Indexer de Vídeo podem ser aplicados em muitos cenários, entre eles:

* *Pesquisa profunda*: Utilize as informações extraídas do vídeo para melhorar a experiência de pesquisa através de uma biblioteca de vídeo. Por exemplo, a indexação de palavras e rostos falados pode permitir a experiência de pesquisa de encontrar momentos num vídeo onde uma pessoa falava certas palavras ou quando duas pessoas eram vistas juntas. A pesquisa com base em tais informações de vídeos é aplicável a agências de notícias, institutos educacionais, emissores, proprietários de conteúdos de entretenimento, aplicações lob da empresa e, em geral, a qualquer indústria que tenha uma biblioteca de vídeo que os utilizadores precisam de pesquisar.
* *Criação de conteúdos*: Crie trailers, realce bobinas, conteúdos de redes sociais ou clips de notícias com base nos extratos de Vídeo Indexer do seu conteúdo. Quadros-chave, marcadores de cenas e selos de tempo para as pessoas e as aparências dos rótulos tornam o processo de criação muito mais suave e fácil, e permite-lhe chegar às partes do vídeo que precisa para o conteúdo que está a criar.
* *Acessibilidade*: Quer pretenda disponibilizar o seu conteúdo para pessoas com deficiência ou se pretende que o seu conteúdo seja distribuído em diferentes regiões utilizando diferentes idiomas, pode utilizar a transcrição e tradução fornecidas pelo indexante de vídeo em vários idiomas.
* *Monetização*: O Indexante de Vídeo pode ajudar a aumentar o valor dos vídeos. Por exemplo, as indústrias que dependem da receita de anúncios (meios de comunicação, redes sociais, e assim por diante) podem entregar anúncios relevantes usando os insights extraídos como sinais adicionais para o servidor de anúncios.
* *Moderação de conteúdos*: Utilize modelos de moderação de conteúdo sonoro e visual para manter os seus utilizadores a salvo de conteúdos inadequados e valide que o conteúdo que publica corresponde aos valores da sua organização. Pode bloquear automaticamente determinados vídeos ou alertar os seus utilizadores sobre o conteúdo.
* *Recomendações*: As informações de vídeo podem ser usadas para melhorar o envolvimento do utilizador, destacando os momentos de vídeo relevantes para os utilizadores. Ao marcar cada vídeo com metadados adicionais, pode recomendar aos utilizadores os vídeos mais relevantes e destacar as partes do vídeo que irão corresponder às suas necessidades.

## <a name="features"></a>Funcionalidades

A lista a seguir mostra as informações que pode obter dos seus vídeos utilizando modelos de vídeo e áudio do Video Indexer:

### <a name="video-insights"></a>Insights de vídeo

* **Face detection** (Deteção facial): deteta e agrupa os rostos que aparecem no vídeo.
* **Identificação de celebridades**: O Video Indexer identifica automaticamente mais de 1 milhão de celebridades — como líderes mundiais, atores, atrizes, atletas, investigadores, negócios e líderes tecnológicos em todo o mundo. Os dados sobre estas celebridades também podem ser encontrados em vários sites (IMDB, Wikipédia, e assim por diante).
* **Identificação facial com base na conta**: o Video Indexer prepara um modelo para uma conta específica. Em seguida, reconhece rostos no vídeo baseados no modelo treinado. Para obter mais informações, consulte [Personalizar um modelo pessoa a partir do site do Indexer de Vídeo](customize-person-model-with-website.md) e [Personalizar um modelo pessoa com a API indexante de vídeo](customize-person-model-with-api.md).
* **Extração de miniaturas para rostos** ("melhor face"): Identifica automaticamente o rosto mais bem capturado em cada grupo de rostos (com base na qualidade, tamanho e posição frontal) e extrai-o como um ativo de imagem.
* **Reconhecimento de texto visual** (OCR): Extrai texto que é visualizado visualmente no vídeo.
* **Visual content moderation** (Moderação de conteúdo visual): deteta elementos visuais para adultos.
* **Labels identification** (Identificação através de etiquetas): identifica as ações e os objetos visuais apresentados.
* **Segmentação de cena**: Determina quando uma cena muda em vídeo com base em pistas visuais. Uma cena retrata um único evento e é composta por uma série de tiros consecutivos, que são semânticas relacionados.
* **Deteção de imagens**: Determina quando um tiro muda em vídeo com base em pistas visuais. Um tiro é uma série de quadros tirados da mesma câmara cinematográfica. Para mais informações, consulte [Cenas, fotos e quadros-chave.](scenes-shots-keyframes.md)
* **Black frame detection** (Deteção de fotogramas pretos): identifica os fotogramas pretos apresentados no vídeo.
* **Keyframe extraction** (Extração de keyframes): deteta keyframes estáveis num vídeo.
* **Créditos rolantes**: Identifica o início e o fim dos créditos rolantes no final de séries e filmes.
* **Deteção de caracteres animados** (pré-visualização): Deteção, agrupamento e reconhecimento de personagens em conteúdo animado através da integração com [a visão personalizada dos Serviços Cognitivos.](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) Para obter mais informações, consulte [a deteção de caracteres animados.](animated-characters-recognition.md)
* **Deteção editorial do tipo de tiro**: Tiros de marcação com base no seu tipo (como tiro largo, tiro médio, close-up, close up extremo, dois tiros, múltiplas pessoas, ao ar livre e interior, e assim por diante). Para obter mais informações, consulte [a deteção do tipo de tiro editorial.](scenes-shots-keyframes.md#editorial-shot-type-detection)

### <a name="audio-insights"></a>Insights áudio

* **Transcrição áudio**: Converte o discurso em texto em 12 línguas e permite extensões. São suportados os seguintes idiomas: alemão, árabe, chinês mandarim, coreano, espanhol, francês, hindu, inglês, italiano, japonês, português e russo.
* **Automatic language detection** (Deteção automática do idioma): identifica automaticamente o idioma falado dominante. São suportados os seguintes idiomas: alemão, chinês mandarim, espanhol, francês, inglês, italiano, japonês, português e russo. Se não conseguir identificar o idioma com confiança, o Video Indexer assumirá que o idioma falado é o inglês. Para obter mais informações, veja [Modelo de identificação de idioma](language-identification-model.md).
* **Identificação e transcrição de fala multi-linguístico**: Identifica automaticamente a língua falada em diferentes segmentos a partir do áudio. Envia cada segmento do ficheiro multimédia para ser transcrito e, em seguida, combina a transcrição de volta numa transcrição unificada. Para obter mais informações, veja [Identificar e transcrever automaticamente conteúdo de vários idiomas](multi-language-identification-transcription.md).
* **Closed captioning** (Legendas): cria legendas em três formatos: VTT, TTML e SRT.
* **Processamento de dois canais**: Auto deteta transcrição separada e funde-se numa única linha temporal.
* **Redução do ruído**: Limpa as gravações áudio ou ruidosas (com base nos filtros Skype).
* **Personalização de transcrições** (CRIS): treina a fala personalizada para modelos de texto para criar transcrições específicas da indústria. Para obter mais informações, consulte [Personalizar um modelo de linguagem a partir do site do Indexer de Vídeo](customize-language-model-with-website.md) e [Personalizar um modelo de linguagem com as APIs do Indexante de Vídeo](customize-language-model-with-api.md).
* **Enumeração do orador**: Mapas e compreende qual orador falou quais as palavras e quando. Dezasseis altifalantes podem ser detetados num único ficheiro áudio.
* **Estatísticas dos oradores**: Fornece estatísticas para os rácios de fala dos oradores.
* **Textual content moderation** (Moderação de conteúdo textual): deteta texto explícito na transcrição de áudio.
* **Efeitos áudio**: Identifica efeitos áudio como palmas das mãos, fala e silêncio.
* **Deteção de emoções**: Identifica emoções baseadas na fala (o que está a ser dito) e na tonalidade da voz (como está a ser dito). A emoção pode ser alegria, tristeza, raiva ou medo.
* **Translation** (Tradução): cria traduções da transcrição de áudio para 54 idiomas diferentes.

### <a name="audio-and-video-insights-multi-channels"></a>Insights de áudio e vídeo (vários canais)

Ao indexar por um canal, o resultado parcial para esses modelos estará disponível.

* **Extração de palavras-chave**: Extrai palavras-chave da fala e do texto visual.
* **Extração de entidades nomeadas**: Extrai marcas, locais e pessoas de linguagem e texto visual através do processamento de linguagem natural (NLP).
* **Topic inference** (Inferência de tópicos): faz inferências relativamente aos principais tópicos a partir de transcrições. A taxonomia IPTC de 2º nível está incluída.
* **Artifacts** (Artefactos): extrai um conjunto avançado de artefactos mais detalhados para cada um dos modelos.
* **Sentiment analysis** (Análise de sentimentos): identifica sentimentos positivos, negativos e neutros a partir do discurso e de texto visual.

## <a name="how-can-i-get-started-with-video-indexer"></a>Como posso começar com o Video Indexer?

Pode aceder às capacidades do Indexador de Vídeo de três formas:

* Portal do Indexer de Vídeo: Uma solução fácil de usar que permite avaliar o produto, gerir a conta e personalizar modelos.

    Para obter mais informações sobre o portal, consulte [Começar com o site do Indexer de Vídeo](video-indexer-get-started.md).  

* Integração da API: Todas as capacidades do Video Indexer estão disponíveis através de uma API REST, que permite integrar a solução nas suas apps e infraestruturas.

    Para começar como um desenvolvedor, consulte [Use Video Indexer REST API](video-indexer-use-apis.md).

* Widget incorporado: Permite incorporar as experiências do Video Indexer, player e editor na sua aplicação.

    Para obter mais informações, consulte [widgets visuais incorporados na sua aplicação.](video-indexer-embed-widgets.md)

Se estiver a utilizar o website, os insights são adicionados como metadados e são visíveis no portal. Se estiver a utilizar APIs, os insights estão disponíveis como um ficheiro JSON.

## <a name="next-steps"></a>Próximos passos

Está pronto para começar a utilizar o Video Indexer. Para obter mais informações, veja os seguintes artigos:

- [Começa com o site do Video Indexer.](video-indexer-get-started.md)
- [Processar conteúdo com índice de vídeo REST API](video-indexer-use-apis.md).
- [Incorporar widgets visuais na sua aplicação](video-indexer-embed-widgets.md).
