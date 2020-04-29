---
title: LUIS e QnAMaker - integração de bots
titleSuffix: Azure Cognitive Services
description: À medida que a sua base de conhecimento qnA Maker cresce, torna-se difícil mantê-la como um único conjunto monolítico. Divida a base de conhecimento em pedaços menores e lógicos.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 09/26/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b0d28c77966668f919cdf1265f8cc63b4931d5fd
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81402709"
---
# <a name="use-a-bot-with-qna-maker-and-luis-to-distribute-your-knowledge-base"></a>Use um bot com QnA Maker e LUIS para distribuir a sua base de conhecimento
À medida que a sua base de conhecimento qnA Maker cresce, torna-se difícil mantê-la como um único conjunto monolítico. Divida a base de conhecimento em pedaços menores e lógicos.

Embora seja simples criar múltiplas bases de conhecimento no QnA Maker, você precisará de alguma lógica para encaminhar a questão de entrada para a base de conhecimento apropriada. Pode fazê-lo usando o LUIS.

Este artigo utiliza o Bot Framework v3 SDK. Se estiver interessado na versão Bot Framework v4 SDK desta informação, consulte [Utilizar vários modelos LUIS e QnA](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&tabs=csharp).

## <a name="architecture"></a>Arquitetura

![Arquitetura de exibição gráfica de QnA Maker com compreensão da linguagem](../media/qnamaker-tutorials-qna-luis/qnamaker-luis-architecture.PNG)

O gráfico anterior mostra que o QnA Maker recebe primeiro a intenção da pergunta de um modelo LUIS. Em seguida, qnA Maker usa essa intenção de encaminhar a questão para a base de conhecimento qnA Maker correta.

## <a name="create-a-luis-app"></a>Criar uma aplicação LUIS

1. Inscreva-se no portal [LUIS.](https://www.luis.ai/)
1. [Criar uma aplicação.](https://docs.microsoft.com/azure/cognitive-services/luis/create-new-app)
1. [Adicione uma intenção](https://docs.microsoft.com/azure/cognitive-services/luis/add-intents) para cada base de conhecimento do Fabricante qnA. As declarações de exemplo devem corresponder a perguntas nas bases de conhecimento do QnA Maker.
1. [Treine a app LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-train) e [publique a app LUIS.](https://docs.microsoft.com/azure/cognitive-services/luis/publishapp)
1. Na secção **Gerir,** tome nota do ID da sua app LUIS, chave de ponto final LUIS e nome de [domínio personalizado](../../cognitive-services-custom-subdomains.md). Vai precisar destes valores mais tarde.

## <a name="create-qna-maker-knowledge-bases"></a>Criar bases de conhecimento do Fabricante qna

1. Inscreva-se no [QnA Maker](https://qnamaker.ai).
1. [Crie](https://www.qnamaker.ai/Create) uma base de conhecimento para cada intenção na app LUIS.
1. Teste e publique as bases do conhecimento. Quando publicar cada um deles, tome nota do ID, nome do recurso (o subdomínio personalizado antes de _.azurewebsites.net/qnamaker),_ e da chave final de autorização. Vai precisar destes valores mais tarde.

    Este artigo assume que as bases de conhecimento são todas criadas na mesma subscrição do Azure QnA Maker.

    ![Screenshot do pedido do QnA Maker HTTP](../media/qnamaker-tutorials-qna-luis/qnamaker-http-request.png)

## <a name="web-app-bot"></a>Bot de aplicação Web

1. [Crie um bot "Basic" com o Serviço Bot Azure,](https://docs.microsoft.com/azure/bot-service/bot-service-quickstart?view=azure-bot-service-4.0)que inclui automaticamente uma aplicação LUIS. Selecione a linguagem de programação C#.

1. Depois de criar o bot da aplicação web, no portal Azure, selecione o bot da aplicação web.
1. Na navegação do serviço bot da aplicação web, selecione Definições de **Aplicação**. Em seguida, desloque-se para a secção de definições de **aplicação** das definições disponíveis.
1. Mude o **LuisAppId** para o valor da app LUIS criada na secção anterior. Em seguida, selecione **Guardar**.


## <a name="change-the-code-in-the-basicluisdialogcs-file"></a>Alterar o código no ficheiro BasicLuisDialog.cs
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

5. Adicione as seguintes aulas para desserializar a resposta do Fabricante qnA:

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


6. Adicione a seguinte classe para fazer um pedido HTTP ao serviço QnA Maker. Note que o valor do cabeçalho da **Autorização** inclui a palavra, `EndpointKey`com um espaço seguindo a palavra. O resultado da JSON é desserializado nas classes anteriores, e a primeira resposta é devolvida.

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


7. Modifique `BasicLuisDialog` a classe. Cada intenção LUIS deve ter um método decorado com **LuisIntent**. O parâmetro para a decoração é o nome de intenção luis real. O nome do método que está decorado _deve_ ser o nome de intenção luis para a legibilidade e manutenção, mas não tem que ser o mesmo em tempo de design ou tempo de execução.

    ```csharp
    [Serializable]
    public class BasicLuisDialog : LuisDialog<object>
    {
        // LUIS Settings
        static string LUIS_appId = "<LUIS APP ID>";
        static string LUIS_apiKey = "<LUIS API KEY>";
        static string LUIS_hostRegion = "westus.api.cognitive.microsoft.com";

        // QnA Maker global settings
        // assumes all knowledge bases are created with same Azure service
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
1. No editor de código, clique à direita **build.cmd**, e selecione **Executar a partir da Consola**.

    ![Screenshot de Run da opção Consola no editor de código](../media/qnamaker-tutorials-qna-luis/run-from-console.png)

2. A vista de código é substituída por uma janela terminal que mostra o progresso e os resultados da construção.

    ![Screenshot da construção da consola](../media/qnamaker-tutorials-qna-luis/console-build.png)

## <a name="test-the-bot"></a>Teste o bot
No portal Azure, selecione **Test in Web Chat** para testar o bot. Digite mensagens de diferentes intenções para obter a resposta da base de conhecimento correspondente.

![Screenshot do teste de chat web](../media/qnamaker-tutorials-qna-luis/qnamaker-web-chat.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Integre a sua base de conhecimento com um agente em Agentes Virtuais de Energia](integrate-with-power-virtual-assistant-fallback-topic.md)
