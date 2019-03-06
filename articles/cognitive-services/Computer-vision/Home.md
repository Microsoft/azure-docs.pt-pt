---
title: O que é a API de Imagem Digitalizada? -Imagem digitalizada
titlesuffix: Azure Cognitive Services
description: O serviço Imagem Digitalizada fornece aos programadores acesso a algoritmos avançados para processamento de imagens e devolução de informações.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 03/04/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 87c5d24f0e9077acf66bacd97097cfc32bd49ead
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57432112"
---
# <a name="what-is-computer-vision"></a>O que é a Imagem Digitalizada?

Serviço de visão do computador do Azure fornece aos desenvolvedores acesso a algoritmos avançados que processar imagens e retornar informações. Para analisar uma imagem, pode carregar uma imagem ou especificar um URL de imagem. As imagens de algoritmos de processamento podem analisar o conteúdo de várias maneiras diferentes, dependendo dos recursos visual que está interessado. Por exemplo, de imagem digitalizada pode determinar se uma imagem contém conteúdo adultos, ou pode encontrar todos os rostos humanos numa imagem.

Pode utilizar de imagem digitalizada na sua aplicação utilizando um SDK nativo ou invocar a API REST diretamente. Esta página aborda amplamente o que pode fazer com a visão do computador.

## <a name="analyze-images-for-insight"></a>Analise imagens para informações detalhadas

