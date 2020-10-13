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
ms.openlocfilehash: 6bb50e427fa85a170c5ad23a63d67c01e898a17d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91665689"
---
# <a name="swagger-documentation"></a>Documentação do Swagger

O serviço Speech oferece uma especificação Swagger para interagir com um punhado de APIs REST usados para importar dados, criar modelos, testar a precisão do modelo, criar pontos finais personalizados, fazer filas de transcrições de lotes e gerir subscrições. A maioria das operações disponíveis através do portal Discurso Personalizado pode ser concluída programáticamente usando estas APIs.

> [!NOTE]
> Tanto as operações Discurso-a-Texto como as de Texto-A-Fala são suportadas como APIs REST, que por sua vez estão documentadas na especificação swagger.

## <a name="generating-code-from-the-swagger-specification"></a>Código gerador a partir da especificação swagger

A [especificação Swagger](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) tem opções que lhe permitem testar rapidamente vários caminhos. No entanto, às vezes é desejável gerar código para todos os caminhos, criando uma única biblioteca de chamadas em que se pode basear soluções futuras. Vamos dar uma olhada no processo para gerar uma biblioteca Python.

Terá de definir o Swagger na mesma região que a subscrição do serviço Speech. Pode confirmar a sua região no portal Azure sob o seu recurso de serviço Speech. Para obter uma lista completa das regiões apoiadas, consulte [as regiões.](regions.md)

1. Num browser, aceda à especificação swagger para a sua região:  
       `https://<your-region>.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0`
1. Nessa página, clique na **definição de API**e clique em **Swagger**. Copie o URL da página que aparece.
1. Em um novo navegador, vá para https://editor.swagger.io
1. Clique **em Arquivo,** clique em **URL de importação,** cole o URL e clique **em OK**.
1. Clique **em Gerar Cliente** e selecione **python**. A biblioteca de clientes descarrega para o seu computador num `.zip` ficheiro.
1. Extrair tudo do download. Podes usar `tar -xf` para extrair tudo.
1. Instale o módulo extraído no seu ambiente Python:  
       `pip install path/to/package/python-client`
1. O pacote instalado tem o nome `swagger_client` . Verifique se a instalação funcionou:  
       `python -c "import swagger_client"`

Pode utilizar a biblioteca Python que gerou com as amostras de [serviço de fala no GitHub.](https://aka.ms/csspeech/samples)

## <a name="reference-docs"></a>Documentos de referência

* [REST (Swagger): Transcrição e personalização do lote](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)
* [REST API: Discurso-a-texto](rest-speech-to-text.md)
* [REST API: Texto-a-discurso](rest-text-to-speech.md)

## <a name="next-steps"></a>Passos seguintes

* [Amostras de serviço de fala no GitHub.](https://aka.ms/csspeech/samples)
* [Obtenha gratuitamente uma chave de subscrição de serviço de discurso](overview.md#try-the-speech-service-for-free)
