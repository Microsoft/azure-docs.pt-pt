---
title: 'Exemplo: Criar uma habilidade personalizada no pipeline de pesquisa cognitiva - Azure Search'
description: Demonstra como utilizar a API de tradução de texto na habilidade personalizada, mapeada para uma pipeline no Azure Search a indexação de pesquisa cognitiva.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: f3b4e6cd18a362775443bb296560a076aaa1497d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57883632"
---
# <a name="example-create-a-custom-skill-using-the-text-translate-api"></a>Exemplo: Criar uma habilidade personalizada com a API de tradução de texto

Neste exemplo, saiba como criar uma web competências personalizadas de API que aceita texto em qualquer linguagem e o converte para inglês. O exemplo utiliza um [função do Azure](https://azure.microsoft.com/services/functions/) encapsular o [API de tradução de texto](https://azure.microsoft.com/services/cognitive-services/translator-text-api/) para que ele implementa a interface de competências personalizadas.

## <a name="prerequisites"></a>Pré-requisitos

+ Leia sobre [interface de competências personalizadas](cognitive-search-custom-skill-interface.md) artigo se não estiver familiarizado com a interface de entrada/saída que uma habilidade personalizada deve implementar.

+ [Inscreva-se a API de texto do Translator](../cognitive-services/translator/translator-text-how-to-signup.md)e obtenha uma chave de API de consumi-las.

+ Instale [Visual Studio 2017 versão 15.5](https://www.visualstudio.com/vs/) ou posterior, incluindo a carga de trabalho de desenvolvimento do Azure.

## <a name="create-an-azure-function"></a>Criar uma Função do Azure

Embora este exemplo utiliza uma função do Azure para alojar uma API web, não é necessário.  Desde que tenha em atenção a [interface requisitos para uma habilidade cognitivo](cognitive-search-custom-skill-interface.md), a abordagem de é irrelevante. As funções do Azure, no entanto, tornam mais fácil criar uma habilidade personalizada.

### <a name="create-a-function-app"></a>Criar uma aplicação de função

1. No Visual Studio, selecione **New** > **projeto** no menu File.

1. Na caixa de diálogo novo projeto, selecione **instalada**, expanda **Visual c#** > **Cloud**, selecione **as funções do Azure**, escreva um Dê um nome para o seu projeto e selecione **OK**. O nome da aplicação de funções deve ser válido como um espaço de nomes C#. Portanto, não use sublinhados, hífenes ou outros carateres não alfanuméricos.

1. Selecione **v2 das funções do Azure (.NET Core)**. Também pode fazê-lo com a versão 1, mas o código escrito abaixo baseia-se no modelo v2.

1. Selecione o tipo a ser **acionador HTTP**

1. Para a conta de armazenamento, pode selecionar **None**, como não precisa de qualquer armazenamento para esta função.

1. Selecione **OK** para criar a função de projeto e HTTP função acionada por um.

### <a name="modify-the-code-to-call-the-translate-cognitive-service"></a>Modificar o código para chamar o serviço cognitivos traduzir

O Visual Studio cria um projeto e neste uma classe que contém o código automático de tipo de função de escolhida. O atributo *FunctionName* nos conjuntos define o nome da função. O atributo *HttpTrigger* especifica que a função é acionada por um pedido de HTTP.

Agora, substitua todo o conteúdo do ficheiro *Function1.cs* com o código a seguir:

```csharp
using System;
using System.Net.Http;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.IO;
using System.Text;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Azure.WebJobs.Host;
using Newtonsoft.Json;

namespace TranslateFunction
{
    // This function will simply translate messages sent to it.
    public static class Function1
    {
        static string path = "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0";

        // NOTE: Replace this example key with a valid subscription key.
        static string key = "<enter your api key here>";

        #region classes used to serialize the response
        private class WebApiResponseError
        {
            public string message { get; set; }
        }

        private class WebApiResponseWarning
        {
            public string message { get; set; }
        }

        private class WebApiResponseRecord
        {
            public string recordId { get; set; }
            public Dictionary<string, object> data { get; set; }
            public List<WebApiResponseError> errors { get; set; }
            public List<WebApiResponseWarning> warnings { get; set; }
        }

        private class WebApiEnricherResponse
        {
            public List<WebApiResponseRecord> values { get; set; }
        }
        #endregion

        [FunctionName("Translate")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)]HttpRequest req,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            string recordId = null;
            string originalText = null;
            string toLanguage = null;
            string translatedText = null;

            string requestBody = new StreamReader(req.Body).ReadToEnd();
            dynamic data = JsonConvert.DeserializeObject(requestBody);

            // Validation
            if (data?.values == null)
            {
                return new BadRequestObjectResult(" Could not find values array");
            }
            if (data?.values.HasValues == false || data?.values.First.HasValues == false)
            {
                // It could not find a record, then return empty values array.
                return new BadRequestObjectResult(" Could not find valid records in values array");
            }

            recordId = data?.values?.First?.recordId?.Value as string;
            originalText = data?.values?.First?.data?.text?.Value as string;
            toLanguage = data?.values?.First?.data?.language?.Value as string;

            if (recordId == null)
            {
                return new BadRequestObjectResult("recordId cannot be null");
            }

            translatedText = TranslateText(originalText, toLanguage).Result;
        
            // Put together response.
            WebApiResponseRecord responseRecord = new WebApiResponseRecord();
            responseRecord.data = new Dictionary<string, object>();
            responseRecord.recordId = recordId;
            responseRecord.data.Add("text", translatedText);

            WebApiEnricherResponse response = new WebApiEnricherResponse();
            response.values = new List<WebApiResponseRecord>();
            response.values.Add(responseRecord);

            return (ActionResult)new OkObjectResult(response);
        }


        /// <summary>
        /// Use Cognitive Service to translate text from one language to another.
        /// </summary>
        /// <param name="originalText">The text to translate.</param>
        /// <param name="toLanguage">The language you want to translate to.</param>
        /// <returns>Asynchronous task that returns the translated text. </returns>
        async static Task<string> TranslateText(string originalText, string toLanguage)
        {
            System.Object[] body = new System.Object[] { new { Text = originalText } };
            var requestBody = JsonConvert.SerializeObject(body);

            var uri = $"{path}&to={toLanguage}";

            string result = "";

            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(uri);
                request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                var response = await client.SendAsync(request);
                var responseBody = await response.Content.ReadAsStringAsync();

                dynamic data = JsonConvert.DeserializeObject(responseBody);
                result = data?.First?.translations?.First?.text?.Value as string;

            }
            return result;
        }
    }
}
```

Certifique-se de inserir sua própria *chave* valor o *TranslateText* método com base na chave de obteve quando inscrever-se para a API de texto a traduzir.

Neste exemplo é um enricher simple que só funciona num registro por vez. Esse fato será importante mais tarde, ao definir o tamanho do lote para o conjunto de capacidades.

## <a name="test-the-function-from-visual-studio"></a>Testar a função a partir do Visual Studio

Prima **F5** para executar os comportamentos de função de programa e teste. Neste caso usaremos a função abaixo para traduzir um texto em espanhol para inglês. Utilize o Postman ou Fiddler para emitir uma chamada como a mostrada abaixo:

```http
POST https://localhost:7071/api/Translate
```
### <a name="request-body"></a>Corpo do pedido
```json
{
   "values": [
        {
            "recordId": "a1",
            "data":
            {
               "text":  "Este es un contrato en Inglés",
               "language": "en"
            }
        }
   ]
}
```
### <a name="response"></a>Resposta
Deverá ver uma resposta semelhante ao seguinte exemplo:

```json
{
    "values": [
        {
            "recordId": "a1",
            "data": {
                "text": "This is a contract in English"
            },
            "errors": null,
            "warnings": null
        }
    ]
}
```

## <a name="publish-the-function-to-azure"></a>Publicar a função no Azure

Quando estiver satisfeito com o comportamento da função, pode publicá-lo.

1. No **Explorador de Soluções**, clique com o botão direito do rato no projeto e selecione **Publicar**. Escolher **criar novo** > **publicar**.

1. Se ainda não tiver associado o Visual Studio à sua conta do Azure, selecione **adicionar uma conta...**

1. Siga as mensagens no ecrã. É-lhe perguntado para especificar a conta do Azure, o grupo de recursos, o plano de alojamento e a conta de armazenamento que pretende utilizar. Pode criar um novo grupo de recursos, um novo plano de alojamento e uma conta de armazenamento, se ainda não tiver estas. Quando terminar, selecione **Create**

1. Depois de concluída a implementação, tenha em atenção o URL do Site. É o endereço da sua aplicação de função no Azure. 

1. Na [portal do Azure](https://portal.azure.com), navegue para o grupo de recursos e procurar a função de traduzir que publicou. Sob o **gerir** secção, deverá ver chaves de anfitrião. Selecione o **cópia** ícone para o *padrão* a chave de anfitrião.  

## <a name="test-the-function-in-azure"></a>Testar a função no Azure

Agora que tem a chave de anfitrião predefinido, testar a sua função da seguinte forma:

```http
POST https://translatecogsrch.azurewebsites.net/api/Translate?code=[enter default host key here]
```
### <a name="request-body"></a>Corpo do Pedido
```json
{
   "values": [
        {
            "recordId": "a1",
            "data":
            {
               "text":  "Este es un contrato en Inglés",
               "language": "en"
            }
        }
   ]
}
```

Isso deve produzir um resultado semelhante ao que viu anteriormente ao executar a função no ambiente local.

## <a name="connect-to-your-pipeline"></a>Ligar ao seu pipeline
Agora que tem uma habilidade personalizada nova, pode adicioná-lo para o conjunto de capacidades. O exemplo abaixo mostra como chamar a habilidade. Uma vez que a habilidade não lida com lotes, adicione uma instrução para o tamanho máximo do lote ser apenas ```1``` enviar documentos um de cada vez.

```json
{
    "skills": [
      ...,  
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "Our new translator custom skill",
        "uri": "http://translatecogsrch.azurewebsites.net/api/Translate?code=[enter default host key here]",
        "batchSize":1,
        "context": "/document",
        "inputs": [
          {
            "name": "text",
            "source": "/document/content"
          },
          {
            "name": "language",
            "source": "/document/destinationLanguage"
          }
        ],
        "outputs": [
          {
            "name": "text",
            "targetName": "translatedText"
          }
        ]
      }
  ]
}
```

## <a name="next-steps"></a>Passos Seguintes
Parabéns! Criou seu primeiro enricher personalizado. Agora, pode seguir o mesmo padrão para adicionar sua própria funcionalidade personalizada. 

+ [Adicionar uma habilidade personalizada para um pipeline de pesquisa cognitiva](cognitive-search-custom-skill-interface.md)
+ [Como definir um conjunto de capacidades](cognitive-search-defining-skillset.md)
+ [Criar conjunto de capacidades (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Como mapear campos plena](cognitive-search-output-field-mapping.md)
