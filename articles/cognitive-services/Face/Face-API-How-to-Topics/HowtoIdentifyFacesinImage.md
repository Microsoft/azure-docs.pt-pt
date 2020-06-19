---
title: 'Exemplo: Identificar rostos em imagens - Face'
titleSuffix: Azure Cognitive Services
description: Este guia demonstra como identificar rostos desconhecidos utilizando objetos do PersonGroup, que são criados a partir de pessoas conhecidas com antecedência.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 04/10/2019
ms.author: sbowles
ms.openlocfilehash: 248bae81db1bc8cb69bac4618bd7593658336636
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84986712"
---
# <a name="example-identify-faces-in-images"></a>Exemplo: Identificar rostos em imagens

Este guia demonstra como identificar rostos desconhecidos utilizando objetos do PersonGroup, que são criados a partir de pessoas conhecidas com antecedência. As amostras são escritas em C# utilizando a biblioteca de clientes Azure Cognitive Services Face.

## <a name="preparation"></a>Preparação

Esta amostra demonstra:

- Como criar um PersonGroup. Este PersonGroup contém uma lista de pessoas conhecidas.
- Como atribuir rostos a cada pessoa. Estes rostos são usados como base para identificar pessoas. Recomendamos que utilize vistas frontais claras de rostos. Um exemplo é uma identificação com foto. Um bom conjunto de fotos inclui rostos da mesma pessoa em diferentes poses, cores de roupa ou penteados.

Para efetuar a demonstração desta amostra, prepare:

- Umas poucas fotografias com o rosto da pessoa. [Descarregue fotos](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data) de amostras para Anna, Bill e Clare.
- Uma série de fotos de teste. As fotos podem ou não conter os rostos de Anna, Bill ou Clare. São usados para testar a identificação. Selecione também algumas imagens de amostra do link anterior.

## <a name="step-1-authorize-the-api-call"></a>Passo 1: Autorizar a chamada da API

Todas as chamadas para a API Face requerem uma chave de subscrição. Esta chave pode ser transmitida através de um parâmetro de cadeia de consulta ou especificada no cabeçalho de pedido. Para passar a chave de subscrição através de uma cadeia de consulta, consulte o URL de pedido para o [Rosto - Detetar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) como exemplo:
```
https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes]
&subscription-key=<Subscription key>
```

Em alternativa, especifique a chave de subscrição no teclado de pedido HTTP **ocp-apim-subscrição-chave: &lt; Chave &gt; de subscrição**.
Quando utiliza uma biblioteca de clientes, a chave de subscrição é transmitida através do construtor da classe FaceClient. Por exemplo:
 
```csharp 
private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials("<subscription key>"),
            new System.Net.Http.DelegatingHandler[] { });
```
 
Siga estas instruções para obter uma chave.

