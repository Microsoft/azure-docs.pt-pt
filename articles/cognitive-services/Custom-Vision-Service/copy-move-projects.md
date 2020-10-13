---
title: Copiar e mover projetos de visão personalizada
titleSuffix: Azure Cognitive Services
description: Saiba como utilizar as APIs do ExportProject e ImportProject para copiar e mover os seus projetos de Visão Personalizada.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: how-to
ms.date: 09/08/2020
ms.author: pafarley
ms.openlocfilehash: 78ae0fc94e74755b481f80724ca26b34da99122c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91758579"
---
# <a name="copy-and-move-your-custom-vision-projects"></a>Copie e mova os seus projetos de Visão Personalizada

Depois de ter criado e treinado um projeto De Visão Personalizada, talvez queira copiar o seu projeto para outro recurso. Por exemplo, você pode querer mover um projeto de um ambiente de desenvolvimento para o ambiente de produção, ou apoiar um projeto para uma conta em uma região de Azure diferente para uma maior segurança de dados.

As **[APIs do Projeto de Exportação](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)** e **[Doprojecto](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee3)** permitem este cenário, permitindo-lhe copiar projetos de uma conta de Visão Personalizada para outras. Este guia mostra-lhe como utilizar estas APIs REST com cURL. Também pode utilizar um serviço de pedido HTTP como o Carteiro para emitir os pedidos.

## <a name="business-scenarios"></a>Cenários de negócio

Se a sua aplicação ou negócio depender da utilização de um projeto de Visão Personalizada, recomendamos que copie o seu modelo para outra conta de Visão Personalizada noutra região. Em seguida, se ocorrer uma paralisação regional, você pode aceder ao seu projeto na região onde foi copiado.

##  <a name="prerequisites"></a>Pré-requisitos

- Dois recursos da Azure Custom Vision. Se não os tiver, vá ao portal Azure e [crie um novo recurso de Visão Personalizada](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision?azure-portal=true).
- As chaves de treino e URLs de ponto final dos seus recursos de Visão Personalizada. Pode encontrar estes valores no **separador Visão Geral** do recurso no portal Azure.
- Um projeto criado de Visão Personalizada. Consulte [Construir um classificador](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/getting-started-build-a-classifier) para obter instruções sobre como fazê-lo.

## <a name="process-overview"></a>Visão geral do processo

O processo de cópia de um projeto consiste nos seguintes passos:

1. Primeiro, obtém a identificação do projeto na sua conta de origem que pretende copiar.
1. Em seguida, você liga para a **ExportProject** API usando o ID do projeto e a chave de formação da sua conta de origem. Vais ter uma corda temporária.
1. Em seguida, você chama a API **do Projeto de Importação** usando a corda simbólica e a chave de treino da sua conta-alvo. O projeto será então listado na sua conta-alvo.

## <a name="get-the-project-id"></a>Obtenha a ID do projeto

Primeiro ligue para **[a GetProjects](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddead)** para ver uma lista dos seus projetos de Visão Personalizada existentes e seus IDs. Use a chave de treino e o ponto final da sua conta de origem.

```curl
curl -v -X GET "{endpoint}/customvision/v3.3/Training/projects"
-H "Training-key: {training key}"
```

Receberá uma `200\OK` resposta com uma lista de projetos e os seus metadados no corpo. O `"id"` valor é a cadeia para copiar para os próximos passos.

```json
[
  {
    "id": "00000000-0000-0000-0000-000000000000",
    "name": "string",
    "description": "string",
    "settings": {
      "domainId": "00000000-0000-0000-0000-000000000000",
      "classificationType": "Multiclass",
      "targetExportPlatforms": [
        "CoreML"
      ],
      "useNegativeSet": true,
      "detectionParameters": "string",
      "imageProcessingSettings": {
        "augmentationMethods": {}
      }
    },
    "created": "string",
    "lastModified": "string",
    "thumbnailUri": "string",
    "drModeEnabled": true,
    "status": "Succeeded"
  }
]
```

## <a name="export-the-project"></a>Exportar o projeto

Ligue **[para exportProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)** usando o ID do projeto e a sua chave de treinamento de origem e ponto final.

```curl
curl -v -X GET "{endpoint}/customvision/v3.3/Training/projects/{projectId}/export"
-H "Training-key: {training key}"
```

Obterá uma `200/OK` resposta com metadados sobre o projeto exportado e uma cadeia de `"token"` referência. Copie o valor do token.

```json
{
  "iterationCount": 0,
  "imageCount": 0,
  "tagCount": 0,
  "regionCount": 0,
  "estimatedImportTimeInMS": 0,
  "token": "string"
}
```

## <a name="import-the-project"></a>Importar o projeto

Ligue **[para o ImportProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee3)** utilizando a sua chave de treino alvo e ponto final, juntamente com o token de referência. Também pode dar um nome ao seu projeto na sua nova conta.

```curl
curl -v -X POST "{endpoint}/customvision/v3.3/Training/projects/import?token={token}?name={name}"
-H "Training-key: {training key}"
```

Receberá uma `200/OK` resposta com metadados sobre o seu projeto recém-importado.

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "description": "string",
  "settings": {
    "domainId": "00000000-0000-0000-0000-000000000000",
    "classificationType": "Multiclass",
    "targetExportPlatforms": [
      "CoreML"
    ],
    "useNegativeSet": true,
    "detectionParameters": "string",
    "imageProcessingSettings": {
      "augmentationMethods": {}
    }
  },
  "created": "string",
  "lastModified": "string",
  "thumbnailUri": "string",
  "drModeEnabled": true,
  "status": "Succeeded"
}
```

## <a name="next-steps"></a>Passos seguintes

Neste guia, aprendeu a copiar e mover um projeto entre os recursos da Visão Personalizada. Em seguida, explore os docs de referência da API para ver o que mais pode fazer com a Visão Personalizada.
* [Documentação de referência da API REST](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)
