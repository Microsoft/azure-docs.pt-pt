---
title: 'Tutorial: Criar, dar formação e exportar um modelo com a API de REST de visão personalizada'
titlesuffix: Azure Cognitive Services
description: Utilize a API REST para criar, preparar, testar e exportar um modelo de visão personalizada.
services: cognitive-services
author: blackmist
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: larryfr
ms.openlocfilehash: 54b5f7bb16803adf91a0a8ea60cfa68d1e322d07
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351104"
---
# <a name="tutorial-create-train-and-export-a-model-with-rest"></a>Tutorial: Criar, dar formação e exportar um modelo com REST

As informações neste documento demonstram como utilizar um cliente REST para trabalhar com a API REST para preparação do Serviço de Visão Personalizada. Os exemplos mostram como utilizar a API através do utilitário `curl` a partir de um ambiente de bash e `Invoke-WebRequest` a partir do Windows PowerShell.

> [!div class="checklist"]
> * Obter chaves
> * Criar um projeto
> * Criar etiquetas
> * Adicionar imagens
> * Preparar e testar o modelo
> * Exportar o modelo

## <a name="prerequisites"></a>Pré-requisitos

* Um conhecimento básico em REST (Representational State Transfer). Este documento não entra em detalhes sobre coisas como verbos HTTP, JSON ou outras coisas geralmente utilizadas com REST.
* Um bash (Bourne Again Shell) com o utilitário [curl](https://curl.haxx.se) ou o Windows PowerShell 3.0 (ou posterior).
* Uma conta de Visão Personalizada. Para obter mais informações, veja o documento [Criar um classificador](getting-started-build-a-classifier.md).

## <a name="get-keys"></a>Obter chaves

Ao utilizar a API REST, tem de se autenticar com uma chave. Ao realizar operações de gestão ou preparação, utilize a __chave de preparação__. Ao utilizar o modelo para fazer previsões, utilize a __chave de predição__.

Quando efetua um pedido, a chave é enviada como um cabeçalho de pedido.

Para obter as chaves para a sua conta, visite a [página Web da Visão Personalizada](https://customvision.ai) e selecione o __ícone de engrenagem__ no canto superior direito. Na secção __Accounts__ (Contas), copie os valores dos campos __Training Key__ (Chave de Preparação) e __Prediction Key__ (Chave de Predição).

![Imagem da IU de chaves](./media/rest-api-tutorial/training-prediction-keys.png)

> [!IMPORTANT]
> Uma vez que as chaves são utilizadas para autenticar cada pedido, os exemplos neste documento partem do princípio de que os valores de chave estão contidos em variáveis de ambiente. Utilize os seguintes comandos para armazenar as chaves em variáveis de ambiente antes de utilizar outros fragmentos de código neste documento:
>
> ```bash
> read -p "Enter the training key: " TRAININGKEY
> read -p "Enter the prediction key: " PREDICTIONKEY
> ```
>
> ```powershell
> $trainingKey = Read-Host 'Enter the training key'
> $predictionKey = Read-Host 'Enter the prediction key'
> ```

## <a name="create-a-new-project"></a>Criar um novo projeto

Os exemplos seguintes criam um novo projeto chamado `myproject` na sua instância do serviço de Visão Personalizada. Este serviço está predefinido para o domínio `General`:

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects?name=myproject" -H "Training-Key: $TRAININGKEY" --data-ascii ""
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects?name=myproject" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey" }
$resp.Content
```

A resposta ao pedido é semelhante ao documento JSON seguinte:

```json
{
  "id":"45d1b19b-69b6-4a22-8e7e-d1ca37504686",
  "name":"myproject",
  "description":"",
  "settings":{
    "domainId":"ee85a72c-405e-4adc-bb47-ffa8ca0c9f31",
    "useNegativeSet":true,
    "classificationType":"Multilabel",
    "detectionParameters":null
  },
  "created":"2018-08-10T17:39:02.5633333",
  "lastModified":"2018-08-10T17:39:02.5633333",
  "thumbnailUri":null
}
```

> [!TIP]
> A entrada `id` na resposta é o ID do novo projeto. Este é utilizado noutros exemplos mais adiante neste documento.

Para obter mais informações sobre este pedido, veja [CreateProject](https://go.microsoft.com/fwlink/?linkid=865446).

### <a name="specific-domains"></a>Domínios específicos

Para criar um projeto para um domínio específico, pode fornecer o __ID de domínio__ como um parâmetro opcional. Os exemplos seguintes mostram como obter uma lista de domínios disponíveis:

```bash
curl -X GET "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/domains" -H "Training-Key: $TRAININGKEY" --data-ascii ""
```

```powershell
$resp = Invoke-WebRequest -Method 'GET' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/domains" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey" }
$resp.Content
```

A resposta ao pedido é semelhante ao documento JSON seguinte:

```json
[  
  {  
    "id":"ee85a74c-405e-4adc-bb47-ffa8ca0c9f31",
    "name":"General",
    "type":"Classification",
    "exportable":false,
    "enabled":true
  },
  {  
    "id":"c151d5b5-dd07-472a-acc8-15d29dea8518",
    "name":"Food",
    "type":"Classification",
    "exportable":false,
    "enabled":true
  },
  {  
    "id":"ca455789-012d-4b50-9fec-5bb63841c793",
    "name":"Landmarks",
    "type":"Classification",
    "exportable":false,
    "enabled":true
  },
  ...
]
```

Para obter mais informações sobre este pedido, veja [GetDomains](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a827d).

O exemplo seguinte demonstra a criação de um novo projeto que utiliza o domínio __Landmarks__ (Marcos):

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects?name=myproject&domainId=ca455789-012d-4b50-9fec-5bb63841c793" -H "Training-Key: $TRAININGKEY" --data-ascii ""
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects?name=myproject&domainId=ca455789-012d-4b50-9fec-5bb63841c793" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey" }
$resp.Content
```

