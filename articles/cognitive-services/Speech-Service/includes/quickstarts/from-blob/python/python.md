---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/04/2020
ms.author: trbye
ms.openlocfilehash: 3ca50a9bad36e0174dc4ee0059c9d01fcc18a5f1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400898"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de:

> [!div class="checklist"]
> * [Crie o seu ambiente de desenvolvimento e crie um projeto vazio](../../../../quickstarts/setup-platform.md?pivots=programming-language-python)
> * [Criar um recurso azure speech](../../../../get-started.md)
> * [Faça upload de um ficheiro fonte para uma bolha Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)

## <a name="download-and-install-the-api-client-library"></a>Descarregue e instale a biblioteca de clientes da API

Para executar a amostra, você precisa gerar a biblioteca Python para a API REST que é gerada através de [Swagger](https://swagger.io).

Siga estes passos para a instalação:

1. Aceda a https://editor.swagger.io.
1. Clique em **File**e, em seguida, clique em **URL de importação**.
1. Introduza o URL Swagger, incluindo `https://<your-region>.cris.ai/docs/v2.0/swagger`a região para a sua subscrição de serviço de Fala: .
1. Clique em **Gerar Cliente** e selecione **Python**.
1. Salve a biblioteca de clientes.
1. Extraia o python-cliente gerado pelo cliente.zip em algum lugar do seu sistema de ficheiros.
1. Instale o módulo de python-cliente extraído `pip install path/to/package/python-client`no seu ambiente Python utilizando pip: .
1. A embalagem instalada tem `swagger_client`o nome . Pode verificar se a instalação `python -c "import swagger_client"`funcionou utilizando o comando .

> [!NOTE]
> Devido a um [bug conhecido na autogeração Swagger,](https://github.com/swagger-api/swagger-codegen/issues/7541) `swagger_client` pode encontrar erros na importação do pacote.
> Estes podem ser corrigidos apagando a linha com o conteúdo
> ```py
> from swagger_client.models.model import Model  # noqa: F401,E501
> ```
> a partir `swagger_client/models/model.py` do arquivo e da linha com o conteúdo
> ```py
> from swagger_client.models.inner_error import InnerError  # noqa: F401,E501
> ```
> a partir `swagger_client/models/inner_error.py` do ficheiro dentro da embalagem instalada. A mensagem de erro dir-lhe-á onde estes ficheiros estão localizados para a sua instalação.

## <a name="install-other-dependencies"></a>Instalar outras dependências

A amostra `requests` usa a biblioteca. Pode instalá-lo com o comando

```bash
pip install requests
```

## <a name="start-with-some-boilerplate-code"></a>Comece com um pouco de código de placa de caldeira

Vamos adicionar um código que funciona como um esqueleto para o nosso projeto.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=1-2,7-34,115-119)]

[!INCLUDE [placeholder-replacements](../placeholder-replacement.md)]

## <a name="create-and-configure-an-http-client"></a>Criar e configurar um Cliente http
A primeira coisa que precisamos é de um Cliente Http que tenha um URL base correto e conjunto de autenticação.
Insira `transcribe` este código em[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=37-45)]

## <a name="generate-a-transcription-request"></a>Gerar um pedido de transcrição
Em seguida, vamos gerar o pedido de transcrição. Adicione este `transcribe` código a[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=52-54)]

## <a name="send-the-request-and-check-its-status"></a>Envie o pedido e verifique o seu estado
Agora publicamos o pedido no serviço de Discurso e verificamos o código de resposta inicial. Este código de resposta indicará simplesmente se o serviço recebeu o pedido. O serviço devolverá um Url nos cabeçalhos de resposta que é o local onde armazenará o estado da transcrição.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=65-73)]

## <a name="wait-for-the-transcription-to-complete"></a>Aguarde a transcrição para completar
Uma vez que o serviço processa a transcrição assincronicamente, precisamos de fazer sondagens para o seu estatuto de vez em quando. Vamos verificar a cada 5 segundos.

Vamos enumerar todas as transcrições que este recurso do serviço speech está a processar e procurar a que criámos.

Aqui está o código de votação com o status display para tudo, exceto uma conclusão bem sucedida, vamos fazê-lo a seguir.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=75-94,99-112)]

## <a name="display-the-transcription-results"></a>Mostrar os resultados da transcrição
Uma vez concluído o serviço com sucesso, os resultados serão armazenados em outro Url que podemos obter da resposta do estado.

Aqui temos o resultado JSON e exibimo-lo.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=95-98)]

## <a name="check-your-code"></a>Verifique o seu código
Neste ponto, o seu código deve ser assim: (Adicionámos alguns comentários a esta versão)[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=1-118)]

## <a name="build-and-run-your-app"></a>Construa e execute a sua app

Agora está pronto para construir a sua app e testar o nosso reconhecimento de voz usando o serviço de Discurso.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [footer](./footer.md)]
