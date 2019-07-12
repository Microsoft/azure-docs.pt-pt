---
title: 'Exemplo: Criar uma habilidade cognitiva personalizada com a API de pesquisa de entidades do Bing - Azure Search'
description: Demonstra como utilizar o serviço de pesquisa de entidades do Bing numa habilidade personalizada, mapeada para um pipeline de indexação de pesquisa cognitiva no Azure Search.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 7d90f46ada9b9453b4c1516a4a898456dc73b8e7
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672148"
---
# <a name="example-create-a-custom-skill-using-the-bing-entity-search-api"></a>Exemplo: Criar uma habilidade personalizada utilizando a API de pesquisa de entidades do Bing

Neste exemplo, saiba como criar uma habilidade personalizada da API da web. Essa habilidade irá aceitar localizações, público figuras e organizações e retornar as descrições para os mesmos. O exemplo utiliza um [função do Azure](https://azure.microsoft.com/services/functions/) encapsular o [API de pesquisa de entidades do Bing](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/) para que ele implementa a interface de competências personalizadas.

## <a name="prerequisites"></a>Pré-requisitos

+ Leia sobre [interface de competências personalizadas](cognitive-search-custom-skill-interface.md) artigo se não estiver familiarizado com a interface de entrada/saída que uma habilidade personalizada deve implementar.

+ [!INCLUDE [cognitive-services-bing-entity-search-signup-requirements](../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

+ Instale [Visual Studio 2019](https://www.visualstudio.com/vs/) ou posterior, incluindo a carga de trabalho de desenvolvimento do Azure.

## <a name="create-an-azure-function"></a>Criar uma Função do Azure

Embora este exemplo utiliza uma função do Azure para alojar uma API web, ele não é necessário.  Desde que tenha em atenção a [interface requisitos para uma habilidade cognitivo](cognitive-search-custom-skill-interface.md), a abordagem de é irrelevante. As funções do Azure, no entanto, tornam mais fácil criar uma habilidade personalizada.

### <a name="create-a-function-app"></a>Criar uma aplicação de função

1. No Visual Studio, selecione **New** > **projeto** no menu File.

1. Na caixa de diálogo novo projeto, selecione **instalada**, expanda **Visual c#**  > **Cloud**, selecione **as funções do Azure**, escreva um Dê um nome para o seu projeto e selecione **OK**. O nome da aplicação de função tem de ser válido como um C# espaço de nomes, portanto, não use carateres de sublinhado, hífenes ou quaisquer outros carateres não alfanuméricos.

1. Selecione **v2 das funções do Azure (.NET Core)** . Também pode fazê-lo com a versão 1, mas o código escrito abaixo baseia-se no modelo v2.

1. Selecione o tipo a ser **acionador HTTP**

1. Para a conta de armazenamento, pode selecionar **None**, como não precisa de qualquer armazenamento para esta função.

1. Selecione **OK** para criar a função de projeto e HTTP função acionada por um.

### <a name="modify-the-code-to-call-the-bing-entity-search-service"></a>Modificar o código para chamar o serviço de pesquisa de entidades do Bing

O Visual Studio cria um projeto e neste uma classe que contém o código automático de tipo de função de escolhida. O atributo *FunctionName* nos conjuntos define o nome da função. O atributo *HttpTrigger* especifica que a função é acionada por um pedido de HTTP.

Agora, substitua todo o conteúdo do ficheiro *Function1.cs* com o código a seguir:

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;

namespace SampleSkills
{
    /// <summary>
    /// Sample custom skill that wraps the Bing entity search API to connect it with a 
    /// cognitive search pipeline.
    /// </summary>
    public static class BingEntitySearch
    {
        #region Credentials
        // IMPORTANT: Make sure to enter your credential and to verify the API endpoint matches yours.
        static readonly string bingApiEndpoint = "https://api.cognitive.microsoft.com/bing/v7.0/entities/";
        static readonly string key = "<enter your api key here>";  
        #endregion

        #region Class used to deserialize the request
        private class InputRecord
        {
            public class InputRecordData
            {
                public string Name { get; set; }
            }

            public string RecordId { get; set; }
            public InputRecordData Data { get; set; }
        }

        private class WebApiRequest
        {
            public List<InputRecord> Values { get; set; }
        }
        #endregion

        #region Classes used to serialize the response

        private class OutputRecord
        {
            public class OutputRecordData
            {
                public string Name { get; set; } = "";
                public string Description { get; set; } = "";
                public string Source { get; set; } = "";
                public string SourceUrl { get; set; } = "";
                public string LicenseAttribution { get; set; } = "";
                public string LicenseUrl { get; set; } = "";
            }

            public class OutputRecordMessage
            {
                public string Message { get; set; }
            }

            public string RecordId { get; set; }
            public OutputRecordData Data { get; set; }
            public List<OutputRecordMessage> Errors { get; set; }
            public List<OutputRecordMessage> Warnings { get; set; }
        }

        private class WebApiResponse
        {
            public List<OutputRecord> Values { get; set; }
        }
        #endregion

        #region Classes used to interact with the Bing API
        private class BingResponse
        {
            public BingEntities Entities { get; set; }
        }
        private class BingEntities
        {
            public BingEntity[] Value { get; set; }
        }

        private class BingEntity
        {
            public class EntityPresentationinfo
            {
                public string[] EntityTypeHints { get; set; }
            }

            public class License
            {
                public string Url { get; set; }
            }

            public class ContractualRule
            {
                public string _type { get; set; }
                public License License { get; set; }
                public string LicenseNotice { get; set; }
                public string Text { get; set; }
                public string Url { get; set; }
            }

            public ContractualRule[] ContractualRules { get; set; }
            public string Description { get; set; }
            public string Name { get; set; }
            public EntityPresentationinfo EntityPresentationInfo { get; set; }
        }
        #endregion

        #region The Azure Function definition

        [FunctionName("EntitySearch")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("Entity Search function: C# HTTP trigger function processed a request.");

            var response = new WebApiResponse
            {
                Values = new List<OutputRecord>()
            };

            string requestBody = new StreamReader(req.Body).ReadToEnd();
            var data = JsonConvert.DeserializeObject<WebApiRequest>(requestBody);

            // Do some schema validation
            if (data == null)
            {
                return new BadRequestObjectResult("The request schema does not match expected schema.");
            }
            if (data.Values == null)
            {
                return new BadRequestObjectResult("The request schema does not match expected schema. Could not find values array.");
            }

            // Calculate the response for each value.
            foreach (var record in data.Values)
            {
                if (record == null || record.RecordId == null) continue;

                OutputRecord responseRecord = new OutputRecord
                {
                    RecordId = record.RecordId
                };

                try
                {
                    responseRecord.Data = GetEntityMetadata(record.Data.Name).Result;
                }
                catch (Exception e)
                {
                    // Something bad happened, log the issue.
                    var error = new OutputRecord.OutputRecordMessage
                    {
                        Message = e.Message
                    };

                    responseRecord.Errors = new List<OutputRecord.OutputRecordMessage>
                    {
                        error
                    };
                }
                finally
                {
                    response.Values.Add(responseRecord);
                }
            }

            return (ActionResult)new OkObjectResult(response);
        }

        #endregion

        #region Methods to call the Bing API
        /// <summary>
        /// Gets metadata for a particular entity based on its name using Bing Entity Search
        /// </summary>
        /// <param name="entityName">The name of the entity to extract data for.</param>
        /// <returns>Asynchronous task that returns entity data. </returns>
        private async static Task<OutputRecord.OutputRecordData> GetEntityMetadata(string entityName)
        {
            var uri = bingApiEndpoint + "?q=" + entityName + "&mkt=en-us&count=10&offset=0&safesearch=Moderate";
            var result = new OutputRecord.OutputRecordData();

            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage {
                Method = HttpMethod.Get,
                RequestUri = new Uri(uri)
            })
            {
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                HttpResponseMessage response = await client.SendAsync(request);
                string responseBody = await response?.Content?.ReadAsStringAsync();

                BingResponse bingResult = JsonConvert.DeserializeObject<BingResponse>(responseBody);
                if (bingResult != null)
                {
                    // In addition to the list of entities that could match the name, for simplicity let's return information
                    // for the top match as additional metadata at the root object.
                    return AddTopEntityMetadata(bingResult.Entities?.Value);
                }
            }

            return result;
        }

        private static OutputRecord.OutputRecordData AddTopEntityMetadata(BingEntity[] entities)
        {
            if (entities != null)
            {
                foreach (BingEntity entity in entities.Where(
                    entity => entity?.EntityPresentationInfo?.EntityTypeHints != null
                        && (entity.EntityPresentationInfo.EntityTypeHints[0] == "Person"
                            || entity.EntityPresentationInfo.EntityTypeHints[0] == "Organization"
                            || entity.EntityPresentationInfo.EntityTypeHints[0] == "Location")
                        && !String.IsNullOrEmpty(entity.Description)))
                {
                    var rootObject = new OutputRecord.OutputRecordData
                    {
                        Description = entity.Description,
                        Name = entity.Name
                    };

                    if (entity.ContractualRules != null)
                    {
                        foreach (var rule in entity.ContractualRules)
                        {
                            switch (rule._type)
                            {
                                case "ContractualRules/LicenseAttribution":
                                    rootObject.LicenseAttribution = rule.LicenseNotice;
                                    rootObject.LicenseUrl = rule.License.Url;
                                    break;
                                case "ContractualRules/LinkAttribution":
                                    rootObject.Source = rule.Text;
                                    rootObject.SourceUrl = rule.Url;
                                    break;
                            }
                        }
                    }

                    return rootObject;
                }
            }

            return new OutputRecord.OutputRecordData();
        }
        #endregion
    }
}
```

Certifique-se de inserir sua própria *chave* valor o `key` constante com base na chave de obteve quando inscrever-se para a API de pesquisa de entidades do Bing.

Este exemplo inclui todo o código necessário num único arquivo para sua comodidade. Pode encontrar uma versão ligeiramente mais estruturada dessa habilidade mesmo na [o repositório de habilidades power](https://github.com/Azure-Samples/azure-search-power-skills/tree/master/Text/BingEntitySearch).

É claro, pode mudar o nome do ficheiro a partir `Function1.cs` para `BingEntitySearch.cs`.

## <a name="test-the-function-from-visual-studio"></a>Testar a função a partir do Visual Studio

Prima **F5** para executar os comportamentos de função de programa e teste. Neste caso, usaremos a função abaixo para pesquisar a duas entidades. Utilize o Postman ou Fiddler para emitir uma chamada como a mostrada abaixo:

```http
POST https://localhost:7071/api/EntitySearch
```

### <a name="request-body"></a>Corpo do pedido
```json
{
    "values": [
        {
            "recordId": "e1",
            "data":
            {
                "name":  "Pablo Picasso"
            }
        },
        {
            "recordId": "e2",
            "data":
            {
                "name":  "Microsoft"
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
            "recordId": "e1",
            "data": {
                "name": "Pablo Picasso",
                "description": "Pablo Ruiz Picasso was a Spanish painter [...]",
                "source": "Wikipedia",
                "sourceUrl": "http://en.wikipedia.org/wiki/Pablo_Picasso",
                "licenseAttribution": "Text under CC-BY-SA license",
                "licenseUrl": "http://creativecommons.org/licenses/by-sa/3.0/"
            },
            "errors": null,
            "warnings": null
        },
        "..."
    ]
}
```

## <a name="publish-the-function-to-azure"></a>Publicar a função no Azure

Quando estiver satisfeito com o comportamento da função, pode publicá-lo.

1. No **Explorador de Soluções**, clique com o botão direito do rato no projeto e selecione **Publicar**. Escolher **criar novo** > **publicar**.

1. Se ainda não tiver associado o Visual Studio à sua conta do Azure, selecione **adicionar uma conta...**

1. Siga as mensagens no ecrã. For solicitado a especificar um nome exclusivo para o serviço de aplicações, a subscrição do Azure, o grupo de recursos, o plano de alojamento e a conta de armazenamento que pretende utilizar. Pode criar um novo grupo de recursos, um novo plano de alojamento e uma conta de armazenamento, se ainda não tiver estas. Quando terminar, selecione **Create**

1. Depois de concluída a implementação, tenha em atenção o URL do Site. É o endereço da sua aplicação de função no Azure. 

1. Na [portal do Azure](https://portal.azure.com), navegue para o grupo de recursos e procure o `EntitySearch` função que publicou. Sob o **gerir** secção, deverá ver chaves de anfitrião. Selecione o **cópia** ícone para o *padrão* a chave de anfitrião.  

## <a name="test-the-function-in-azure"></a>Testar a função no Azure

Agora que tem a chave de anfitrião predefinido, testar a sua função da seguinte forma:

```http
POST https://[your-entity-search-app-name].azurewebsites.net/api/EntitySearch?code=[enter default host key here]
```

### <a name="request-body"></a>Corpo do Pedido
```json
{
    "values": [
        {
            "recordId": "e1",
            "data":
            {
                "name":  "Pablo Picasso"
            }
        },
        {
            "recordId": "e2",
            "data":
            {
                "name":  "Microsoft"
            }
        }
    ]
}
```

Neste exemplo deve produzir o mesmo resultado que viu anteriormente ao executar a função no ambiente local.

## <a name="connect-to-your-pipeline"></a>Ligar ao seu pipeline
Agora que tem uma habilidade personalizada nova, pode adicioná-lo para o conjunto de capacidades. O exemplo abaixo mostra como chamar a capacidade de adicionar descrições para as organizações no documento (isso pode ser estendido para também funcionam em localizações e as pessoas). Substitua `[your-entity-search-app-name]` com o nome da sua aplicação.

```json
{
    "skills": [
      "[... your existing skills remain here]",  
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "Our new Bing entity search custom skill",
        "uri": "https://[your-entity-search-app-name].azurewebsites.net/api/EntitySearch?code=[enter default host key here]",
          "context": "/document/merged_content/organizations/*",
          "inputs": [
            {
              "name": "name",
              "source": "/document/merged_content/organizations/*"
            }
          ],
          "outputs": [
            {
              "name": "description",
              "targetName": "description"
            }
          ]
      }
  ]
}
```

Aqui, podemos está contando com o incorporado [habilidade de reconhecimento de entidades](cognitive-search-skill-entity-recognition.md) estar presente das competências e para ter enriquecida o documento com a lista de organizações. Para referência, eis uma configuração de habilidades de extração de entidades que seria o suficiente na geração dos dados que precisamos:

```json
{
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "name": "#1",
    "description": "Organization name extraction",
    "context": "/document/merged_content",
    "categories": [ "Organization" ],
    "defaultLanguageCode": "en",
    "inputs": [
        {
            "name": "text",
            "source": "/document/merged_content"
        },
        {
            "name": "languageCode",
            "source": "/document/language"
        }
    ],
    "outputs": [
        {
            "name": "organizations",
            "targetName": "organizations"
        }
    ]
},
```

## <a name="next-steps"></a>Passos Seguintes
Parabéns! Criou seu primeiro enricher personalizado. Agora, pode seguir o mesmo padrão para adicionar sua própria funcionalidade personalizada. 

+ [Adicionar uma habilidade personalizada para um pipeline de pesquisa cognitiva](cognitive-search-custom-skill-interface.md)
+ [Como definir um conjunto de capacidades](cognitive-search-defining-skillset.md)
+ [Criar conjunto de capacidades (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Como mapear campos plena](cognitive-search-output-field-mapping.md)