## <a name="create-tags"></a>Criar etiquetas

Para etiquetar imagens, tem de utilizar um ID de etiqueta. O exemplo seguinte demonstra como criar uma nova etiqueta com o nome `cat` e obter um ID de etiqueta. Substitua `{projectId}` pelo ID do seu projeto. Utilize o parâmetro `name=` para especificar o nome da etiqueta:

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/tags?name=cat" -H "Training-Key: $TRAININGKEY" --data-ascii ""
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/tags?name=cat" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey" }
$resp.Content
```

A resposta ao pedido é semelhante ao documento JSON: 

```json
{"id":"ed6f7ab6-5132-47ad-8649-3ec42ee62d43","name":"cat","description":null,"imageCount":0}
```

Guarde o valor `id`, uma vez que é utilizado para identificar as imagens.

Para obter mais informações sobre este pedido, veja [CreateTag](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a829d).

## <a name="add-images"></a>Adicionar imagens

Os exemplos seguintes demonstram a adição de um ficheiro a partir do URL. Substitua `{projectId}` pelo ID do seu projeto. Substitua `{tagId}` pelo ID da etiqueta da imagem:

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/images/urls" -H "Training-Key: $TRAININGKEY" -H "Content-Type: application/json" --data-ascii '{"images": [{"url": "http://myimages/cat.jpg","tagIds": ["{tagId}"],"regions": [{"tagId": "{tagId}","left": 119.0,"top": 94.0,"width": 240.0,"height": 140.0}]}], "tagIds": ["{tagId}"]}'
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/images/urls" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey"; "Content-Type"="application/json" } `
    -Body '{"images": [{"url": "http://myimages/cat.jpg","tagIds": ["{tagId}"],"regions": [{"tagId": "{tagId}","left": 119.0,"top": 94.0,"width": 240.0,"height": 140.0}]}], "tagIds": ["{tagId}"]}'
$resp.Content
```

A resposta ao pedido é semelhante ao documento JSON seguinte:

```json
{  
  "isBatchSuccessful":true,
  "images":[  
    {  
      "sourceUrl":"http://myimages/cat.jpg",
      "status":"OK",
      "image":{  
        "id":"081adaee-a76b-4d94-a70e-e4fd0935a28f",
        "created":"2018-08-13T13:24:22.0815638",
        "width":640,
        "height":480,
        "imageUri":"https://linktoimage",
        "thumbnailUri":"https://linktothumbnail",
        "tags":[  
          {  
            "tagId":"ed6f7ab6-5132-47ad-8649-3ec42ee62d43",
            "tagName":null,
            "created":"2018-08-13T13:24:22.104936"
          }
        ],
        "regions":[  
          {  
            "regionId":"40f206a1-3f8a-4de7-a6c3-c7b4643117df",
            "tagName":null,
            "created":"2018-08-13T13:24:22.104936",
            "tagId":"ed6f7ab6-5132-47ad-8649-3ec42ee62d43",
            "left":119,
            "top":94,
            "width":240,
            "height":140
          }
        ]
      }
    }
  ]
}
```

Para obter mais informações sobre este pedido, veja [CreateImagesFromUrls](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a8287).

## <a name="train-the-model"></a>Dar formação sobre o modelo

Os exemplos seguintes demonstram como preparar o modelo. Substitua `{projectId}` pelo ID do seu projeto:

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/train" -H "Training-Key: $TRAININGKEY" -H "Content-Type: application/json" --data-ascii ""
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/train" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey"; "Content-Type"="application/json" } 
$resp.Content
```

