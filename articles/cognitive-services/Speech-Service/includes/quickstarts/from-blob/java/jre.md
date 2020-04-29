---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 01/13/2020
ms.author: trbye
ms.openlocfilehash: 804cc2213379364002a093509f2393e375c3971b
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81401002"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de:

> [!div class="checklist"]
> * [Crie o seu ambiente de desenvolvimento e crie um projeto vazio](../../../../quickstarts/setup-platform.md?tabs=jre&pivots=programmming-language-java)
> * [Criar um recurso azure speech](../../../../get-started.md)
> * [Faça upload de um ficheiro fonte para uma bolha Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)


## <a name="open-your-project-in-eclipse"></a>Abra o seu projeto em Eclipse

O primeiro passo é garantir que tem o seu projeto aberto no Eclipse.

1. Inicie o Eclipse
2. Carregue o seu `Main.java`projeto e abra.

## <a name="add-a-reference-to-gson"></a>Adicione uma referência a Gson
Vamos usar um serializador/desserializador externo da JSON neste arranque rápido. Para Java escolhemos [Gson.](https://github.com/google/gson)

Abra o seu pom.xml e adicione a seguinte referência.

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/pom.xml?range=19-25)]

## <a name="start-with-some-boilerplate-code"></a>Comece com um pouco de código de placa de caldeira

Vamos adicionar um código que funciona como um esqueleto para o nosso projeto.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=1-13,95-105,206-207)]

[!INCLUDE [placeholder-replacements](../placeholder-replacement.md)]

## <a name="json-wrappers"></a>Invólucros JSON

À medida que os pedidos da API rest's tomam em formato JSON e também devolvem resultados em JSON poderíamos interagir com eles usando apenas cordas, mas isso não é recomendado.
Para facilitar a gestão dos pedidos e respostas, declararemos algumas classes para serializar/desserializar o JSON.

Vá em frente e `Main`coloque as suas declarações antes.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=15-93)]

## <a name="create-and-configure-an-http-client"></a>Criar e configurar um Cliente http
A primeira coisa que precisamos é de um Cliente Http que tenha um URL base correto e conjunto de autenticação.
Insira `Main` este código em[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=106-113)]

## <a name="generate-a-transcription-request"></a>Gerar um pedido de transcrição
Em seguida, vamos gerar o pedido de transcrição. Adicione este `Main` código a[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=115-116)]

## <a name="send-the-request-and-check-its-status"></a>Envie o pedido e verifique o seu estado
Agora publicamos o pedido no serviço de Discurso e verificamos o código de resposta inicial. Este código de resposta indicará simplesmente se o serviço recebeu o pedido. O serviço devolverá um Url nos cabeçalhos de resposta que é o local onde armazenará o estado da transcrição.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=118-129)]

## <a name="wait-for-the-transcription-to-complete"></a>Aguarde a transcrição para completar
Uma vez que o serviço processa a transcrição assincronicamente, precisamos de fazer sondagens para o seu estatuto de vez em quando. Vamos verificar a cada 5 segundos.

Podemos verificar o estado recuperando o conteúdo no Url que obtivemos quando o pedido publicou. Quando recuperarmos o conteúdo, desserializámo-lo numa das nossas aulas de ajuda para facilitar a interação.

Aqui está o código de votação com o status display para tudo, exceto uma conclusão bem sucedida, vamos fazê-lo a seguir.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=131-159,192-204)]

## <a name="display-the-transcription-results"></a>Mostrar os resultados da transcrição
Uma vez concluído o serviço com sucesso, os resultados serão armazenados em outro Url que podemos obter da resposta do estado.

Vamos descarregar o conteúdo desse URL, desserializar o JSON e dar a volta aos resultados, imprimindo o texto do ecrã à medida que avançamos.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=6-160-190)]

## <a name="check-your-code"></a>Verifique o seu código
Neste ponto, o seu código deve ser assim: (Adicionámos alguns comentários a esta versão) [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java]

## <a name="build-and-run-your-app"></a>Construa e execute a sua app

Agora está pronto para construir a sua app e testar o nosso reconhecimento de voz usando o serviço de Discurso.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [footer](./footer.md)]
