---
title: 'Exemplo: Identificar rostos em imagens - Face API'
titleSuffix: Azure Cognitive Services
description: Utilize a API Face para identificar rostos em imagens.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 04/10/2019
ms.author: sbowles
ms.openlocfilehash: c22230545ccbe1ef1b4bfa35a33f0302197463b1
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66124533"
---
# <a name="example-identify-faces-in-images"></a>Exemplo: Identificar rostos em imagens

Este guia demonstra como identificar rostos desconhecidos com objetos de PersonGroup, que são criados de pessoas conhecidas com antecedência. Os exemplos são escritos em C# com a biblioteca de cliente de API de Face de serviços cognitivos do Azure.

## <a name="preparation"></a>Preparação

Este exemplo demonstra:

- Como criar um PersonGroup. Este PersonGroup contém uma lista de pessoas conhecidas.
- Como atribuir rostos a cada pessoa. Estes rostos são utilizados como uma linha de base para identificar as pessoas. Recomendamos que utilize vistas clear frontal de faces. Um exemplo é um ID de fotos. Um bom conjunto de fotos inclui rostos da mesma pessoa poses diferentes, cores de vestuário ou hairstyles.

Para executar a demonstração deste exemplo, prepare:

- Umas poucas fotografias com o rosto da pessoa. [Transferir fotografias de exemplo](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data) para Ana, Bill e Clare.
- Uma série de fotos de teste. As fotos podem ou não podem conter os rostos de Anna, fatura ou Clare. Eles são usados para testar a identificação. Selecione também algumas imagens de exemplo da ligação anterior.

## <a name="step-1-authorize-the-api-call"></a>Passo 1: Autorizar a chamada de API

Todas as chamadas para a API Face requerem uma chave de subscrição. Esta chave pode passar por um parâmetro de cadeia de caracteres de consulta ou ser especificada no cabeçalho do pedido. Para passar a chave de subscrição por meio de uma cadeia de consulta, consulte o URL do pedido para o [enfrentam – detetar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) como exemplo:
```
https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes]
&subscription-key=<Subscription key>
```

Como alternativa, especificar a chave de subscrição no cabeçalho do pedido HTTP **ocp-apim-subscription-key: &lt;Chave de subscrição&gt;**.
Quando utiliza uma biblioteca de cliente, a chave de subscrição é passada por meio do construtor da classe FaceServiceClient. Por exemplo:
 
```CSharp 
faceServiceClient = new FaceServiceClient("<Subscription Key>");
```
 
