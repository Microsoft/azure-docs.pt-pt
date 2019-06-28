---
title: Bot de compreensão de idioma C# v4
titleSuffix: Language Understanding - Azure Cognitive Services
description: Com o C#, crie um chatbot integrado com compreensão de idiomas (LUIS). O bot baseia-se com o Bot Framework versão 4 e o serviço de bot de aplicação Web do Azure.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 06/24/2019
ms.author: diberry
ms.openlocfilehash: 8a03d87441f26d3116aff8af33fd94da0ef9a909
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67438441"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-c"></a>Tutorial: Utilize um bot de funções ativada com compreensão de idiomas emC#

Utilize C# criar um chatbot integrado (LUIS) de compreensão de idiomas. O bot baseia-se com o Azure [bot de funções da Web](https://docs.microsoft.com/azure/bot-service/) recursos e [versão de Bot Framework](https://github.com/Microsoft/botbuilder-dotnet) V4.

**Neste tutorial, vai aprender a:**

> [!div class="checklist"]
> * Criar um bot de aplicação Web. Este processo cria uma aplicação LUIS nova.
> * Transfira o projeto de bot criado pelo serviço de bot do Web
> * Iniciar o bot e o emulador localmente no seu computador
> * Ver os resultados de expressão no bot

## <a name="prerequisites"></a>Pré-requisitos

* [Emulador de bot](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)


## <a name="create-a-web-app-bot-resource"></a>Criar recurso de um bot de aplicação web

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
    |Plano de serviço de aplicações/localização|Não é alterado de valor predefinido fornecido.|
    |Application Insights|Não é alterado de valor predefinido fornecido.|
    |ID da aplicação Microsoft e a palavra-passe|Não é alterado de valor predefinido fornecido.|

1. Na **modelo de Bot**, selecione o seguinte, em seguida, escolha a **selecione** botão sob estas definições:

    |Definição|Objetivo|Seleção|
    |--|--|--|
    |Versão do SDK|Versão do Bot Framework|**SDK v4**|
    |Idioma do SDK|Linguagem de programação do bot|**C#**|
    |Bot|Tipo de bot|**Bot básico**|
    
