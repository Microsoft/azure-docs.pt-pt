---
title: 'Exemplo: Identificar rostos em imagens - Rosto'
titleSuffix: Azure Cognitive Services
description: Este guia demonstra como identificar rostos desconhecidos utilizando objetos Do PersonGroup, que são criados antecipadamente a partir de pessoas conhecidas.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 04/10/2019
ms.author: sbowles
ms.openlocfilehash: 0b1cf99fe6e2aa4d7fcb12c3fb96b10b42c7c0b7
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169918"
---
# <a name="example-identify-faces-in-images"></a>Exemplo: Identificar rostos em imagens

Este guia demonstra como identificar rostos desconhecidos utilizando objetos Do PersonGroup, que são criados antecipadamente a partir de pessoas conhecidas. As amostras são escritas em C# usando a biblioteca de clientes Azure Cognitive Services Face.

## <a name="preparation"></a>Preparação

Esta amostra demonstra:

- Como criar um PersonGroup. Este PersonGroup contém uma lista de pessoas conhecidas.
- Como atribuir rostos a cada pessoa. Estes rostos são usados como base para identificar pessoas. Recomendamos que use vistas frontais claras de rostos. Um exemplo é uma identificação fotográfica. Um bom conjunto de fotos inclui rostos da mesma pessoa em diferentes poses, cores de roupa ou penteados.

Para realizar a demonstração desta amostra, prepare:

- Umas poucas fotografias com o rosto da pessoa. [Descarregue fotos](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data) da amostra para Anna, Bill e Clare.
- Uma série de fotos de teste. As fotos podem ou não conter os rostos de Anna, Bill ou Clare. Estão habituados a testar a identificação. Selecione também algumas imagens da amostra do link anterior.

## <a name="step-1-authorize-the-api-call"></a>Passo 1: Autorizar a chamada da API

Todas as chamadas para a API Face requerem uma chave de subscrição. Esta chave pode ser passada através de um parâmetro de corda de consulta ou especificada no cabeçalho de pedido. Para passar a chave de subscrição através de uma corda de consulta, consulte o URL de pedido para o [Face - Detete](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) como um exemplo:
```
https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes]
&subscription-key=<Subscription key>
```

Como alternativa, especifique a chave de subscrição na chave de **assinatura &lt;http-apim-assinatura:&gt;Chave**de subscrição .
Quando utiliza uma biblioteca de clientes, a chave de subscrição é transmitida através do construtor da classe FaceClient. Por exemplo:
 
```csharp 
private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials("<subscription key>"),
            new System.Net.Http.DelegatingHandler[] { });
```
 
