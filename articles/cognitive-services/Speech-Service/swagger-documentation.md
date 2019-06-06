---
title: Documentação - serviços de voz do swagger
titleSuffix: Azure Cognitive Services
description: A documentação de Swagger pode ser utilizada para gerar automaticamente SDKs para várias linguagens de programação. Todas as operações no nosso serviço são suportadas pelo Swagger
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 04/12/2019
ms.author: erhopf
ms.openlocfilehash: 6cf3ab6480900aa763598120e6ff7e308f5044e1
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/06/2019
ms.locfileid: "66743210"
---
# <a name="swagger-documentation"></a>Documentação do Swagger

Os serviços de voz oferecem uma especificação do Swagger para interagir com um punhado de APIs REST, utilizado para importar dados, criar modelos, testar a precisão do modelo, criar pontos finais personalizados, enfileirar transcrições de batch e gerir as subscrições. A maioria das operações disponíveis através do portal de voz personalizada pode ser concluída por meio de programação com essas APIs. 

> [!NOTE]
> Operações de conversão de voz em texto e voz são suportadas disponível como APIs REST, que por sua vez estão documentados na especificação do Swagger.

## <a name="generating-code-from-the-swagger-specification"></a>A geração de código a especificação do Swagger

O [especificação do Swagger](https://cris.ai/swagger/ui/index) tem opções que permitem que teste rapidamente para vários caminhos. No entanto, às vezes, é desejável para gerar o código para todos os caminhos, criação de uma única biblioteca de chamadas pode basear soluções futuras. Vamos dar uma olhada no processo para gerar uma biblioteca de Python.

Terá de definir o Swagger para a mesma região que a sua subscrição do serviço de voz. Pode confirmar a sua região no portal do Azure em seu recurso de serviços de voz. Para obter uma lista completa de regiões suportadas, consulte [regiões](regions.md).

1. Ir para https://editor.swagger.io
2. Clique em **arquivo**, em seguida, clique em **importação**
3. Introduza o URL de swagger, incluindo a região para a sua subscrição de serviços de voz `https://<your-region>.cris.ai/docs/v2.0/swagger`
4. Clique em **gerar cliente** e selecione o Python
5. Guardar a biblioteca de cliente

Pode utilizar a biblioteca de Python que gerou com o [exemplos de serviços de voz no GitHub](https://aka.ms/csspeech/samples).

## <a name="reference-docs"></a>Documentos de referência

* [REST (Swagger): Transcrição de batch e personalização](https://westus.cris.ai/swagger/ui/index)
* [REST API: Speech-to-text](rest-speech-to-text.md)
* [REST API: Text-to-speech](rest-text-to-speech.md)

## <a name="next-steps"></a>Passos Seguintes

* [Exemplos de serviços de voz no GitHub](https://aka.ms/csspeech/samples).
* [Obtenha gratuitamente uma chave de subscrição de serviços de voz](get-started.md)
