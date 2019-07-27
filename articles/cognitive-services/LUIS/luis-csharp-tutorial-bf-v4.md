---
title: Bot C# de reconhecimento vocal v4
titleSuffix: Azure Cognitive Services
description: Com o C#, crie um chatbot integrado com compreensão de idiomas (LUIS). O bot é criado com o bot Framework versão 4 e o serviço de bot do aplicativo Web do Azure.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 06/24/2019
ms.author: diberry
ms.openlocfilehash: 210724e8a8b9b585a3e308b8e321d809e4e897a1
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560654"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-c"></a>Tutorial: Usar um bot de aplicativo Web habilitado com Reconhecimento vocal noC#

Use C# para criar um bot de chat integrado ao Luis (reconhecimento de linguagem). O bot é criado com o recurso de [bot do aplicativo Web](https://docs.microsoft.com/azure/bot-service/) do Azure e a versão v4 da [estrutura de bot](https://github.com/Microsoft/botbuilder-dotnet) .

**Neste tutorial, vai aprender a:**

> [!div class="checklist"]
> * Criar um bot de aplicação Web. Este processo cria uma aplicação LUIS nova.
> * Baixar o projeto de bot criado pelo serviço de bot da Web
> * Iniciar o bot e o emulador localmente no seu computador
> * Ver os resultados de expressão no bot

## <a name="prerequisites"></a>Pré-requisitos

* [Emulador de bot](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)


## <a name="create-a-web-app-bot-resource"></a>Criar um recurso de bot de aplicativo Web

