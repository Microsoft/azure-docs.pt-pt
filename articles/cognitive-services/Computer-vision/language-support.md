---
title: Suporte linguístico - Visão Computacional
titleSuffix: Azure Cognitive Services
description: Este artigo fornece uma lista de línguas naturais suportadas pelas funcionalidades da Visão Computacional; OCR, Reconhecer Texto e Ler.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: a207118af0b07be79c934a9665f47e73c5eecde0
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83589198"
---
# <a name="language-support-for-computer-vision"></a>Suporte linguístico para visão computacional

Algumas funcionalidades da Visão Computacional suportam múltiplas línguas; quaisquer características não mencionadas aqui apenas suportam inglês.

## <a name="text-recognition"></a>Reconhecimento de texto

A Visão Computacional pode reconhecer texto em muitas línguas. As APIs oCR e Read não exigem que especifique um código de idioma. Consulte [reconhecer texto impresso e manuscrito](concept-recognizing-text.md) para obter mais informações sobre esta funcionalidade e as vantagens de cada API.

|Linguagem| Código do idioma | OCR API | Ler API |
|:-----|:----:|:-----:|:---:|
|Árabe | `ar`|✔ | |
|Chinês (Simplificado) | `zh-Hans`|✔ | |
|Chinês (Tradicional) | `zh-Hant`|✔ | |
|Checo | `cs` |✔ | |
|Dinamarquês | `da` |✔ | |
|Neerlandês | `nl` |✔ |✔ |
|Inglês | `en` |✔ |✔ |
|Finlandês | `fi` |✔ | |
|Francês | `fr` |✔ |✔ |
|Alemão | `de` |✔ |✔ |
|Grego | `el` |✔ | |
|Húngaro | `hu` |✔ | |
|Italiano | `it` |✔ |✔ |
|Japonês | `ja` |✔ | |
|Coreano | `ko` |✔ | |
|Norueguês | `nb` |✔ | |
|Polaco | `pl` |✔ | |
|Português | `pt` |✔ |✔ |
|Romeno | `ro` |✔ | |
|Russo | `ru` |✔ | |
|Sérvio (Cirílico) | `sr-Cyrl` |✔ | |
|Sérvio (Latim) | `sr-Latn` |✔ | |
|Eslovaco | `sk` |✔ | |
|Espanhol | `es` |✔ |✔ |
|Sueco | `sw` |✔ | |
|Turco | `tr` |✔ | |

## <a name="image-analysis"></a>Análise de imagem

Algumas ações da [Análise -](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) APi de imagem podem devolver resultados em outras línguas, especificadas com o parâmetro de `language` consulta. Outras ações devolvem resultados em inglês, independentemente da língua especificada, e outras lançam uma exceção para línguas não apoiadas. As ações são especificadas com os `visualFeatures` parâmetros e `details` consultas; consulte a [visão geral](home.md) para uma lista de todas as ações que pode fazer com a análise de imagem.

|Linguagem | Código do idioma | Categorias | Etiquetas | Descrição | Adulto | Marcas | Cor | Rostos | Tipo de imagem | Objetos | Celebridades | Pontos de referência |
|:---|:---:|:----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|Chinês | `zh`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Inglês | `en`   | ✔ | ✔| ✔|✔|✔|✔|✔|✔|✔|✔|✔|
|Japonês | `ja`   | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Português | `pt` | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Espanhol | `es`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|

## <a name="next-steps"></a>Passos seguintes

Começar a usar as funcionalidades de Visão computacional mencionadas neste guia.

* [Analisar uma imagem local (REST)](./quickstarts/csharp-analyze.md)
* [Extrair texto impresso (REST)](./quickstarts/csharp-print-text.md)
