---
title: O que é a Imagem Digitalizada?
titleSuffix: Azure Cognitive Services
description: O serviço De Visão De Computador fornece-lhe acesso a algoritmos avançados para processar imagens e devolver informações.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 11/13/2020
ms.author: pafarley
ms.custom:
- seodec18
- cog-serv-seo-aug-2020
keywords: visão computacional, aplicações de visão computacional, serviço de visão computacional
ms.openlocfilehash: aeaa10870b8b40b907da1d1660e77ebf0271c300
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629755"
---
# <a name="what-is-computer-vision"></a>O que é a Imagem Digitalizada?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

O serviço de Visão Computacional da Azure dá-lhe acesso a algoritmos avançados que processam imagens e devolvem informações com base nas funcionalidades visuais que lhe interessam. Por exemplo, a Visão Computacional pode determinar se uma imagem contém conteúdo adulto, encontrar marcas ou objetos específicos ou encontrar rostos humanos.

Pode criar aplicações de Visão de Computador através de uma [biblioteca de clientes SDK](./quickstarts-sdk/client-library.md) ou ligando diretamente para a [API REST.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) Esta página cobre amplamente o que pode fazer com a Visão de Computador.

## <a name="optical-character-recognition-ocr"></a>Reconhecimento ótico de carateres (OCR)

A Visão Computacional inclui capacidades [de reconhecimento de caracteres óticos (OCR).](concept-recognizing-text.md) Pode utilizar a nova API de leitura para extrair texto impresso e manuscrito a partir de imagens e documentos. Utiliza os modelos mais recentes e trabalha com texto em várias superfícies e fundos. Estes incluem recibos, cartazes, cartões de visita, cartas e quadros. As duas APIs OCR suportam a extração de texto impresso em [várias línguas.](./language-support.md) Siga um [arranque rápido](./quickstarts-sdk/client-library.md) para começar.

## <a name="computer-vision-for-digital-asset-management"></a>Visão de Computador para gestão de ativos digitais

A Visão Computacional pode alimentar muitos cenários de gestão de ativos digitais (DAM). A DAM é o processo de negócio de organização, armazenamento e recuperação de ativos de media ricos e gestão de direitos e permissões digitais. Por exemplo, uma empresa pode querer agrupar e identificar imagens com base em logótipos visíveis, rostos, objetos, cores, e assim por diante. Ou, pode querer gerar automaticamente [legendas para imagens](./Tutorials/storage-lab-tutorial.md) e anexar palavras-chave para que sejam pescandizáveis. Para obter uma solução DAM tudo-em-um utilizando serviços cognitivos, pesquisa cognitiva Azure e relatórios inteligentes, consulte o [Guia de Acelerador de Soluções de Mineração de Conhecimento](https://github.com/Azure-Samples/azure-search-knowledge-mining) no GitHub. Para outros exemplos da DAM, consulte o [repositório de modelos de solução de visão](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates) computacional.

## <a name="analyze-images-for-insight"></a>Analisar imagens para obter informações

Pode analisar imagens para fornecer insights sobre as suas características e características visuais. Todas as funcionalidades da tabela abaixo são fornecidas pela [API de Imagem de Análise.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) Siga um [arranque rápido](./quickstarts-sdk/client-library.md) para começar.


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

Detete rostos numa imagem e forneça informações sobre cada rosto detetado. A Imagem Digitalizada devolve as coordenadas, o retângulo, o sexo e a idade para cada rosto detetado.<br/>A Visão Computacional fornece um subconjunto da funcionalidade do serviço [Face.](/azure/cognitive-services/face/) Pode utilizar o serviço Face para análises mais detalhadas, como identificação facial e deteção de poses. [Detetar rostos](concept-detecting-faces.md)

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

## <a name="use-containers"></a>Utilizar contentores

Utilize recipientes Do Docker de visão de computador para utilizar o serviço no local. Atualmente existem dois contentores disponíveis:

* O [recipiente de leitura de Visão de Computador](computer-vision-how-to-install-containers.md) permite-lhe reconhecer texto impresso e manuscrito em imagens.

* O [recipiente de análise espacial Computer Vision](spatial-analysis-container.md) permite-lhe analisar vídeos de streaming em tempo real para entender as relações espaciais entre as pessoas e o seu movimento através de ambientes físicos.

## <a name="image-requirements"></a>Requisitos de imagem

A Imagem Digitalizada pode analisar imagens que cumprem os requisitos seguintes:

- A imagem tem de ser apresentada no formato JPEG, PNG, GIF ou BMP
- O tamanho do ficheiro da imagem tem de ser inferior a 4 megabytes (MB)
- As dimensões da imagem têm de ser superiores a 50 x 50 píxeis
  - Para a API de leitura, as dimensões da imagem devem estar entre 50 x 50 e 10000 x 10000 pixels.

## <a name="data-privacy-and-security"></a>Privacidade e segurança dos dados

Tal como acontece com todos os Serviços Cognitivos, os desenvolvedores que usam o serviço de Visão de Computador devem estar cientes das políticas da Microsoft sobre os dados dos clientes. Consulte a [página de Serviços Cognitivos](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) no Microsoft Trust Center para saber mais.

## <a name="next-steps"></a>Passos seguintes

Começa com a Visão De Computador seguindo o guia de arranque rápido na tua linguagem de desenvolvimento preferida:

- [Quickstart: Biblioteca de clientes da Visão De Computador](./quickstarts-sdk/client-library.md)