1. No [portal do Azure](https://portal.azure.com), selecione **Criar novo recurso**.

1. Na caixa de pesquisa, procure e selecione **Bot de Aplicação Web**. Selecione **Criar**.

1. Em **Bot Service** (Serviço de Bot), indique as informações necessárias:

    |Definição|Objetivo|Definição sugerida|
    |--|--|--|
    |Nome do bot|Nome do recurso|`luis-csharp-bot-` + `<your-name>`, por exemplo, `luis-csharp-bot-johnsmith`|
    |Subscrição|Subscrição na qual vai criar o bot.|A sua subscrição principal.
    |Grupo de recursos|Grupo lógico de recursos do Azure|Crie um grupo novo para armazenar todos os recursos utilizados com este bot e dê ao grupo o nome `luis-csharp-bot-resource-group`.|
    |Localização|Região do Azure - não tem de ser a mesma da região de criação ou publicação do LUIS.|`westus`|
    |Escalão de preço|Utilizado para limites de pedidos de serviço e faturação.|`F0` é o escalão gratuito.
    |Nome da aplicação|O nome é utilizado como o subdomínio quando o bot é implementado na cloud (por exemplo, humanresourcesbot.azurewebsites.net).|`luis-csharp-bot-` + `<your-name>`, por exemplo, `luis-csharp-bot-johnsmith`|
    |Modelo de bot|Definições do Bot Framework - veja a tabela seguinte|
    |Localização da Aplicação LUIS|Tem de ser a mesma da região de recursos do LUIS|`westus`|
    |Plano do serviço de aplicações/localização|Não altere o valor padrão fornecido.|
    |Application Insights|Não altere o valor padrão fornecido.|
    |ID e senha do aplicativo Microsoft|Não altere o valor padrão fornecido.|

1. No **modelo de bot**, selecione o seguinte e, em seguida, escolha o botão **selecionar** nessas configurações:

    |Definição|Objetivo|Seleção|
    |--|--|--|
    |Versão do SDK|Versão do Bot Framework|**SDK v4**|
    |Idioma do SDK|Linguagem de programação do bot|**C#**|
    |Bot|Tipo de bot|**Bot básico**|
    
1. Selecione **Criar**. Esta ação cria e implementa o serviço de bot no Azure. Parte deste processo cria uma aplicação LUIS com o nome `luis-csharp-bot-XXXX`. Esse nome se baseia no nome do aplicativo de serviço de bot/Azure.

    [![Criar bot de aplicativo Web](./media/bfv4-csharp/create-web-app-service.png)](./media/bfv4-csharp/create-web-app-service.png#lightbox)

    Aguarde até que o serviço de bot seja criado antes de continuar.

## <a name="the-bot-has-a-language-understanding-model"></a>O bot tem um modelo de Reconhecimento vocal

O processo de criação do serviço bot também cria um novo aplicativo LUIS com tentativas e declarações de exemplo. O bot fornece o mapeamento das intenções à nova aplicação LUIS para as intenções seguintes: 

|Intenções do LUIS de bot básico|expressão de exemplo|
|--|--|
|Vôo do livro|`Travel to Paris`|
|Cancelar|`bye`|
|Nenhum|Tudo o que estiver fora do domínio da aplicação.|

## <a name="test-the-bot-in-web-chat"></a>Testar o bot em Web Chat

1. Ainda na portal do Azure para o novo bot, selecione **testar no Web Chat**. 
1. Na caixa de texto **Digite sua mensagem** , insira o `hello`texto. O bot responde com informações sobre o bot Framework, bem como consultas de exemplo para o modelo LUIS específico, como a reserva de um vôo para Paris. 

    ![Captura de tela de portal do Azure, insira o texto ' Olá '.](./media/bfv4-csharp/ask-bot-question-in-portal-test-in-web-chat.png)

    Você pode usar a funcionalidade de teste para testar rapidamente o bot. Para um teste mais completo, incluindo depuração, baixe o código do bot e use o Visual Studio. 

## <a name="download-the-web-app-bot-source-code"></a>Baixar o código-fonte do bot do aplicativo Web
Para poder desenvolver o código de bot de aplicação Web, transfira o código e utilize-o no seu computador local. 

1. No portal do Azure, selecione **Compilar**, na secção **Gestão de bot**. 

1. Selecione **Transferir o código-fonte de Bot**. 

    [![Baixar o código-fonte do bot do aplicativo Web para bot básico](../../../includes/media/cognitive-services-luis/bfv4/download-code.png)](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

1. Quando a caixa de diálogo pop-up perguntar **inclui configurações de aplicativo no arquivo zip baixado?** , selecione **Sim**.

1. Quando o código de origem estiver zipado, é disponibilizada uma ligação numa mensagem para o transferir. Selecione a ligação. 

1. Guarde o ficheiro zip no computador local e extraia os ficheiros. Abra o projeto com o Visual Studio. 

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>Revisar o código para enviar expressão para LUIS e obter resposta

1. Abra o arquivo **LuisHelper.cs** . É aqui que a expressão do utilizador introduzida no bot é enviada para o LUIS. A resposta de LUIS é retornada do método como um objeto **BookDetails** . Ao criar seu próprio bot, você também deve criar seu próprio objeto para retornar os detalhes de LUIS. 


    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    using System;
    using System.Linq;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.AI.Luis;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Logging;
    
    namespace Microsoft.BotBuilderSamples
    {
        public static class LuisHelper
        {
            public static async Task<BookingDetails> ExecuteLuisQuery(IConfiguration configuration, ILogger logger, ITurnContext turnContext, CancellationToken cancellationToken)
            {
                var bookingDetails = new BookingDetails();
    
                try
                {
                    // Create the LUIS settings from configuration.
                    var luisApplication = new LuisApplication(
                        configuration["LuisAppId"],
                        configuration["LuisAPIKey"],
                        "https://" + configuration["LuisAPIHostName"]
                    );
    
                    var recognizer = new LuisRecognizer(luisApplication);
    
                    // The actual call to LUIS
                    var recognizerResult = await recognizer.RecognizeAsync(turnContext, cancellationToken);
    
                    var (intent, score) = recognizerResult.GetTopScoringIntent();
                    if (intent == "Book_flight")
                    {
                        // We need to get the result from the LUIS JSON which at every level returns an array.
                        bookingDetails.Destination = recognizerResult.Entities["To"]?.FirstOrDefault()?["Airport"]?.FirstOrDefault()?.FirstOrDefault()?.ToString();
                        bookingDetails.Origin = recognizerResult.Entities["From"]?.FirstOrDefault()?["Airport"]?.FirstOrDefault()?.FirstOrDefault()?.ToString();
    
                        // This value will be a TIMEX. And we are only interested in a Date so grab the first result and drop the Time part.
                        // TIMEX is a format that represents DateTime expressions that include some ambiguity. e.g. missing a Year.
                        bookingDetails.TravelDate = recognizerResult.Entities["datetime"]?.FirstOrDefault()?["timex"]?.FirstOrDefault()?.ToString().Split('T')[0];
                    }
                }
                catch (Exception e)
                {
                    logger.LogWarning($"LUIS Exception: {e.Message} Check your LUIS configuration.");
                }
    
                return bookingDetails;
            }
        }
    }
    ```

1. Abra **BookingDetails.cs** para exibir como o objeto abstrai as informações de Luis. 

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    namespace Microsoft.BotBuilderSamples
    {
        public class BookingDetails
        {
            public string Destination { get; set; }
    
            public string Origin { get; set; }
    
            public string TravelDate { get; set; }
        }
    }
    ```

1. Abrir **caixas de diálogo-> BookingDialog.cs** para entender como o objeto BookingDetails é usado para gerenciar o fluxo de conversa. Os detalhes da viagem são solicitados em etapas. em seguida, toda a reserva é confirmada e, finalmente, repetida para o usuário. 

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.Dialogs;
    using Microsoft.Recognizers.Text.DataTypes.TimexExpression;
    
    namespace Microsoft.BotBuilderSamples.Dialogs
    {
        public class BookingDialog : CancelAndHelpDialog
        {
            public BookingDialog()
                : base(nameof(BookingDialog))
            {
                AddDialog(new TextPrompt(nameof(TextPrompt)));
                AddDialog(new ConfirmPrompt(nameof(ConfirmPrompt)));
                AddDialog(new DateResolverDialog());
                AddDialog(new WaterfallDialog(nameof(WaterfallDialog), new WaterfallStep[]
                {
                    DestinationStepAsync,
                    OriginStepAsync,
                    TravelDateStepAsync,
                    ConfirmStepAsync,
                    FinalStepAsync,
                }));
    
                // The initial child Dialog to run.
                InitialDialogId = nameof(WaterfallDialog);
            }
    
            private async Task<DialogTurnResult> DestinationStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                var bookingDetails = (BookingDetails)stepContext.Options;
    
                if (bookingDetails.Destination == null)
                {
                    return await stepContext.PromptAsync(nameof(TextPrompt), new PromptOptions { Prompt = MessageFactory.Text("Where would you like to travel to?") }, cancellationToken);
                }
                else
                {
                    return await stepContext.NextAsync(bookingDetails.Destination, cancellationToken);
                }
            }
    
            private async Task<DialogTurnResult> OriginStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                var bookingDetails = (BookingDetails)stepContext.Options;
    
                bookingDetails.Destination = (string)stepContext.Result;
    
                if (bookingDetails.Origin == null)
                {
                    return await stepContext.PromptAsync(nameof(TextPrompt), new PromptOptions { Prompt = MessageFactory.Text("Where are you traveling from?") }, cancellationToken);
                }
                else
                {
                    return await stepContext.NextAsync(bookingDetails.Origin, cancellationToken);
                }
            }
            private async Task<DialogTurnResult> TravelDateStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                var bookingDetails = (BookingDetails)stepContext.Options;
    
                bookingDetails.Origin = (string)stepContext.Result;
    
                if (bookingDetails.TravelDate == null || IsAmbiguous(bookingDetails.TravelDate))
                {
                    return await stepContext.BeginDialogAsync(nameof(DateResolverDialog), bookingDetails.TravelDate, cancellationToken);
                }
                else
                {
                    return await stepContext.NextAsync(bookingDetails.TravelDate, cancellationToken);
                }
            }
    
            private async Task<DialogTurnResult> ConfirmStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                var bookingDetails = (BookingDetails)stepContext.Options;
    
                bookingDetails.TravelDate = (string)stepContext.Result;
    
                var msg = $"Please confirm, I have you traveling to: {bookingDetails.Destination} from: {bookingDetails.Origin} on: {bookingDetails.TravelDate}";
    
                return await stepContext.PromptAsync(nameof(ConfirmPrompt), new PromptOptions { Prompt = MessageFactory.Text(msg) }, cancellationToken);
            }
    
            private async Task<DialogTurnResult> FinalStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                if ((bool)stepContext.Result)
                {
                    var bookingDetails = (BookingDetails)stepContext.Options;
    
                    return await stepContext.EndDialogAsync(bookingDetails, cancellationToken);
                }
                else
                {
                    return await stepContext.EndDialogAsync(null, cancellationToken);
                }
            }
    
            private static bool IsAmbiguous(string timex)
            {
                var timexProperty = new TimexProperty(timex);
                return !timexProperty.Types.Contains(Constants.TimexTypes.Definite);
            }
        }
    }
    ```


## <a name="start-the-bot-code-in-visual-studio"></a>Iniciar o código de bot no Visual Studio

No Visual Studio, inicie o bot. É aberta uma janela de browser com o site do bot de aplicação Web em `http://localhost:3978/`. Um home page é exibido com informações sobre o bot.

