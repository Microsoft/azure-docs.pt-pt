---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 01/13/2020
ms.author: dapine
ms.openlocfilehash: 731248c88855e2c23d2d7e94a01a9681d30259ce
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75942653"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de:

> [!div class="checklist"]
> * [Configurar seu ambiente de desenvolvimento](../../../../quickstarts/setup-platform.md?tabs=linux&pivots=programmming-language-cpp)
> * [Criar um projeto de exemplo vazio](../../../../quickstarts/create-project.md?pivots=programmming-language-cpp)
> * [Criar um recurso de fala do Azure](../../../../get-started.md)
> * [Carregar um arquivo de origem em um blob do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)

## <a name="open-your-project-in-visual-studio"></a>Abra seu projeto no Visual Studio

A primeira etapa é certificar-se de que seu projeto esteja aberto no Visual Studio.

1. Inicie o Visual Studio 2019.
2. Carregue seu projeto e abra `helloworld.cpp`.

## <a name="add-a-references"></a>Adicionar referências

Para acelerar nosso desenvolvimento de código, vamos usar alguns componentes externos:
* [SDK do CPP REST](https://github.com/microsoft/cpprestsdk) Uma biblioteca de cliente para fazer chamadas REST para um serviço REST.
* [nlohmann/JSON](https://github.com/nlohmann/json) Biblioteca de análise/desserialização de JSON útil.

Ambos podem ser instalados usando [vcpkg](https://github.com/Microsoft/vcpkg/).

```
vcpkg install cpprestsdk cpprestsdk:x64-windows
vcpkg install nlohmann-json
```

## <a name="start-with-some-boilerplate-code"></a>Comece com algum código clichê

Vamos adicionar um código que funciona como um esqueleto para nosso projeto.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=7-32,187-190,300-309)]

[!INCLUDE [placeholder-replacements](../placeholder-replacement.md)]

## <a name="json-wrappers"></a>Wrappers JSON

Como as solicitações de API REST no formato JSON e também retornam resultados em JSON, poderíamos interagir com elas usando apenas cadeias de caracteres, mas isso não é recomendado.
Para facilitar o gerenciamento das solicitações e das respostas, vamos declarar algumas classes a serem usadas para serializar/desserializar o JSON e alguns métodos para auxiliar o nlohmann/JSON.

Vá em frente e coloque suas declarações antes de `recognizeSpeech`.
[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=33-185)]

## <a name="create-and-configure-an-http-client"></a>Criar e configurar um cliente http
A primeira coisa que precisaremos é de um cliente http que tenha uma URL base correta e um conjunto de autenticação.
Inserir este código em `recognizeSpeech`

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=191-197)]

## <a name="generate-a-transcription-request"></a>Gerar uma solicitação de transcrição
Em seguida, geraremos a solicitação de transcrição. Adicione este código a `recognizeSpeech`

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=199-203)]

## <a name="send-the-request-and-check-its-status"></a>Enviar a solicitação e verificar seu status
Agora, lançamos a solicitação para o serviço de fala e verificamos o código de resposta inicial. Esse código de resposta simplesmente indicará se o serviço recebeu a solicitação. O serviço retornará uma URL nos cabeçalhos de resposta que é o local onde ele armazenará o status de transcrição.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=204-216)]

## <a name="wait-for-the-transcription-to-complete"></a>Aguarde a conclusão da transcrição
Como o serviço processa a transcrição de forma assíncrona, precisamos Pesquisar seu status a cada frequência. Verificaremos a cada 5 segundos.

Podemos verificar o status recuperando o conteúdo na URL que recebemos quando o postou a solicitação. Quando obtemos o conteúdo de volta, desserializamos-o em uma de nossas classes auxiliares para facilitar a interação com o.

Aqui está o código de sondagem com o status exibição para tudo, exceto uma conclusão bem-sucedida, faremos isso em seguida.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=222-245,285-299)]

## <a name="display-the-transcription-results"></a>Exibir os resultados da transcrição
Depois que o serviço tiver concluído com êxito a transcrição, os resultados serão armazenados em outra URL que pode ser obtida da resposta de status.

Vamos baixar o conteúdo dessa URL, desserializar o JSON e fazer um loop pelos resultados imprimindo o texto de exibição à medida que vamos.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=246-284)]

## <a name="check-your-code"></a>Verifique seu código
Neste ponto, seu código deve ter a seguinte aparência: (adicionamos alguns comentários a esta versão)

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=7-308)]

## <a name="build-and-run-your-app"></a>Compilar e executar seu aplicativo

Agora você está pronto para criar seu aplicativo e testar nosso reconhecimento de fala usando o serviço de fala.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [footer](./footer.md)]
