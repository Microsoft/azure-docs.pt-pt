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
ms.openlocfilehash: 77ab519ae966ab6b3dfc9fd309ce9a5740a5ce0f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503795"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de:

> [!div class="checklist"]
> * [Criar um recurso de fala do Azure](../../../../get-started.md)
> * [Carregar um arquivo de origem em um blob do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
> * [Configurar seu ambiente de desenvolvimento](../../../../quickstarts/setup-platform.md?tabs=dotnet)
> * [Criar um projeto de exemplo vazio](../../../../quickstarts/create-project.md?tabs=dotnet)

## <a name="open-your-project-in-visual-studio"></a>Abra seu projeto no Visual Studio

A primeira etapa é certificar-se de que seu projeto esteja aberto no Visual Studio.

1. Inicie o Visual Studio 2019.
2. Carregue seu projeto e abra `Program.cs`.

## <a name="add-a-reference-to-newtonsoftjson"></a>Adicionar uma referência a NewtonSoftJSon

1. Na Gerenciador de Soluções, clique com o botão direito do mouse no projeto **HelloWorld** e selecione **gerenciar pacotes NuGet** para mostrar o Gerenciador de pacotes NuGet.

1. No canto superior direito, localize a caixa suspensa **origem do pacote** e verifique se **`nuget.org`** está selecionada.

1. No canto superior esquerdo, selecione **procurar**.

1. Na caixa de pesquisa, digite *newtonsoft. JSON* e selecione **Enter**.

1. Nos resultados da pesquisa, selecione o pacote **Newtonsoft. JSON** e, em seguida, selecione **instalar** para instalar a versão estável mais recente.

1. Aceite todos os contratos e licenças para iniciar a instalação.

   Depois que o pacote é instalado, uma confirmação é exibida na janela do **console do Gerenciador de pacotes** .

## <a name="start-with-some-boilerplate-code"></a>Comece com algum código clichê

Vamos adicionar um código que funciona como um esqueleto para nosso projeto.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=6-43,138,277)]
(Você precisará substituir os valores de `YourSubscriptionKey`, `YourServiceRegion`e `YourFileUrl` pelos seus próprios valores.)
## <a name="json-wrappers"></a>Wrappers JSON

Como as solicitações de API REST no formato JSON e também retornam resultados em JSON, poderíamos interagir com elas usando apenas cadeias de caracteres, mas isso não é recomendado.
Para facilitar o gerenciamento das solicitações e das respostas, vamos declarar algumas classes a serem usadas para serializar/desserializar o JSON.

Vá em frente e coloque suas declarações depois de `TranscribeAsync`.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=140-276)]

## <a name="create-and-configure-an-http-client"></a>Criar e configurar um cliente http
A primeira coisa que precisaremos é de um cliente http que tenha uma URL base correta e um conjunto de autenticação.
Insira este código em `TranscribeAsync` [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=46-50)]

## <a name="generate-a-transcription-request"></a>Gerar uma solicitação de transcrição
Em seguida, geraremos a solicitação de transcrição. Adicione este código a `TranscribeAsync` [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=52-57)]

## <a name="send-the-request-and-check-its-status"></a>Enviar a solicitação e verificar seu status
Agora, lançamos a solicitação para o serviço de fala e verificamos o código de resposta inicial. Esse código de resposta simplesmente indicará se o serviço recebeu a solicitação. O serviço retornará uma URL nos cabeçalhos de resposta que é o local onde ele armazenará o status de transcrição.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=59-70)]

## <a name="wait-for-the-transcription-to-complete"></a>Aguarde a conclusão da transcrição
Como o serviço processa a transcrição de forma assíncrona, precisamos Pesquisar seu status a cada frequência. Verificaremos a cada 5 segundos.

Podemos verificar o status recuperando o conteúdo na URL que recebemos quando o postou a solicitação. Quando obtemos o conteúdo de volta, desserializamos-o em uma de nossas classes auxiliares para facilitar a interação com o.

Aqui está o código de sondagem com o status exibição para tudo, exceto uma conclusão bem-sucedida, faremos isso em seguida.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=72-106,121-137)]

## <a name="display-the-transcription-results"></a>Exibir os resultados da transcrição
Depois que o serviço tiver concluído com êxito a transcrição, os resultados serão armazenados em outra URL que pode ser obtida da resposta de status.

Aqui, fazemos uma solicitação para baixar esses resultados em um arquivo temporário antes de ler e desserializá-los.
Depois que os resultados são carregados, podemos imprimi-los no console.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=107-120)]

## <a name="check-your-code"></a>Verifique seu código
Neste ponto, seu código deve ter esta aparência: (adicionamos alguns comentários a esta versão) [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=6-277)]

## <a name="build-and-run-your-app"></a>Compilar e executar seu aplicativo

Agora você está pronto para criar seu aplicativo e testar nosso reconhecimento de fala usando o serviço de fala.

1. **Compilar o código** -na barra de menus do Visual Studio, escolha **Compilar** > **Compilar solução**.
2. **Inicie seu aplicativo** -na barra de menus, escolha **depurar** > **Iniciar Depuração** ou pressione **F5**.
3. **Iniciar reconhecimento** -ele solicitará que você fale uma frase em inglês. Sua fala é enviada ao serviço de fala, transcrita como texto e renderizada no console do.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [footer](./footer.md)]