Para obter a chave de subscrição, vá para o Azure Marketplace no portal do Azure. Para obter mais informações, consulte [subscrições](https://azure.microsoft.com/try/cognitive-services/).

## <a name="step-2-create-the-persongroup"></a>Passo 2: Criar o PersonGroup

Neste passo, um PersonGroup com o nome "MyFriends" contém Ana, Bill e Clare. Cada pessoa tem vários rostos registados. Os rostos devem ser detetados a partir das imagens. No final destes passos, tem um PersonGroup semelhante à imagem seguinte:

![MyFriends](../Images/group.image.1.jpg)

### <a name="step-21-define-people-for-the-persongroup"></a>Passo 2.1: Definir as pessoas para o PersonGroup
Uma pessoa é uma unidade básica de identidade. Uma pessoa pode ter um ou mais rostos conhecidos registados. Um PersonGroup é uma coleção de pessoas. Cada pessoa é definida dentro de um determinado PersonGroup. Identificação é feita em relação a um PersonGroup. A tarefa é criar um PersonGroup e, em seguida, crie as pessoas no mesmo, como a Ana, Bill e Clare.

Primeiro, crie um novo PersonGroup utilizando o [PersonGroup - criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) API. A API da biblioteca de cliente correspondente é o método CreatePersonGroupAsync para a classe FaceServiceClient. O ID de grupo especificado para criar o grupo é exclusivo para cada subscrição. Também pode obter, atualizar ou eliminar PersonGroups com outras APIs de PersonGroup. 

Depois de um grupo é definido, pode definir as pessoas dentro da mesma, utilizando o [PersonGroup pessoa - criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API. O método da biblioteca de cliente é o CreatePersonAsync. Pode adicionar um rosto a cada pessoa depois de ocorrer.

```CSharp 
// Create an empty PersonGroup
string personGroupId = "myfriends";
await faceServiceClient.CreatePersonGroupAsync(personGroupId, "My Friends");
 
// Define Anna
CreatePersonResult friend1 = await faceServiceClient.CreatePersonAsync(
    // Id of the PersonGroup that the person belonged to
    personGroupId,    
    // Name of the person
    "Anna"            
);
 
// Define Bill and Clare in the same way
```
### <a name="step2-2"></a> Passo 2.2: Detetar rostos e registá-los para a pessoa correta
A deteção é feita pelo envio de um pedido "POST" da Web para a API [Rosto – Detetar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) com o ficheiro de imagem no corpo do pedido HTTP. Quando utiliza a biblioteca de cliente, deteção de rostos é feita através do método DetectAsync para a classe FaceServiceClient.

Para cada rosto detetado, chame [PersonGroup pessoa – adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) para adicioná-la à pessoa correta.

O código a seguir demonstra o processo de como detetar um rosto a partir de uma imagem e adicioná-lo a uma pessoa:
```CSharp 
// Directory contains image files of Anna
const string friend1ImageDir = @"D:\Pictures\MyFriends\Anna\";
 
foreach (string imagePath in Directory.GetFiles(friend1ImageDir, "*.jpg"))
{
    using (Stream s = File.OpenRead(imagePath))
    {
        // Detect faces in the image and add to Anna
        await faceServiceClient.AddPersonFaceAsync(
            personGroupId, friend1.PersonId, s);
    }
}
// Do the same for Bill and Clare
``` 
Se a imagem tiver mais de um rosto, é adicionado apenas o mostrador da maior. Pode adicionar outros rostos à pessoa. Passar uma cadeia de caracteres no formato de "targetFace = à esquerda, superior, largura, altura" para [PersonGroup pessoa - adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) parâmetro de consulta de targetFace da API. Também pode utilizar o parâmetro opcional de targetFace para o método AddPersonFaceAsync para adicionar outros rostos. Cada rosto adicionado à pessoa é fornecido um ID exclusivo de rostos persistentes. Pode utilizar este ID no [PersonGroup pessoa – eliminar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523e) e [enfrentam – identificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="step-3-train-the-persongroup"></a>Passo 3: Preparar o PersonGroup

O PersonGroup deve ser treinado para que identificação pode ser possível efetuar ao utilizá-lo. O PersonGroup tem reestruturar os depois de adicionar ou remover qualquer pessoa, ou se editar face registado de uma pessoa. O treino é feito com a API [PersonGroup – Treinar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249). Ao utilizar a biblioteca de cliente, é uma chamada ao método TrainPersonGroupAsync:
 
```CSharp 
await faceServiceClient.TrainPersonGroupAsync(personGroupId);
```
 
Treinamento é um processo assíncrono. Não pode ser concluída, mesmo depois do método TrainPersonGroupAsync retorna. Poderá ter de consultar o estado de treinamento. Utilize o [PersonGroup - obter o estado de treinamento](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247) método API ou GetPersonGroupTrainingStatusAsync da biblioteca de clientes. O código a seguir demonstra uma lógica simples de esperar por PersonGroup formação para concluir:
 
```CSharp 
TrainingStatus trainingStatus = null;
while(true)
{
    trainingStatus = await faceServiceClient.GetPersonGroupTrainingStatusAsync(personGroupId);
 
    if (trainingStatus.Status != Status.Running)
    {
        break;
    }
 
    await Task.Delay(1000);
} 
``` 

## <a name="step-4-identify-a-face-against-a-defined-persongroup"></a>Passo 4: Identificar um rosto em relação a um PersonGroup definido

Quando a API Face executa identificações, calcula a semelhança de um rosto de teste entre todos os rostos dentro de um grupo. Devolve as pessoas mais comparáveis para o rosto de teste. Esse processo é realizado através da [enfrentam – identificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) API ou o método IdentifyAsync da biblioteca de clientes.

A face de teste deve ser detetada através dos passos anteriores. Em seguida, o face ID é passado para a API de identificação como um segundo argumento. Face vários IDs pode ser identificado de uma só vez. O resultado contém todos os resultados identificados. Por predefinição, o processo de identificação devolve apenas uma pessoa que corresponda a face de teste melhor. Se preferir, especifique o parâmetro opcional maxNumOfCandidatesReturned para permitir que o processo de identificação devolver mais candidatos.

O código a seguir demonstra o processo de identificação:

```CSharp 
string testImageFile = @"D:\Pictures\test_img1.jpg";

using (Stream s = File.OpenRead(testImageFile))
{
    var faces = await faceServiceClient.DetectAsync(s);
    var faceIds = faces.Select(face => face.FaceId).ToArray();
 
    var results = await faceServiceClient.IdentifyAsync(personGroupId, faceIds);
    foreach (var identifyResult in results)
    {
        Console.WriteLine("Result of face: {0}", identifyResult.FaceId);
        if (identifyResult.Candidates.Length == 0)
        {
            Console.WriteLine("No one identified");
        }
        else
        {
            // Get top 1 among all candidates returned
            var candidateId = identifyResult.Candidates[0].PersonId;
            var person = await faceServiceClient.GetPersonAsync(personGroupId, candidateId);
            Console.WriteLine("Identified as {0}", person.Name);
        }
    }
}
``` 

Depois de concluir os passos, tente identificar rostos diferentes. Veja se os rostos de Anna, fatura ou Clare podem ser identificados corretamente, de acordo com as imagens carregadas para a deteção de rostos. Veja os exemplos seguintes:

![Identifique rostos diferentes](../Images/identificationResult.1.jpg )

## <a name="step-5-request-for-large-scale"></a>Passo 5: Pedido de dimensionamento de grande escala

Um PersonGroup pode conter até 10 000 pessoas com base na limitação de design anterior.
Para obter mais informações sobre cenários na escala dos milhões, veja [Como utilizar a funcionalidade em grande escala](how-to-use-large-scale.md).

## <a name="summary"></a>Resumo

Neste guia, aprendeu o processo de criação de um PersonGroup e identificar uma pessoa. As seguintes funcionalidades foram explicadas e demonstrei:

- Detetar rostos com o [enfrentam – detetar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d) API.
- Criar PersonGroups utilizando o [PersonGroup - criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) API.
- Criar a pessoas ao utilizar o [PersonGroup pessoa - criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API.
- Treinar um PersonGroup utilizando o [PersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) API.
- Identifique rostos desconhecidos contra o PersonGroup utilizando o [enfrentam – identificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) API.

## <a name="related-topics"></a>Tópicos relacionados

- [Conceitos de reconhecimento de rostos](../concepts/face-recognition.md)
- [Detetar rostos numa imagem](HowtoDetectFacesinImage.md)
- [Adicionar rostos](how-to-add-faces.md)
- [Utilizar a funcionalidade em grande escala](how-to-use-large-scale.md)
