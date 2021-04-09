---
title: Documentação swagger - Serviço de fala
titleSuffix: Azure Cognitive Services
description: A documentação swagger pode ser usada para gerar automaticamente SDKs para uma série de linguagens de programação. Todas as operações ao nosso serviço são apoiadas pela Swagger
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: reference
ms.date: 02/16/2021
ms.author: alexeyo
ms.openlocfilehash: d4369b66bacbe8cff4fc9712ffcd0cb5a370c439
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "100636273"
---
# <a name="swagger-documentation"></a>Documentação do Swagger

O serviço de fala oferece uma especificação Swagger para interagir com um punhado de APIs REST usados para importar dados, criar modelos, testar a precisão do modelo, criar pontos finais personalizados, fazer filas de transcrições de lotes e gerir subscrições. A maioria das operações disponíveis através [da área de Discurso Personalizado do Estúdio da Fala](https://aka.ms/customspeech) pode ser concluída programáticamente usando estes APIs.

> [!NOTE]
> O serviço de fala tem várias APIs DE REPOUSO para [Discurso-a-Texto](rest-speech-to-text.md) e [Texto-a-voz](rest-text-to-speech.md).  
>
> No entanto, [apenas a API API de expressão-a-texto v3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) e v2.0 estão documentadas na especificação swagger. Consulte os documentos referenciados no parágrafo anterior para obter informações sobre todas as outras APIs dos Serviços de Fala.

## <a name="generating-code-from-the-swagger-specification"></a>Código gerador a partir da especificação swagger

A [especificação Swagger](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) tem opções que lhe permitem testar rapidamente vários caminhos. No entanto, às vezes é desejável gerar código para todos os caminhos, criando uma única biblioteca de chamadas em que se pode basear soluções futuras. Vamos dar uma olhada no processo para gerar uma biblioteca Python.

Terá de definir o Swagger na região do seu recurso speech. Pode confirmar a região na parte **geral** das definições de recursos da Sua Fala no portal Azure. A lista completa das regiões apoiadas está disponível [aqui.](regions.md#speech-to-text)

1. Num browser, aceda à especificação swagger para a sua [região:](regions.md#speech-to-text)  
       `https://<your-region>.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0`
1. Nessa página, clique na **definição de API** e clique em **Swagger**. Copie o URL da página que aparece.
1. Em um novo navegador, vá para [https://editor.swagger.io](https://editor.swagger.io)
1. Clique **em Arquivo,** clique em **URL de importação,** cole o URL e clique **em OK**.
1. Clique **em Gerar Cliente** e selecione **python**. A biblioteca de clientes descarrega para o seu computador num `.zip` ficheiro.
1. Extrair tudo do download. Podes usar `tar -xf` para extrair tudo.
1. Instale o módulo extraído no seu ambiente Python:  
      `pip install path/to/package/python-client`
1. O pacote instalado tem o nome `swagger_client` . Verifique se a instalação funcionou:  
       `python -c "import swagger_client"`

Pode utilizar a biblioteca Python que gerou com as amostras de [serviço de fala no GitHub.](https://aka.ms/csspeech/samples)

## <a name="reference-documents"></a>Documentos de referência

* [Swagger: Discurso-a-texto REST API v3.0](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)
* [API REST de conversão de voz em texto](rest-speech-to-text.md)
* [API REST de conversão de texto em voz](rest-text-to-speech.md)

## <a name="next-steps"></a>Passos seguintes

* [Amostras de serviço de fala no GitHub.](https://aka.ms/csspeech/samples)
* [Obtenha gratuitamente uma chave de subscrição de serviço de discurso](overview.md#try-the-speech-service-for-free)
