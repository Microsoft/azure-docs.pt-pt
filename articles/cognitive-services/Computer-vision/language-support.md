---
title: Suporte linguístico - Visão computacional
titleSuffix: Azure Cognitive Services
description: Este artigo fornece uma lista de línguas naturais suportadas por funcionalidades de Visão Computacional; OCR, análise de imagem.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: b065b36103b69f0601daa1388b45865856543d2b
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94540523"
---
# <a name="language-support-for-computer-vision"></a>Suporte linguístico para Visão de Computador

Algumas funcionalidades da Visão Computacional suportam vários idiomas; quaisquer funcionalidades não mencionadas aqui apenas suportam inglês.

## <a name="optical-character-recognition-ocr"></a>Reconhecimento Ótico de Carateres (OCR)

As APIs de OCR da Visão Computacional suportam vários idiomas. Não exigem que especifique um código linguístico. Consulte [o Reconhecimento de Caracteres Óticos (OCR)](concept-recognizing-text.md) para obter mais informações.

|Linguagem| Código do idioma | OCR API | Ler 3.0 e 3.1 | Ler v3.2-pré-visualização.1 |
|:-----|:----:|:-----:|:---:|:---:|
|Árabe | `ar`|✔ | | |
|Chinês (Simplificado) | `zh-Hans`|✔ | |✔ |
|Chinês (Tradicional) | `zh-Hant`|✔ | | |
|Checo | `cs` |✔ | | |
|Dinamarquês | `da` |✔ | | |
|Neerlandês | `nl` |✔ |✔ |✔ |
|Inglês | `en` |✔ |✔ |✔ |
|Finlandês | `fi` |✔ | | |
|Francês | `fr` |✔ |✔ |✔ |
|Alemão | `de` |✔ |✔ |✔ |
|Grego | `el` |✔ | | |
|Húngaro | `hu` |✔ | | |
|Italiano | `it` |✔ |✔ |✔ |
|Japonês | `ja` |✔ | |✔ |
|Coreano | `ko` |✔ | | |
|Norueguês | `nb` |✔ | | |
|Polaco | `pl` |✔ | | |
|Português | `pt` |✔ |✔ |✔ |
|Romeno | `ro` |✔ | | |
|Russo | `ru` |✔ | | |
|Sérvio (Cirílico) | `sr-Cyrl` |✔ | | |
|Sérvio (Latim) | `sr-Latn` |✔ | | |
|Eslovaco | `sk` |✔ | | |
|Espanhol | `es` |✔ |✔ |✔ |
|Sueco | `sw` |✔ | | |
|Turco | `tr` |✔ | | |

## <a name="image-analysis"></a>Análise de imagem

Algumas ações da [Análise - API de imagem](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) pode devolver resultados em outras línguas, especificadas com o parâmetro de `language` consulta. Outras ações retornam resultados em inglês, independentemente do que é especificado, e outras lançam uma exceção para línguas não apoiadas. As ações são especificadas com os `visualFeatures` parâmetros e `details` consultas; consulte a [Visão Geral](overview.md) para uma lista de todas as ações que pode fazer com a análise de imagem.

|Linguagem | Código do idioma | Categorias | Etiquetas | Description | Adulto | Marcas | Cor | Rostos | ImagemType | Objetos | Celebridades | Pontos de referência |
|:---|:---:|:----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|Chinês | `zh`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Inglês | `en`   | ✔ | ✔| ✔|✔|✔|✔|✔|✔|✔|✔|✔|
|Japonês | `ja`   | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Português | `pt` | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Espanhol | `es`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|

## <a name="next-steps"></a>Passos seguintes

Começa a utilizar as funcionalidades de Visão de Computador mencionadas neste guia.

* [Analise uma imagem local (REST)](./quickstarts/csharp-analyze.md)
* [Extrato de texto impresso (REST)](./quickstarts/csharp-print-text.md)
