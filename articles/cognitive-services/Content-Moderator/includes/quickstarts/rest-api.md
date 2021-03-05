---
title: Moderador de conteúdo REST API quickstart
titleSuffix: Azure Cognitive Services
description: Neste arranque rápido, aprenda a começar com o API moderador de conteúdo Azure. Construa software de filtragem de conteúdos na sua aplicação para cumprir os regulamentos ou manter o ambiente pretendido para os seus utilizadores.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 12/08/2020
ms.author: pafarley
ms.openlocfilehash: 346a30b538af8006eaada13b00c77762b9cdfca8
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102194026"
---
Começa com o Moderador de Conteúdo Azure REST API. 

Content Moderator é um serviço de IA que permite lidar com conteúdos potencialmente ofensivos, arriscados ou indesejáveis. Utilize o serviço de moderação de conteúdo movido a IA para digitalizar texto, imagem e vídeos e aplicar automaticamente as bandeiras de conteúdo. Em seguida, integre a sua app com a ferramenta Review, um ambiente moderador on-line para uma equipa de revisores humanos. Construa software de filtragem de conteúdos na sua aplicação para cumprir os regulamentos ou manter o ambiente pretendido para os seus utilizadores.

Utilize a API do Moderador de Conteúdo PARA:

* Texto moderado
* Imagens moderadas

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* Assim que tiver a subscrição do Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator"  title=" crie um recurso De Moderador de Conteúdo crie um recurso De Moderador de Conteúdo no portal "  target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Azure para obter a sua chave e ponto final. Aguarde que seja implantado e clique no botão Go para o botão **de recursos.**
    * Necessitará da chave e ponto final do recurso que criar para ligar a sua aplicação ao Moderador de Conteúdo. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
    * Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.
* [Versão PowerShell 6.0+](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-7.1)ou uma aplicação de linha de comando semelhante.


## <a name="moderate-text"></a>Texto moderado

Utilizará um comando como o seguinte para ligar para a API do Moderador de Conteúdo para analisar um conjunto de textos e imprimir os resultados para a consola.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/content-moderator/quickstart.sh" ID="textmod":::

Copie o comando a um editor de texto e faça as seguintes alterações:

1. Atribua `Ocp-Apim-Subscription-Key` à sua chave de subscrição Face válida.
1. Altere a primeira parte do URL de consulta para corresponder ao ponto final que corresponde à sua chave de subscrição.
   [!INCLUDE [subdomains-note](../../../../../includes/cognitive-services-custom-subdomains-note.md)]
1. Opcionalmente altere o corpo do pedido para qualquer cadeia de texto que queira analisar.

Depois de fazer as alterações, abra um pedido de comando e introduza o novo comando. 

### <a name="examine-the-results"></a>Examinar os resultados

Deve ver os resultados da moderação de texto apresentados como dados JSON na janela da consola. Por exemplo:

```json
{
  "OriginalText": "Is this a crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255,\n1 Microsoft Way, Redmond, WA 98052\n",
  "NormalizedText": "Is this a crap email abide@ abed. com, phone: 6657789887, IP: 255. 255. 255. 255, \n1 Microsoft Way, Redmond, WA 98052",
  "AutoCorrectedText": "Is this a crap email abide@ abed. com, phone: 6657789887, IP: 255. 255. 255. 255, \n1 Microsoft Way, Redmond, WA 98052",
  "Misrepresentation": null,
  "PII": {
    "Email": [
      {
        "Detected": "abcdef@abcd.com",
        "SubType": "Regular",
        "Text": "abcdef@abcd.com",
        "Index": 21
      }
    ],
    "IPA": [
      {
        "SubType": "IPV4",
        "Text": "255.255.255.255",
        "Index": 61
      }
    ],
    "Phone": [
      {
        "CountryCode": "US",
        "Text": "6657789887",
        "Index": 45
      }
    ],
    "Address": [
      {
        "Text": "1 Microsoft Way, Redmond, WA 98052",
        "Index": 78
      }
    ]
  },
 "Classification": {
    "Category1": 
    {
      "Score": 0.5
    },
    "Category2": 
    {
      "Score": 0.6
    },
    "Category3": 
    {
      "Score": 0.5
    },
    "ReviewRecommended": true
  },
  "Language": "eng",
  "Terms": [
    {
      "Index": 10,
      "OriginalIndex": 10,
      "ListId": 0,
      "Term": "crap"
    }
  ],
  "Status": {
    "Code": 3000,
    "Description": "OK",
    "Exception": null
  },
  "TrackingId": "1717c837-cfb5-4fc0-9adc-24859bfd7fac"
}
```

Para obter mais informações sobre os atributos de texto para os quais o Moderador de Conteúdo é exibido, consulte o guia [de conceitos de moderação](../../text-moderation-api.md) de texto.

## <a name="moderate-images"></a>Imagens moderadas

Utilizará um comando como o seguinte para ligar para a API do Moderador de Conteúdo para moderar uma imagem remota e imprimir os resultados para a consola.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/content-moderator/quickstart.sh" ID="imagemod":::

Copie o comando a um editor de texto e faça as seguintes alterações:

1. Atribua `Ocp-Apim-Subscription-Key` à sua chave de subscrição Face válida.
1. Altere a primeira parte do URL de consulta para corresponder ao ponto final que corresponde à sua chave de subscrição.
1. Altere opcionalmente o `"Value"` URL no corpo de pedido para qualquer imagem remota que queira moderar.

> [!TIP]
> Também pode moderar as imagens locais, passando os seus dados byte para o corpo de pedido. Consulte a [documentação de referência](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c) para aprender a fazê-lo.

Depois de fazer as alterações, abra um pedido de comando e introduza o novo comando. 

### <a name="examine-the-results"></a>Examinar os resultados

Deve ver os resultados da moderação de imagem apresentados como dados JSON na janela da consola. 

```json
{
  "AdultClassificationScore": x.xxx,
  "IsImageAdultClassified": <Bool>,
  "RacyClassificationScore": x.xxx,
  "IsImageRacyClassified": <Bool>,
  "AdvancedInfo": [],
  "Result": false,
  "Status": {
    "Code": 3000,
    "Description": "OK",
    "Exception": null
  },
  "TrackingId": "<Request Tracking Id>"
}
```

Para obter mais informações sobre os atributos de imagem para os quais o Moderador de Conteúdo é exibido, consulte o guia [de conceitos de moderação de imagem.](../../image-moderation-api.md)

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretender limpar e remover uma subscrição dos Serviços Cognitivos, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos que lhe sejam associados.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a usar o Moderador de Conteúdo REST API para fazer tarefas de moderação. Em seguida, saiba mais sobre a moderação de imagens ou outros meios de comunicação, lendo um guia conceptual.

* [Conceitos de moderação de imagem](../../image-moderation-api.md)
* [Conceitos de moderação de texto](../../text-moderation-api.md)
* [O que é o Content Moderator do Azure?](../../overview.md)