---
title: 'Exemplo: identificar faces em imagens-face'
titleSuffix: Azure Cognitive Services
description: Este guia demonstra como identificar rostos desconhecidas usando objetos do Person, que são criados de pessoas conhecidas com antecedência.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 04/10/2019
ms.author: sbowles
ms.openlocfilehash: 0b1cf99fe6e2aa4d7fcb12c3fb96b10b42c7c0b7
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76169918"
---
# <a name="example-identify-faces-in-images"></a>Exemplo: identificar faces em imagens

Este guia demonstra como identificar rostos desconhecidas usando objetos do Person, que são criados de pessoas conhecidas com antecedência. Os exemplos são gravados C# no usando a biblioteca de cliente facial dos serviços cognitivas do Azure.

## <a name="preparation"></a>Preparação

Este exemplo demonstra:

- Como criar um Person. Este Person contém uma lista de pessoas conhecidas.
- Como atribuir faces a cada pessoa. Essas faces são usadas como uma linha de base para identificar pessoas. Recomendamos que você use exibições claras do frontais de rostos. Um exemplo é uma ID de foto. Um bom conjunto de fotos inclui faces da mesma pessoa em diferentes poses, cores de roupas ou Hairstyles.

Para realizar a demonstração deste exemplo, prepare:

- Umas poucas fotografias com o rosto da pessoa. [Baixe fotos de exemplo](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data) para Anna, Bill e Clare.
- Uma série de fotos de teste. As fotos podem ou não conter as faces de Anna, Bill ou Clare. Eles são usados para testar a identificação. Além disso, selecione algumas imagens de exemplo do link anterior.

## <a name="step-1-authorize-the-api-call"></a>Passo 1: Autorizar a chamada da API

Todas as chamadas para a API Face requerem uma chave de subscrição. Essa chave pode ser passada por meio de um parâmetro de cadeia de caracteres de consulta ou especificado no cabeçalho da solicitação. Para passar a chave de assinatura por meio de uma cadeia de caracteres de consulta, consulte a URL de solicitação para a [detecção facial](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) como exemplo:
```
https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes]
&subscription-key=<Subscription key>
```

Como alternativa, especifique a chave de assinatura no cabeçalho de solicitação HTTP **OCP-APIM-Subscription-Key: &lt;chave de assinatura&gt;** .
Quando você usa uma biblioteca de cliente, a chave de assinatura é passada por meio do construtor da classe FaceClient. Por exemplo:
 
```csharp 
private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials("<subscription key>"),
            new System.Net.Http.DelegatingHandler[] { });
```
 
