---
title: O que é a Imagem Digitalizada? -Pesquisa Visual Computacional
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
ms.openlocfilehash: 17dd428f79f66bc70da47527314ad43a27955c82
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76772508"
---
# <a name="what-is-computer-vision"></a>O que é a Imagem Digitalizada?

O serviço de Pesquisa Visual Computacional do Azure fornece aos desenvolvedores acesso a algoritmos avançados que processam imagens e retornam informações, dependendo dos recursos visuais nos quais você está interessado. Por exemplo, Pesquisa Visual Computacional pode determinar se uma imagem contém conteúdo adulto ou pode encontrar todas as faces humanas em uma imagem.

Você pode usar Pesquisa Visual Computacional em seu aplicativo por meio de um SDK nativo ou invocando a API REST diretamente. Esta página abrange em grande escala o que você pode fazer com Pesquisa Visual Computacional.

## <a name="computer-vision-for-digital-asset-management"></a>Pesquisa Visual Computacional para o gerenciamento de ativos digitais

Pesquisa Visual Computacional pode alimentar muitos cenários de DAM (gerenciamento de ativos digitais). DAM é o processo comercial de organizar, armazenar e recuperar ativos de mídia avançados e gerenciar direitos e permissões digitais. Por exemplo, uma empresa pode querer agrupar e identificar imagens com base em logotipos, rostos, objetos, cores visíveis e assim por diante. Ou, talvez você queira gerar automaticamente [legendas para imagens](./Tutorials/storage-lab-tutorial.md) e anexar palavras-chave para que elas sejam pesquisáveis. Para uma solução de DAM All-in-One usando serviços cognitivas, Pesquisa Cognitiva do Azure e relatórios inteligentes, consulte o [Guia do Solution Accelerator do Knowledge Mining](https://github.com/Azure-Samples/azure-search-knowledge-mining) no github. Para outros exemplos de DAM, consulte o Pesquisa Visual Computacional repositório de [modelos de solução](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates) .

## <a name="analyze-images-for-insight"></a>Analisar imagens para introspeção

Você pode analisar imagens para detectar e fornecer informações sobre seus recursos e características visuais. Todos os recursos na tabela a seguir são fornecidos pela API de [análise de imagem](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) .

| Ação | Descrição |
| ------ | ----------- |
|**[Etiquetar elementos visuais](concept-tagging-images.md)**|Identificar e marcar recursos visuais em uma imagem, de um conjunto de milhares de objetos reconhecíveis, coisas em ação, cenários e ações. Quando as marcas são ambíguas ou não são conhecimento comuns, a resposta da API fornece dicas para esclarecer o contexto da marca. A identificação não se limita ao motivo principal, por exemplo, uma pessoa em primeiro plano, mas também inclui o cenário (interior ou exterior), mobiliário, ferramentas, plantas, animais, acessórios, gadgets, etc.|
|**[Detectar objetos](concept-object-detection.md)**| A detecção de objeto é semelhante à marcação, mas a API retorna as coordenadas da caixa delimitadora para cada marca aplicada. Por exemplo, se uma imagem contiver um cachorro, um gato e uma pessoa, a operação de detecção listará esses objetos junto com suas coordenadas na imagem. Você pode usar essa funcionalidade para processar mais relações entre os objetos em uma imagem. Ele também permite que você saiba quando há várias instâncias da mesma marca em uma imagem.|
|**[Detectar marcas](concept-brand-detection.md)**|Identifique marcas comerciais em imagens ou vídeos de um banco de dados de milhares de logotipos globais. Você pode usar esse recurso, por exemplo, para descobrir quais marcas são mais populares em mídia social ou mais predominantes no posicionamento do produto de mídia.|
|**[Categorizar uma imagem](concept-categorizing-images.md)**|Identifique e categorize uma imagem inteira, com uma [taxonomia de categorias](Category-Taxonomy.md) com hierarquias hereditárias principais/subordinadas. As categorias podem ser utilizadas isoladamente ou com os nossos novos modelos de identificação.<br/>Atualmente, o inglês é o único idioma suportado para identificar e categorizar imagens.|
|**[Descrever uma imagem](concept-describing-images.md)**|Gere uma descrição de uma imagem inteira numa linguagem legível por humanos, com frases completas. Os algoritmos da Imagem Digitalizada geram várias descrições com base nos objetos identificados na imagem. Cada uma das descrições é avaliada e é gerada uma pontuação de confiança. Em seguida, é devolvida uma lista ordenada da pontuação de confiança mais alta para a mais baixa.|
|**[Detetar rostos](concept-detecting-faces.md)** |Detete rostos numa imagem e forneça informações sobre cada rosto detetado. A Imagem Digitalizada devolve as coordenadas, o retângulo, o sexo e a idade para cada rosto detetado.<br/>Pesquisa Visual Computacional fornece um subconjunto da funcionalidade do serviço de [face](/azure/cognitive-services/face/) . Você pode usar o serviço de face para análise mais detalhada, como identificação facial e detecção de pose.|
|**[Detetar tipos de imagem](concept-detecting-image-types.md)**|Detete características sobre uma imagem como, por exemplo, se uma imagem é um desenho de linha ou a probabilidade de uma imagem ser ClipArt.|
|**[Detetar conteúdo específico de um domínio](concept-detecting-domain-content.md)**|Utilize modelos de domínio para detetar e identificar conteúdo específico de um domínio numa imagem, como celebridades e marcos de referência. Por exemplo, se uma imagem contiver pessoas, Pesquisa Visual Computacional poderá usar um modelo de domínio para celebridades para determinar se as pessoas detectadas na imagem são celebridades conhecidas.|
|**[Detetar o esquema de cores](concept-detecting-color-schemes.md)**|Analise a utilização de cor numa imagem. A Imagem Digitalizada pode determinar se uma imagem é a preto e branco ou a cores, e no caso das imagens a cores, identificar as cores dominantes e de destaque.|
|**[Gerar uma miniatura](concept-generating-thumbnails.md)**|Analise o conteúdo de uma imagem para gerar uma miniatura adequada dessa imagem. Pesquisa Visual Computacional primeiro gera uma miniatura de alta qualidade e, em seguida, analisa os objetos dentro da imagem para determinar a *área de interesse*. Pesquisa Visual Computacional, em seguida, corta a imagem para se ajustar aos requisitos da área de interesse. A miniatura gerada pode ser apresentada com uma proporção diferente da proporção da imagem original, consoante as suas necessidades.|
|**[Obter a área de interesse](concept-generating-thumbnails.md#area-of-interest)**|Analise o conteúdo de uma imagem para retornar as coordenadas da *área de interesse*. Em vez de cortar a imagem e gerar uma miniatura, Pesquisa Visual Computacional retorna as coordenadas da caixa delimitadora da região, para que o aplicativo de chamada possa modificar a imagem original conforme desejado.|

## <a name="extract-text-from-images"></a>Extrair texto de imagens

Você pode usar Pesquisa Visual Computacional API de [leitura](concept-recognizing-text.md#read-api) para extrair texto impresso e manuscrito de imagens em um fluxo de caracteres legível por máquina. A API de leitura usa nossos modelos mais recentes e trabalha com texto em uma variedade de superfícies e planos de fundo, como recibos, pôsteres, cartões de visita, cartas e quadros de comunicações. Atualmente, o inglês é o único idioma com suporte.

Você também pode usar a API de [OCR (reconhecimento óptico de caracteres)](concept-recognizing-text.md#ocr-optical-character-recognition-api) para extrair o texto impresso em vários idiomas. Se necessário, o OCR corrige a rotação do texto reconhecido e fornece as coordenadas de quadro de cada palavra. O OCR dá suporte a 25 idiomas e detecta automaticamente o idioma do texto reconhecido.

## <a name="moderate-content-in-images"></a>Conteúdo moderado em imagens

Você pode usar Pesquisa Visual Computacional para [detectar conteúdo adulto](concept-detecting-adult-content.md) em uma imagem e retornar pontuações de confiança para diferentes classificações. O limite para a sinalização de conteúdo pode ser definido em uma escala deslizante para acomodar suas preferências.

## <a name="use-containers"></a>Utilizar contentores

[Use pesquisa Visual computacional contêineres](computer-vision-how-to-install-containers.md) para reconhecer texto impresso e manuscrito localmente, instalando um contêiner do Docker padronizado mais perto de seus dados.

## <a name="image-requirements"></a>Requisitos de imagem

A Imagem Digitalizada pode analisar imagens que cumprem os requisitos seguintes:

- A imagem tem de ser apresentada no formato JPEG, PNG, GIF ou BMP
- O tamanho do ficheiro da imagem tem de ser inferior a 4 megabytes (MB)
- As dimensões da imagem têm de ser superiores a 50 x 50 píxeis
  - Para a API de leitura, as dimensões da imagem devem estar entre 50 x 50 e 10000 x 10000 pixels.

## <a name="data-privacy-and-security"></a>Privacidade e segurança de dados

Assim como acontece com todos os serviços cognitivas, os desenvolvedores que usam o serviço de Pesquisa Visual Computacional devem estar cientes das políticas da Microsoft nos dados do cliente. Consulte a página de [Serviços Cognitivos](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) no Microsoft Trust Center para saber mais.

## <a name="next-steps"></a>Passos seguintes

Comece a usar o Pesquisa Visual Computacional seguindo um guia de início rápido:

- [Início rápido: SDK do .NET Pesquisa Visual Computacional](quickstarts-sdk/csharp-sdk.md)
- [Início rápido: SDK do Pesquisa Visual Computacional Python](quickstarts-sdk/python-sdk.md)
- [Início rápido: SDK do Pesquisa Visual Computacional Java](quickstarts-sdk/java-sdk.md)
