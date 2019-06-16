---
title: Suporte de idioma - o de imagem digitalizada
titleSuffix: Azure Cognitive Services
description: Uma lista de idiomas suportados pelas funcionalidades de imagem digitalizada.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: article
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: 1a70d1b2ea504d0ccfba925810a2d19d0c7583cc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60759610"
---
# <a name="language-support-for-computer-vision"></a>Suporte de idiomas para imagem digitalizada

Alguns recursos de imagem digitalizada suportam a vários idiomas; quaisquer funcionalidades não foram mencionadas suportam apenas inglês.

## <a name="text-recognition"></a>Reconhecimento de texto

Imagem digitalizada pode reconhecer texto em vários idiomas. Especificamente, o [OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) API suporta uma variedade de linguagens, ao passo que o [leitura](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) API e [reconhecer texto](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) API só suportam o inglês. Ver [reconhecer texto impresso e manuscritos](concept-recognizing-text.md) para obter mais informações sobre esta funcionalidade e as vantagens de cada API.

OCR Deteta automaticamente o idioma de material de entrada, pelo que é necessário especificar um código de idioma na chamada de API. No entanto, os códigos de idioma sempre são retornados como o valor da `"language"` nó na resposta JSON.

|Idioma| Código do idioma | API DE OCR |
|:-----|:----:|:-----:|
|Árabe | `ar`|✔ |
|Chinês (Simplificado) | `zh-Hans`|✔ |
|Chinês (Tradicional) | `zh-Hant`|✔ |
|Checo | `cs` |✔ |
|Dinamarquês | `da` |✔ |
|Neerlandês | `nl` |✔ |
|Português | `en` |✔ |
|Finlandês | `fi` |✔ |
|Francês | `fr` |✔ |
|Alemão | `de` |✔ |
|Grego | `el` |✔ |
|Húngaro | `hu` |✔ |
|Italiano | `it` |✔ |
|Japonês | `ja` |✔ |
|Coreano | `ko` |✔ |
|Norueguês | `nb` |✔ |
|Polaco | `pl` |✔ |
|Português | `pt` |✔ |
|Romeno | `ro` |✔ |
|Russo | `ru` |✔ |
|Sérvio (Cirílico) | `sr-Cyrl` |✔ |
|Sérvio (Latim) | `sr-Latn` |✔ |
|Eslovaco | `sk` |✔ |
|Espanhol | `es` |✔ |
|Sueco | `sw` |✔ |
|Turco | `tr` |✔ |

## <a name="image-analysis"></a>Análise de imagem

Algumas ações do [analise - o de imagem](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API pode retornar resultados em outros idiomas, especificados com o `language` parâmetro de consulta. Outras ações devolvem resultados em inglês, independentemente de qual idioma especificado e outras pessoas lançam uma exceção para idiomas não suportados. Ações são especificadas com o `visualFeatures` e `details` parâmetros de consulta; consulte a [descrição geral](home.md) para obter uma lista de todas as ações que pode fazer com a análise de imagem.

|Idioma | Código do idioma | Categories | Tags | Descrição | Adulto | Marcas | Cor | Rostos | TipoImagem | Objetos | Celebridades | Pontos de referência |
|:---|:---:|:----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|Chinês | `zh`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Português | `en`   | ✔ | ✔| ✔|✔|✔|✔|✔|✔|✔|✔|✔|
|Japonês | `ja`   | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Português | `pt` | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Espanhol | `es`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|

## <a name="next-steps"></a>Passos Seguintes

Começar a utilizar as funcionalidades de imagem digitalizada mencionadas neste guia.

* [Analisar uma imagem local (REST)](./quickstarts/csharp-analyze.md)
* [Extrair texto impresso (REST)](./quickstarts/csharp-print-text.md)