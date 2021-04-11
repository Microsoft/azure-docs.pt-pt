---
title: O que é Análise de Imagem?
titleSuffix: Azure Cognitive Services
description: O serviço de Análise de Imagem utiliza modelos de IA pré-treinados para extrair muitas funcionalidades visuais diferentes das imagens.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 03/30/2021
ms.author: pafarley
keywords: visão computacional, aplicações de visão computacional, serviço de visão computacional
ms.openlocfilehash: f262fdb49cac4ab9abe7f3f6873160d3059968c6
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106287276"
---
# <a name="what-is-image-analysis"></a>O que é Análise de Imagem?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

O serviço de Análise de Imagem de Visão por Computador pode extrair uma grande variedade de funcionalidades visuais a partir das suas imagens. Por exemplo, pode determinar se uma imagem contém conteúdo adulto, encontrar marcas ou objetos específicos ou encontrar rostos humanos.

Pode utilizar a Análise de Imagem através de uma biblioteca de clientes SDK ou ligando diretamente para a [API REST.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) Siga o [quickstart](quickstarts-sdk/image-analysis-client-library.md) para começar.

Esta documentação contém os seguintes tipos de artigos:
* Os [arranques rápidos](./quickstarts-sdk/image-analysis-client-library.md) são instruções passo a passo que permitem fazer chamadas para o serviço e obter resultados num curto espaço de tempo. 
* Os [guias de como fazer](./Vision-API-How-to-Topics/HowToCallVisionAPI.md) contêm instruções para utilizar o serviço de forma mais específica ou personalizada.
* Os [artigos conceptuais](concept-tagging-images.md) fornecem explicações aprofundadas sobre a funcionalidade e funcionalidades do serviço.
* Os [tutoriais](./tutorials/storage-lab-tutorial.md) são guias mais longos que mostram como usar este serviço como componente em soluções de negócio mais amplas.

## <a name="image-analysis-features"></a>Recursos de Análise de Imagem

Pode analisar imagens para fornecer insights sobre as suas características e características visuais. Todas as funcionalidades da lista abaixo são fornecidas pela [API de Imagem de Análise.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) Siga um [arranque rápido](./quickstarts-sdk/image-analysis-client-library.md) para começar.


### <a name="tag-visual-features"></a>Etiquetar elementos visuais

Identificar e marcar características visuais numa imagem, a partir de um conjunto de milhares de objetos reconhecíveis, seres vivos, cenários e ações. Quando as etiquetas são ambíguas ou não são de conhecimento comum, a resposta da API fornece dicas para clarificar o contexto da etiqueta. A identificação não se limita ao motivo principal, por exemplo, uma pessoa em primeiro plano, mas também inclui o cenário (interior ou exterior), mobiliário, ferramentas, plantas, animais, acessórios, gadgets, etc. [Etiquetar elementos visuais](concept-tagging-images.md)

### <a name="detect-objects"></a>Detetar objetos

A deteção de objetos é semelhante à marcação, mas a API devolve as coordenadas da caixa de limitação para cada tag aplicada. Por exemplo, se uma imagem contiver um cão, gato e pessoa, a operação Detect listará esses objetos juntamente com as suas coordenadas na imagem. Pode utilizar esta funcionalidade para processar mais relações entre os objetos numa imagem. Também lhe permite saber quando há vários casos da mesma etiqueta numa imagem. [Detetar objetos](concept-object-detection.md)

### <a name="detect-brands"></a>Detetar marcas

Identifique marcas comerciais em imagens ou vídeos de uma base de dados de milhares de logótipos globais. Pode utilizar esta funcionalidade, por exemplo, para descobrir quais as marcas mais populares nas redes sociais ou as mais predominantes na colocação de produtos de mídia. [Detetar marcas](concept-brand-detection.md)

### <a name="categorize-an-image"></a>Categorizar uma imagem

Identifique e categorize uma imagem inteira, com uma [taxonomia de categorias](Category-Taxonomy.md) com hierarquias hereditárias principais/subordinadas. As categorias podem ser utilizadas isoladamente ou com os nossos novos modelos de identificação.<br/>Atualmente, o inglês é o único idioma suportado para identificar e categorizar imagens. [Categorizar uma imagem](concept-categorizing-images.md)

### <a name="describe-an-image"></a>Descrever uma imagem

