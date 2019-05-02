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
ms.openlocfilehash: 7da146cafaf9af5c91bbbb2a3a23d8a90d49d8cd
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64699331"
---
# <a name="example-identify-faces-in-images"></a>Exemplo: Identificar rostos em imagens

Este guia demonstra como identificar rostos desconhecidos com PersonGroups, que são criados com antecedência a partir de pessoas conhecidas. Os exemplos foram escritos em C# com a biblioteca de cliente da API Face.

## <a name="preparation"></a>Preparação

Neste exemplo, vamos demonstrar o seguinte:

- Como criar um PersonGroup – este PersonGroup contém uma lista de pessoas conhecidas.
- Como atribuir rostos a cada pessoa – estes rostos servem de linha de base para identificar as pessoas. É recomendado que utilize rostos claros frontais, tal como o seu ID de fotografia. Um bom conjunto de fotografias deve conter rostos da mesma pessoa em diferentes poses, cores de roupas ou penteados.

Para executar a demonstração deste exemplo, terá de preparar uma porção de imagens:

- Umas poucas fotografias com o rosto da pessoa. [Transferir fotografias de exemplo](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data) para Ana, Bill e Clare.
- Uma série de fotografias de teste, que podem conter ou não os rostos de Ana, Bernardo e Clara, são utilizadas para testar a identificação. Também pode selecionar algumas imagens de exemplo da ligação anterior.

## <a name="step-1-authorize-the-api-call"></a>Passo 1: Autorizar a chamada de API

Todas as chamadas para a API Face requerem uma chave de subscrição. Esta chave pode passar por um parâmetro de cadeia de consulta ou pode ser especificada no cabeçalho do pedido. Para passar a chave de subscrição através da cadeia de consulta, veja o URL do pedido para [Rosto – Detetar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) como exemplo:
```
https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes]
&subscription-key=<Subscription key>
```

Como alternativa, a chave de subscrição também pode ser especificada no cabeçalho do pedido HTTP: **ocp-apim-subscription-key: &lt;Chave de subscrição&gt;**  quando utilizar uma biblioteca de cliente, a chave de subscrição é passada por meio do construtor da classe FaceServiceClient. Por exemplo:
 
```CSharp 
faceServiceClient = new FaceServiceClient("<Subscription Key>");
```
 
