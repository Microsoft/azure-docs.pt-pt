---
title: Documentação swagger - Serviço de fala
titleSuffix: Azure Cognitive Services
description: A documentação swagger pode ser usada para gerar automaticamente SDKs para uma série de linguagens de programação. Todas as operações ao nosso serviço são apoiadas pela Swagger
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: reference
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: e42d9713d1d61bb681c0ee51e46cc52eb0bcc807
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91363193"
---
# <a name="swagger-documentation"></a>Documentação do Swagger

O serviço Speech oferece uma especificação Swagger para interagir com um punhado de APIs REST usados para importar dados, criar modelos, testar a precisão do modelo, criar pontos finais personalizados, fazer filas de transcrições de lotes e gerir subscrições. A maioria das operações disponíveis através do portal Discurso Personalizado pode ser concluída programáticamente usando estas APIs.

> [!NOTE]
> Tanto as operações Discurso-a-Texto como as de Texto-A-Fala são suportadas como APIs REST, que por sua vez estão documentadas na especificação swagger.

## <a name="generating-code-from-the-swagger-specification"></a>Código gerador a partir da especificação swagger

A [especificação Swagger](https://cris.ai/swagger/ui/index) tem opções que lhe permitem testar rapidamente vários caminhos. No entanto, às vezes é desejável gerar código para todos os caminhos, criando uma única biblioteca de chamadas em que se pode basear soluções futuras. Vamos dar uma olhada no processo para gerar uma biblioteca Python.

Terá de definir o Swagger na mesma região que a subscrição do serviço Speech. Pode confirmar a sua região no portal Azure sob o seu recurso de serviço Speech. Para obter uma lista completa das regiões apoiadas, consulte [as regiões.](regions.md)

1. Ir para https://editor.swagger.io
2. Clique **em 'Arquivar'** e, em seguida, clique em **Importar**
3. Insira o URL swagger, incluindo a região para a sua subscrição de serviço de discurso `https://<your-region>.cris.ai/docs/v2.0/swagger`
4. Clique **em Gerar Cliente** e selecione Python
5. Salve a biblioteca do cliente

Pode utilizar a biblioteca Python que gerou com as amostras de [serviço de fala no GitHub.](https://aka.ms/csspeech/samples)

## <a name="reference-docs"></a>Documentos de referência

* [REST (Swagger): Transcrição e personalização do lote](https://westus.cris.ai/swagger/ui/index)
* [REST API: Discurso-a-texto](rest-speech-to-text.md)
* [REST API: Texto-a-discurso](rest-text-to-speech.md)

## <a name="next-steps"></a>Passos seguintes

* [Amostras de serviço de fala no GitHub.](https://aka.ms/csspeech/samples)
* [Obtenha gratuitamente uma chave de subscrição de serviço de discurso](overview.md#try-the-speech-service-for-free)