A resposta ao pedido é semelhante ao documento JSON seguinte:

```json
{
    "id": "23de09d6-42a1-413e-839e-8db6ee6d3496",
    "name": "Iteration 1",
    "isDefault": false,
    "status": "Training",
    "created": "2018-08-10T17:39:02.5766667",
    "lastModified": "2018-08-16T17:15:07.5250661",
    "projectId": "45d1b19b-69b8-4b22-8e7e-d1ca37504686",
    "exportable": false,
    "domainId": null
}
```

Guarde o valor `id`, uma vez que é utilizado para testar e exportar o modelo.

Para obter mais informações, veja [TrainProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a8294).

## <a name="test-the-model"></a>Testar o modelo

Os exemplos seguintes demonstram como efetuar um teste do modelo. Substitua `{projectId}` pelo ID do seu projeto. Substitua `{iterationId}` pelo ID devolvida da preparação do modelo. Substitua `https://linktotestimage` pelo caminho para a imagem de teste.

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/quicktest/url?iterationId={iterationId}" -H "Training-Key: $TRAININGKEY" -H "Content-Type: application/json" --data-ascii '{"url":"https://linktotestimage"}'
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/quicktest/url?iterationId={iterationId}" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey"; "Content-Type"="application/json" } `
    -Body '{"url":"https://linktotestimage"}'
$resp.Content
```

A resposta ao pedido é semelhante ao documento JSON seguinte:

```json
{  
  "id":"369b010b-2a92-4f48-a918-4c1a0af91888",
  "project":"45d1b19b-69b8-4b22-8e7e-d1ca37504686",
  "iteration":"23de09d6-42a1-413e-839e-8db6ee6d3496",
  "created":"2018-08-16T17:39:20.7944508Z",
  "predictions":[  
    {  
      "probability":0.8390652,
      "tagId":"ed6f7ab6-5132-47ad-8649-3ec42ee62d43",
      "tagName":"cat"
    }
  ]
}
```

Para obter mais informações, veja [QuickTestImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a828d).

## <a name="export-the-model"></a>Exportar o modelo

A exportação de um modelo é um processo de dois passos. Primeiro, tem de especificar o formato do modelo e, em seguida, pedir ao URL o modelo exportado.

### <a name="request-a-model-export"></a>Pedir uma exportação de modelo

Os exemplos seguintes demonstram como exportar um modelo `coreml`. Substitua `{projectId}` pelo ID do seu projeto. Substitua `{iterationId}` pelo ID devolvida da preparação do modelo.

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/iterations/{iterationId}/export?platform=coreml" -H "Training-Key: $TRAININGKEY" -H "Content-Type: application/json" --data-ascii ''
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/iterations/{iterationId}/export?platform=coreml" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey"; "Content-Type"="application/json" }
$resp.Content
```

A resposta ao pedido é semelhante ao documento JSON seguinte:

```json
{  
  "platform":"CoreML",
  "status":"Exporting",
  "downloadUri":null,
  "flavor":null
}
```

Para obter mais informações, veja [ExportIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a829b).

### <a name="download-the-exported-model"></a>Transferir o modelo exportado

Os exemplos seguintes demonstram como obter o URL do modelo exportado. Substitua `{projectId}` pelo ID do seu projeto. Substitua `{iterationId}` pelo ID devolvida da preparação do modelo.

```bash
curl -X GET "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/iterations/{iterationId}/export" -H "Training-Key: $TRAININGKEY" -H "Content-Type: application/json" --data-ascii ''
```

```powershell
$resp = Invoke-WebRequest -Method 'GET' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/iterations/{iterationId}/export" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey"; "Content-Type"="application/json" }
$resp.Content
```

A resposta ao pedido é semelhante ao documento JSON seguinte:

```json
[  
  {  
    "platform":"CoreML",
    "status":"Done",
    "downloadUri":"https://linktoexportedmodel",
    "flavor":null
  }
]
```

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações, veja [GetExports](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a829a).