Gere uma descrição de uma imagem inteira numa linguagem legível por humanos, com frases completas. Os algoritmos da Imagem Digitalizada geram várias descrições com base nos objetos identificados na imagem. Cada uma das descrições é avaliada e é gerada uma pontuação de confiança. Em seguida, é devolvida uma lista ordenada da pontuação de confiança mais alta para a mais baixa. [Descrever uma imagem](concept-describing-images.md)

### <a name="detect-faces"></a>Detetar rostos

Detete rostos numa imagem e forneça informações sobre cada rosto detetado. A Imagem Digitalizada devolve as coordenadas, o retângulo, o sexo e a idade para cada rosto detetado.<br/>A Visão Computacional fornece um subconjunto da funcionalidade do serviço [Face.](../face/index.yml) Pode utilizar o serviço Face para análises mais detalhadas, como identificação facial e deteção de poses. [Detetar rostos](concept-detecting-faces.md)

### <a name="detect-image-types"></a>Detetar tipos de imagem

Detete características sobre uma imagem como, por exemplo, se uma imagem é um desenho de linha ou a probabilidade de uma imagem ser ClipArt. [Detetar tipos de imagem](concept-detecting-image-types.md)

### <a name="detect-domain-specific-content"></a>Detetar conteúdo específico de um domínio

Utilize modelos de domínio para detetar e identificar conteúdo específico de um domínio numa imagem, como celebridades e marcos de referência. Por exemplo, se uma imagem contiver pessoas, a Visão de Computador pode usar um modelo de domínio para as celebridades determinarem se as pessoas detetadas na imagem são celebridades conhecidas. [Detetar conteúdo específico de um domínio](concept-detecting-domain-content.md)

### <a name="detect-the-color-scheme"></a>Detetar o esquema de cores

Analise a utilização de cor numa imagem. A Imagem Digitalizada pode determinar se uma imagem é a preto e branco ou a cores, e no caso das imagens a cores, identificar as cores dominantes e de destaque. [Detetar o esquema de cores](concept-detecting-color-schemes.md)

### <a name="generate-a-thumbnail"></a>Gerar uma miniatura

Analise o conteúdo de uma imagem para gerar uma miniatura adequada dessa imagem. A Visão Computacional gera primeiro uma miniatura de alta qualidade e depois analisa os objetos dentro da imagem para determinar a *área de interesse*. A Visão Computacional, em seguida, planta a imagem para se adaptar às exigências da área de interesse. A miniatura gerada pode ser apresentada com uma proporção diferente da proporção da imagem original, consoante as suas necessidades. [Gerar uma miniatura](concept-generating-thumbnails.md)

### <a name="get-the-area-of-interest"></a>Obter a área de interesse

Analise o conteúdo de uma imagem para devolver as coordenadas da *área de interesse.* Em vez de cortar a imagem e gerar uma miniatura, a Visão Computacional devolve as coordenadas da caixa de delimitação da região, para que a aplicação de chamada possa modificar a imagem original conforme desejado. [Obter a área de interesse](concept-generating-thumbnails.md#area-of-interest)

## <a name="moderate-content-in-images"></a>Moderar conteúdo em imagens

Pode utilizar a Visão De Computador para [detetar conteúdo adulto](concept-detecting-adult-content.md) numa imagem e devolver pontuações de confiança para diferentes classificações. O limiar para o conteúdo de sinalização pode ser definido numa escala deslizante para acomodar as suas preferências.

## <a name="image-requirements"></a>Requisitos de imagem

A Análise de Imagem funciona em imagens que cumprem os seguintes requisitos:

- A imagem tem de ser apresentada no formato JPEG, PNG, GIF ou BMP
- O tamanho do ficheiro da imagem tem de ser inferior a 4 megabytes (MB)
- As dimensões da imagem têm de ser superiores a 50 x 50 píxeis

## <a name="data-privacy-and-security"></a>Privacidade e segurança dos dados

Tal como acontece com todos os Serviços Cognitivos, os desenvolvedores que usam o serviço de Visão de Computador devem estar cientes das políticas da Microsoft sobre os dados dos clientes. Consulte a [página de Serviços Cognitivos](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) no Microsoft Trust Center para saber mais.

## <a name="next-steps"></a>Passos seguintes

Começa com a Análise de Imagem seguindo o guia de arranque rápido na tua linguagem de desenvolvimento preferida:

- [Quickstart: Computer Vision REST API ou bibliotecas de clientes](./quickstarts-sdk/image-analysis-client-library.md)