![Um home page é exibido com informações sobre o bot.](./media/bfv4-csharp/running-bot-web-home-page-success.png)

## <a name="use-the-bot-emulator-to-test-the-bot"></a>Usar o emulador de bot para testar o bot

1. Inicie o emulador de bot e selecione **abrir bot**.
1. Na caixa de diálogo pop-up **abrir um bot** , insira a URL do bot, `http://localhost:3978/api/messages`como. A `/api/messages` rota é o endereço Web para o bot.
1. Insira a **ID do aplicativo** da Microsoft e a **senha do aplicativo Microsoft**, encontradas no arquivo **appSettings. JSON** na raiz do código do bot que você baixou.

    Opcionalmente, você pode criar uma nova configuração de bot e copiar `appId` e `appPassword` do arquivo **appSettings. JSON** no projeto do Visual Studio para o bot. O nome do arquivo de configuração do bot deve ser o mesmo que o nome do bot. 

    ```json
    {
        "name": "<bot name>",
        "description": "<bot description>",
        "services": [
            {
                "type": "endpoint",
                "appId": "<appId from appsettings.json>",
                "appPassword": "<appPassword from appsettings.json>",
                "endpoint": "http://localhost:3978/api/messages",
                "id": "<don't change this value>",
                "name": "http://localhost:3978/api/messages"
            }
        ],
        "padlock": "",
        "version": "2.0",
        "overrides": null,
        "path": "<local path to .bot file>"
    }
    ```

1. No emulador de bot, `Hello` Insira e obtenha a mesma resposta para o bot básico como você recebeu no **teste no Web Chat**.

    [![Resposta básica de bot no emulador](./media/bfv4-csharp/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-csharp/ask-bot-emulator-a-question-and-get-response.png#lightbox)


## <a name="ask-bot-a-question-for-the-book-flight-intent"></a>Perguntar ao bot uma pergunta sobre a intenção de vôo do livro

1. No emulador de bot, marque um vôo digitando o seguinte expressão: 

    ```bot
    Book a flight from Paris to Berlin on March 22, 2020
    ```

    O emulador de bot pede para confirmar. 

1. Selecione **Sim**. O bot responde com um resumo de suas ações. 
1. No log do emulador de bot, selecione a linha que inclui `Luis Trace`. Isso exibe a resposta JSON de LUIS para a intenção e entidades do expressão.

    [![Resposta básica de bot no emulador](./media/bfv4-csharp/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-csharp/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)

[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>Passos Seguintes

Veja mais [exemplos](https://github.com/microsoft/botframework-solutions) com bots de conversação. 

> [!div class="nextstepaction"]
> [Criar um aplicativo Reconhecimento vocal com um domínio de assunto personalizado](luis-quickstart-intents-only.md)