Para obter a chave de subscrição, vá ao Azure Marketplace a partir do portal Azure. Para mais informações, consulte [Subscrições](https://azure.microsoft.com/try/cognitive-services/).

## <a name="step-2-create-the-persongroup"></a>Passo 2: Criar o PersonGroup

Neste passo, um PersonGroup chamado "MyFriends" contém Anna, Bill e Clare. Cada pessoa tem vários rostos registados. Os rostos devem ser detetados a partir das imagens. No final destes passos, tem um PersonGroup semelhante à imagem seguinte:

![Meus Amigos](../Images/group.image.1.jpg)

### <a name="step-21-define-people-for-the-persongroup"></a>Passo 2.1: Definir pessoas para o PersonGroup
Uma pessoa é uma unidade básica de identidade. Uma pessoa pode ter um ou mais rostos conhecidos registados. Um PersonGroup é uma coleção de pessoas. Cada pessoa é definida dentro de um grupo de pessoas em particular. A identificação é feita contra um PersonGroup. A tarefa é criar um PersonGroup, e depois criar as pessoas nele, como Anna, Bill e Clare.

Em primeiro lugar, crie um novo PersonGroup utilizando o [PersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) API. A biblioteca de clientes correspondente API é o método CreatePersonGroupAsync para a classe FaceClient. O ID de grupo especificado para criar o grupo é único para cada subscrição. Também pode obter, atualizar ou eliminar PersonGroups utilizando outras APIs do PersonGroup. 

Depois de definido um grupo, pode definir pessoas dentro dele utilizando a [Pessoa do Grupo De Pessoa - Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API. O método da biblioteca de cliente é o CreatePersonAsync. Pode adicionar um rosto a cada pessoa depois de ser criada.

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
### <a name="step-22-detect-faces-and-register-them-to-the-correct-person"></a><a name="step2-2"></a>Passo 2.2: Detetar rostos e registá-los na pessoa correta
A deteção é feita pelo envio de um pedido "POST" da Web para a API [Rosto – Detetar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) com o ficheiro de imagem no corpo do pedido HTTP. Quando utiliza a biblioteca do cliente, a deteção facial é feita através de um dos Detect.. Métodos de asincronização da classe FaceClient.

Para cada rosto que é detetado, ligue para [persongroup person – Adicione face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) para adicionar à pessoa correta.

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
Se a imagem contiver mais do que um rosto, apenas o maior rosto é adicionado. Pode adicionar outros rostos à pessoa. Passe uma corda no formato de "targetFace = esquerda, superior, largura, altura" para [persongroup person - Adicione](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) o parâmetro de consulta de face API. Também pode utilizar o parâmetro opcional targetFace para o método AddPersonFaceAsync para adicionar outras faces. Cada rosto adicionado à pessoa recebe um único ID de rosto persistido. Pode utilizar este ID em [PessoaDo Group – Eliminar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523e) e Face – [Identificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="step-3-train-the-persongroup"></a>Passo 3: Treinar o PersonGroup

O PersonGroup deve ser treinado antes de uma identificação poder ser efetuada utilizando-o. O PersonGroup deve ser retreinado depois de adicionar ou remover qualquer pessoa ou se editar o rosto registado de uma pessoa. O treino é feito com a API [PersonGroup – Treinar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249). Quando utiliza a biblioteca do cliente, é uma chamada para o método TrainPersonGroupAsync:
 
```csharp 
await faceClient.PersonGroup.TrainAsync(personGroupId);
```
 
O treino é um processo assíncrono. Pode não ser terminado mesmo depois do retorno do método TrainPersonGroupAsync. Talvez precises de consultar o estado do treino. Utilize o [PersonGroup - Obtenha](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247) o Estado de Formação API ou o método GetPersonGroupTrainingStatusAsync da biblioteca do cliente. O seguinte código demonstra uma lógica simples de esperar que a formação do PersonGroup termine:
 
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

Quando o serviço Face realiza identificações, calcula a semelhança de um rosto de teste entre todos os rostos dentro de um grupo. Devolve as pessoas mais comparáveis para o rosto de teste. Este processo é feito através do [Face - Identificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) a API ou o método IdentifyAsync da biblioteca do cliente.

O rosto de teste deve ser detetado utilizando os passos anteriores. Em seguida, o ID facial é passado para a Identificação API como um segundo argumento. Várias identificações faciais podem ser identificadas ao mesmo tempo. O resultado contém todos os resultados identificados. Por padrão, o processo de identificação devolve apenas uma pessoa que corresponde melhor à face do teste. Se preferir, especifique o parâmetro opcional maxNumOfCandidatesReturned para permitir que o processo de identificação devolva mais candidatos.

O seguinte código demonstra o processo de identificação:

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

Depois de terminar os passos, tente identificar diferentes rostos. Veja se os rostos de Anna, Bill ou Clare podem ser corretamente identificados de acordo com as imagens enviadas para deteção facial. Veja os exemplos seguintes:

![Identificar diferentes rostos](../Images/identificationResult.1.jpg )

## <a name="step-5-request-for-large-scale"></a>Passo 5: Pedido de grande escala

Um PersonGroup pode conter até 10.000 pessoas com base na limitação de design anterior.
Para obter mais informações sobre cenários na escala dos milhões, veja [Como utilizar a funcionalidade em grande escala](how-to-use-large-scale.md).

## <a name="summary"></a>Resumo

Neste guia, aprendeu o processo de criação de um PersonGroup e de identificar uma pessoa. As seguintes características foram explicadas e demonstradas:

- Detete rostos utilizando o [Rosto - Detete](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d) a API.
- Criar PersonGroups utilizando o [PersonGroup - Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) API.
- Criar pessoas utilizando a [Pessoa do Grupo De Pessoa - Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API.
- Treine um PersonGroup utilizando o [PersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) API.
- Identifique rostos desconhecidos contra o PersonGroup utilizando o [Face - Identifique](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) a API.

## <a name="related-topics"></a>Tópicos relacionados

- [Conceitos de reconhecimento facial](../concepts/face-recognition.md)
- [Detetar rostos numa imagem](HowtoDetectFacesinImage.md)
- [Adicionar rostos](how-to-add-faces.md)
- [Utilizar a funcionalidade de grande escala](how-to-use-large-scale.md)
