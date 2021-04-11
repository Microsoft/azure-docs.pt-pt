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
ms.date: 03/29/2021
ms.author: pafarley
ms.custom:
- seodec18
- cog-serv-seo-aug-2020
- contperf-fy21q2
keywords: visão computacional, aplicações de visão computacional, serviço de visão computacional
ms.openlocfilehash: 875ef961148668a83e94c116622b5e461d2413fa
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286139"
---
# <a name="what-is-computer-vision"></a>O que é a Imagem Digitalizada?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

O serviço de Visão Computacional da Azure dá-lhe acesso a algoritmos avançados que processam imagens e devolvem informações com base nas funcionalidades visuais que lhe interessam. 

| Serviço|Description|
|---|---|
|[Reconhecimento ótico de carateres (OCR)](overview-ocr.md)|O serviço de Reconhecimento de Caracteres Óticos (OCR) extrai texto de imagens. Pode utilizar a nova API de leitura para extrair texto impresso e manuscrito de fotos e documentos. Utiliza modelos baseados em aprendizagem profunda e trabalha com texto em uma variedade de superfícies e fundos. Estes incluem documentos comerciais, faturas, recibos, cartazes, cartões de visita, cartas e quadros brancos. As APIs do OCR suportam a extração de texto impresso em [várias línguas.](./language-support.md) Siga o [arranque rápido do OCR](quickstarts-sdk/client-library.md) para começar.|
|[Análise de Imagem](overview-image-analysis.md)| O serviço de Análise de Imagem extrai muitas funcionalidades visuais de imagens, tais como objetos, rostos, conteúdo adulto e descrições de texto geradas automaticamente. Siga o [quickstart de Análise de Imagem](quickstarts-sdk/image-analysis-client-library.md) para começar.|
| [Análise Espacial](intro-to-spatial-analysis-public-preview.md)| O serviço de Análise Espacial analisa a presença e o movimento de pessoas num feed de vídeo e produz eventos aos que outros sistemas podem responder. Instale o [recipiente de Análise Espacial](spatial-analysis-container.md) para começar.|

## <a name="computer-vision-for-digital-asset-management"></a>Visão de Computador para gestão de ativos digitais

A Visão Computacional pode alimentar muitos cenários de gestão de ativos digitais (DAM). A DAM é o processo de negócio de organização, armazenamento e recuperação de ativos de media ricos e gestão de direitos e permissões digitais. Por exemplo, uma empresa pode querer agrupar e identificar imagens com base em logótipos visíveis, rostos, objetos, cores, e assim por diante. Ou, pode querer gerar automaticamente [legendas para imagens](./Tutorials/storage-lab-tutorial.md) e anexar palavras-chave para que sejam pescandizáveis. Para obter uma solução DAM tudo-em-um utilizando serviços cognitivos, pesquisa cognitiva Azure e relatórios inteligentes, consulte o [Guia de Acelerador de Soluções de Mineração de Conhecimento](https://github.com/Azure-Samples/azure-search-knowledge-mining) no GitHub. Para outros exemplos da DAM, consulte o [repositório de modelos de solução de visão](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates) computacional.

## <a name="image-requirements"></a>Requisitos de imagem

A Imagem Digitalizada pode analisar imagens que cumprem os requisitos seguintes:

- A imagem tem de ser apresentada no formato JPEG, PNG, GIF ou BMP
- O tamanho do ficheiro da imagem tem de ser inferior a 4 megabytes (MB)
- As dimensões da imagem têm de ser superiores a 50 x 50 píxeis
  - Para a API de leitura, as dimensões da imagem devem estar entre 50 x 50 e 10000 x 10000 pixels.

## <a name="data-privacy-and-security"></a>Privacidade e segurança dos dados

Tal como acontece com todos os Serviços Cognitivos, os desenvolvedores que usam o serviço de Visão de Computador devem estar cientes das políticas da Microsoft sobre os dados dos clientes. Consulte a [página de Serviços Cognitivos](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) no Microsoft Trust Center para saber mais.

## <a name="next-steps"></a>Passos seguintes

Siga um quickstart para implementar e executar um serviço na sua língua de desenvolvimento preferida.

* [Quickstart: Reconhecimento ótico de caracteres (OCR)](quickstarts-sdk/client-library.md)
* [Quickstart: Análise de Imagem](quickstarts-sdk/image-analysis-client-library.md)
* [Quickstart: Recipiente de análise espacial](spatial-analysis-container.md)
