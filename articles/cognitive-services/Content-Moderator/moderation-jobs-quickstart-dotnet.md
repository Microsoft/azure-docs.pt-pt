---
title: 'Início rápido: Iniciar tarefas de moderação usando o .NET - Content Moderator'
titlesuffix: Azure Cognitive Services
description: Como iniciar trabalhos de moderação com o SDK do Azure Content Moderator para .NET.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: quickstart
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: f55906aa9de6faa5979ecedbc3ef288f164fd6c8
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2019
ms.locfileid: "54261950"
---
# <a name="quickstart-start-moderation-jobs-using-net"></a>Início rápido: Iniciar tarefas de moderação usando o .NET

Este artigo apresenta informações e exemplos de código para ajudá-lo a começar a utilizar o [SDK do Content Moderator para .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) para:
 
- Iniciar uma tarefa de moderação para analisar e criar as revisões para moderadores humanos
- Obter o estado da revisão pendente
- Monitorizar e obter o estado final da revisão
- Submeter o resultado ao Url de chamada de retorno

Este artigo pressupõe que já está familiarizado com o Visual Studio e C#.

## <a name="sign-up-for-content-moderator"></a>Inscrever-se no Content Moderator

Antes de poder utilizar os serviços do Content Moderator através da API REST ou do SDK, precisa de uma chave de subscrição. Siga as instruções em [Create a Cognitive Services account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) (Criar uma conta dos Serviços Cognitivos) para subscrever o Content Moderator e obter a sua chave.

## <a name="sign-up-for-a-review-tool-account-if-not-completed-in-the-previous-step"></a>Inscrever-se numa conta da ferramenta de revisão, se não o tiver feito no passo anterior

Se chegou ao Content Moderator a partir do portal do Azure, [inscreva-se na conta da ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com/) e crie uma equipa de revisão. Irá precisar do ID da equipa e da ferramenta de revisão para chamar a API de revisão, para iniciar uma Tarefa e ver as revisões na ferramenta de revisão.

## <a name="ensure-your-api-key-can-call-the-review-api-for-review-creation"></a>Certifique-se de que a chave de API pode chamar a API de revisão para a criação de revisões

Depois de concluir os passos anteriores, pode ficar com duas chaves do Content Moderator, se tiver iniciado a partir do portal do Azure. 

Se planear utilizar a chave de API dada pelo Azure no seu exemplo de SDK, siga os passos mencionados na secção [Utilizar a chave do Azure com a API de revisão](review-tool-user-guide/credentials.md#use-the-azure-account-with-the-review-tool-and-review-api) para permitir que a aplicação chame a API de revisão e crie as revisões.

Se utilizar a chave de avaliação gratuita gerada pela ferramenta de revisão, a sua conta da ferramenta de revisão já conhece a chave e, por conseguinte, não são necessários passos adicionais.

## <a name="define-a-custom-moderation-workflow"></a>Definir um fluxo de trabalho de moderação personalizado

Uma tarefa de moderação analisa o conteúdo com as APIs e utiliza um **fluxo de trabalho** para determinar se deve ou não criar revisões.
Embora a ferramenta de revisão contenha um fluxo de trabalho predefinido, vamos [definir um fluxo de trabalho personalizado](Review-Tool-User-Guide/Workflows.md) para este início rápido.

Utilize o nome do fluxo de trabalho no código que inicia a tarefa de moderação.

## <a name="create-your-visual-studio-project"></a>Criar o projeto do Visual Studio

1. Adicione um novo projeto **Aplicação de consola (.NET Framework)** à sua solução.

   No código de exemplo, dê ao projeto o nome **CreateReviews**.

1. Selecione este projeto como o projeto de arranque único para a solução.

### <a name="install-required-packages"></a>Instalar pacotes necessários

Instale os seguintes pacotes NuGet:

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Atualizar as instruções de utilização do programa

Modifique as instruções de utilização do programa.

    using Microsoft.Azure.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator.Models;
    using Newtonsoft.Json;
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Threading;

### <a name="create-the-content-moderator-client"></a>Criar o cliente do Content Moderator

Adicione o seguinte código para criar um cliente do Content Moderator para a sua subscrição.

> [!IMPORTANT]
> Atualize os campos **AzureRegion** e **CMSubscriptionKey** com os valores do identificador da região e da chave de subscrição.


    /// <summary>
    /// Wraps the creation and configuration of a Content Moderator client.
    /// </summary>
    /// <remarks>This class library contains insecure code. If you adapt this 
    /// code for use in production, use a secure method of storing and using
    /// your Content Moderator subscription key.</remarks>
    public static class Clients
    {
        /// <summary>
        /// The region/location for your Content Moderator account, 
        /// for example, westus.
        /// </summary>
        private static readonly string AzureRegion = "YOUR API REGION";

        /// <summary>
        /// The base URL fragment for Content Moderator calls.
        /// </summary>
        private static readonly string AzureBaseURL =
            $"https://{AzureRegion}.api.cognitive.microsoft.com";

        /// <summary>
        /// Your Content Moderator subscription key.
        /// </summary>
        private static readonly string CMSubscriptionKey = "YOUR API KEY";

        /// <summary>
        /// Returns a new Content Moderator client for your subscription.
        /// </summary>
        /// <returns>The new client.</returns>
        /// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
        /// When you have finished using the client,
        /// you should dispose of it either directly or indirectly. </remarks>
        public static ContentModeratorClient NewClient()
        {
            // Create and initialize an instance of the Content Moderator API wrapper.
            ContentModeratorClient client = new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey));

            client.Endpoint = AzureBaseURL;
            return client;
        }
    }