Pode analisar imagens para detetar e fornecem informações sobre seus recursos do visual e características. Todos os recursos na tabela abaixo são fornecidos pela [analisar imagem](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API.

| Ação | Descrição |
| ------ | ----------- |
|**[Etiquetar elementos visuais](concept-tagging-images.md)**|Identificar e etiquetar recursos visuais numa imagem, a partir de um conjunto de milhares de objetos reconhecíveis, vivos, paisagens e ações. Quando as etiquetas são ambíguas ou dados de conhecimento não comuns, a resposta de API fornece "sugestões" para clarificar o significado da etiqueta no contexto de uma configuração conhecida. A identificação não se limita ao motivo principal, por exemplo, uma pessoa em primeiro plano, mas também inclui o cenário (interior ou exterior), mobiliário, ferramentas, plantas, animais, acessórios, gadgets, etc.|
|**[Detetar objetos](concept-object-detection.md)**| Deteção de objetos é semelhante à marcação, mas a API devolve as coordenadas da caixa delimitadora para cada etiqueta aplicada. Por exemplo, se uma imagem contiver um cachorro, gato e person, a operação de deteção irá listar esses objetos, juntamente com suas coordenadas na imagem. Pode utilizar esta funcionalidade para processar mais relações entre os objetos numa imagem. Ele também permite-lhe saber quando existem várias instâncias da mesma marca numa imagem.|
|**[Detectar marcas](concept-brand-detection.md)**|Identifica marcas comerciais em imagens ou vídeos de um banco de dados de milhares de logotipos global. Pode utilizar esta funcionalidade, por exemplo, para descobrir quais marcas são mais populares nas redes sociais ou mais predominante no posicionamento de produto do suporte de dados.|
|**[Categorizar uma imagem](concept-categorizing-images.md)**|Identifique e categorize uma imagem inteira, com uma [taxonomia de categorias](Category-Taxonomy.md) com hierarquias hereditárias principais/subordinadas. As categorias podem ser utilizadas isoladamente ou com os nossos novos modelos de identificação.<br/>Atualmente, o inglês é o único idioma suportado para identificar e categorizar imagens.|
|**[Descrever uma imagem](concept-describing-images.md)**|Gere uma descrição de uma imagem inteira numa linguagem legível por humanos, com frases completas. Os algoritmos da Imagem Digitalizada geram várias descrições com base nos objetos identificados na imagem. Cada uma das descrições é avaliada e é gerada uma pontuação de confiança. Em seguida, é devolvida uma lista ordenada da pontuação de confiança mais alta para a mais baixa.|
|**[Detetar rostos](concept-detecting-faces.md)** |Detete rostos numa imagem e forneça informações sobre cada rosto detetado. A Imagem Digitalizada devolve as coordenadas, o retângulo, o sexo e a idade para cada rosto detetado.<br/>A Imagem Digitalizada fornece um subconjunto das funcionalidades que podem ser encontradas na [Face](/azure/cognitive-services/face/) e pode utilizar o serviço Face para uma análise mais detalhada, como identificação facial e deteção de poses.|
|**[Detetar tipos de imagem](concept-detecting-image-types.md)**|Detete características sobre uma imagem como, por exemplo, se uma imagem é um desenho de linha ou a probabilidade de uma imagem ser ClipArt.|
|**[Detetar conteúdo específico de um domínio](concept-detecting-domain-content.md)**|Utilize modelos de domínio para detetar e identificar conteúdo específico de um domínio numa imagem, como celebridades e marcos de referência. Por exemplo, se uma imagem incluir pessoas, a Imagem Digitalizada pode utilizar um modelo de domínio para celebridades incluído com o serviço para determinar se as pessoas detetadas na imagem correspondem a celebridades conhecidas.|
|**[Detetar o esquema de cores](concept-detecting-color-schemes.md)**|Analise a utilização de cor numa imagem. A Imagem Digitalizada pode determinar se uma imagem é a preto e branco ou a cores, e no caso das imagens a cores, identificar as cores dominantes e de destaque.|
|**[Gerar uma miniatura](concept-generating-thumbnails.md)**|Analise o conteúdo de uma imagem para gerar uma miniatura adequada dessa imagem. Imagem digitalizada primeiro gera uma miniatura de alta qualidade e, em seguida, analisa os objetos na imagem para determinar a *área de interesse*. Imagem digitalizada, em seguida, recorta a imagem de acordo com os requisitos da área de interesse. A miniatura gerada pode ser apresentada com uma proporção diferente da proporção da imagem original, consoante as suas necessidades.|
|**[Obter a área de interesse](concept-generating-thumbnails.md#area-of-interest)**|Analisar o conteúdo de uma imagem para retornar as coordenadas do *área de interesse*. Esta é a mesma função que é utilizada para gerar uma miniatura, mas em vez de corte a imagem, de imagem digitalizada devolve as coordenadas da caixa delimitadora da região, para que o aplicativo de chamada pode modificar a imagem original, conforme desejado.|

## <a name="extract-text-from-images"></a>Extrair texto de imagens

Pode usar de imagem digitalizada para extrair texto de uma imagem num meio de fluxo de carateres legível por máquina [reconhecimento ótico de carateres (OCR)](concept-recognizing-text.md#ocr-optical-character-recognition-api). Se for necessário, OCR corrige a rotação do texto reconhecido e fornece as coordenadas de quadro de cada palavra. OCR suporta 25 idiomas e Deteta automaticamente o idioma do texto reconhecido.

Também pode utilizar o [leitura API](concept-recognizing-text.md#read-api) para extrair texto de impresso e manuscrito de imagens e documentos de muito texto. A API de leitura utiliza modelos atualizados e funciona para objetos uma variedade com diferentes superfícies e fundos, como recibos, cartazes, cartões de visita, letras e quadros de comunicações. Atualmente, a API de leitura está em pré-visualização e o inglês é o único idioma suportado.

## <a name="moderate-content-in-images"></a>Moderar conteúdo em imagens

Pode usar para imagem digitalizada [detetar conteúdos para adultos](concept-detecting-adult-content.md) numa imagem e devolver uma confiança de pontuação para ambos. O filtro para a deteção de conteúdo para adultos ou profano pode ser definido numa escala móvel para se adaptar às suas preferências.

## <a name="use-containers"></a>Utilizar contentores

[Utilize contentores de imagem digitalizada](computer-vision-how-to-install-containers.md) reconhecer texto impresso e manuscritos localmente ao instalar um contentor Docker padronizado mais perto aos seus dados.

## <a name="image-requirements"></a>Requisitos de imagem

A Imagem Digitalizada pode analisar imagens que cumprem os requisitos seguintes:

- A imagem tem de ser apresentada no formato JPEG, PNG, GIF ou BMP
- O tamanho do ficheiro da imagem tem de ser inferior a 4 megabytes (MB)
- As dimensões da imagem têm de ser superiores a 50 x 50 píxeis
  - Para OCR, as dimensões da imagem têm de ser entre 50 x 50 e 4200 x 4200 píxeis

## <a name="data-privacy-and-security"></a>Segurança e privacidade dos dados

Como com todos os serviços cognitivos, os desenvolvedores que usam o serviço de visão do computador devem estar ciente das políticas da Microsoft em dados do cliente. Consulte a [página dos serviços cognitivos](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) no Microsoft Trust Center para obter mais informações.

## <a name="next-steps"></a>Passos Seguintes

Introdução à visão do computador ao seguir um guia de início rápido:

- [Quickstart: Analisar uma imagem](quickstarts-sdk/csharp-analyze-sdk.md)
- [Quickstart: Extrair texto manuscrito](quickstarts-sdk/csharp-hand-text-sdk.md)
- [Quickstart: Gere uma miniatura](quickstarts-sdk/csharp-thumb-sdk.md)
