---
title: Conceitos de reconhecimento facial
titleSuffix: Azure Cognitive Services
description: Este artigo explica os conceitos das operações de verificação, find similar, grupo e identificação de controlo facial e das estruturas de dados subjacentes.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pafarley
ms.openlocfilehash: 164e5a8c107f445b376d26f9be7db92a7983b0d3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "73743071"
---
# <a name="face-recognition-concepts"></a>Conceitos de reconhecimento facial

Este artigo explica os conceitos das operações de verificação, find similar, grupo e identificação de controlo facial e das estruturas de dados subjacentes. Em termos gerais, o reconhecimento descreve o trabalho de comparar duas faces diferentes para determinar se são semelhantes ou pertencem à mesma pessoa.

## <a name="recognition-related-data-structures"></a>Estruturas de dados relacionadas com o reconhecimento

As operações de reconhecimento utilizam principalmente as seguintes estruturas de dados. Estes objetos são armazenados na nuvem e podem ser referenciados pelas suas cordas de identificação. As cadeias de ID são sempre únicas dentro de uma subscrição. Os campos de nome podem ser duplicados.

|Nome|Descrição|
|:--|:--|
|Detetou Face| Esta representação facial única é recuperada pela operação de [deteção facial.](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md) A sua identificação expira 24 horas depois de ter sido criada.|
|PersistiuFace| Quando os objetos DetectedFace são adicionados a um grupo, como faceList ou pessoa, tornam-se objetos PersistedFace. Podem ser [recuperados](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c) a qualquer momento e não expiram.|
|[FaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b) ou [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)| Esta estrutura de dados é uma lista variada de objetos PersistedFace. Um FaceList tem um ID único, uma cadeia de nomes e opcionalmente uma cadeia de dados do utilizador.|
|[Pessoa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)| Esta estrutura de dados é uma lista de objetos PersistedFace que pertencem à mesma pessoa. Tem um ID único, uma cadeia de nomes e opcionalmente uma cadeia de dados do utilizador.|
|[PersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) ou [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)| Esta estrutura de dados é uma lista variada de objetos pessoais. Tem um ID único, uma cadeia de nomes e opcionalmente uma cadeia de dados do utilizador. Um PersonGroup deve ser [treinado](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) antes de poder ser utilizado em operações de reconhecimento.|

## <a name="recognition-operations"></a>Operações de reconhecimento

Esta secção detalha como as quatro operações de reconhecimento utilizam as estruturas de dados previamente descritas. Para uma descrição alargada de cada operação de reconhecimento, consulte [a visão geral](../Overview.md).

### <a name="verify"></a>Verificar

A operação [Check](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) retira um ID facial do DetectedFace ou do PersistedFace e ou de outro id facial ou de um objeto de pessoa e determina se pertencem à mesma pessoa. Se passar num objeto Pessoa, pode opcionalmente passar num PersonGroup ao qual essa Pessoa pertence para melhorar o desempenho.

### <a name="find-similar"></a>Encontrar Similar

A operação [Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) retira um ID facial do DetectedFace ou do PersistedFace e ou de um FaceList ou de uma série de outros IDs faciais. Com um FaceList, devolve um FaceList menor de rostos que são semelhantes ao rosto dado. Com uma variedade de iDs faciais, ele também devolve uma matriz menor.

### <a name="group"></a>Grupo

A operação [do Grupo](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) pega numa série de iDs faciais variados do DetectedFace ou PersistedFace e devolve os mesmos IDs agrupados em várias matrizes menores. Cada matriz de "grupos" contém iDs faciais que parecem semelhantes. Uma única matriz de "messyGroup" contém iDs faciais para os quais não foram encontradas semelhanças.

### <a name="identify"></a>Identificar

A operação [Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) requer um ou vários IDs faciais do DetectedFace ou PersistedFace e um PersonGroup e devolve uma lista de objetos pessoais a que cada rosto pode pertencer. Os objetos de Pessoa Devolvida são embrulhados como objetos candidatos, que têm um valor de confiança de previsão.

## <a name="input-data"></a>Dados de entrada

Utilize as seguintes dicas para garantir que as imagens de entrada dão os resultados de reconhecimento mais precisos:

* Os formatos de imagem de entrada suportados são JPEG, PNG, GIF (o primeiro quadro), BMP.
* O tamanho do ficheiro de imagem não deve ser superior a 4 MB.
* Quando criar objetos pessoais, utilize fotografias que apresentam diferentes tipos de ângulos e iluminação.
* Alguns rostos podem não ser reconhecidos devido a desafios técnicos, tais como:
  * Imagens com iluminação extrema, por exemplo, iluminação severa.
  * Obstruções que bloqueiam um ou ambos os olhos.
  * Diferenças no tipo de cabelo ou nos pelos faciais.
  * Mudanças na aparência facial por causa da idade.
  * Expressões faciais extremas.

## <a name="next-steps"></a>Passos seguintes

Agora que está familiarizado com conceitos de reconhecimento facial, aprenda a escrever um guião que identifique rostos contra um PersonGroup treinado.

* [Identificar rostos em imagens](../Face-API-How-to-Topics/HowtoIdentifyFacesinImage.md)