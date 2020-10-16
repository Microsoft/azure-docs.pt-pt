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
ms.openlocfilehash: b5c263506db68ea62b0d65b7b866cfab33a36236
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91976883"
---
# <a name="language-support-for-computer-vision"></a>Suporte linguístico para Visão de Computador

Algumas funcionalidades da Visão Computacional suportam vários idiomas; quaisquer funcionalidades não mencionadas aqui apenas suportam inglês.

## <a name="optical-character-recognition-ocr"></a>Reconhecimento Ótico de Carateres (OCR)

As APIs de OCR da Visão Computacional suportam vários idiomas. Não exigem que especifique um código linguístico. Consulte [o Reconhecimento de Caracteres Óticos (OCR)](concept-recognizing-text.md) para obter mais informações.

|Linguagem| Código do idioma | OCR API | Ler v3.1 | Ler v3.1 pré-visualização.2 |
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

Algumas ações da [Análise - API de imagem](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) pode devolver resultados em outras línguas, especificadas com o parâmetro de `language` consulta. Outras ações retornam resultados em inglês, independentemente do que é especificado, e outras lançam uma exceção para línguas não apoiadas. As ações são especificadas com os `visualFeatures` parâmetros e `details` consultas; consulte a [Visão Geral](overview.md) para uma lista de todas as ações que pode fazer com a análise de imagem.

|Linguagem | Código do idioma | Categorias | Etiquetas | Descrição | Adulto | Marcas | Color | Rostos | ImagemType | Objetos | Celebridades | Pontos de referência |
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
