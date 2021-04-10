---
title: Como especificar um modelo de reconhecimento - Face
titleSuffix: Azure Cognitive Services
description: Este artigo irá mostrar-lhe como escolher qual modelo de reconhecimento usar com a sua aplicação Azure Face.
services: cognitive-services
author: longli0
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/05/2021
ms.author: longl
ms.custom: devx-track-csharp
ms.openlocfilehash: 3d22f2fb0bb550d966cbc5e181882552514513b2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105936585"
---
# <a name="specify-a-face-recognition-model"></a>Especifique um modelo de reconhecimento facial

Este guia mostra-lhe como especificar um modelo de reconhecimento facial para deteção facial, identificação e pesquisa de semelhança utilizando o serviço Azure Face.

O serviço Face utiliza modelos de machine learning para realizar operações em rostos humanos em imagens. Continuamos a melhorar a precisão dos nossos modelos com base no feedback dos clientes e nos avanços na investigação, e entregamos estas melhorias como atualizações de modelos. Os desenvolvedores têm a opção de especificar qual a versão do modelo de reconhecimento facial que gostariam de usar; podem escolher o modelo que melhor se adequa ao seu caso de utilização.

O serviço Azure Face tem quatro modelos de reconhecimento disponíveis. Os modelos _recognition_01_ (publicados em 2017), _recognition_02_ (publicados em 2019) e _recognition_03_ (publicado em 2020) são continuamente suportados para garantir retrocompatibilidade para clientes que usam FaceLists ou **PersonGroup** criados com estes modelos. Um **FaceList** ou **Persongroup** usará sempre o modelo de reconhecimento com o que foi criado, e novas caras ficarão associadas a este modelo quando forem adicionadas. Isto não pode ser alterado após a criação e os clientes terão de utilizar o modelo de reconhecimento correspondente com o **FaceList** ou **o PersonGroup** correspondentes.

Pode mudar-se para modelos de reconhecimento posteriores à sua própria conveniência; no entanto, terá de criar novos FaceLists e PersonGroups com o modelo de reconhecimento à sua escolha.

O modelo _recognition_04_ (publicado em 2021) é o modelo mais preciso atualmente disponível. Se for um novo cliente, recomendamos a utilização deste modelo. _Recognition_04_ proporcionará uma maior precisão tanto para comparações de semelhança como para comparações de pessoas. _Recognition_04_ melhora o reconhecimento para utilizadores inscritos que usam capas faciais (máscaras cirúrgicas, máscaras N95, máscaras de pano). Agora pode construir experiências de utilizador seguras e sem emenda que usam o modelo _de detection_03_ mais recente para detetar se um utilizador inscrito está a usar uma capa facial e, em seguida, reconhecer quem são com o modelo _de recognition_04_ mais recente. Note que cada modelo funciona independentemente dos outros, e um limiar de confiança definido para um modelo não deve ser comparado entre os outros modelos de reconhecimento.

