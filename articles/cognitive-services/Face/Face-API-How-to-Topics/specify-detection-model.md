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
ms.custom: devx-track-csharp
ms.openlocfilehash: 5a70b10f7d22c9cc04427bdfbb44243fad457ba0
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913488"
---
# <a name="specify-a-face-detection-model"></a>Especifique um modelo de deteção facial

Este guia mostra-lhe como especificar um modelo de deteção facial para o serviço Azure Face.

O serviço Face utiliza modelos de machine learning para realizar operações em rostos humanos em imagens. Continuamos a melhorar a precisão dos nossos modelos com base no feedback dos clientes e nos avanços na investigação, e entregamos estas melhorias como atualizações de modelos. Os desenvolvedores têm a opção de especificar qual a versão do modelo de deteção facial que gostariam de usar; podem escolher o modelo que melhor se adequa ao seu caso de utilização.

Continuar a ler Para saber como especificar o modelo de deteção facial em determinadas operações faciais. O serviço Face utiliza a deteção facial sempre que converte uma imagem de um rosto em outra forma de dados.

Se não tiver a certeza se deve utilizar o modelo mais recente, salte para a secção [avaliar diferentes modelos](#evaluate-different-models) para avaliar o novo modelo e comparar resultados utilizando o seu conjunto de dados atuais.

## <a name="prerequisites"></a>Pré-requisitos

Deve estar familiarizado com o conceito de deteção facial de IA. Se não estiver, consulte o guia conceptual de deteção facial ou o guia:

* [Conceitos de deteção de rosto](../concepts/face-detection.md)
* [Como detetar rostos numa imagem](HowtoDetectFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>Detetar rostos com modelo especificado

A deteção facial encontra as localizações da caixa de limites dos rostos humanos e identifica os seus marcos visuais. Extrai as características do rosto e armazena-as para posterior utilização em operações [de reconhecimento.](../concepts/face-recognition.md)

Quando utilizar o [Face - Detetar] API, pode atribuir a versão do modelo com o `detectionModel` parâmetro. Os valores disponíveis são:

* `detection_01`
* `detection_02`

Um URL de pedido para o [Rosto - Detect] REST API será assim:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel][&detectionModel]&subscription-key=<Subscription key>`

Se estiver a utilizar a biblioteca do cliente, pode atribuir o valor `detectionModel` através de uma cadeia apropriada. Se o deixar por assinar, a API utilizará a versão do modelo predefinido `detection_01` (). Consulte o seguinte exemplo de código para a biblioteca de clientes .NET.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, false, false, recognitionModel: "recognition_03", detectionModel: "detection_02");
```

## <a name="add-face-to-person-with-specified-model"></a>Adicionar rosto a Pessoa com modelo especificado

O serviço Face pode extrair dados faciais de uma imagem e associá-lo a um objeto **pessoa** através da Pessoa Grupo - Adicionar API [face.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) Nesta chamada API, pode especificar o modelo de deteção da mesma forma que no [Face - Deteto].

Consulte o seguinte exemplo de código para a biblioteca de clientes .NET.

```csharp
// Create a PersonGroup and add a person with face detected by "detection_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_03");

string personId = (await faceClient.PersonGroupPerson.CreateAsync(personGroupId, "My Person Name")).PersonId;

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.PersonGroupPerson.AddFaceFromUrlAsync(personGroupId, personId, imageUrl, detectionModel: "detection_02");
```

Este código cria um **PersonGroup** com ID `mypersongroupid` e adiciona-lhe uma **Pessoa.** Em seguida, adiciona um Rosto a esta **Pessoa** usando o `detection_02` modelo. Se não especificar o parâmetro *de detecçãoModel,* a API utilizará o modelo predefinido, `detection_01` .

> [!NOTE]
> Não precisa de utilizar o mesmo modelo de deteção para todas as faces num objeto **Da Pessoa,** e não precisa de utilizar o mesmo modelo de deteção ao detetar novos rostos para comparar com um objeto **pessoa** (no Rosto [- Identificar] a API, por exemplo).

## <a name="add-face-to-facelist-with-specified-model"></a>Adicione face ao FaceList com modelo especificado

Também pode especificar um modelo de deteção quando adicionar um rosto a um objeto **FaceList** existente. Consulte o seguinte exemplo de código para a biblioteca de clientes .NET.

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_03");

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.FaceList.AddFaceFromUrlAsync(faceListId, imageUrl, detectionModel: "detection_02");
```

Este código cria uma **FaceList** chamada `My face collection` e adiciona-lhe um Face com o `detection_02` modelo. Se não especificar o parâmetro *de detecçãoModel,* a API utilizará o modelo predefinido, `detection_01` .

> [!NOTE]
> Não precisa de utilizar o mesmo modelo de deteção para todas as faces num objeto **FaceList,** e não precisa de utilizar o mesmo modelo de deteção ao detetar novas faces para comparar com um objeto **FaceList.**

## <a name="evaluate-different-models"></a>Avaliar diferentes modelos

Os diferentes modelos de deteção facial são otimizados para diferentes tarefas. Consulte a tabela seguinte para uma visão geral das diferenças.

|**detection_01**  |**detection_02**  |
|---------|---------|
|Escolha padrão para todas as operações de deteção facial. | Lançado em maio de 2019 e disponível opcionalmente em todas as operações de deteção facial.
|Não otimizado para rostos pequenos, laterais ou borrados.  | Precisão melhorada em faces pequenas, laterais e desfocadas. |
|Devolve os atributos faciais (pose da cabeça, idade, emoção, e assim por diante) se forem especificados na chamada de deteção. |  Não devolve atributos faciais.     |
|Retorna marcos faciais se forem especificados na chamada de deteção.   | Não devolve marcos faciais.  |

A melhor forma de comparar as performances dos `detection_01` modelos e `detection_02` modelos é usá-las num conjunto de dados de amostra. Recomendamos chamar o [Face - Detetar] a API numa variedade de imagens, especialmente imagens de muitas faces ou de rostos difíceis de ver, utilizando cada modelo de deteção. Preste atenção ao número de rostos que cada modelo devolve.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a especificar o modelo de deteção para utilizar com diferentes APIs do Rosto. Em seguida, siga um quickstart para começar a usar a deteção facial.

* [Face .NET SDK](../quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)
* [Face Python SDK](../quickstarts/client-libraries.md?pivots=programming-language-python%253fpivots%253dprogramming-language-python)
* [Face Go SDK](../quickstarts/client-libraries.md?pivots=programming-language-go%253fpivots%253dprogramming-language-go)

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