---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/04/2020
ms.author: trbye
ms.openlocfilehash: edf08715290fc68b65f2d910440093be7df37af0
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94424823"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de:

> [!div class="checklist"]
> * [Configurar o seu ambiente de desenvolvimento e criar um projeto vazio](../../../../quickstarts/setup-platform.md?pivots=programming-language-python)
> * [Criar um recurso de discurso azul](../../../../overview.md#try-the-speech-service-for-free)
> * [Faça o upload de um ficheiro de origem para uma bolha de Azure](../../../../../../storage/blobs/storage-quickstart-blobs-portal.md)

## <a name="download-and-install-the-api-client-library"></a>Descarregue e instale a biblioteca de clientes da API

Para executar a amostra é necessário gerar a biblioteca Python para a API REST que é gerada através de [Swagger.](https://swagger.io)

Siga estes passos para a instalação:

1. Aceda a https://editor.swagger.io.
1. Clique **em 'Arquivar'** e, em seguida, clique em **URL de importação.**
1. Insira o URL Swagger, incluindo a região para a sua subscrição de serviço de discurso: `https://<your-region>.cris.ai/docs/v2.0/swagger` .
1. Clique **em Gerar Cliente** e selecione **Python**.
1. Salve a biblioteca do cliente.
1. Extraia as python-client-generated.zip descarregadas algures no seu sistema de ficheiros.
1. Instale o módulo python-cliente extraído no seu ambiente Python utilizando pip: `pip install path/to/package/python-client` .
1. O pacote instalado tem o nome `swagger_client` . Pode verificar se a instalação funcionou utilizando o comando `python -c "import swagger_client"` .

> [!NOTE]
> Devido a um [bug conhecido na autogeração de Swagger,](https://github.com/swagger-api/swagger-codegen/issues/7541)poderá encontrar erros na importação do `swagger_client` pacote.
> Estes podem ser corrigidos eliminando a linha com o conteúdo
> ```py
> from swagger_client.models.model import Model  # noqa: F401,E501
> ```
> do ficheiro `swagger_client/models/model.py` e da linha com o conteúdo
> ```py
> from swagger_client.models.inner_error import InnerError  # noqa: F401,E501
> ```
> a partir do ficheiro `swagger_client/models/inner_error.py` dentro do pacote instalado. A mensagem de erro dir-lhe-á onde estes ficheiros estão localizados para a sua instalação.

## <a name="install-other-dependencies"></a>Instalar outras dependências

A amostra usa a `requests` biblioteca. Pode instalá-lo com o comando

```bash
pip install requests
```

## <a name="start-with-some-boilerplate-code"></a>Comece com um código de placa de caldeira

Vamos adicionar um código que funcione como um esqueleto para o nosso projeto.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=1-2,7-34,115-119)]

[!INCLUDE [placeholder-replacements](../placeholder-replacement.md)]

## <a name="create-and-configure-an-http-client"></a>Criar e configurar um Cliente Http
A primeira coisa que vamos precisar é de um Cliente Http que tenha um URL de base correto e conjunto de autenticação.
Insira este código em `transcribe`[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=37-45)]

## <a name="generate-a-transcription-request"></a>Gerar um pedido de transcrição
Em seguida, geraremos o pedido de transcrição. Adicione este código para `transcribe`[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=52-54)]

## <a name="send-the-request-and-check-its-status"></a>Envie o pedido e verifique o seu estado
Agora publicamos o pedido ao serviço de Discurso e verificamos o código de resposta inicial. Este código de resposta apenas indicará se o serviço recebeu o pedido. O serviço devolverá um Url nos cabeçalhos de resposta que é o local onde irá armazenar o estado de transcrição.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=65-73)]

## <a name="wait-for-the-transcription-to-complete"></a>Aguarde que a transcrição esteja completa.
Uma vez que o serviço processa a transcrição assíncroneamente, precisamos de sondar o seu estatuto de vez em quando. Vamos verificar a cada 5 segundos.

Enumeraremos todas as transcrições que este recurso de serviço de discurso está a processar e procurar o que criamos.

Aqui está o código de sondagens com exibição de estado para tudo, exceto uma conclusão bem sucedida, vamos fazê-lo a seguir.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=75-94,99-112)]

## <a name="display-the-transcription-results"></a>Mostrar os resultados da transcrição
Uma vez que o serviço tenha concluído com sucesso a transcrição, os resultados serão armazenados em outro Url que podemos obter da resposta de estado.

Aqui temos o resultado JSON e exibimo-lo.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=95-98)]

## <a name="check-your-code"></a>Verifique o seu código
Neste momento, o seu código deve ser assim: (Adicionámos alguns comentários a esta versão) [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=1-118)]

## <a name="build-and-run-your-app"></a>Construa e execute a sua app

Agora está pronto para construir a sua app e testar o nosso reconhecimento de voz usando o serviço Speech.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [footer](./footer.md)]