### <a name="initialize-application-specific-settings"></a>Inicializar definições específicas da aplicação

Adicione as seguintes constantes e campos estáticos à classe **Programa** em Program.cs.

> [!NOTE]
> Defina a constante TeamName com o nome que utilizou quando criou a sua subscrição do Content Moderator. Pode obter o TeamName do [site do Content Moderator](https://westus.contentmoderator.cognitive.microsoft.com/).
> Depois de iniciar sessão, selecione **Credenciais** no menu **Definições** (ícone de roda dentada).
>
> O nome da sua equipa é o valor do campo **Id** na secção **API**.


    /// <summary>
    /// The moderation job will use this workflow that you defined earlier.
    /// See the quickstart article to learn how to setup custom workflows.
    /// </summary>
    private const string WorkflowName = "OCR";
    
    /// <summary>
    /// The name of the team to assign the job to.
    /// </summary>
    /// <remarks>This must be the team name you used to create your 
    /// Content Moderator account. You can retrieve your team name from
    /// the Content Moderator web site. Your team name is the Id associated 
    /// with your subscription.</remarks>
    private const string TeamName = "***";

    /// <summary>
    /// The URL of the image to create a review job for.
    /// </summary>
    private const string ImageUrl =
        "https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png";

    /// <summary>
    /// The name of the log file to create.
    /// </summary>
    /// <remarks>Relative paths are relative to the execution directory.</remarks>
    private const string OutputFile = "OutputLog.txt";

    /// <summary>
    /// The number of seconds to delay after a review has finished before
    /// getting the review results from the server.
    /// </summary>
    private const int latencyDelay = 45;

    /// <summary>
    /// The callback endpoint for completed reviews.
    /// </summary>
    /// <remarks>Reviews show up for reviewers on your team. 
    /// As reviewers complete reviews, results are sent to the
    /// callback endpoint using an HTTP POST request.</remarks>
    private const string CallbackEndpoint = "";

## <a name="add-code-to-auto-moderate-create-a-review-and-get-the-job-details"></a>Adicionar código para moderar automaticamente, criar uma revisão e obter os detalhes da tarefa

> [!Note]
> Na prática, vai definir o URL de chamada de retorno **CallbackEndpoint** para o URL que recebe os resultados da revisão manual (por meio de um pedido HTTP POST).

Comece por adicionar o seguinte código ao método **Principal**.

    using (TextWriter writer = new StreamWriter(OutputFile, false))
    {
        using (var client = Clients.NewClient())
        {
            writer.WriteLine("Create review job for an image.");
            var content = new Content(ImageUrl);
        
            // The WorkflowName contains the name of the workflow defined in the online review tool.
            // See the quickstart article to learn more.
            var jobResult = client.Reviews.CreateJobWithHttpMessagesAsync(
                    TeamName, "image", "contentID", WorkflowName, "application/json", content, CallbackEndpoint);

            // Record the job ID.
            var jobId = jobResult.Result.Body.JobIdProperty;

            // Log just the response body from the returned task.
            writer.WriteLine(JsonConvert.SerializeObject(
                jobResult.Result.Body, Formatting.Indented));

            Thread.Sleep(2000);
            writer.WriteLine();

            writer.WriteLine("Get review job status.");
            var jobDetails = client.Reviews.GetJobDetailsWithHttpMessagesAsync(
                    TeamName, jobId);

            // Log just the response body from the returned task.
            writer.WriteLine(JsonConvert.SerializeObject(
                    jobDetails.Result.Body, Formatting.Indented));

            Console.WriteLine();
            Console.WriteLine("Perform manual reviews on the Content Moderator site.");
            Console.WriteLine("Then, press any key to continue.");
            Console.ReadKey();

            Console.WriteLine();
            Console.WriteLine($"Waiting {latencyDelay} seconds for results to propagate.");
            Thread.Sleep(latencyDelay * 1000);

            writer.WriteLine("Get review details.");
            jobDetails = client.Reviews.GetJobDetailsWithHttpMessagesAsync(
            TeamName, jobId);

            // Log just the response body from the returned task.
            writer.WriteLine(JsonConvert.SerializeObject(
            jobDetails.Result.Body, Formatting.Indented));
        }
        writer.Flush();
        writer.Close();
    }

> [!NOTE]
> A chave de serviço do Content Moderator tem um limite de velocidade de pedidos por segundo (RPS). Se ultrapassar o limite, o SDK emite uma exceção com o código de erro 429. 
>
> Uma chave de escalão gratuito tem um limite de velocidade de um RPS.

## <a name="run-the-program-and-review-the-output"></a>Executar o programa e rever o resultado

Verá a seguinte saída de exemplo na consola:

    Perform manual reviews on the Content Moderator site.
    Then, press any key to continue.

Inicie sessão na ferramenta de revisão do Content Moderator para ver a revisão da imagem pendente.

Utilize o botão **Seguinte** para submeter.

![Revisão de imagem para moderadores humanos](images/ocr-sample-image.PNG)

## <a name="see-the-sample-output-in-the-log-file"></a>Ver a saída de exemplo no ficheiro de registo

> [!NOTE]
> No ficheiro de saída, as cadeias de carateres **Teamname**, **ContentId**, **CallBackEndpoint** e **WorkflowId** refletem os valores que utilizou anteriormente.

    Create moderation job for an image.
    {
        "JobId": "2018014caceddebfe9446fab29056fd8d31ffe"
    }

    Get review details.
    {
        "Id": "2018014caceddebfe9446fab29056fd8d31ffe",
        "TeamName": "some team name",
        "Status": "InProgress",
        "WorkflowId": "OCR",
        "Type": "Image",
        "CallBackEndpoint": "",
        "ReviewId": "",
        "ResultMetaData": [],
        "JobExecutionReport": [
        {
            "Ts": "2018-01-07T00:38:26.7714671",
            "Msg": "Successfully got hasText response from Moderator"
        },
        {
            "Ts": "2018-01-07T00:38:26.4181346",
            "Msg": "Getting hasText from Moderator"
        },
        {
            "Ts": "2018-01-07T00:38:25.5122828",
            "Msg": "Starting Execution - Try 1"
        }
        ]
    }


## <a name="your-callback-url-if-provided-receives-this-response"></a>Se tiver sido fornecido, o Url da chamada de retorno recebe esta resposta.

Verá uma resposta semelhante ao seguinte exemplo:

> [!NOTE]
> Na resposta à chamada de retorno, as cadeias de carateres **ContentId** e **WorkflowId** refletem os valores que utilizou anteriormente.

    {
        "JobId": "2018014caceddebfe9446fab29056fd8d31ffe",
        "ReviewId": "201801i28fc0f7cbf424447846e509af853ea54",
        "WorkFlowId": "OCR",
        "Status": "Complete",
        "ContentType": "Image",
        "CallBackType": "Job",
        "ContentId": "contentID",
        "Metadata": {
            "hastext": "True",
            "ocrtext": "IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n",
            "imagename": "contentID"
        }
    }


## <a name="next-steps"></a>Passos Seguintes

Obtenha o [SDK .NET do Content Moderator](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) e a [solução do Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) para este e outros inícios rápidos do Content Moderator para .NET e comece a trabalhar na sua integração.