1. Criar uma [conta Azure](https://azure.microsoft.com/free/cognitive-services/). Se já tem um, pode saltar para o próximo passo.
2. Crie um [recurso Face](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace) no portal Azure para obter a sua chave. Certifique-se de que seleciona o nível livre (F0) durante a configuração. 
3. Depois de os recursos serem implantados, clique em **Ir ao recurso** para recolher a sua chave. 

## <a name="step-2-create-the-persongroup"></a>Passo 2: Criar o PersonGroup

Neste passo, um PersonGroup chamado "MyFriends" contém Anna, Bill e Clare. Cada pessoa tem vários rostos registados. Os rostos devem ser detetados a partir das imagens. No final destes passos, tem um PersonGroup semelhante à imagem seguinte:

![MyFriends](../Images/group.image.1.jpg)

### <a name="step-21-define-people-for-the-persongroup"></a>Passo 2.1: Definir pessoas para o Grupo de Pessoas
Uma pessoa é uma unidade básica de identidade. Uma pessoa pode ter um ou mais rostos conhecidos registados. Um PersonGroup é uma coleção de pessoas. Cada pessoa é definida dentro de um grupo de pessoas particular. A identificação é feita contra um Grupo de Pessoas. A tarefa é criar um PersonGroup, e depois criar as pessoas nele, como Anna, Bill e Clare.

Em primeiro lugar, crie um novo PersonGroup utilizando o [PersonGroup - Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) API. A API da biblioteca de clientes correspondente é o método CreatePersonGroupAsync para a classe FaceClient. O ID do grupo que é especificado para criar o grupo é único para cada subscrição. Também pode obter, atualizar ou eliminar Grupos pessoais utilizando outras APIs do PersonGroup. 

Depois de definido um grupo, pode definir pessoas dentro dele usando a [Pessoa Grupo - Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API. O método da biblioteca de cliente é o CreatePersonAsync. Pode adicionar um rosto a cada pessoa depois de ser criado.

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
A deteção é feita pelo envio de um pedido "POST" da Web para a API [Rosto – Detetar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) com o ficheiro de imagem no corpo do pedido HTTP. Quando utiliza a biblioteca do cliente, a deteção facial é feita através de um dos Detect.. Métodos assínc da classe FaceClient.

Para cada face detetada, ligue para [persongroup person person person – Adicione Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) para adicioná-lo à pessoa correta.

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
Se a imagem contiver mais de um rosto, apenas é adicionado o maior rosto. Pode adicionar outros rostos à pessoa. Passe uma cadeia no formato de "targetFace = esquerda, superior, largura, altura" para [PersonGroup Person - Adicione](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) o parâmetro de consulta targetFace da Face. Também pode utilizar o parâmetro opcional targetFace para o método AddPersonFaceAsync para adicionar outras faces. Cada rosto adicionado à pessoa recebe um único ID do rosto persistido. Pode utilizar este ID em [PersonGroup Person – Delete Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523e) and Face – [Identifique.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)

## <a name="step-3-train-the-persongroup"></a>Passo 3: Treinar o PersonGroup

O PersonGroup deve ser treinado antes de uma identificação poder ser efetuada através da sua utilização. O PersonGroup deve ser retreinado depois de adicionar ou remover qualquer pessoa ou se editar o rosto registado de uma pessoa. O treino é feito com a API [PersonGroup – Treinar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249). Quando utiliza a biblioteca do cliente, é uma chamada para o método TrainPersonGroupAsync:
 
```csharp 
await faceClient.PersonGroup.TrainAsync(personGroupId);
```
 
O treino é um processo assíncronos. Pode não ser terminado mesmo após o regresso do método TrainPersonGroupAsync. Talvez precises de consultar o estado do treino. Utilize o [método PersonGroup - Obter O Estado de Formação](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247) API ou o método GetPersonGroupTrainingStatusAsync da biblioteca do cliente. O seguinte código demonstra uma lógica simples de esperar que a formação do PersonGroup termine:
 
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

Quando o serviço Face realiza identificações, calcula a semelhança de um rosto de teste entre todos os rostos dentro de um grupo. Devolve as pessoas mais comparáveis para o rosto do teste. Este processo é feito através do [Face - Identificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) a API ou o método IdentifyAsync da biblioteca do cliente.

O rosto do teste deve ser detetado utilizando os passos anteriores. Em seguida, a identificação do rosto é passada para a API de identificação como um segundo argumento. Várias identificações faciais podem ser identificadas de uma só vez. O resultado contém todos os resultados identificados. Por predefinição, o processo de identificação devolve apenas uma pessoa que corresponde melhor ao rosto do teste. Se preferir, especifique o parâmetro opcional maxNumOfCandidatesReturado para permitir que o processo de identificação devolva mais candidatos.

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

Um PersonGroup pode ter até 10.000 pessoas com base na limitação de design anterior.
Para obter mais informações sobre cenários na escala dos milhões, veja [Como utilizar a funcionalidade em grande escala](how-to-use-large-scale.md).

## <a name="summary"></a>Resumo

Neste guia, aprendeu o processo de criar um PersonGroup e identificar uma pessoa. Foram explicadas e demonstradas as seguintes características:

- Detetar rostos utilizando o [Face - Detect API.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d)
- Criar Grupos de Pessoas utilizando o [PersonGroup - Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) API.
- Criar pessoas utilizando a [Pessoa Grupo Pessoa - Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API.
- Treine um PersonGroup utilizando o [PersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) API.
- Identifique rostos desconhecidos contra o PersonGroup utilizando a [API Face - Identifique](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) a API.

## <a name="related-topics"></a>Tópicos relacionados

- [Conceitos de reconhecimento facial](../concepts/face-recognition.md)
- [Detetar rostos numa imagem](HowtoDetectFacesinImage.md)
- [Adicionar rostos](how-to-add-faces.md)
- [Utilizar a funcionalidade de grande escala](how-to-use-large-scale.md)
