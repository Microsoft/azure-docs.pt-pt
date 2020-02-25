---
title: O que é a Imagem Digitalizada? - Visão Computacional
titleSuffix: Azure Cognitive Services
description: O serviço Imagem Digitalizada fornece aos programadores acesso a algoritmos avançados para processamento de imagens e devolução de informações.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 01/27/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: eb338c39ed3b1a095c1ad31e3f695e8d3fec0ed3
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2020
ms.locfileid: "77565966"
---
# <a name="what-is-computer-vision"></a>O que é a Imagem Digitalizada?

O serviço De Visão Computacional da Azure fornece aos desenvolvedores acesso a algoritmos avançados que processam imagens e devolvem informações, dependendo das funcionalidades visuais em que está interessado. Por exemplo, a Visão computacional pode determinar se uma imagem contém conteúdo adulto, ou pode encontrar todos os rostos humanos numa imagem.

Pode utilizar a Visão computacional na sua aplicação através de um SDK nativo ou invocando diretamente a API REST. Esta página cobre amplamente o que pode fazer com a Visão Computacional.

## <a name="computer-vision-for-digital-asset-management"></a>Visão Computacional para gestão de ativos digitais

A Computer Vision pode alimentar muitos cenários de gestão de ativos digitais (DAM). O DAM é o processo de negócio de organização, armazenamento e recuperação de ativos de mídia ricos e gestão de direitos e permissões digitais. Por exemplo, uma empresa pode querer agrupar e identificar imagens com base em logótipos visíveis, rostos, objetos, cores, e assim por diante. Ou, é melhor gerar automaticamente [legendas para imagens](./Tutorials/storage-lab-tutorial.md) e anexar palavras-chave para que sejam pesquisáveis. Para uma solução de DAM tudo-em-um utilizando Serviços Cognitivos, Pesquisa Cognitiva Azure e relatórios inteligentes, consulte o Guia acelerador de [soluções de mineração](https://github.com/Azure-Samples/azure-search-knowledge-mining) de conhecimento no GitHub. Para outros exemplos da DAM, consulte o repositório de modelos de solução de [visão computacional.](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates)

## <a name="analyze-images-for-insight"></a>Analisar imagens para introspeção

Pode analisar imagens para detetar e fornecer insights sobre as suas características visuais e características. Todas as funcionalidades na tabela abaixo são fornecidas pela [API de Imagem de Análise.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)

| Ação | Descrição |
| ------ | ----------- |
|**[Etiquetar elementos visuais](concept-tagging-images.md)**|Identifique e marque características visuais numa imagem, a partir de um conjunto de milhares de objetos reconhecíveis, seres vivos, cenários e ações. Quando as etiquetas são ambíguas ou não são do conhecimento comum, a resposta da API fornece dicas para clarificar o contexto da etiqueta. A identificação não se limita ao motivo principal, por exemplo, uma pessoa em primeiro plano, mas também inclui o cenário (interior ou exterior), mobiliário, ferramentas, plantas, animais, acessórios, gadgets, etc.|
|**[Detetar objetos](concept-object-detection.md)**| A deteção de objetos é semelhante à marcação, mas a API devolve as coordenadas da caixa de delimitação para cada etiqueta aplicada. Por exemplo, se uma imagem contiver um cão, um gato e uma pessoa, a operação Detect listará esses objetos juntamente com as suas coordenadas na imagem. Pode utilizar esta funcionalidade para processar mais relações entre os objetos numa imagem. Também permite saber quando há vários casos da mesma etiqueta numa imagem.|
|**[Detetar marcas](concept-brand-detection.md)**|Identifique marcas comerciais em imagens ou vídeos a partir de uma base de dados de milhares de logótipos globais. Pode utilizar esta funcionalidade, por exemplo, para descobrir quais as marcas mais populares nas redes sociais ou mais predominantes na colocação de produtos de mídia.|
|**[Categorizar uma imagem](concept-categorizing-images.md)**|Identifique e categorize uma imagem inteira, com uma [taxonomia de categorias](Category-Taxonomy.md) com hierarquias hereditárias principais/subordinadas. As categorias podem ser utilizadas isoladamente ou com os nossos novos modelos de identificação.<br/>Atualmente, o inglês é o único idioma suportado para identificar e categorizar imagens.|
|**[Descrever uma imagem](concept-describing-images.md)**|Gere uma descrição de uma imagem inteira numa linguagem legível por humanos, com frases completas. Os algoritmos da Imagem Digitalizada geram várias descrições com base nos objetos identificados na imagem. Cada uma das descrições é avaliada e é gerada uma pontuação de confiança. Em seguida, é devolvida uma lista ordenada da pontuação de confiança mais alta para a mais baixa.|
|**[Detetar rostos](concept-detecting-faces.md)** |Detete rostos numa imagem e forneça informações sobre cada rosto detetado. A Imagem Digitalizada devolve as coordenadas, o retângulo, o sexo e a idade para cada rosto detetado.<br/>A Computer Vision fornece um subconjunto da funcionalidade do serviço [Face.](/azure/cognitive-services/face/) Pode utilizar o serviço Face para uma análise mais detalhada, como identificação facial e deteção de poses.|
|**[Detetar tipos de imagem](concept-detecting-image-types.md)**|Detete características sobre uma imagem como, por exemplo, se uma imagem é um desenho de linha ou a probabilidade de uma imagem ser ClipArt.|
|**[Detetar conteúdo específico de um domínio](concept-detecting-domain-content.md)**|Utilize modelos de domínio para detetar e identificar conteúdo específico de um domínio numa imagem, como celebridades e marcos de referência. Por exemplo, se uma imagem contiver pessoas, a Computer Vision pode usar um modelo de domínio para celebridades para determinar se as pessoas detetadas na imagem são celebridades conhecidas.|
|**[Detetar o esquema de cores](concept-detecting-color-schemes.md)**|Analise a utilização de cor numa imagem. A Imagem Digitalizada pode determinar se uma imagem é a preto e branco ou a cores, e no caso das imagens a cores, identificar as cores dominantes e de destaque.|
|**[Gerar uma miniatura](concept-generating-thumbnails.md)**|Analise o conteúdo de uma imagem para gerar uma miniatura adequada dessa imagem. A Computer Vision primeiro gera uma miniatura de alta qualidade e depois analisa os objetos dentro da imagem para determinar a *área de interesse*. A Visão Computacional então planta a imagem para se adaptar aos requisitos da área de interesse. A miniatura gerada pode ser apresentada com uma proporção diferente da proporção da imagem original, consoante as suas necessidades.|
|**[Obter a área de interesse](concept-generating-thumbnails.md#area-of-interest)**|Analise o conteúdo de uma imagem para devolver as coordenadas da *área de interesse.* Em vez de cortar a imagem e gerar uma miniatura, a Computer Vision devolve as coordenadas da caixa de delimitação da região, para que a aplicação de chamada possa modificar a imagem original conforme desejado.|

## <a name="extract-text-from-images"></a>Extrair texto de imagens

Pode utilizar a Computer Vision [Read](concept-recognizing-text.md#read-api) API para extrair texto impresso e manuscrito a partir de imagens num fluxo de caracteres legível por máquina. A API read usa os nossos modelos mais recentes e trabalha com texto em várias superfícies e fundos, tais como recibos, cartazes, cartões de visita, cartas e quadros. Atualmente, o inglês e o espanhol são as únicas línguas apoiadas.

Também pode utilizar a API de reconhecimento ótico de [caracteres (OCR)](concept-recognizing-text.md#ocr-optical-character-recognition-api) para extrair texto impresso em várias línguas. Se necessário, o OCR corrige a rotação do texto reconhecido e fornece as coordenadas de quadro de cada palavra. O OCR suporta 25 línguas e deteta automaticamente a linguagem do texto reconhecido.

## <a name="moderate-content-in-images"></a>Conteúdo moderado em imagens

Pode usar a Computer Vision para [detetar conteúdo adulto](concept-detecting-adult-content.md) numa imagem e devolver pontuações de confiança para diferentes classificações. O limiar para o sinalizamento do conteúdo pode ser definido numa escala deslizante para acomodar as suas preferências.

## <a name="use-containers"></a>Utilizar contentores

[Utilize recipientes Computer Vision](computer-vision-how-to-install-containers.md) para reconhecer texto impresso e manuscrito localmente, instalando um recipiente Docker padronizado mais próximo dos seus dados.

## <a name="image-requirements"></a>Requisitos de imagem

A Imagem Digitalizada pode analisar imagens que cumprem os requisitos seguintes:

- A imagem tem de ser apresentada no formato JPEG, PNG, GIF ou BMP
- O tamanho do ficheiro da imagem tem de ser inferior a 4 megabytes (MB)
- As dimensões da imagem têm de ser superiores a 50 x 50 píxeis
  - Para a API read, as dimensões da imagem devem estar entre 50 x 50 e 10000 x 10000 pixels.

## <a name="data-privacy-and-security"></a>Privacidade e segurança de dados

Tal como acontece com todos os Serviços Cognitivos, os desenvolvedores que utilizam o serviço Computer Vision devem estar cientes das políticas da Microsoft em dados de clientes. Consulte a página de [Serviços Cognitivos](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) no Microsoft Trust Center para saber mais.

## <a name="next-steps"></a>Passos seguintes

Comece com a Visão computacional seguindo um guia de arranque rápido:

- [Quickstart: Computer Vision .NET SDK](quickstarts-sdk/csharp-sdk.md)
- [Quickstart: Computer Vision Python SDK](quickstarts-sdk/python-sdk.md)
- [Quickstart: Computer Vision Java SDK](quickstarts-sdk/java-sdk.md)
