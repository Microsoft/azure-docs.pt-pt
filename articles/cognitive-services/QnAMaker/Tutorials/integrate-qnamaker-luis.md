---
title: LUIS e QnAMaker - integração de Bot
titleSuffix: Azure Cognitive Services
description: À medida que sua base de dados de conhecimento do QnA Maker cresce grandes, torna-se difícil mantê-lo como um único monolítico definido e há a necessidade de dividir a base de dados de conhecimento em partes lógicas menores.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/11/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 6605aa268a7ee7fe75254df5dbe96e9dfbc71d79
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71272419"
---
# <a name="use-bot-with-qna-maker-and-luis-to-distribute-your-knowledge-base"></a>Usar bot com QnA Maker e LUIS para distribuir sua base de dados de conhecimento
À medida que sua base de dados de conhecimento do QnA Maker cresce grandes, torna-se difícil mantê-lo como um único monolítico definido e há a necessidade de dividir a base de dados de conhecimento em partes lógicas menores.

Embora seja fácil para criar várias bases de dados de conhecimento no Criador de FAQ, terá de alguma lógica para rotear a pergunta de entrada para a base de dados de conhecimento adequada. Pode fazê-lo ao utilizar o LUIS.

Este artigo utiliza o Bot Framework v3 SDK. Consulte este [artigo de Bot Framework](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&tabs=csharp), se estiver interessado numa versão do SDK do Bot Framework v4 dessas informações.

## <a name="architecture"></a>Arquitetura

![QnA Maker com arquitetura de Reconhecimento vocal](../media/qnamaker-tutorials-qna-luis/qnamaker-luis-architecture.PNG)

No cenário acima, o QnA Maker obtém primeiro a intenção da pergunta recebida de um modelo do LUIS e, em seguida, usá-lo para encaminhar para a base de dados de conhecimento do QnA Maker correta.

## <a name="create-a-luis-app"></a>Criar uma aplicação LUIS