A chave de subscrição pode ser obtida da página do Marketplace no portal do Azure. Veja [Subscrições](https://azure.microsoft.com/try/cognitive-services/).

## <a name="step-2-create-the-persongroup"></a>Passo 2: Criar o PersonGroup

Neste passo, criamos um PersonGroup com o nome "MyFriends" que contém três pessoas: A Anna, Bill e Clare. Cada pessoa tem vários rostos registados. Os rostos têm de ser detetados a partir das imagens. No final destes passos, tem um PersonGroup semelhante à imagem seguinte:

![HowToIdentify1](../Images/group.image.1.jpg)

### <a name="21-define-people-for-the-persongroup"></a>2.1 Definir as pessoas para o PersonGroup
Uma pessoa é uma unidade básica de identidade. Uma pessoa pode ter um ou mais rostos conhecidos registados. No entanto, um PersonGroup é uma coleção de pessoas e cada pessoa está definida dentro de um determinado PersonGroup. A identificação é feita em relação a um PersonGroup. Por isso, a tarefa é criar um PersonGroup e, em seguida, criar as pessoas no mesmo, como a Ana, o Bernardo e a Clara.

Em primeiro lugar, terá de criar um novo PersonGroup. Isto é feito ao utilizar a API [PersonGroup – Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244). A API da biblioteca de cliente correspondente é o método CreatePersonGroupAsync para a classe FaceServiceClient. O ID de grupo especificado para criar o grupo é exclusivo para cada subscrição – também pode obter, atualizar ou eliminar PersonGroups com outras APIs de PersonGroup. Depois de um grupo ter sido definido, as pessoas podem, então, ser definidas dentro do mesmo, com a API [Pessoa do PersonGroup – Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c). O método da biblioteca de cliente é o CreatePersonAsync. Pode adicionar rostos a cada pessoa depois de terem sido criadas.

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
### <a name="step2-2"></a> 2.2 Detetar rostos e registá-los na pessoa correta
A deteção é feita pelo envio de um pedido "POST" da Web para a API [Rosto – Detetar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) com o ficheiro de imagem no corpo do pedido HTTP. Ao utilizar a biblioteca de cliente, a deteção de rostos é executada através do método DetectAsync para a classe FaceServiceClient.

Para cada rosto detetado pode chamar [Pessoa de PersonGroup – Adicionar Rosto](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) para adicioná-lo à pessoa correta.

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
Note que, se a imagem tiver mais de um rosto, será apenas adicionado o rosto maior. Pode adicionar outros rostos à pessoa ao passar uma cadeia de caracteres no formato "targetFace =  esquerda, topo, largura, altura" para o parâmetro de consulta targetFace da API [Pessoa de PersonGroup – Adicionar Rosto](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) ou pode utilizar o parâmetro opcional de targetFace para o método AddPersonFaceAsync para adicionar outros rostos. Cada rosto adicionado à pessoa ficará com um ID exclusivo de rosto persistente, que pode ser utilizado em [Pessoa do PersonGroup – Eliminar Rosto](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523e) e em [Rosto – Identificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="step-3-train-the-persongroup"></a>Passo 3: Preparar o PersonGroup

O PersonGroup deve ser treinado para que ao utilizá-lo se possa fazer uma identificação. Além disso, ele deve ser treinado de novo após a adição ou remoção de qualquer pessoa ou se houve edição do rosto registado de uma pessoa. O treino é feito com a API [PersonGroup – Treinar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249). Ao utilizar a biblioteca de cliente, é simplesmente uma chamada ao método TrainPersonGroupAsync:
 
```CSharp 
await faceServiceClient.TrainPersonGroupAsync(personGroupId);
```
 
O treino é um processo assíncrono. Não pode ser concluído, mesmo depois do método TrainPersonGroupAsync devolver. Poderá ter de consultar o estado de treino pela API [PersonGroup – Obter o Estado do Treino](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247) ou pelo método GetPersonGroupTrainingStatusAsync da biblioteca de cliente. O código a seguir demonstra uma lógica simples de treino PersonGroup à espera para concluir:
 
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

Ao realizar identificações, a API Face pode computar a semelhança de um rosto de teste em relação a todos os rostos dentro de um grupo e devolve a(s) pessoa(s) que melhor se compara com esse rosto de teste. Isto é feito com a API [Rosto – Identificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) ou com o método IdentifyAsync da biblioteca de cliente.

O rosto de teste tem de ser detetado através dos passos anteriores e, então, o ID de rosto é passado para a API de identificação como um segundo argumento. Vários IDs de rosto podem ser identificados ao mesmo tempo e o resultado irá conter todos os resultados de identificação. Por predefinição, a identidade devolve apenas uma pessoa que melhor corresponde ao rosto de teste. Se preferir, poderá especificar o parâmetro opcional maxNumOfCandidatesReturned para permitir que a identificação devolva mais candidatos.

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

Quando tiver concluídos os passos, poderá tentar identificar rostos diferentes e ver se os rostos de Ana, Bernardo ou Clara podem ser corretamente identificados, de acordo com as imagens carregadas para a deteção de rostos. Veja os exemplos seguintes:

![HowToIdentify2](../Images/identificationResult.1.jpg )

## <a name="step-5-request-for-large-scale"></a>Passo 5: Pedir para em grande escala

Tal como é sabido, um PersonGroup pode conter até 10 000 pessoas devido à limitação do design anterior.
Para obter mais informações sobre cenários na escala dos milhões, veja [Como utilizar a funcionalidade em grande escala](how-to-use-large-scale.md).

## <a name="summary"></a>Resumo

Neste guia, aprendeu o processo de criação de um PersonGroup e de identificação de uma pessoa. Segue-se um lembrete rápido das funcionalidades anteriormente explicadas e demonstradas:

- Detetar rostos com a API [Rosto – Detetar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d)
- Criar PersonGroups com a API [PersonGroup – Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)
- Criar pessoas com a API [Pessoa de PersonGroup – Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)
- Treinar um PersonGroup com a API [PersonGroup – Treinar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249)
- Identificar rostos desconhecidos em relação ao PersonGroup com a API [Rosto – Identificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)

## <a name="related-topics"></a>Tópicos Relacionados

- [Conceitos de reconhecimento de rostos](../concepts/face-recognition.md)
- [Como Detetar Rostos na Imagem](HowtoDetectFacesinImage.md)
- [Como Adicionar Rostos](how-to-add-faces.md)
- [Como utilizar a funcionalidade de grande escala](how-to-use-large-scale.md)
