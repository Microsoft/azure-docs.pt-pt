---
title: Conceitos de reconhecimento de rostos
titleSuffix: Azure Cognitive Services
description: Aprenda conceitos sobre reconhecimento de rostos.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pafarley
ms.openlocfilehash: fa38c492530cb8938e49bc15e13fdd39ed5b6f1c
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65890875"
---
# <a name="face-recognition-concepts"></a>Conceitos de reconhecimento de rostos

Este artigo explica os conceitos das operações de reconhecimento de rostos verificar, encontrar semelhante, grupo e identificar e as estruturas de dados subjacente. De forma ampla, reconhecimento descreve o trabalho de comparar dois rostos diferentes para determinar se são semelhantes ou pertencem à mesma pessoa.

## <a name="recognition-related-data-structures"></a>Estruturas de dados relacionados com o reconhecimento

As operações de reconhecimento utilizam principalmente as seguinte estruturas de dados. Esses objetos são armazenados na cloud e podem ser referenciados por suas cadeias de caracteres de ID. Cadeias de caracteres de ID são sempre exclusivas numa subscrição. Campos de nome podem estar duplicados.

|Name|Descrição|
|:--|:--|
|DetectedFace| Essa representação face único é obtida pelos [deteção facial](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md) operação. O ID de expira 24 horas depois de criado.|
|PersistedFace| Quando os objetos de DetectedFace são adicionados a um grupo, tal como FaceList ou pessoa, eles tornam-se PersistedFace objetos. Eles podem ser [obtidos](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c) a qualquer hora e não expiram.|
|[FaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b) ou [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)| Essa estrutura de dados é uma lista variada de objetos de PersistedFace. Um FaceList tem um ID exclusivo, uma cadeia de caracteres de nome e, opcionalmente, uma cadeia de caracteres de dados do utilizador.|
|[Pessoa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)| Essa estrutura de dados é uma lista de objetos de PersistedFace que pertencem à mesma pessoa. Ele tem um ID exclusivo, uma cadeia de caracteres de nome e, opcionalmente, uma cadeia de caracteres de dados do utilizador.|
|[PersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) ou [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)| Essa estrutura de dados é uma lista variada de objetos de pessoa. Ele tem um ID exclusivo, uma cadeia de caracteres de nome e, opcionalmente, uma cadeia de caracteres de dados do utilizador. Tem de ser um PersonGroup [treinado](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) antes de poder ser utilizada em operações de reconhecimento.|

## <a name="recognition-operations"></a>Operações de reconhecimento

Esta secção fornece detalhes sobre como as quatro operações de reconhecimento usam as estruturas de dados descritas anteriormente. Para obter uma descrição geral de cada operação de reconhecimento, consulte [descrição geral](../Overview.md).

### <a name="verify"></a>Validar

O [Verifique se](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) operação utiliza uma identificação de face de DetectedFace ou PersistedFace e outro face ID ou um objeto Person e determina se a que pertencem à mesma pessoa. Se passar um objeto Person, pode, opcionalmente, passar um PersonGroup a que essa pessoa pertence, para melhorar o desempenho.

### <a name="find-similar"></a>Encontrar semelhantes

O [encontrar semelhante](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) operação utiliza uma identificação de face de DetectedFace ou PersistedFace e um FaceList ou uma matriz de outros IDs de rostos. Com um FaceList, ele retorna um FaceList menor de faces semelhantes para o rosto. Com uma matriz de IDs de rosto, da mesma forma devolve uma matriz de menor.

### <a name="group"></a>Agrupar

O [grupo](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) operação pega uma matriz de IDs de face variado de DetectedFace ou PersistedFace e retorna os mesmos IDs agrupadas em várias matrizes menores. Cada matriz de "grupos" contém face IDs que ter um aspeto semelhante. Uma matriz de "messyGroup" única contém IDs de rostos para que não existem semelhanças foram encontradas.

### <a name="identify"></a>Identificar

O [identificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) operação usa face um ou vários IDs do DetectedFace ou PersistedFace e um PersonGroup e retorna uma lista de objetos de pessoa que cada rosto poderá pertencer ao. Devolveu pessoa objetos são encapsulados como objetos de Release Candidate, que tem um valor de confiança da previsão.

## <a name="input-data"></a>Dados de entrada

Utilize as sugestões seguintes para se certificar de que as imagens de entrada dê os resultados mais precisos do reconhecimento:

* Os formatos de entrada de imagem suportados são PNG, JPEG, BMP e GIF (o primeiro quadro).
* Tamanho do ficheiro de imagem deve ser não maior do que 4 MB.
* Ao criar objetos Person, utilize fotografias que apresentam diferentes tipos de ângulos e de iluminação.
* Alguns rostos poderão não reconhecidos por causa de desafios técnicos, como:
  * Imagens extreme iluminação, por exemplo, grave backlighting.
  * Obstructions bloquear um ou ambos os olhos.
  * Diferenças de cabelo de tipo ou facial cabelos.
  * Alterações na aparência facial devido a idade.
  * Expressões faciais extremas.

## <a name="next-steps"></a>Passos Seguintes

Agora que está familiarizado com conceitos de reconhecimento de rostos, saiba como escrever um script que identifique rostos em relação a um PersonGroup treinado.

* [Identificar rostos em imagens](../Face-API-How-to-Topics/HowtoIdentifyFacesinImage.md)