1. Inicie sessão para o [LUIS](https://www.luis.ai/) portal.
1. [Criar uma aplicação](https://docs.microsoft.com/azure/cognitive-services/luis/create-new-app).
1. [Adicionar uma intenção](https://docs.microsoft.com/azure/cognitive-services/luis/add-intents) para cada base de dados de conhecimento do QnA Maker. As expressões de exemplo devem corresponder a perguntas, as bases de dados de conhecimento do QnA Maker.
1. [Preparar a aplicação do LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-train) e [publicar a aplicação do LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/publishapp) sua aplicação LUIS.
1. Na **gerir** secção, anote o LUIS ID da aplicação, chave de ponto final do LUIS e região do anfitrião. Irá necessitar destes valores mais tarde. 

## <a name="create-qna-maker-knowledge-bases"></a>Criar bases de dados de conhecimento do QnA Maker

1. Inicie sessão no [QnA Maker](https://qnamaker.ai).
1. [Criar](https://www.qnamaker.ai/Create) um bases de dados de conhecimento para cada objetivo na aplicação do LUIS.
1. Testar e publicar as bases de dados de conhecimento. Quando publicar cada KB, anote o ID da BDC, hospedar (subdomínio antes _.azurewebsites.net/qnamaker_) e a chave de ponto final de autorização. Irá necessitar destes valores mais tarde. 

    Este artigo pressupõe que o KBs são criados na mesma subscrição do Azure QnA Maker.

    ![Pedido de HTTP do QnA Maker](../media/qnamaker-tutorials-qna-luis/qnamaker-http-request.png)

## <a name="web-app-bot"></a>Bot de funções

1. [Crie um bot de aplicativo Web "básico"](https://docs.microsoft.com/azure/bot-service/bot-service-quickstart?view=azure-bot-service-4.0) que inclui automaticamente um aplicativo Luis. Selecione C# linguagem de programação.

1. Uma vez criado o bot de aplicação web, no portal do Azure, selecione o bot de aplicação web.
1. Selecione **as configurações do aplicativo** na navegação de serviço da Web app bot, em seguida, desloque para baixo até **configurações de aplicativo** secção de definições disponíveis.
1. Alteração da **LuisAppId** para o valor da aplicação LUIS criado na secção anterior, em seguida, selecione **guardar**.


## <a name="change-code-in-basicluisdialogcs"></a>Alterar o código no BasicLuisDialog.cs
1. Do **gestão de Bot** secção a navegação de bot de aplicação web no portal do Azure, selecione **criar**.
2. Selecione **editor de código online aberto**. Um novo separador do browser abre-se com o ambiente de edição online. 
3. Na **WWWROOT** secção, selecione a **caixas de diálogo** diretório, em seguida, abra **BasicLuisDialog.cs**.
4. Adicionar dependências na parte superior dos **BasicLuisDialog.cs** ficheiro:

    ```csharp
    using System;
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Collections.Generic;
    using Microsoft.Bot.Builder.Dialogs;
    using Microsoft.Bot.Builder.Luis;
    using Microsoft.Bot.Builder.Luis.Models;
    using Newtonsoft.Json;
    using System.Text;
    ```

5. Adicionar o abaixo classes para anular a serialização da resposta do QnA Maker:

    ```csharp
    public class Metadata
    {
        public string name { get; set; }
        public string value { get; set; }
    }

    public class Answer
    {
        public IList<string> questions { get; set; }
        public string answer { get; set; }
        public double score { get; set; }
        public int id { get; set; }
        public string source { get; set; }
        public IList<object> keywords { get; set; }
        public IList<Metadata> metadata { get; set; }
    }

    public class QnAAnswer
    {
        public IList<Answer> answers { get; set; }
    }
    ```


6. Adicione a seguinte classe para fazer uma solicitação HTTP para o serviço QnA Maker. Tenha em atenção que o **autorização** valor do cabeçalho inclui word, `EndpointKey` com um espaço a palavra a seguir. O resultado JSON é desserializado para as classes anteriores e a primeira resposta é retornada.

    ```csharp
    [Serializable]
    public class QnAMakerService
    {
        private string qnaServiceHostName;
        private string knowledgeBaseId;
        private string endpointKey;

        public QnAMakerService(string hostName, string kbId, string endpointkey)
        {
            qnaServiceHostName = hostName;
            knowledgeBaseId = kbId;
            endpointKey = endpointkey;

        }
        async Task<string> Post(string uri, string body)
        {
            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(uri);
                request.Content = new StringContent(body, Encoding.UTF8, "application/json");
                request.Headers.Add("Authorization", "EndpointKey " + endpointKey);

                var response = await client.SendAsync(request);
                return  await response.Content.ReadAsStringAsync();
            }
        }
        public async Task<string> GetAnswer(string question)
        {
            string uri = qnaServiceHostName + "/qnamaker/knowledgebases/" + knowledgeBaseId + "/generateAnswer";
            string questionJSON = "{\"question\": \"" + question.Replace("\"","'") +  "\"}";

            var response = await Post(uri, questionJSON);

            var answers = JsonConvert.DeserializeObject<QnAAnswer>(response);
            if (answers.answers.Count > 0)
            {
                return answers.answers[0].answer;
            }
            else
            {
                return "No good match found.";
            }
        }
    }
    ```


7. Modificar a classe BasicLuisDialog. Cada objetivo de LUIS deve ter um método decorado com **LuisIntent**. O parâmetro para a decoração é o nome de intenção de LUIS real. O nome do método que está decorado _deve_ ser o nome de intenção de LUIS para facilitar a leitura e a capacidade de manutenção, mas não tem de ser o mesmo design ou tempo de execução.  

    ```csharp
    [Serializable]
    public class BasicLuisDialog : LuisDialog<object>
    {
        // LUIS Settings
        static string LUIS_appId = "<LUIS APP ID>";
        static string LUIS_apiKey = "<LUIS API KEY>";
        static string LUIS_hostRegion = "westus.api.cognitive.microsoft.com";

        // QnA Maker global settings
        // assumes all KBs are created with same Azure service
        static string qnamaker_endpointKey = "<QnA Maker endpoint KEY>";
        static string qnamaker_endpointDomain = "my-qnamaker-s0-s";
        
        // QnA Maker Human Resources Knowledge base
        static string HR_kbID = "<QnA Maker KNOWLEDGE BASE ID>";

        // QnA Maker Finance Knowledge base
        static string Finance_kbID = "<QnA Maker KNOWLEDGE BASE ID>";

        // Instantiate the knowledge bases
        public QnAMakerService hrQnAService = new QnAMakerService("https://" + qnamaker_endpointDomain + ".azurewebsites.net", HR_kbID, qnamaker_endpointKey);
        public QnAMakerService financeQnAService = new QnAMakerService("https://" + qnamaker_endpointDomain + ".azurewebsites.net", Finance_kbID, qnamaker_endpointKey);

        public BasicLuisDialog() : base(new LuisService(new LuisModelAttribute(
            LUIS_appId,
            LUIS_apiKey,
            domain: LUIS_hostRegion)))
        {
        }

        [LuisIntent("None")]
        public async Task NoneIntent(IDialogContext context, LuisResult result)
        {
            HttpClient client = new HttpClient();
            await this.ShowLuisResult(context, result);
        }

        // HR Intent
        [LuisIntent("HR")]
        public async Task HumanResourcesIntent(IDialogContext context, LuisResult result)
        {
            // Ask the HR knowledge base
            var qnaMakerAnswer = await hrQnAService.GetAnswer(result.Query);
            await context.PostAsync($"{qnaMakerAnswer}");
            context.Wait(MessageReceived);
        }

        // Finance intent
        [LuisIntent("Finance")]
        public async Task FinanceIntent(IDialogContext context, LuisResult result)
        {
            // Ask the finance knowledge base
            var qnaMakerAnswer = await financeQnAService.GetAnswer(result.Query);
            await context.PostAsync($"{qnaMakerAnswer}");
            context.Wait(MessageReceived);
        }
        private async Task ShowLuisResult(IDialogContext context, LuisResult result)
        {
            await context.PostAsync($"You have reached {result.Intents[0].Intent}. You said: {result.Query}");
            context.Wait(MessageReceived);
        }
    }
    ```


## <a name="build-the-bot"></a>Criar o bot
1. No editor de código, faça duplo clique em `build.cmd` e selecione **executar consola**.

    ![executar a partir da consola](../media/qnamaker-tutorials-qna-luis/run-from-console.png)

2. A visualização de código é substituída por uma janela de terminal que mostra o progresso e os resultados da compilação.

    ![compilação de consola](../media/qnamaker-tutorials-qna-luis/console-build.png)

## <a name="test-the-bot"></a>Testar o bot
No portal do Azure, selecione **teste na Web Chat** para testar o bot. Escreva mensagens desde intenções diferentes para obter a resposta da base de dados de conhecimento correspondente.

![teste de chat na Web](../media/qnamaker-tutorials-qna-luis/qnamaker-web-chat.png)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Criar um plano de continuidade de negócio para o QnA Maker](../How-To/business-continuity-plan.md)
