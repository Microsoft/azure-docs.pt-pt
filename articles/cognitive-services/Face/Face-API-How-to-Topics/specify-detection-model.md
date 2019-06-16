---
title: Como especificar um modelo de deteção - Face API
titleSuffix: Azure Cognitive Services
description: Este artigo irá mostrar como escolher qual modelo de deteção de rostos para utilizar com a sua aplicação de API de rostos do Azure.
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.component: face-api
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: yluiu
ms.openlocfilehash: dde5623bf5bd579a13fa7271dfba64f9df61bad1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66576700"
---
# <a name="specify-a-face-detection-model"></a>Especifique um modelo de deteção facial

Este guia mostra como especificar um modelo de deteção de rostos para a API de rostos do Azure.

A API de rostos utiliza modelos de aprendizagem automática para executar operações no rostos humanos em imagens. Continuamos a melhorar a exatidão dos nossos modelos com base nos comentários dos clientes e avanços na investigação e para fornecer esses aprimoramentos como atualizações do modelo. Os desenvolvedores têm a opção para especificar que versão do modelo de deteção de rostos que gostariam de utilizar. podem escolher o modelo que melhor se adequa ao seu caso de utilização.

Continue a ler para saber como especificar o modelo de deteção de rostos em determinadas operações de rostos. A API de rostos utiliza a deteção de rostos sempre que ele converte uma imagem de um rosto em alguma outra forma de dados.

Se não tiver a certeza se deve usar o modelo mais recente, avance para o [avaliar modelos diferentes](#evaluate-different-models) secção para avaliar o novo modelo e comparar os resultados com o seu conjunto de dados atual.

## <a name="prerequisites"></a>Pré-requisitos

Deve estar familiarizado com o conceito de deteção de rostos de IA. Se não estiver, consulte o guia conceitual de deteção de rostos ou o guia de procedimentos:

* [Conceitos de deteção de rostos](../concepts/face-detection.md)
* [Como detetar rostos numa imagem](HowtoDetectFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>Detetar rostos com o modelo especificado

Deteção de rostos localiza as localizações da caixa delimitadora de rostos humanos e identifica os respetivos pontos de referência visual. Extrai os recursos do mostrador e armazena-os para utilização posterior num [reconhecimento](../concepts/face-recognition.md) operações.

Quando utiliza a [Se deparam - detetar] API, pode atribuir a versão de modelo com o `detectionModel` parâmetro. Os valores disponíveis são:

* `detection_01`
* `detection_02`

Um URL do pedido para o [Se deparam - detetar] REST API terá esta aparência:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel][&detectionModel]
&subscription-key=<Subscription key>`

Se estiver a utilizar a biblioteca de cliente, pode atribuir o valor para `detectionModel` ao transmitir uma cadeia de caracteres apropriada. Se o deixar não atribuídos, a API irá utilizar a versão de modelo padrão (`detection_01`). Veja o seguinte exemplo de código para a biblioteca de cliente .NET.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceServiceClient.Face.DetectWithUrlAsync(imageUrl, false, false, recognitionModel: "recognition_02", detectionModel: "detection_02");
```

## <a name="add-face-to-person-with-specified-model"></a>Adicionar face à pessoa com o modelo especificado

A API Face pode extrair dados face a partir de uma imagem e associá-la com um **pessoa** objeto por meio do [PersonGroup pessoa - adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API. Esta chamada à API, pode especificar o modelo de deteção da mesma forma como na [Se deparam - detetar].

Veja o seguinte exemplo de código para a biblioteca de cliente .NET.

```csharp
// Create a PersonGroup and add a person with face detected by "detection_02" model
string personGroupId = "mypersongroupid";
await faceServiceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");

string personId = (await faceServiceClient.PersonGroupPerson.CreateAsync(personGroupId, "My Person Name")).PersonId;

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.PersonGroupPerson.AddFaceFromUrlAsync(personGroupId, personId, imageUrl, detectionModel: "detection_02");
```

Este código cria um **PersonGroup** com o ID `mypersongroupid` e adiciona uma **pessoa** a ele. Em seguida, ele adiciona um Face a este **pessoa** usando o `detection_02` modelo. Se não especificar a *detectionModel* parâmetro, a API irá utilizar o modelo padrão, `detection_01`.

> [!NOTE]
> Não precisa de utilizar o mesmo modelo de deteção para todos os rostos num **pessoa** objeto e não precisa de utilizar o mesmo modelo de deteção, quando detetar rostos novo, para comparar com um **pessoa** objeto (no [Rosto – Identificar] API, por exemplo).

## <a name="add-face-to-facelist-with-specified-model"></a>Adicionar face ao FaceList com o modelo especificado

Também pode especificar um modelo de deteção quando adiciona um rosto um existente já **FaceList** objeto. Veja o seguinte exemplo de código para a biblioteca de cliente .NET.

```csharp
await faceServiceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.FaceList.AddFaceFromUrlAsync(faceListId, imageUrl, detectionModel: "detection_02");
```

Este código cria um **FaceList** chamado `My face collection` e adiciona um rosto a ela com o `detection_02` modelo. Se não especificar a *detectionModel* parâmetro, a API irá utilizar o modelo padrão, `detection_01`.

> [!NOTE]
> Não precisa de utilizar o mesmo modelo de deteção para todos os rostos num **FaceList** objeto e não precisa de utilizar o mesmo modelo de deteção, quando detetar rostos novo, para comparar com um **FaceList** objeto.

## <a name="evaluate-different-models"></a>Avaliar modelos diferentes

Os modelos de deteção de rostos diferentes estão otimizados para diferentes tarefas. Consulte a tabela seguinte para obter uma descrição geral das diferenças.

|**detection_01**  |**detection_02**  |
|---------|---------|
|Escolha do padrão para todas as operações de deteção de rostos. | Lançado em Maio de 2019 e disponível, opcionalmente, em todas as operações de deteção de rostos.
|Não otimizados para pequenas, do lado do Vista ou rostos indistinta.  | Maior exatidão em pequenas, do lado do Vista e rostos indistinta. |
|Devolve enfrenta atributos (representam principal, idade, emoção etc.) se eles estão especificados na chamada de detetar. |  Não devolve atributos faciais.     |
|Devolve enfrenta pontos de referência, se eles estão especificados na chamada de detetar.   | Não devolve pontos de referência do rosto.  |

A melhor forma de comparar os desempenhos do `detection_01` e `detection_02` modelos é usá-los num conjunto de dados de exemplo. É recomendável chamar a [Se deparam - detetar] API numa variedade de imagens, especialmente imagens de muitas faces ou de faces que são difíceis de ver, com cada modelo de deteção. Preste atenção ao número de rostos que retorna cada modelo.

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu como especificar o modelo de deteção a utilizar com as APIs de rostos diferentes. Em seguida, seguir um guia de introdução para começar a utilizar a deteção de rostos.

* [Detetar rostos numa imagem (SDK do .NET)](../quickstarts/csharp-detect-sdk.md)

[Se deparam - detetar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Face - Find Similar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[Rosto – Identificar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Face - Verify]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[PersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[PersonGroup - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup - Train]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[FaceList - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250
[LargeFaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc