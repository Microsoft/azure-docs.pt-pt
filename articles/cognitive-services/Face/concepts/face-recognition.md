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
ms.openlocfilehash: 8c0bf001c2bdbedaa041dbd10b5c7edb08eec4c6
ms.sourcegitcommit: 524625dd12e0537173616a991802075e2dc9da12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2019
ms.locfileid: "64416002"
---
# <a name="face-recognition-concepts"></a>Conceitos de reconhecimento de rostos

Este artigo explica os conceitos das operações de reconhecimento de rostos vários (verificação, encontre de forma semelhante, agrupamento, identificação) e as estruturas de dados subjacente. De forma ampla, reconhecimento descreve o trabalho de comparar dois rostos diferentes para determinar se eles são semelhantes ou se pertencem à mesma pessoa.

## <a name="recognition-related-data-structures"></a>Estruturas de dados relacionados com o reconhecimento

As operações de reconhecimento utilizam principalmente as seguinte estruturas de dados. Esses objetos são armazenados na cloud e podem ser referenciados por suas cadeias de caracteres de ID. Cadeias de caracteres de ID são, por conseguinte, sempre exclusivas numa subscrição, ao passo que os campos de nome podem ser duplicados.

|Name|Descrição|
|:--|:--|
|**DetectedFace**| Esta é uma representação de face único obtida através da [deteção facial](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md) operação. O ID de expira 24 horas depois de criado.|
|**PersistedFace**| Quando **DetectedFace** objetos são adicionados a um grupo (tal como **FaceList** ou **pessoa**), que elas forem **PersistedFace** objetos, que podem ser [obtidos](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c) a qualquer hora e não expiram.|
|**[FaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b)**/**[LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)**| Esta é uma lista variada de **PersistedFace** objetos. R **FaceList** tem um ID exclusivo, uma cadeia de caracteres de nome e, opcionalmente, uma cadeia de caracteres de dados do utilizador.|
|**[Pessoa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)**| Esta é uma lista das **PersistedFace** objetos que pertencem à mesma pessoa. Ele tem um ID exclusivo, uma cadeia de caracteres de nome e, opcionalmente, uma cadeia de caracteres de dados do utilizador.|
|**[PersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)**/**[LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)**| Esta é uma lista variada de **pessoa** objetos. Ele tem um ID exclusivo, uma cadeia de caracteres de nome e, opcionalmente, uma cadeia de caracteres de dados do utilizador. R **PersonGroup** tem de ser [treinado](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) antes de poder ser utilizada em operações de reconhecimento.|

## <a name="recognition-operations"></a>Operações de reconhecimento

Esta secção fornece detalhes sobre como as quatro operações de reconhecimento usam as estruturas de dados acima. Consulte a [descrição geral](../Overview.md) para obter uma descrição geral de cada operação de reconhecimento.

### <a name="verification"></a>Verificação

O [Verifique se](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) operação utiliza uma identificação de face (**DetectedFace** ou **PersistedFace**) e de qualquer outro face ID ou um **pessoa** objeto e Determina se a que pertencem à mesma pessoa. Se passar um **pessoa**, pode, opcionalmente, transmitir num **PersonGroup** a que **pessoa** pertence a fim de melhorar o desempenho.

### <a name="find-similar"></a>Encontrar semelhantes

O [encontrar semelhante](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) operação utiliza uma identificação de face (**DetectedFace** ou **PersistedFace**) e um **FaceList** ou uma matriz de outro face IDs. Com um **FaceList**, ele retorna um menor **FaceList** de faces semelhantes para o rosto. Com uma matriz de IDs de rosto, da mesma forma devolve uma matriz de menor.

### <a name="grouping"></a>Agrupamento

O [grupo](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) operação assume uma matriz de IDs de rosto variado (**DetectedFace** ou **PersistedFace**) e retorna os mesmos IDs agrupadas em várias matrizes menores. Cada matriz de "grupos" contém face IDs que ter um aspeto semelhante e uma matriz de único "messyGroup" face IDs para que não existem semelhanças foram encontradas.

### <a name="identification"></a>Identificação

O [identificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) face um ou vários IDs de demora de operação (**DetectedFace** ou **PersistedFace**) e um **PersonGroup** e retorna uma lista dos **pessoa** objetos que cada rosto poderá pertencem a. Devolvido **pessoa** objetos são encapsulados como **Release Candidate** objetos, que tem um valor de confiança da previsão.

## <a name="input-data"></a>Dados de entrada

Utilize as sugestões seguintes para se certificar de que as imagens de entrada dê os resultados mais precisos do reconhecimento:

* Os formatos de entrada de imagem suportados são PNG, JPEG, BMP e GIF (o primeiro quadro).
* Tamanho do ficheiro de imagem deve ser não maior do que 4 MB.
* Ao criar **pessoa** objetos, deve usar as fotografias que possuem uma variedade de ângulos e de iluminação.
* Alguns rostos poderão não reconhecer devido a desafios técnicos:
  * Imagens de iluminação extreme (por exemplo, grave backlighting).
  * Obstructions um ou ambos os olhos de bloqueio.
  * Diferenças de cabelo de estilo ou facial cabelos.
  * Alterações na aparência da face devido a idade.
  * Expressões faciais extremas.

## <a name="next-steps"></a>Passos Seguintes

Agora que já está familiarizado com conceitos de reconhecimento de rostos, saiba como escrever um script simples que identifique rostos em relação a um treinado **PersonGroup**.

* [Como identificar rostos em imagens](../Face-API-How-to-Topics/HowtoIdentifyFacesinImage.md)