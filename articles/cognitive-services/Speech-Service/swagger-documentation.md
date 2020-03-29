---
title: Documentação swagger - Serviço de fala
titleSuffix: Azure Cognitive Services
description: A documentação Swagger pode ser usada para gerar SDKs de geração automática para uma série de linguagens de programação. Todas as operações ao nosso serviço são apoiadas pela Swagger
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: reference
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: fcc43caf895dadfaf832a47c3254f9b828bcb71a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77430810"
---
# <a name="swagger-documentation"></a>Documentação do Swagger

O serviço Speech oferece uma especificação Swagger para interagir com um punhado de APIs REST usados para importar dados, criar modelos, testar precisão do modelo, criar pontos finais personalizados, fazer filas de transcrições de lotes e gerir subscrições. A maioria das operações disponíveis através do portal Custom Speech podem ser concluídas programáticamente usando estas APIs.

> [!NOTE]
> Tanto as operações discurso-a-texto como as operações de texto-a-fala são suportadas como APIs REST, que por sua vez estão documentadas na especificação Swagger.

## <a name="generating-code-from-the-swagger-specification"></a>Código gerador a partir da especificação Swagger

A [especificação Swagger](https://cris.ai/swagger/ui/index) tem opções que lhe permitem testar rapidamente para vários caminhos. No entanto, às vezes é desejável gerar código para todos os caminhos, criando uma única biblioteca de chamadas em que pode basear soluções futuras. Vamos dar uma olhada no processo para gerar uma biblioteca Python.

Terá de colocar swagger na mesma região que a sua assinatura de serviço speech. Pode confirmar a sua região no portal Azure sob o seu recurso de serviço speech. Para obter uma lista completa de regiões apoiadas, consulte [regiões.](regions.md)

1. Ir para https://editor.swagger.io
2. Clique em **Ficheiro,** em seguida, clique **em Importar**
3. Introduza o URL swagger incluindo a região para a sua subscrição de serviço speech`https://<your-region>.cris.ai/docs/v2.0/swagger`
4. Clique em **Gerar Cliente** e selecione Python
5. Salve a biblioteca de clientes

Você pode usar a biblioteca Python que você gerou com as amostras do [serviço speech no GitHub](https://aka.ms/csspeech/samples).

## <a name="reference-docs"></a>Doutorados de referência

* [REST (Swagger): Transcrição e personalização do lote](https://westus.cris.ai/swagger/ui/index)
* [REST API: Discurso-a-texto](rest-speech-to-text.md)
* [REST API: Texto-a-falar](rest-text-to-speech.md)

## <a name="next-steps"></a>Passos seguintes

* [Amostras de serviço de fala no GitHub.](https://aka.ms/csspeech/samples)
* [Obtenha uma chave de subscrição do serviço Speech gratuitamente](get-started.md)
