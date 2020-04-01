---
title: LUIS e QnAMaker - Integração bot
titleSuffix: Azure Cognitive Services
description: À medida que a sua base de conhecimento qnA Maker cresce grande, torna-se difícil mantê-la como um único conjunto monolítico e há a necessidade de dividir a base de conhecimento em pedaços lógicos menores.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 09/26/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: c01f5f41e61cd65855789bb753a7a297fe475885
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396353"
---
# <a name="use-bot-with-qna-maker-and-luis-to-distribute-your-knowledge-base"></a>Use bot com QnA Maker e LUIS para distribuir a sua base de conhecimento
À medida que a sua base de conhecimento qnA Maker cresce grande, torna-se difícil mantê-la como um único conjunto monolítico e há a necessidade de dividir a base de conhecimento em pedaços lógicos menores.

Embora seja simples criar múltiplas bases de conhecimento no QnA Maker, você precisará de alguma lógica para encaminhar a questão de entrada para a base de conhecimento apropriada. Pode fazê-lo usando o LUIS.

Este artigo utiliza bot framework v3 SDK. Consulte este [artigo Bot Framework,](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&tabs=csharp)se estiver interessado na versão Bot Framework v4 SDK desta informação.

## <a name="architecture"></a>Arquitetura

![QnA Maker com arquitetura de compreensão de linguagem](../media/qnamaker-tutorials-qna-luis/qnamaker-luis-architecture.PNG)

No cenário acima, a QnA Maker obtém primeiro a intenção da pergunta de entrada de um modelo LUIS, e depois usa-a para encaminhar para a base de conhecimento correta do QnA Maker.

## <a name="create-a-luis-app"></a>Criar uma aplicação LUIS

1. Inscreva-se no portal [LUIS.](https://www.luis.ai/)
1. [Criar uma aplicação.](https://docs.microsoft.com/azure/cognitive-services/luis/create-new-app)
1. [Adicione uma intenção](https://docs.microsoft.com/azure/cognitive-services/luis/add-intents) para cada base de conhecimento do Fabricante qnA. As declarações de exemplo devem corresponder a perguntas nas bases de conhecimento do QnA Maker.
1. [Treine a app LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-train) e [publique a app LUIS a](https://docs.microsoft.com/azure/cognitive-services/luis/publishapp) sua App LUIS.
1. Na secção **Gerir,** tome nota do ID da sua app LUIS, chave de ponto final LUIS e nome de [domínio personalizado](../../cognitive-services-custom-subdomains.md). Vai precisar destes valores mais tarde.

## <a name="create-qna-maker-knowledge-bases"></a>Criar bases de conhecimento do Fabricante qna

1. Inscreva-se no [QnA Maker](https://qnamaker.ai).
1. [Crie](https://www.qnamaker.ai/Create) bases de conhecimento para cada intenção na app LUIS.
1. Teste e publique as bases do conhecimento. Quando publicar cada KB, tome nota do ID KB, do nome do recurso (subdomínio personalizado antes _de .azurewebsites.net/qnamaker),_ e da chave final de ponto de autorização. Vai precisar destes valores mais tarde.

    Este artigo assume que os KBs são todos criados na mesma subscrição do Azure QnA Maker.

    ![Pedido de Http do Fabricante qnA](../media/qnamaker-tutorials-qna-luis/qnamaker-http-request.png)

## <a name="web-app-bot"></a>Web app Bot

1. [Crie um bot Web App "Basic"](https://docs.microsoft.com/azure/bot-service/bot-service-quickstart?view=azure-bot-service-4.0) que inclua automaticamente uma aplicação LUIS. Selecione linguagem de programação C#.

1. Assim que o bot da aplicação web for criado, no portal Azure, selecione o bot da aplicação web.
1. Selecione Definições de **aplicação** na navegação do serviço bot da aplicação Web e, em seguida, desloque-se para a secção de definições de **aplicação** das definições disponíveis.
1. Mude o **LuisAppId** para o valor da aplicação LUIS criada na secção anterior e, em seguida, selecione **Save**.


## <a name="change-code-in-basicluisdialogcs"></a>Alterar código em BasicLuisDialog.cs
1. A partir da secção **bot Management** da navegação bot da aplicação web no portal Azure, selecione **Build**.
2. Selecione **Open online code editor**. Um novo separador de navegador abre com o ambiente de edição online.
3. Na secção **WWWROOT,** selecione o diretório de **Diálogos** e abra **BasicLuisDialog.cs**.
4. Adicione dependências ao topo do ficheiro **BasicLuisDialog.cs:**

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

5. Adicione as classes abaixo para desserializar a resposta do Fabricante qnA:

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


6. Adicione a seguinte classe para fazer um pedido HTTP ao serviço QnA Maker. Note que o valor do cabeçalho da **Autorização** inclui a palavra, `EndpointKey` com um espaço seguindo a palavra. O resultado da JSON é desserializado nas classes anteriores e a primeira resposta é devolvida.

    ```csharp
    [Serializable]
    public class QnAMakerService
    {
        private string qnaServiceResourceName;
        private string knowledgeBaseId;
        private string endpointKey;

        public QnAMakerService(string resourceName, string kbId, string endpointkey)
        {
            qnaServiceResourceName = resourceName;
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
            string uri = qnaServiceResourceName + "/qnamaker/knowledgebases/" + knowledgeBaseId + "/generateAnswer";
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


7. Modifique a aula basicLuisDialog. Cada intenção LUIS deve ter um método decorado com **LuisIntent**. O parâmetro para a decoração é o nome de intenção luis real. O nome do método que está decorado _deve_ ser o nome de intenção luis para a legibilidade e manutenção, mas não tem que ser o mesmo no tempo de design ou execução.

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
        static string qnamaker_resourceName = "my-qnamaker-s0-s";

        // QnA Maker Human Resources Knowledge base
        static string HR_kbID = "<QnA Maker KNOWLEDGE BASE ID>";

        // QnA Maker Finance Knowledge base
        static string Finance_kbID = "<QnA Maker KNOWLEDGE BASE ID>";

        // Instantiate the knowledge bases
        public QnAMakerService hrQnAService = new QnAMakerService("https://" + qnamaker_resourceName + ".azurewebsites.net", HR_kbID, qnamaker_endpointKey);
        public QnAMakerService financeQnAService = new QnAMakerService("https://" + qnamaker_resourceName + ".azurewebsites.net", Finance_kbID, qnamaker_endpointKey);

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


## <a name="build-the-bot"></a>Construa o bot
1. No editor de código, `build.cmd` clique à direita e selecione **Executar a partir da Consola**.

    ![correr a partir da consola](../media/qnamaker-tutorials-qna-luis/run-from-console.png)

2. A vista de código é substituída por uma janela terminal que mostra o progresso e os resultados da construção.

    ![construção de consola](../media/qnamaker-tutorials-qna-luis/console-build.png)

## <a name="test-the-bot"></a>Teste o bot
No portal Azure, selecione **Test in Web Chat** para testar o bot. Digite mensagens de diferentes intenções para obter a resposta da base de conhecimento correspondente.

![teste de chat web](../media/qnamaker-tutorials-qna-luis/qnamaker-web-chat.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Integre a sua base de conhecimento com um Agente Virtual Power](integrate-with-power-virtual-assistant-fallback-topic.md)
