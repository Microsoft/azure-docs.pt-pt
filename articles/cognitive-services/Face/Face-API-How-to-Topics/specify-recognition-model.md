---
title: Como especificar um modelo de reconhecimento - Face API
titleSuffix: Azure Cognitive Services
description: Este artigo irá mostrar como escolher qual modelo de reconhecimento para utilizar com a sua aplicação de API de rostos do Azure.
services: cognitive-services
author: longl
manager: nitinme
ms.service: cognitive-services
ms.component: face-api
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: longl
ms.openlocfilehash: e8d5c416183a7d475a46c5e538577069612baf8e
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449011"
---
# <a name="specify-a-face-recognition-model"></a>Especifique um modelo de reconhecimento facial

Este guia mostra como especificar um modelo de reconhecimento de rostos para deteção de rostos, a identificação e a pesquisa de semelhanças com a API Face do Azure.

A API de rostos utiliza modelos de aprendizagem automática para executar operações no rostos humanos em imagens. Continuamos a melhorar a exatidão dos nossos modelos com base nos comentários dos clientes e avanços na investigação e para fornecer esses aprimoramentos como atualizações do modelo. Os desenvolvedores têm a opção para especificar que versão do modelo de reconhecimento de rostos que gostariam de utilizar. podem escolher o modelo que melhor se adequa ao seu caso de utilização.

Se for um novo utilizador, recomendamos que utilize o modelo mais recente. Continue a ler para saber como especificá-lo em operações diferentes do rosto, evitando conflitos de modelo. Se for utilizador avançado e que não-se de que se deve mudar para o modelo mais recente, avance para o [avaliar modelos diferentes](#evaluate-different-models) secção para avaliar o novo modelo e comparar os resultados com o seu conjunto de dados atual.

## <a name="prerequisites"></a>Pré-requisitos

Deve estar familiarizado com os conceitos de deteção de rostos de IA e identificação. Se não estiver, veja estes guias de procedimentos primeiro:

* [Como detetar rostos numa imagem](HowtoDetectFacesinImage.md)
* [Como identificar rostos numa imagem](HowtoIdentifyFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>Detetar rostos com o modelo especificado

Deteção de rostos identifica os pontos de referência visual de rostos humanos e localiza seus locais de caixa delimitadora. Também extrai funcionalidades o mostrador e armazena-os para utilização na identificação. Todas essas informações de formulários a representação de um rosto.

O modelo de reconhecimento é usado quando os recursos de face são extraídos, então, pode especificar uma versão de modelo ao efetuar a operação de deteção.

Ao utilizar o [Se deparam - detetar] API, atribuir a versão de modelo com o `recognitionModel` parâmetro. Os valores disponíveis são:

* `recognition_01`
* `recognition_02`

Opcionalmente, pode especificar a _returnRecognitionModel_ parâmetro (predefinição **false**) para indicar se _recognitionModel_ deve ser devolvido na resposta. Deste modo, um URL do pedido para o [Se deparam - detetar] REST API terá esta aparência:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel]
&subscription-key=<Subscription key>`

Se estiver a utilizar a biblioteca de cliente, pode atribuir o valor para `recognitionModel` passando uma cadeia de caracteres que representa a versão.
Se deixá-lo não atribuído, a versão de modelo padrão (_recognition_01_) será utilizado. Veja o seguinte exemplo de código para a biblioteca de cliente .NET.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, recognitionModel: "recognition_02", returnRecognitionModel: true);
```

## <a name="identify-faces-with-specified-model"></a>Identifique rostos com o modelo especificado

A API Face pode extrair dados face a partir de uma imagem e associá-la com um **pessoa** objeto (através do [adicionar face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) chamada de API, por exemplo) e vários **pessoa** objetos podem ser armazenadas num **PersonGroup**. Em seguida, um novo rosto pode ser comparado com um **PersonGroup** (com o [Rosto – Identificar] chamar), e a pessoa correspondente dentro desse grupo pode ser identificada.

A **PersonGroup** deve ter um modelo de reconhecimento exclusivo para todos os **pessoa**s e pode especificar isso usando o `recognitionModel` parâmetro ao criar o grupo de ([PersonGroup – Criar] ou [LargePersonGroup – criar]). Se não especificar este parâmetro, o original `recognition_01` modelo é usado. Um grupo irá utilizar sempre o modelo de reconhecimento de que foi criado com e rostos novo passará a ser associados a este modelo quando são adicionados a ela; Isto não pode ser alterado após a criação de um grupo. Para ver o modelo de um **PersonGroup** está configurado com, utilize o [PersonGroup - Get] API com o _returnRecognitionModel_ parâmetro definido como **true**.

Veja o seguinte exemplo de código para a biblioteca de cliente .NET.

```csharp
// Create an empty PersonGroup with "recognition_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");
```

Nesse código, um **PersonGroup** com o ID `mypersongroupid` for criado, e este está configurado para utilizar os _recognition_02_ modelo para extrair recursos face.

Do mesmo modo, tem de especificar qual modelo deve ser usado quando a deteção de rostos para comparar em relação a isso **PersonGroup** (por meio do [Se deparam - detetar] API). O modelo utiliza sempre deve ser consistente com o **PersonGroup**da configuração; caso contrário, a operação falhará devido a modelos incompatíveis.

Não há nenhuma alteração no [Rosto – Identificar] API; só precisa de especificar a versão de modelo na detecção.

## <a name="find-similar-faces-with-specified-model"></a>Encontrar rostos semelhantes com o modelo especificado

Também pode especificar um modelo de reconhecimento para pesquisa de semelhança. Pode atribuir a versão de modelo com `recognitionModel` ao criar a lista de rostos com [FaceList - Create] API ou [LargeFaceList - Create]. Se não especificar este parâmetro, o original `recognition_01` modelo é usado. Uma lista de rostos utiliza sempre o modelo de reconhecimento de que foi criado com e rostos novo passará a ser associados a este modelo quando são adicionados a ela; Isto não pode ser alterado após a criação. Para ver o modelo de uma lista de face está configurada com, utilize o [FaceList - Get] API com o _returnRecognitionModel_ parâmetro definido como **verdadeiro**.

Veja o seguinte exemplo de código para a biblioteca de cliente .NET.

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");
```

Este código cria uma lista de face chamada `My face collection`, utilizando o _recognition_02_ modelos de extração de funcionalidade. Quando pesquisa esta lista de face de rostos semelhantes para um novo rosto detetado, que enfrentam tem foram detetado ([Se deparam - detetar]) com o _recognition_02_ modelo. Como na seção anterior, o modelo tem de ser consistente.

Não há nenhuma alteração no [Face - Find semelhantes] API; apenas especificar a versão de modelo na deteção.

## <a name="verify-faces-with-specified-model"></a>Verifique rostos com o modelo especificado

O [Se deparam - verificar] API verifica se os dois rostos pertencem à mesma pessoa. Não há nenhuma alteração na API de verificar em relação a modelos de reconhecimento, mas só é possível comparar rostos que foram detetados com o mesmo modelo. Por isso, os dois rostos ambos tem de ter sido detectados usando `recognition_01` ou `recognition_02`.

## <a name="evaluate-different-models"></a>Avaliar modelos diferentes

Se desejar comparar os desempenhos do _recognition_01_ e _recognition_02_ modelos nos seus dados, tem de:

1. Criar dois **PersonGroup**s com _recognition_01_ e _recognition_02_ , respetivamente.
1. Utilize os dados de imagem para detetar rostos e registá-los para **pessoa**s para essas duas **PersonGroup**s e o treinamento de Acionador processam com [PersonGroup – Preparar] API.
1. Testar com [Rosto – Identificar] em ambos **PersonGroup**s e compare os resultados.

Se especificar um limiar de confiança (um valor entre zero e que determina a certeza de como o modelo tem de ser para identificar um rosto) normalmente, se pretender utilizar limiares diferentes para diferentes modelos. Um limite para um modelo não deve ser partilhado para outro e não será necessariamente a produzir os mesmos resultados.

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu como especificar o modelo de reconhecimento para utilizar com as APIs de serviço de rostos diferentes. Em seguida, seguir um guia de introdução para começar a utilizar a deteção de rostos.

* [Detetar rostos numa imagem](../quickstarts/csharp-detect-sdk.md)

[Se deparam - detetar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Face - Find semelhantes]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[Rosto – Identificar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Se deparam - verificar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[PersonGroup – Criar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[PersonGroup - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup – Preparar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup – Criar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[LargeFaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc
