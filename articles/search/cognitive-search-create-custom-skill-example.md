---
title: Exemplo de habilidade personalizada usando API de pesquisa de entidade bing
titleSuffix: Azure Cognitive Search
description: Demonstra o uso do serviço de pesquisa de entidades Bing numa habilidade personalizada mapeada para um pipeline de indexação enriquecido em IA em Pesquisa Cognitiva Azure.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 2994c55b39d30ff16a0ca135e93a116784feb201
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74113814"
---
# <a name="example-create-a-custom-skill-using-the-bing-entity-search-api"></a>Exemplo: Criar uma habilidade personalizada utilizando a API de Pesquisa de Entidades Bing

Neste exemplo, aprenda a criar uma habilidade personalizada da Web API. Esta habilidade irá aceitar locais, figuras públicas e organizações, e devolver descrições para eles. O exemplo utiliza uma [Função Azure](https://azure.microsoft.com/services/functions/) para embrulhar a API de Pesquisa de [Entidades Bing](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/) para que implemente a interface de habilidadepersonalizada.

## <a name="prerequisites"></a>Pré-requisitos

+ Leia sobre o artigo de interface de [habilidadepersonalizada](cognitive-search-custom-skill-interface.md) se não estiver familiarizado com a interface de entrada/saída que uma habilidade personalizada deve implementar.

+ [!INCLUDE [cognitive-services-bing-entity-search-signup-requirements](../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

+ Instale o [Visual Studio 2019](https://www.visualstudio.com/vs/) ou mais tarde, incluindo a carga de trabalho de desenvolvimento do Azure.

## <a name="create-an-azure-function"></a>Criar uma Função do Azure

Embora este exemplo utilize uma Função Azure para alojar uma API web, não é necessário.  Desde que cumpra os requisitos de [interface para uma habilidade cognitiva,](cognitive-search-custom-skill-interface.md)a abordagem que toma é imaterial. As Funções Azure, no entanto, facilitam a criação de uma habilidade personalizada.

### <a name="create-a-function-app"></a>Criar uma aplicação de função

1. No Estúdio Visual, selecione **New** > **Project** a partir do menu File.

1. No diálogo New Project, selecione **Instalado**, expanda **Visual C#** > **Cloud,** selecione **Funções Azure,** escreva um nome para o seu projeto e selecione **OK**. O nome da aplicação de função deve ser válido como um espaço de nome C#, por isso não utilize sublinhados, hífens ou quaisquer outros caracteres não alfanuméricos.

1. Selecione **Funções Azure v2 (.NET Core)**. Também pode fazê-lo com a versão 1, mas o código escrito abaixo baseia-se no modelo v2.

1. Selecione o tipo para ser **HTTP Trigger**

1. Para a Conta de Armazenamento, pode selecionar **Nenhuma,** uma vez que não necessitará de qualquer armazenamento para esta função.

1. Selecione **OK** para criar o projeto de função e função ativada HTTP.

### <a name="modify-the-code-to-call-the-bing-entity-search-service"></a>Modificar o código para ligar para o Serviço de Pesquisa de Entidades Bing

O Visual Studio cria um projeto e neste uma classe que contém o código automático de tipo de função de escolhida. O atributo *FunctionName* nos conjuntos define o nome da função. O atributo *HttpTrigger* especifica que a função é acionada por um pedido de HTTP.

Agora, substitua todo o conteúdo do ficheiro *Function1.cs* pelo seguinte código:

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
    /// AI enrichment pipeline.
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

Certifique-se de introduzir o `key` seu próprio *valor-chave* na constante com base na chave que obteve ao inscrever-se na API da entidade Bing.

Esta amostra inclui todo o código necessário num único ficheiro por conveniência. Pode encontrar uma versão um pouco mais estruturada dessa mesma habilidade no [repositório de habilidades de poder.](https://github.com/Azure-Samples/azure-search-power-skills/tree/master/Text/BingEntitySearch)

Claro, pode mudar o nome `Function1.cs` `BingEntitySearch.cs`do ficheiro de .

## <a name="test-the-function-from-visual-studio"></a>Teste a função do Estúdio Visual

Pressione **F5** para executar os comportamentos da função do programa e teste. Neste caso, usaremos a função abaixo para procurar duas entidades. Use o Carteiro ou o Violinista para emitir uma chamada como a que se encontra abaixo:

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
Deve ver uma resposta semelhante ao seguinte exemplo:

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

1. No **Explorador de Soluções**, clique com o botão direito do rato no projeto e selecione **Publicar**. Escolha **criar nova** > **publicação.**

1. Se ainda não ligou o Visual Studio à sua conta Azure, selecione **Adicionar uma conta....**

1. Siga as instruções no ecrã. É-lhe pedido que especifique um nome único para o seu serviço de aplicações, a subscrição do Azure, o grupo de recursos, o plano de hospedagem e a conta de armazenamento que pretende utilizar. Você pode criar um novo grupo de recursos, um novo plano de hospedagem, e uma conta de armazenamento se você ainda não tem estes. Quando terminar, selecione **Criar**

1. Depois de concluída a implementação, note o URL do Site. É o endereço da sua app de funções em Azure. 

1. No [portal Azure,](https://portal.azure.com)navegue para o Grupo `EntitySearch` de Recursos e procure a Função que publicou. Na secção **Gerir,** deve ver as Teclas do Anfitrião. Selecione o ícone **Copiar** para a tecla de hospedar *predefinida.*  

## <a name="test-the-function-in-azure"></a>Teste a função em Azure

Agora que tem a chave de acolhimento padrão, teste a sua função da seguinte forma:

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

Este exemplo deve produzir o mesmo resultado que viu anteriormente ao executar a função no ambiente local.

## <a name="connect-to-your-pipeline"></a>Ligue-se ao seu oleoduto
Agora que tens uma nova habilidade personalizada, podes adicioná-la à tua habilidade. O exemplo abaixo mostra como chamar a habilidade para adicionar descrições às organizações no documento (isto poderia ser estendido para também trabalhar em locais e pessoas). Substitua-o `[your-entity-search-app-name]` pelo nome da sua aplicação.

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

Aqui, contamos com a habilidade de reconhecimento de [entidades](cognitive-search-skill-entity-recognition.md) incorporadas para estar presente no skillset e ter enriquecido o documento com a lista de organizações. Para referência, aqui está uma configuração de habilidade de extração de entidade que seria suficiente para gerar os dados que precisamos:

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

## <a name="next-steps"></a>Passos seguintes
Parabéns! Criaste a tua primeira habilidade personalizada. Agora pode seguir o mesmo padrão para adicionar a sua própria funcionalidade personalizada. Clique nos seguintes links para saber mais.

+ [Power Skills: um repositório de habilidades personalizadas](https://github.com/Azure-Samples/azure-search-power-skills)
+ [Adicione uma habilidade personalizada a um oleoduto de enriquecimento de IA](cognitive-search-custom-skill-interface.md)
+ [Como definir um conjunto de habilidades](cognitive-search-defining-skillset.md)
+ [Criar Skillset (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Como mapear campos enriquecidos](cognitive-search-output-field-mapping.md)
