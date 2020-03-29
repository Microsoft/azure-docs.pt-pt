---
title: Como especificar um modelo de deteção - Face
titleSuffix: Azure Cognitive Services
description: Este artigo irá mostrar-lhe como escolher qual modelo de deteção facial usar com a sua aplicação Azure Face.
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: yluiu
ms.openlocfilehash: 40ca1dbf981c5a9025cf5a0bac6b007709d69a77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "76934566"
---
# <a name="specify-a-face-detection-model"></a>Especifique um modelo de deteção facial

Este guia mostra-lhe como especificar um modelo de deteção facial para o serviço Azure Face.

O serviço Face utiliza modelos de machine learning para realizar operações em rostos humanos em imagens. Continuamos a melhorar a precisão dos nossos modelos com base no feedback dos clientes e nos avanços na investigação, e entregamos estas melhorias como atualizações de modelos. Os desenvolvedores têm a opção de especificar qual a versão do modelo de deteção facial que gostariam de utilizar; podem escolher o modelo que melhor se adequa ao seu caso de utilização.

Leia mais para saber como especificar o modelo de deteção facial em determinadas operações faciais. O serviço Face utiliza a deteção facial sempre que converte uma imagem de um rosto em outra forma de dados.

Se não tiver a certeza se deve utilizar o modelo mais recente, salte para a secção [Avaliar diferentes modelos](#evaluate-different-models) para avaliar o novo modelo e comparar resultados utilizando o conjunto de dados atual.

## <a name="prerequisites"></a>Pré-requisitos

Deve estar familiarizado com o conceito de deteção facial de IA. Se não estiver, consulte o guia conceptual de deteção facial ou o guia como orientar:

* [Conceitos de deteção facial](../concepts/face-detection.md)
* [Como detetar rostos numa imagem](HowtoDetectFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>Detetar rostos com modelo especificado

A deteção facial encontra os locais de delimitação de rostos humanos e identifica os seus marcos visuais. Extrai as características do rosto e armazena-as para posterior utilização em operações de [reconhecimento.](../concepts/face-recognition.md)

Quando utilizar o [Face - Detete] a API, `detectionModel` pode atribuir a versão do modelo ao parâmetro. Os valores disponíveis são:

* `detection_01`
* `detection_02`

Um URL de pedido para o [Rosto - Detete] a API REST será assim:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel][&detectionModel]&subscription-key=<Subscription key>`

Se estiver a utilizar a biblioteca do cliente, pode atribuir o valor por `detectionModel` passar numa corda apropriada. Se o deixar desatribuído, a API utilizará`detection_01`a versão padrão do modelo ( ). Consulte o seguinte código exemplo para a biblioteca de clientes .NET.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, false, false, recognitionModel: "recognition_02", detectionModel: "detection_02");
```

## <a name="add-face-to-person-with-specified-model"></a>Adicionar rosto a pessoa com modelo especificado

O serviço Face pode extrair dados faciais de uma imagem e associá-los a um objeto **pessoa** através da [Pessoa Pessoa - Adicionar API facial.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) Nesta chamada API, pode especificar o modelo de deteção da mesma forma que no [Face - Detete].

Consulte o seguinte código exemplo para a biblioteca de clientes .NET.

```csharp
// Create a PersonGroup and add a person with face detected by "detection_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");

string personId = (await faceClient.PersonGroupPerson.CreateAsync(personGroupId, "My Person Name")).PersonId;

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.PersonGroupPerson.AddFaceFromUrlAsync(personGroupId, personId, imageUrl, detectionModel: "detection_02");
```

Este código cria um `mypersongroupid` **PersonGroup** com ID e adiciona-lhe uma **Pessoa.** Em seguida, adiciona **Person** um Rosto `detection_02` a esta Pessoa usando o modelo. Se não especificar o parâmetro Model de *deteção,* a API utilizará o modelo predefinido, `detection_01`.

> [!NOTE]
> Não é necessário utilizar o mesmo modelo de deteção para todas as faces de um objeto **Pessoa,** e não precisa de usar o mesmo modelo de deteção ao detetar novos rostos para comparar com um objeto **pessoa** (no [Rosto - Identificar] API, por exemplo).

## <a name="add-face-to-facelist-with-specified-model"></a>Adicione face ao FaceList com modelo especificado

Também pode especificar um modelo de deteção quando adicionar um rosto a um objeto **FaceList** existente. Consulte o seguinte código exemplo para a biblioteca de clientes .NET.

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.FaceList.AddFaceFromUrlAsync(faceListId, imageUrl, detectionModel: "detection_02");
```

Este código cria um `My face collection` **FaceList** chamado e `detection_02` adiciona-lhe um Face com o modelo. Se não especificar o parâmetro Model de *deteção,* a API utilizará o modelo predefinido, `detection_01`.

> [!NOTE]
> Não precisa de usar o mesmo modelo de deteção para todas as faces num objeto **FaceList,** e não precisa de usar o mesmo modelo de deteção ao detetar novas faces para se comparar com um objeto **FaceList.**

## <a name="evaluate-different-models"></a>Avaliar diferentes modelos

Os diferentes modelos de deteção facial são otimizados para diferentes tarefas. Consulte a tabela seguinte para uma visão geral das diferenças.

|**detection_01**  |**detection_02**  |
|---------|---------|
|Escolha padrão para todas as operações de deteção facial. | Lançado em maio de 2019 e disponível opcionalmente em todas as operações de deteção facial.
|Não otimizado para rostos pequenos, laterais ou borrados.  | Melhor precisão em rostos pequenos, laterais e borrados. |
|Devoluções de atributos faciais (pose da cabeça, idade, emoção, e assim por diante) se forem especificados na chamada de deteção. |  Não devolve atributos faciais.     |
|Devoluções enfrentam marcos se forem especificados na chamada de deteção.   | Não volta a enfrentar marcos faciais.  |

A melhor maneira de comparar `detection_01` os `detection_02` desempenhos dos modelos e modelos é usá-los num conjunto de dados de amostra. Recomendamos chamar o [Rosto - Detete] a API numa variedade de imagens, especialmente imagens de muitas faces ou de rostos que são difíceis de ver, utilizando cada modelo de deteção. Preste atenção ao número de rostos que cada modelo retorna.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a especificar o modelo de deteção a utilizar com diferentes APIs face. Em seguida, siga um início rápido para começar a usar a deteção facial.

* [Face .NET SDK](../Quickstarts/csharp-sdk.md)
* [Face Python SDK](../Quickstarts/python-sdk.md)
* [Face Go SDK](../Quickstarts/go-sdk.md)

[Rosto – Detetar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
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
