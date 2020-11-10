---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 01/13/2020
ms.author: trbye
ms.openlocfilehash: 1b0f0fdbed20b8ab9a48b3156faf176e8ecfc44b
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94424981"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de:

> [!div class="checklist"]
> * [Configurar o seu ambiente de desenvolvimento e criar um projeto vazio](../../../../quickstarts/setup-platform.md?tabs=jre&pivots=programmming-language-java)
> * [Criar um recurso de discurso azul](../../../../overview.md#try-the-speech-service-for-free)
> * [Faça o upload de um ficheiro de origem para uma bolha de Azure](../../../../../../storage/blobs/storage-quickstart-blobs-portal.md)


## <a name="open-your-project-in-eclipse"></a>Abra o seu projeto em Eclipse

O primeiro passo é garantir que o seu projeto está aberto no Eclipse.

1. Inicie o Eclipse
2. Carregue o seu projeto e `Main.java` abra.

## <a name="add-a-reference-to-gson"></a>Adicione uma referência a Gson
Vamos usar um serializador /deserializador JSON externo neste arranque rápido. Para Java escolhemos [Gson.](https://github.com/google/gson)

Abra a pom.xml e adicione a seguinte referência.

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/pom.xml?range=19-25)]

## <a name="start-with-some-boilerplate-code"></a>Comece com um código de placa de caldeira

Vamos adicionar um código que funcione como um esqueleto para o nosso projeto.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=1-13,95-105,206-207)]

[!INCLUDE [placeholder-replacements](../placeholder-replacement.md)]

## <a name="json-wrappers"></a>Invólucros JSON

Como a API do REST aceita pedidos em formato JSON e também retorno resultados em JSON poderíamos interagir com eles usando apenas cordas, mas isso não é recomendado.
De forma a facilitar a gestão dos pedidos e respostas, declararemos algumas aulas para usar para serializar/deserizar o JSON.

Vá em frente e coloque as suas declarações `Main` antes.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=15-93)]

## <a name="create-and-configure-an-http-client"></a>Criar e configurar um Cliente Http
A primeira coisa que vamos precisar é de um Cliente Http que tenha um URL de base correto e conjunto de autenticação.
Insira este código em `Main`[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=106-113)]

## <a name="generate-a-transcription-request"></a>Gerar um pedido de transcrição
Em seguida, geraremos o pedido de transcrição. Adicione este código para `Main`[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=115-116)]

## <a name="send-the-request-and-check-its-status"></a>Envie o pedido e verifique o seu estado
Agora publicamos o pedido ao serviço de Discurso e verificamos o código de resposta inicial. Este código de resposta apenas indicará se o serviço recebeu o pedido. O serviço devolverá um Url nos cabeçalhos de resposta que é o local onde irá armazenar o estado de transcrição.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=118-129)]

## <a name="wait-for-the-transcription-to-complete"></a>Aguarde que a transcrição esteja completa.
Uma vez que o serviço processa a transcrição assíncroneamente, precisamos de sondar o seu estatuto de vez em quando. Vamos verificar a cada 5 segundos.

Podemos verificar o estado recuperando o conteúdo no Url que obtivemos quando o pedido foi publicado. Quando recuperarmos o conteúdo, deserizamo-lo numa das nossas aulas de ajudante para facilitar a interação.

Aqui está o código de sondagens com exibição de estado para tudo, exceto uma conclusão bem sucedida, vamos fazê-lo a seguir.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=131-159,192-204)]

## <a name="display-the-transcription-results"></a>Mostrar os resultados da transcrição
Uma vez que o serviço tenha concluído com sucesso a transcrição, os resultados serão armazenados em outro Url que podemos obter da resposta de estado.

Vamos descarregar o conteúdo desse URL, desseeciar o JSON e dar-nos-emos através dos resultados que imprimem o texto do ecrã à medida que avançamos.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=6-160-190)]

## <a name="check-your-code"></a>Verifique o seu código
Neste momento, o seu código deve ser assim: (Adicionámos alguns comentários a esta versão) [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java]

## <a name="build-and-run-your-app"></a>Construa e execute a sua app

Agora está pronto para construir a sua app e testar o nosso reconhecimento de voz usando o serviço Speech.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [footer](./footer.md)]