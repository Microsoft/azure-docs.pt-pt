---
title: Suporte ao idioma-Pesquisa Visual Computacional
titleSuffix: Azure Cognitive Services
description: Uma lista de idiomas naturais com suporte pelos recursos do Pesquisa Visual Computacional.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: 516d21bc69bbc20f924a3bdf39eda7245fc08a28
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882975"
---
# <a name="language-support-for-computer-vision"></a>Suporte a idiomas para Pesquisa Visual Computacional

Alguns recursos do Pesquisa Visual Computacional dão suporte a vários idiomas; todos os recursos não mencionados aqui só dão suporte ao inglês.

## <a name="text-recognition"></a>Reconhecimento de texto

Pesquisa Visual Computacional pode reconhecer texto em vários idiomas. Especificamente, a API de [OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) dá suporte a uma variedade de linguagens, enquanto a API de [leitura](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) e a API de [reconhecimento de texto](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) dão suporte apenas ao inglês. Consulte [reconhecer texto impresso e manuscrito](concept-recognizing-text.md) para obter mais informações sobre essa funcionalidade e as vantagens de cada API.

O OCR detecta automaticamente o idioma do material de entrada, portanto, não é necessário especificar um código de idioma na chamada à API. No entanto, os códigos de idioma sempre são retornados como `"language"` o valor do nó na resposta JSON.

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

Algumas ações da API de [análise de imagem](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) podem retornar resultados em outras linguagens, especificadas com `language` o parâmetro de consulta. Outras ações retornam resultados em inglês, independentemente do idioma especificado, e outras geram uma exceção para idiomas sem suporte. As ações são especificadas com `visualFeatures` os `details` parâmetros de consulta e. consulte a [visão geral](home.md) para obter uma lista de todas as ações que você pode fazer com a análise de imagem.

|Idioma | Código do idioma | Categories | Tags | Descrição | Adulto | Marcas | Cor | Rostos | TipoImagem | Objetos | Celebridades | Pontos |
|:---|:---:|:----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|Chinês | `zh`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Português | `en`   | ✔ | ✔| ✔|✔|✔|✔|✔|✔|✔|✔|✔|
|Japonês | `ja`   | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Português | `pt` | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Espanhol | `es`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|

## <a name="next-steps"></a>Passos Seguintes

Comece a usar os recursos de Pesquisa Visual Computacional mencionados neste guia.

* [Analisar uma imagem local (REST)](./quickstarts/csharp-analyze.md)
* [Extrair texto impresso (REST)](./quickstarts/csharp-print-text.md)