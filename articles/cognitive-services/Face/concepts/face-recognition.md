---
title: Conceitos de reconhecimento facial
titleSuffix: Azure Cognitive Services
description: Este artigo explica os conceitos das operações de Verificação, Descoberta Similar, Grupo e Identificação de Rostos e estruturas de dados subjacentes.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pafarley
ms.openlocfilehash: 00dadf8a91b7ed01ab9f91933d296744305a95af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "92518811"
---
# <a name="face-recognition-concepts"></a>Conceitos de reconhecimento facial

Este artigo explica os conceitos das operações de Verificação, Descoberta Similar, Grupo e Identificação de Rostos e estruturas de dados subjacentes. Em termos gerais, o reconhecimento descreve o trabalho de comparar duas caras diferentes para determinar se são semelhantes ou pertencem à mesma pessoa.

## <a name="recognition-related-data-structures"></a>Estruturas de dados relacionadas com o reconhecimento

As operações de reconhecimento utilizam principalmente as seguintes estruturas de dados. Estes objetos são armazenados na nuvem e podem ser referenciados pelas suas cordas de ID. As cordas de ID são sempre únicas dentro de uma subscrição. Os campos de nome podem ser duplicados.

|Nome|Descrição|
|:--|:--|
|Face detetado| Esta representação facial única é recuperada pela operação [de deteção facial.](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md) A sua identificação expira 24 horas depois de ser criada.|
|Caracteres Persistidos| Quando os objetos DetectadosFace são adicionados a um grupo, como o FaceList ou o Person, tornam-se objetos PersistedFace. Podem ser [recuperados a](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c) qualquer momento e não expirarem.|
|[FaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b) ou [BigFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)| Esta estrutura de dados é uma lista variada de objetos PersistedFace. Um FaceList tem um ID único, uma cadeia de nomes e opcionalmente uma cadeia de dados do utilizador.|
|[Pessoa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)| Esta estrutura de dados é uma lista de objetos PersistedFace que pertencem à mesma pessoa. Tem um ID único, uma cadeia de nomes, e opcionalmente uma cadeia de dados do utilizador.|
|[PersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) ou [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)| Esta estrutura de dados é uma lista variada de objetos pessoais. Tem um ID único, uma cadeia de nomes, e opcionalmente uma cadeia de dados do utilizador. Um PersonGroup deve ser [treinado](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) antes de poder ser utilizado em operações de reconhecimento.|

## <a name="recognition-operations"></a>Operações de reconhecimento

Esta secção detalha como as quatro operações de reconhecimento utilizam as estruturas de dados anteriormente descritas. Para obter uma descrição alargada de cada operação de reconhecimento, consulte [a visão geral](../Overview.md).

### <a name="verify"></a>Verificação

A operação [Verificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) retira um ID facial de DetectedFace ou PersistedFace e ou outro ID facial ou um objeto de Pessoa e determina se pertencem à mesma pessoa. Se passar num objeto de Pessoa, pode passar opcionalmente num PersonGroup ao qual essa Pessoa pertence para melhorar o desempenho.

### <a name="find-similar"></a>Encontrar Similar

A operação [Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) requer um ID facial de DetectedFace ou PersistedFace e um FaceList ou uma série de outros IDs faciais. Com uma FaceList, devolve uma FaceList menor de rostos semelhantes ao rosto dado. Com uma variedade de IDs faciais, retorna igualmente uma matriz menor.

### <a name="group"></a>Group

A operação [do Grupo](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) requer uma série de IDs faciais variados do DetectedFace ou PersistedFace e devolve os mesmos IDs agrupados em várias matrizes menores. Cada matriz de "grupos" contém IDs faciais que parecem semelhantes. Uma única matriz de "MessyGroup" contém IDs faciais para os quais não foram encontradas semelhanças.

### <a name="identify"></a>Identificar

A operação [Identificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) requer um ou vários IDs faciais de DetectedFace ou PersistedFace e um PersonGroup e devolve uma lista de objetos pessoais a que cada rosto pode pertencer. Os objetos de pessoa retornada são embrulhados como objetos candidatos, que têm um valor de confiança de previsão.

## <a name="input-data"></a>Dados de entrada

Utilize as seguintes dicas para garantir que as suas imagens de entrada dão os resultados de reconhecimento mais precisos:

* Os formatos de imagem de entrada suportados são JPEG, PNG, GIF (o primeiro quadro), BMP.
* O tamanho do ficheiro de imagem não deve ser superior a 6 MB.
* Quando criar objetos pessoais, utilize fotos que possuam diferentes tipos de ângulos e iluminação.
* Alguns rostos podem não ser reconhecidos devido a desafios técnicos, tais como:
  * Imagens com iluminação extrema, por exemplo, luz de fundo severa.
  * Obstruções que bloqueiam um ou ambos os olhos.
  * Diferenças no tipo de cabelo ou pelos faciais.
  * Mudanças na aparência facial por causa da idade.
  * Expressões faciais extremas.

## <a name="next-steps"></a>Passos seguintes

Agora que está familiarizado com os conceitos de reconhecimento facial, escreva um script que identifique rostos contra um PersonGroup treinado.

* [Face cliente biblioteca quickstart](../Quickstarts/client-libraries.md)