1. Selecione **Criar**. Esta ação cria e implementa o serviço de bot no Azure. Parte deste processo cria uma aplicação LUIS com o nome `luis-csharp-bot-XXXX`. Este nome é baseado no nome de aplicação do serviço de Bot de /Azure.

    [![Criar bot de funções](./media/bfv4-csharp/create-web-app-service.png)](./media/bfv4-csharp/create-web-app-service.png#lightbox)

    Aguarde até que o serviço de bot é criado antes de continuar.

## <a name="the-bot-has-a-language-understanding-model"></a>O bot tem um modelo de compreensão de idiomas

O processo de criação do bot service também cria uma nova aplicação LUIS com intenções e expressões de exemplo. O bot fornece o mapeamento das intenções à nova aplicação LUIS para as intenções seguintes: 

|Intenções do LUIS de bot básico|expressão de exemplo|
|--|--|
|Reservar vôo|`Travel to Paris`|
|Cancelar|`bye`|
|Nenhuma|Tudo o que estiver fora do domínio da aplicação.|

## <a name="test-the-bot-in-web-chat"></a>Testar o bot em Web Chat

1. Ainda no portal do Azure para o novo bot, selecione **teste na Web Chat**. 
1. Na **escreva a mensagem** caixa de texto, introduza o texto `hello`. O bot responde com informações sobre a arquitetura de bot, bem como consultas de exemplo para o modelo do LUIS específico, como reserva um voo para Paris. 

    ![Captura de ecrã do portal do Azure, introduza o texto "Olá".](./media/bfv4-csharp/ask-bot-question-in-portal-test-in-web-chat.png)

    Pode usar a funcionalidade de teste para testar rapidamente o seu bot. Para obter mais informações concluir teste, incluindo a depuração, Baixe o código de bot e utilizar o Visual Studio. 

## <a name="download-the-web-app-bot-source-code"></a>Baixe o código de origem do bot de aplicação web
Para poder desenvolver o código de bot de aplicação Web, transfira o código e utilize-o no seu computador local. 

1. No portal do Azure, selecione **Compilar**, na secção **Gestão de bot**. 

1. Selecione **Transferir o código-fonte de Bot**. 

    [![Baixe o código-fonte web app bot de bot básico](../../../includes/media/cognitive-services-luis/bfv4/download-code.png)](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

1. Quando a caixa de diálogo pop-up pede-lhe **incluem definições da aplicação no ficheiro zip transferido?** , selecione **Sim**.

1. Quando o código de origem estiver zipado, é disponibilizada uma ligação numa mensagem para o transferir. Selecione a ligação. 

1. Guarde o ficheiro zip no computador local e extraia os ficheiros. Abra o projeto com o Visual Studio. 

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>Reveja o código para enviar a expressão para LUIS e obter resposta

1. Abra o **LuisHelper.cs** ficheiro. É aqui que a expressão do utilizador introduzida no bot é enviada para o LUIS. A resposta do LUIS é retornada do método como um **BookDetails** objeto. Ao criar o seu bot, também deve criar seu próprio objeto para devolver os detalhes do LUIS. 


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

1. Open **BookingDetails.cs** para ver como o objeto abstrai as informações de LUIS. 

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

1. Open **caixas de diálogo -> BookingDialog.cs** para compreender como o objeto de BookingDetails é usado para gerenciar o fluxo de conversação. Detalhes de viagens perguntados nos passos, em seguida, as reservas toda é confirmada e finalmente repetida para o utilizador. 

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

No Visual Studio, inicie o bot. É aberta uma janela de browser com o site do bot de aplicação Web em `http://localhost:3978/`. Uma home page apresenta informações sobre o seu bot.

![Uma home page apresenta informações sobre o seu bot.](./media/bfv4-csharp/running-bot-web-home-page-success.png)

## <a name="use-the-bot-emulator-to-test-the-bot"></a>Utilizar o emulador de bot para testar o bot

1. Iniciar o emulador de Bot e selecione **Bot aberto**.
1. Na **abra um bot** caixa de diálogo pop-up, introduza o URL de bot, tais como `http://localhost:3978/api/messages`. O `/api/messages` rota é o endereço da web para o bot.
1. Introduza o **ID da aplicação Microsoft** e **palavra-passe do Microsoft App**, foi encontrado no **appSettings** ficheiro na raiz do código de bot que transferiu.

    Opcionalmente, pode criar um bot de nova configuração e copie o `appId` e `appPassword` partir o **appSettings** ficheiro no projeto do Visual Studio para o bot. O nome do ficheiro de configuração do bot deve ser igual ao nome do bot. 

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

1. No emulador do bot, introduza `Hello` e obter a mesma resposta para o bot básico que recebeu na **teste na Web Chat**.

    [![Resposta de bot básico no emulador](./media/bfv4-csharp/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-csharp/ask-bot-emulator-a-question-and-get-response.png#lightbox)


## <a name="ask-bot-a-question-for-the-book-flight-intent"></a>Faça uma pergunta para a intenção de reservar vôo de bot

1. No emulador do bot, programar um vôo inserindo a seguinte expressão: 

    ```bot
    Book a flight from Paris to Berlin on March 22, 2020
    ```

    O emulador de bot pede confirmar. 

1. Selecione **Sim**. O bot responde com um resumo das suas ações. 
1. O registo do emulador de bot, selecione a linha que inclui `Luis Trace`. Esta ação apresenta a resposta JSON do LUIS para a intenção e as entidades da expressão.

    [![Resposta de bot básico no emulador](./media/bfv4-csharp/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-csharp/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)

[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>Passos Seguintes

Ver mais [amostras](https://github.com/microsoft/botframework-solutions) com conversacionais bots. 

> [!div class="nextstepaction"]
> [Criar uma aplicação de compreensão de idiomas com um domínio de requerente personalizado](luis-quickstart-intents-only.md)
