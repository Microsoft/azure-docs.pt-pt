---
title: 'Início rápido: reconhecer a fala armazenada no armazenamento de BLOBs, serviço de fala Java'
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
ms.openlocfilehash: d4a436540460240ebd97c39d3406b2886586a6df
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74829128"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de:

> [!div class="checklist"]
> * [Criar um recurso de fala do Azure](../../../../get-started.md)
> * [Carregar um arquivo de origem em um blob do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
> * [Configurar seu ambiente de desenvolvimento](../../../../quickstarts/setup-platform.md?tabs=dotnet)
> * [Criar um projeto de exemplo vazio](../../../../quickstarts/create-project.md?tabs=dotnet)

## <a name="open-your-project-in-eclipse"></a>Abra seu projeto no Eclipse

A primeira etapa é certificar-se de que seu projeto esteja aberto no Eclipse.

1. Inicie o Eclipse
2. Carregue seu projeto e abra `Main.java`.

## <a name="add-a-reference-to-gson"></a>Adicionar uma referência a Gson
Vamos usar um serializador/desserializador JSON externo neste guia de início rápido. Para Java, escolhemos [Gson](https://github.com/google/gson).

Abra o pom. xml e adicione a seguinte referência: [!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/pom.xml?range=19-25)]

## <a name="start-with-some-boilerplate-code"></a>Comece com algum código clichê

Vamos adicionar um código que funciona como um esqueleto para nosso projeto.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=1-13,95-105,206-207)]
(Você precisará substituir os valores de `YourSubscriptionKey`, `YourServiceRegion`e `YourFileUrl` pelos seus próprios valores.)

## <a name="json-wrappers"></a>Wrappers JSON

Como as solicitações de API REST no formato JSON e também retornam resultados em JSON, poderíamos interagir com elas usando apenas cadeias de caracteres, mas isso não é recomendado.
Para facilitar o gerenciamento das solicitações e das respostas, vamos declarar algumas classes a serem usadas para serializar/desserializar o JSON.

Vá em frente e coloque suas declarações antes de `Main`.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=15-93)]

## <a name="create-and-configure-an-http-client"></a>Criar e configurar um cliente http
A primeira coisa que precisaremos é de um cliente http que tenha uma URL base correta e um conjunto de autenticação.
Insira este código em `Main` [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=106-113)]

## <a name="generate-a-transcription-request"></a>Gerar uma solicitação de transcrição
Em seguida, geraremos a solicitação de transcrição. Adicione este código a `Main` [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=115-116)]

## <a name="send-the-request-and-check-its-status"></a>Enviar a solicitação e verificar seu status
Agora, lançamos a solicitação para o serviço de fala e verificamos o código de resposta inicial. Esse código de resposta simplesmente indicará se o serviço recebeu a solicitação. O serviço retornará uma URL nos cabeçalhos de resposta que é o local onde ele armazenará o status de transcrição.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=118-129)]

## <a name="wait-for-the-transcription-to-complete"></a>Aguarde a conclusão da transcrição
Como o serviço processa a transcrição de forma assíncrona, precisamos Pesquisar seu status a cada frequência. Verificaremos a cada 5 segundos.

Podemos verificar o status recuperando o conteúdo na URL que recebemos quando o postou a solicitação. Quando obtemos o conteúdo de volta, desserializamos-o em uma de nossas classes auxiliares para facilitar a interação com o.

Aqui está o código de sondagem com o status exibição para tudo, exceto uma conclusão bem-sucedida, faremos isso em seguida.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=131-159,192-204)]

## <a name="display-the-transcription-results"></a>Exibir os resultados da transcrição
Depois que o serviço tiver concluído com êxito a transcrição, os resultados serão armazenados em outra URL que pode ser obtida da resposta de status.

Vamos baixar o conteúdo dessa URL, desserializar o JSON e fazer um loop pelos resultados imprimindo o texto de exibição à medida que vamos.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=6-160-190)]

## <a name="check-your-code"></a>Verifique seu código
Neste ponto, seu código deve ter a seguinte aparência: (adicionamos alguns comentários a esta versão) [! Code-Java [] (~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java]

## <a name="build-and-run-your-app"></a>Compilar e executar seu aplicativo

Agora você está pronto para criar seu aplicativo e testar nosso reconhecimento de fala usando o serviço de fala.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [footer](./footer.md)]
