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
ms.date: 12/03/2019
ms.author: longl
ms.openlocfilehash: da9ad5576d146c007e45124668875e9681860ce6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "76938830"
---
# <a name="specify-a-face-recognition-model"></a>Especifique um modelo de reconhecimento facial

Este guia mostra como especificar um modelo de reconhecimento facial para deteção facial, identificação e pesquisa de semelhança utilizando o serviço Azure Face.

O serviço Face utiliza modelos de machine learning para realizar operações em rostos humanos em imagens. Continuamos a melhorar a precisão dos nossos modelos com base no feedback dos clientes e nos avanços na investigação, e entregamos estas melhorias como atualizações de modelos. Os desenvolvedores têm a opção de especificar qual a versão do modelo de reconhecimento facial que gostariam de utilizar; podem escolher o modelo que melhor se adequa ao seu caso de utilização.

Se for um novo utilizador, recomendamos que utilize o modelo mais recente. Continuar a ler Para aprender a especificá-lo em diferentes operações face, evitando conflitos de modelos. Se for um utilizador avançado e não tiver a certeza se deve mudar para o modelo mais recente, salte para a secção [Avaliar diferentes modelos](#evaluate-different-models) para avaliar o novo modelo e comparar resultados utilizando o seu conjunto de dados atual.

## <a name="prerequisites"></a>Pré-requisitos

Deve estar familiarizado com os conceitos de deteção e identificação de rostos de IA. Se não estiver, veja primeiro estes guias:

* [Como detetar rostos numa imagem](HowtoDetectFacesinImage.md)
* [Como identificar rostos numa imagem](HowtoIdentifyFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>Detetar rostos com modelo especificado

A deteção facial identifica os marcos visuais dos rostos humanos e encontra as suas localizações de delimitação. Extrai também as características do rosto e armazena-as para utilização na identificação. Toda esta informação forma a representação de um rosto.

O modelo de reconhecimento é utilizado quando as características faciais são extraídas, para que possa especificar uma versão modelo ao executar a operação Detect.

Ao utilizar o [Face - Detete] a API, atribua a versão do modelo com o `recognitionModel` parâmetro. Os valores disponíveis são:

* `recognition_01`
* `recognition_02`

Opcionalmente, pode especificar o parâmetro _returnRecognitionModel_ **(predefinição falso)** para indicar se o _reconhecimentoModelo_ deve ser devolvido em resposta. Assim, um URL de pedido para o [Rosto - Detete] a API REST será assim:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel]&subscription-key=<Subscription key>`

Se estiver a utilizar a biblioteca do cliente, pode atribuir o valor por `recognitionModel` passar uma corda que represente a versão.
Se o deixar não atribuído, será utilizada a versão padrão do modelo _(recognition_01)._ Consulte o seguinte código exemplo para a biblioteca de clientes .NET.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, recognitionModel: "recognition_02", returnRecognitionModel: true);
```

## <a name="identify-faces-with-specified-model"></a>Identificar rostos com modelo especificado

O serviço Face pode extrair dados faciais de uma imagem e associá-los a um objeto **Pessoa** (através da chamada API [face Add,](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) por exemplo), e vários objetos **pessoa** podem ser armazenados juntos num **PersonGroup**. Em seguida, um novo rosto pode ser comparado com um **PersonGroup** (com a [chamada Face - Identificar),] e a pessoa correspondente dentro desse grupo pode ser identificada.

Um **PersonGroup** deve ter um modelo de reconhecimento único para todos `recognitionModel` os **S**pessoas, e pode especificar isso usando o parâmetro quando criar o grupo ([PersonGroup - Criar] ou [LargePersonGroup - Criar]). Se não especificar este parâmetro, `recognition_01` o modelo original é utilizado. Um grupo utilizará sempre o modelo de reconhecimento com o que foi criado, e novas faces ficarão associadas a este modelo quando lhe forem adicionadas; isto não pode ser alterado após a criação de um grupo. Para ver com que modelo um **PersonGroup** está configurado, utilize o [PersonGroup - Obtenha] API com o parâmetro _returnRecognitionModel_ definido como **verdadeiro**.

Consulte o seguinte código exemplo para a biblioteca de clientes .NET.

```csharp
// Create an empty PersonGroup with "recognition_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");
```

Neste código, é criado `mypersongroupid` um **PersonGroup** com ID e é criado para utilizar o modelo _recognition_02_ para extrair características faciais.

Do mesmo modo, é necessário especificar qual o modelo a utilizar ao detetar rostos para comparar com este **PersonGroup** (através do [Face - Deteta).] O modelo que utiliza deve ser sempre consistente com a configuração do **PersonGroup;** caso contrário, a operação falhará devido a modelos incompatíveis.

Não há alteração no [Rosto - Identificar] API; basta especificar a versão do modelo na deteção.

## <a name="find-similar-faces-with-specified-model"></a>Encontre rostos semelhantes com modelo especificado

Também pode especificar um modelo de reconhecimento para pesquisa de semelhanças. Pode atribuir a versão `recognitionModel` do modelo ao criar a lista de rosto com [faceList - Criar] API ou [LargeFaceList - Criar]. Se não especificar este parâmetro, `recognition_01` o modelo original é utilizado. Uma lista facial utilizará sempre o modelo de reconhecimento com o que foi criado, e novas faces ficarão associadas a este modelo quando lhe forem adicionadas; isto não pode ser alterado após a criação. Para ver com que modelo está configurada uma lista de rosto, utilize o [FaceList - Obtenha] API com o parâmetro de _retornoRecognitionModel_ definido como **verdadeiro**.

Consulte o seguinte código exemplo para a biblioteca de clientes .NET.

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");
```

Este código cria uma `My face collection`lista facial chamada , utilizando o modelo _recognition_02_ para extração de recurso. Ao pesquisar esta lista de rostos semelhantes a um novo rosto detetado, esse rosto deve ter sido detetado[(Face - Detete)]utilizando o modelo _recognition_02._ Tal como na secção anterior, o modelo tem de ser consistente.

Não há alteração no Rosto - Encontre API [semelhante;] só especifica a versão do modelo na deteção.

## <a name="verify-faces-with-specified-model"></a>Verificar rostos com modelo especificado

O [Face - Verifique] se a API verifica se duas faces pertencem à mesma pessoa. Não existe qualquer alteração na API verificar no que diz respeito aos modelos de reconhecimento, mas só é possível comparar rostos que foram detetados com o mesmo modelo. Assim, as duas faces terão de ser `recognition_01` `recognition_02`detetadas usando ou .

## <a name="evaluate-different-models"></a>Avaliar diferentes modelos

Se quiser comparar as performances dos _modelos recognition_01_ e _recognition_02_ nos seus dados, terá de:

1. Crie dois **PersonGroup**s com _recognition_01_ e _recognition_02_ respectivamente.
1. Utilize os seus dados de imagem para detetar rostos e registá-los para **pessoas**para estes dois **PersonGroup**s, e desencadear o processo de formação com o [PersonGroup - Train] API.
1. Teste com [Face - Identifique] em ambos os **PersonGroup**s e compare os resultados.

Se normalmente especificar um limiar de confiança (um valor entre zero e um que determina o quão confiante o modelo deve ser para identificar um rosto), poderá ter de utilizar diferentes limiares para diferentes modelos. Um limiar para um modelo não deve ser partilhado para outro e não produzirá necessariamente os mesmos resultados.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a especificar o modelo de reconhecimento a utilizar com diferentes APIs do serviço Face. Em seguida, siga um início rápido para começar a usar a deteção facial.

* [Face .NET SDK](../Quickstarts/csharp-sdk.md)
* [Face Python SDK](../Quickstarts/python-sdk.md)
* [Face Go SDK](../Quickstarts/go-sdk.md)

[Rosto – Detetar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Face - Encontrar Similar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[Rosto – Identificar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Rosto - Verificar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[PersonGroup – Criar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[PersonGroup - Obter]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup – Preparar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup – Criar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList - Criar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList - Obter]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[LargeFaceList - Criar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc
