---
title: Documentação do Swagger-serviço de fala
titleSuffix: Azure Cognitive Services
description: A documentação do Swagger pode ser usada para gerar SDKs automaticamente para várias linguagens de programação. Todas as operações em nosso serviço têm suporte do Swagger
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: reference
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 232435a424d2461bce4598356a986473cb1d3644
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68552571"
---
# <a name="swagger-documentation"></a>Documentação do Swagger

Os serviços de fala oferecem uma especificação Swagger para interagir com algumas APIs REST usadas para importar dados, criar modelos, testar a precisão do modelo, criar pontos de extremidade personalizados, enfileirar transcrições em lote e gerenciar assinaturas. A maioria das operações disponíveis por meio do portal de Fala Personalizada pode ser concluída programaticamente usando essas APIs.

> [!NOTE]
> As operações de conversão de texto em texto e de Text para fala têm suporte disponíveis como APIs REST, que, por sua vez, estão documentadas na especificação do Swagger.

## <a name="generating-code-from-the-swagger-specification"></a>Gerando código a partir da especificação do Swagger

A [especificação do Swagger](https://cris.ai/swagger/ui/index) tem opções que permitem que você teste rapidamente vários caminhos. No entanto, às vezes é desejável gerar código para todos os caminhos, criando uma única biblioteca de chamadas nas quais você pode basear soluções futuras. Vamos dar uma olhada no processo para gerar uma biblioteca do Python.

Você precisará definir o Swagger para a mesma região que sua assinatura do serviço de fala. Você pode confirmar sua região no portal do Azure em seu recurso de serviços de fala. Para obter uma lista completa das regiões com suporte, consulte [regiões](regions.md).

1. Ir para https://editor.swagger.io
2. Clique em **arquivo**e em **importar**
3. Insira a URL do Swagger, incluindo a região da sua assinatura dos serviços de fala`https://<your-region>.cris.ai/docs/v2.0/swagger`
4. Clique em **gerar cliente** e selecione Python
5. Salvar a biblioteca de cliente

Você pode usar a biblioteca do Python gerada com os [exemplos de serviços de fala no GitHub](https://aka.ms/csspeech/samples).

## <a name="reference-docs"></a>Documentos de referência

* [REST (Swagger): Transcrição e personalização do lote](https://westus.cris.ai/swagger/ui/index)
* [API REST: Conversão de fala em texto](rest-speech-to-text.md)
* [API REST: Conversão de texto em fala](rest-text-to-speech.md)

## <a name="next-steps"></a>Passos Seguintes

* [Exemplos de serviços de fala no GitHub](https://aka.ms/csspeech/samples).
* [Obtenha uma chave de assinatura de serviços de fala gratuitamente](get-started.md)