Para obter a chave de assinatura, vá para o Azure Marketplace do portal do Azure. Para obter mais informações, consulte [assinaturas](https://azure.microsoft.com/try/cognitive-services/).

## <a name="step-2-create-the-persongroup"></a>Passo 2: Criar o PersonGroup

Nesta etapa, um nome de usuário chamado "myfriends" contém Anna, Bill e Clare. Cada pessoa tem vários rostos registados. As faces devem ser detectadas nas imagens. No final destes passos, tem um PersonGroup semelhante à imagem seguinte:

![Myfriends](../Images/group.image.1.jpg)

### <a name="step-21-define-people-for-the-persongroup"></a>Etapa 2,1: definir pessoas para o usuário
Uma pessoa é uma unidade básica de identidade. Uma pessoa pode ter um ou mais rostos conhecidos registados. Um Person é uma coleção de pessoas. Cada pessoa é definida dentro de um determinado Person. A identificação é feita em relação a um Person. A tarefa é criar um Person e, em seguida, criar as pessoas nele, como Anna, Bill e Clare.

Primeiro, crie um novo Person usando a API [Person-Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) . A API de biblioteca de cliente correspondente é o método CreatePersonGroupAsync para a classe FaceClient. A ID do grupo que é especificada para criar o grupo é exclusiva para cada assinatura. Você também pode obter, atualizar ou excluir PersonGroups usando outras APIs do Person. 

Depois que um grupo é definido, você pode definir pessoas dentro dele usando a API [Person-Create de Person](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) . O método da biblioteca de cliente é o CreatePersonAsync. Você pode adicionar uma face a cada pessoa após sua criação.

```csharp 
// Create an empty PersonGroup
string personGroupId = "myfriends";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Friends");
 
// Define Anna
CreatePersonResult friend1 = await faceClient.PersonGroupPerson.CreateAsync(
    // Id of the PersonGroup that the person belonged to
    personGroupId,    
    // Name of the person
    "Anna"            
);
 
// Define Bill and Clare in the same way
```
### <a name="step2-2"></a>Etapa 2,2: detectar faces e registrá-las na pessoa correta
A deteção é feita pelo envio de um pedido "POST" da Web para a API [Rosto – Detetar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) com o ficheiro de imagem no corpo do pedido HTTP. Quando você usa a biblioteca de cliente, a detecção facial é feita por meio de uma das Detectações. Métodos assíncronos da classe FaceClient.

Para cada face detectada, ligue para [pessoa de pessoa física – adicione face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) para adicioná-la à pessoa correta.

O código a seguir demonstra o processo de como detetar um rosto a partir de uma imagem e adicioná-lo a uma pessoa:

```csharp 
// Directory contains image files of Anna
const string friend1ImageDir = @"D:\Pictures\MyFriends\Anna\";
 
foreach (string imagePath in Directory.GetFiles(friend1ImageDir, "*.jpg"))
{
    using (Stream s = File.OpenRead(imagePath))
    {
        // Detect faces in the image and add to Anna
        await faceClient.PersonGroupPerson.AddFaceFromStreamAsync(
            personGroupId, friend1.PersonId, s);
    }
}
// Do the same for Bill and Clare
``` 
Se a imagem contiver mais de uma face, apenas a face maior será adicionada. Você pode adicionar outras faces à pessoa. Passe uma cadeia de caracteres no formato "targetFace = esquerda, superior, largura, altura" para [pessoa da pessoa-adicionar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) o parâmetro de consulta targetFace da API facial. Você também pode usar o parâmetro opcional targetFace para o método AddPersonFaceAsync para adicionar outras faces. Cada face adicionada à pessoa recebe uma ID de face persistente exclusiva. Você pode usar essa ID em [pessoa do Person – excluir face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523e) e [face – identificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="step-3-train-the-persongroup"></a>Passo 3: Treinar o PersonGroup

O Person deve ser treinado antes que uma identificação possa ser executada usando-o. O Person deve ser retreinado depois de adicionar ou remover qualquer pessoa ou se você editar a face registrada de uma pessoa. O treino é feito com a API [PersonGroup – Treinar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249). Quando você usa a biblioteca de cliente, é uma chamada para o método TrainPersonGroupAsync:
 
```csharp 
await faceClient.PersonGroup.TrainAsync(personGroupId);
```
 
O treinamento é um processo assíncrono. Ele pode não ser concluído mesmo após o retorno do método TrainPersonGroupAsync. Talvez seja necessário consultar o status de treinamento. Use a API de [status de treinamento](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247) ou o método GetPersonGroupTrainingStatusAsync da biblioteca de cliente Person. O código a seguir demonstra uma lógica simples de aguardar a conclusão do treinamento do Person:
 
```csharp 
TrainingStatus trainingStatus = null;
while(true)
{
    trainingStatus = await faceClient.PersonGroup.GetTrainingStatusAsync(personGroupId);
 
    if (trainingStatus.Status != TrainingStatusType.Running)
    {
        break;
    }
 
    await Task.Delay(1000);
} 
``` 

## <a name="step-4-identify-a-face-against-a-defined-persongroup"></a>Passo 4: Identificar um rosto em relação a um PersonGroup definido

Quando o serviço de face executa identificações, ele computa a similaridade de uma face de teste entre todas as faces em um grupo. Ele retorna as pessoas mais comparáveis para a face de teste. Esse processo é feito por meio da API de [identificação facial](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) ou do método IdentifyAsync da biblioteca do cliente.

A face de teste deve ser detectada usando as etapas anteriores. Em seguida, a ID de face é passada para a API de identificação como um segundo argumento. Várias IDs de face podem ser identificadas de uma vez. O resultado contém todos os resultados identificados. Por padrão, o processo de identificação retorna apenas uma pessoa que corresponde melhor à face do teste. Se preferir, especifique o parâmetro opcional maxNumOfCandidatesReturned para permitir que o processo de identificação retorne mais candidatos.

O código a seguir demonstra o processo de identificação:

```csharp 
string testImageFile = @"D:\Pictures\test_img1.jpg";

using (Stream s = File.OpenRead(testImageFile))
{
    var faces = await faceClient.Face.DetectWithStreamAsync(s);
    var faceIds = faces.Select(face => face.FaceId.Value).ToArray();
 
    var results = await faceClient.Face.IdentifyAsync(faceIds, personGroupId);
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
            var person = await faceClient.PersonGroupPerson.GetAsync(personGroupId, candidateId);
            Console.WriteLine("Identified as {0}", person.Name);
        }
    }
}
``` 

Depois de concluir as etapas, tente identificar faces diferentes. Veja se as faces de Anna, Bill ou Clare podem ser identificadas corretamente de acordo com as imagens carregadas para detecção facial. Veja os exemplos seguintes:

![Identificar faces diferentes](../Images/identificationResult.1.jpg )

## <a name="step-5-request-for-large-scale"></a>Etapa 5: solicitação de grande escala

Um pessoa pode conter até 10.000 pessoas com base na limitação de design anterior.
Para obter mais informações sobre cenários na escala dos milhões, veja [Como utilizar a funcionalidade em grande escala](how-to-use-large-scale.md).

## <a name="summary"></a>Resumo

Neste guia, você aprendeu o processo de criar um Person e identificar uma pessoa. Os seguintes recursos foram explicados e demonstrados:

- Detectar faces usando a API de [detecção facial](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d) .
- Crie PersonGroups usando a API de [criação de pessoa-criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) .
- Crie pessoas usando a API [pessoa-criar Person](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) .
- Treine um Person usando a API [Person-Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) .
- Identificar rostos desconhecidos em relação ao Person usando a API de [identificação facial](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) .

## <a name="related-topics"></a>Tópicos relacionados

- [Conceitos de reconhecimento facial](../concepts/face-recognition.md)
- [Detectar faces em uma imagem](HowtoDetectFacesinImage.md)
- [Adicionar faces](how-to-add-faces.md)
- [Usar o recurso de grande escala](how-to-use-large-scale.md)