Continuar a ler Para saber como especificar um modelo selecionado em diferentes operações face, evitando conflitos de modelos. Se é um utilizador avançado e gostaria de determinar se deve mudar para o modelo mais recente, salte para a secção [avaliar diferentes modelos](#evaluate-different-models) para avaliar o novo modelo e comparar resultados utilizando o seu conjunto de dados atuais.


## <a name="prerequisites"></a>Pré-requisitos

Deve estar familiarizado com os conceitos de deteção e identificação de rostos de IA. Se não estiver, consulte estes guias primeiro:

* [Conceitos de deteção de rosto](../concepts/face-detection.md)
* [Conceitos de reconhecimento facial](../concepts/face-recognition.md)
* [Como detetar rostos numa imagem](HowtoDetectFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>Detetar rostos com modelo especificado

A deteção facial identifica os marcos visuais dos rostos humanos e encontra as suas localizações de caixa de delimitação. Também extrai as características do rosto e armazena-as para utilização na identificação. Toda esta informação forma a representação de um rosto.

O modelo de reconhecimento é utilizado quando as características do rosto são extraídas, para que possa especificar uma versão modelo ao executar a operação Deteção.

Ao utilizar o [Face - Detetar] API, atribua a versão do modelo com o `recognitionModel` parâmetro. Os valores disponíveis são:
* recognition_01
* recognition_02
* recognition_03
* recognition_04


Opcionalmente, pode especificar o _parâmetro ReturnRecognitionModel_ **(falso** predefinido) para indicar se _o modelo de reconhecimento_ deve ser devolvido em resposta. Assim, um URL de pedido para o [Rosto - Detect] REST API será assim:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel]&subscription-key=<Subscription key>`

Se estiver a utilizar a biblioteca do cliente, pode atribuir o valor `recognitionModel` ao passar uma cadeia que representa a versão. Se o deixar sem ser atribuído, será utilizada uma versão modelo `recognition_01` padrão. Consulte o seguinte exemplo de código para a biblioteca de clientes .NET.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, recognitionModel: "recognition_01", returnRecognitionModel: true);
```

## <a name="identify-faces-with-specified-model"></a>Identificar rostos com modelo especificado

O serviço Face pode extrair dados faciais de uma imagem e associá-lo a um objeto **pessoa** (através da chamada [API face Add,](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) por exemplo), e vários objetos **pessoais** podem ser armazenados juntos num **PersonGroup**. Em seguida, um novo rosto pode ser comparado com um **PersonGroup** (com a chamada [Face - Identifique),] e a pessoa correspondente dentro desse grupo pode ser identificada.

Um **PersonGroup** deve ter um modelo de reconhecimento único para todos os **Pessoas,** e pode especiá-lo usando o `recognitionModel` parâmetro quando criar o grupo [(PersonGroup - Criar] ou [LargePersonGroup - Criar]). Se não especificar este parâmetro, o modelo original `recognition_01` é utilizado. Um grupo usará sempre o modelo de reconhecimento com o que foi criado, e novas caras ficarão associadas a este modelo quando forem adicionadas ao mesmo; isto não pode ser alterado após a criação de um grupo. Para ver com que modelo um **PersonGroup** está configurado, utilize o [PersonGroup - Obtenha] API com o _retornadoRecognitionModel_ definido como **verdadeiro**.

Consulte o seguinte exemplo de código para a biblioteca de clientes .NET.

```csharp
// Create an empty PersonGroup with "recognition_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");
```

Neste código, é criado um **PersonGroup** com `mypersongroupid` ID, e é configurado para utilizar o modelo _recognition_02_ para extrair características faciais.

Consequentemente, é necessário especificar qual o modelo a utilizar ao detetar rostos para comparar com este **PersonGroup** (através do [Face - Detect] API). O modelo que utiliza deve ser sempre consistente com a configuração do **PersonGroup;** caso contrário, a operação falhará devido a modelos incompatíveis.

Não há alteração no [Rosto - Identificar] API; basta especificar a versão do modelo na deteção.

## <a name="find-similar-faces-with-specified-model"></a>Encontre rostos semelhantes com modelo especificado

Também pode especificar um modelo de reconhecimento para pesquisa de semelhança. Pode atribuir a versão do modelo ao `recognitionModel` criar o **FaceList** com [FaceList - Criar] API ou [LargeFaceList - Criar]. Se não especificar este parâmetro, o `recognition_01` modelo é utilizado por predefinição. Um **FaceList** utilizará sempre o modelo de reconhecimento com o que foi criado, e novas caras ficarão associadas a este modelo quando forem adicionadas à lista; não podes mudar isto depois da criação. Para ver com que modelo um **FaceList** está configurado, utilize o [FaceList - Obtenha] API com o _retornadoRecognitionModel_ definido como **verdadeiro**.

Consulte o seguinte exemplo de código para a biblioteca de clientes .NET.

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_04");
```

Este código cria uma **Lista Face Chamada** , utilizando o modelo `My face collection` _recognition_04_ para a extração de funcionalidades. Quando procura este **FaceList** por rostos semelhantes a um novo rosto detetado, esse rosto deve ter sido detetado [(Face - Detect]) utilizando o modelo _recognition_04._ Tal como na secção anterior, o modelo tem de ser consistente.

Não há alteração no Rosto - Encontre API [similar;] apenas especificar a versão do modelo na deteção.

## <a name="verify-faces-with-specified-model"></a>Verificar rostos com modelo especificado

O [Rosto - Verifique se a] API verifica se duas faces pertencem à mesma pessoa. Não existe qualquer alteração na API de Verificação no que diz respeito aos modelos de reconhecimento, mas só é possível comparar rostos que foram detetados com o mesmo modelo.

## <a name="evaluate-different-models"></a>Avaliar diferentes modelos

Se quiser comparar as performances de diferentes modelos de reconhecimento nos seus próprios dados, terá de:
1. Crie quatro Grupos pessoais utilizando _recognition_01_, _recognition_02_, _recognition_03_ e _recognition_04_ respectivamente.
1. Utilize os seus dados de imagem para detetar rostos e registá-los na **Pessoa** s dentro destes quatro **PersonGroup** s. 
1. Treine os seus Grupos pessoais utilizando o PersonGroup - Train API.
1. Teste com Face - Identifique nos quatro **PersonGroup** s e compare os resultados.


Se normalmente especificar um limiar de confiança (um valor entre zero e um que determina o quão confiante o modelo deve estar para identificar um rosto), poderá ter de utilizar diferentes limiares para diferentes modelos. Um limiar para um modelo não deve ser partilhado para outro e não produzirá necessariamente os mesmos resultados.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a especificar o modelo de reconhecimento a utilizar com diferentes APIs de serviço Face. Em seguida, siga um quickstart para começar a usar a deteção facial.

* [Face .NET SDK](../quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)
* [Face Python SDK](../quickstarts/client-libraries.md?pivots=programming-language-python%253fpivots%253dprogramming-language-python)
* [Face Go SDK](../quickstarts/client-libraries.md?pivots=programming-language-go%253fpivots%253dprogramming-language-go)

[Rosto – Detetar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Rosto - Encontrar Similar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[Rosto – Identificar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Rosto - Verificar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[PersonGroup – Criar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[PersonGroup - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup - Train]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup – Criar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList - Criar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[LargeFaceList - Criar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc