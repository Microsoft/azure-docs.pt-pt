---
title: 'Início rápido: reconhecer a fala armazenada no C# armazenamento de BLOBs,-serviço de fala'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 40b226796b4dfb9aced3c6b00eba1a12bad66894
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73504026"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de:

> [!div class="checklist"]
> * [Criar um recurso de fala do Azure](../../../../get-started.md)
> * [Carregar um arquivo de origem em um blob do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
> * [Configurar seu ambiente de desenvolvimento](../../../../quickstarts/setup-platform.md)
> * [Criar um projeto de exemplo vazio](../../../../quickstarts/create-project.md)

## <a name="download-and-install-the-api-client-library"></a>Baixar e instalar a biblioteca de cliente de API

Para executar o exemplo, você precisa gerar a biblioteca do Python para a API REST que é gerada por meio do [Swagger](https://swagger.io).

Siga estas etapas para a instalação:

1. Aceda a https://editor.swagger.io.
1. Clique em **arquivo**e em **importar URL**.
1. Insira a URL do Swagger incluindo a região para sua assinatura dos serviços de fala: `https://<your-region>.cris.ai/docs/v2.0/swagger`.
1. Clique em **gerar cliente** e selecione **Python**.
1. Salve a biblioteca de cliente.
1. Extraia o Python-Client-generated. zip baixado em algum lugar no sistema de arquivos.
1. Instale o módulo de cliente Python extraído em seu ambiente Python usando Pip: `pip install path/to/package/python-client`.
1. O pacote instalado tem o nome `swagger_client`. Você pode verificar se a instalação funcionou usando o comando `python -c "import swagger_client"`.

> **Observação:** Devido a um [bug conhecido na geração automática do Swagger](https://github.com/swagger-api/swagger-codegen/issues/7541), você pode encontrar erros ao importar o pacote de `swagger_client`.
> Eles podem ser corrigidos com a exclusão da linha com o conteúdo
> ```py
> from swagger_client.models.model import Model  # noqa: F401,E501
> ```
> do arquivo `swagger_client/models/model.py` e a linha com o conteúdo
> ```py
> from swagger_client.models.inner_error import InnerError  # noqa: F401,E501
> ```
> do arquivo `swagger_client/models/inner_error.py` dentro do pacote instalado. A mensagem de erro informará onde esses arquivos estão localizados para a instalação.

## <a name="install-other-dependencies"></a>Instalar outras dependências

O exemplo usa a biblioteca `requests`. Você pode instalá-lo com o comando

```bash
pip install requests
```

## <a name="start-with-some-boilerplate-code"></a>Comece com algum código clichê

Vamos adicionar um código que funciona como um esqueleto para nosso projeto.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=1-2,7-34,115-119)]
(Você precisará substituir os valores de `YourSubscriptionKey`, `YourServiceRegion`e `YourFileUrl` pelos seus próprios valores.)

## <a name="create-and-configure-an-http-client"></a>Criar e configurar um cliente http
A primeira coisa que precisaremos é de um cliente http que tenha uma URL base correta e um conjunto de autenticação.
Insira este código em `transcribe` [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=37-45)]

## <a name="generate-a-transcription-request"></a>Gerar uma solicitação de transcrição
Em seguida, geraremos a solicitação de transcrição. Adicione este código a `transcribe` [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=52-54)]

## <a name="send-the-request-and-check-its-status"></a>Enviar a solicitação e verificar seu status
Agora, lançamos a solicitação para o serviço de fala e verificamos o código de resposta inicial. Esse código de resposta simplesmente indicará se o serviço recebeu a solicitação. O serviço retornará uma URL nos cabeçalhos de resposta que é o local onde ele armazenará o status de transcrição.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=65-73)]

## <a name="wait-for-the-transcription-to-complete"></a>Aguarde a conclusão da transcrição
Como o serviço processa a transcrição de forma assíncrona, precisamos Pesquisar seu status a cada frequência. Verificaremos a cada 5 segundos.

Vamos enumerar todas as transcrições que esse recurso do serviço de fala está processando e procurar o que criamos.

Aqui está o código de sondagem com o status exibição para tudo, exceto uma conclusão bem-sucedida, faremos isso em seguida.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=75-94,99-112)]

## <a name="display-the-transcription-results"></a>Exibir os resultados da transcrição
Depois que o serviço tiver concluído com êxito a transcrição, os resultados serão armazenados em outra URL que pode ser obtida da resposta de status.

Aqui, obtemos o resultado JSON e o exibimos.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=95-98)]

## <a name="check-your-code"></a>Verifique seu código
Neste ponto, seu código deve ter esta aparência: (adicionamos alguns comentários a esta versão) [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=1-118)]

## <a name="build-and-run-your-app"></a>Compilar e executar seu aplicativo

Agora você está pronto para criar seu aplicativo e testar nosso reconhecimento de fala usando o serviço de fala.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [footer](./footer.md)]
