---
title: Exemplo de habilidade personalizada usando API de Pesquisa de Entidade Bing
titleSuffix: Azure Cognitive Search
description: Demonstra a utilização do serviço de pesquisa de entidades Bing numa habilidade personalizada mapeada para um pipeline de indexação enriquecido em IA em Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 5755e14e53d359fd8b322939bf1325d21536d593
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89020189"
---
# <a name="example-create-a-custom-skill-using-the-bing-entity-search-api"></a>Exemplo: Criar uma habilidade personalizada utilizando a API de Pesquisa de Entidade Bing

Neste exemplo, aprenda a criar uma habilidade personalizada da API web. Esta habilidade aceitará locais, figuras públicas e organizações, e devolver-lhes-á descrições. O exemplo utiliza uma [Função Azure](https://azure.microsoft.com/services/functions/) para embrulhar a [API de Pesquisa de Entidade Bing](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/) para que implemente a interface de habilidade personalizada.

## <a name="prerequisites"></a>Pré-requisitos

+ Leia sobre o artigo [de interface de habilidade personalizada](cognitive-search-custom-skill-interface.md) se não estiver familiarizado com a interface entrada/saída que uma habilidade personalizada deve implementar.

+ [!INCLUDE [cognitive-services-bing-entity-search-signup-requirements](../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

+ Instale [o Visual Studio 2019](https://www.visualstudio.com/vs/) ou mais tarde, incluindo a carga de trabalho de desenvolvimento do Azure.

## <a name="create-an-azure-function"></a>Criar uma Função do Azure

Embora este exemplo utilize uma Função Azure para hospedar uma API web, não é necessário.  Desde que cumpra os requisitos de [interface para uma habilidade cognitiva,](cognitive-search-custom-skill-interface.md)a abordagem que toma é irrelevante. As Funções Azure, no entanto, facilitam a criação de uma habilidade personalizada.

### <a name="create-a-function-app"></a>Criar uma aplicação de funções

1. No Visual Studio, selecione **New** > **Project** (Novo Projeto) no menu File (Ficheiro).

1. No diálogo do Novo Projeto, selecione **Instalar,** expandir a Nuvem **Visual C.**  >  , selecione **Funções Azure,** digite um Nome para o seu projeto e selecione **OK**. O nome da aplicação de função deve ser válido como um espaço de nome C#, por isso não utilize sublinhados, hífens ou quaisquer outros caracteres não alfanuméricos.

1. Selecione **Azure Functions v2 (.NET Core)**. Também pode fazê-lo com a versão 1, mas o código escrito abaixo baseia-se no modelo v2.

1. Selecione o tipo para ser **HTTP Trigger**

1. Para conta de armazenamento, pode selecionar **Nenhum**, uma vez que não necessitará de armazenamento para esta função.

1. Selecione **OK** para criar o projeto de função e a função HTTP desencadeou.

### <a name="modify-the-code-to-call-the-bing-entity-search-service"></a>Modifique o código para chamar o Serviço de Pesquisa de Entidade Bing

O Visual Studio cria um projeto e neste uma classe que contém o código automático de tipo de função de escolhida. O atributo *FunctionName* nos conjuntos define o nome da função. O atributo *HttpTrigger* especifica que a função é acionada por um pedido de HTTP.

Agora, substitua todo o conteúdo do ficheiro *Função1.cs* pelo seguinte código:

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

Certifique-se de inserir o seu próprio valor *chave* na `key` constante com base na chave que obteve ao inscrever-se na API de pesquisa da entidade Bing.

Esta amostra inclui todo o código necessário num único ficheiro por conveniência. Pode encontrar uma versão um pouco mais estruturada dessa mesma habilidade [no repositório de habilidades de potência.](https://github.com/Azure-Samples/azure-search-power-skills/tree/master/Text/BingEntitySearch)

Claro que pode mudar o nome do ficheiro `Function1.cs` `BingEntitySearch.cs` de.

## <a name="test-the-function-from-visual-studio"></a>Teste a função do Visual Studio

Prima **F5** para executar os comportamentos de função do programa e do teste. Neste caso, usaremos a função abaixo para procurar duas entidades. Use o Carteiro ou o Violinista para emitir uma chamada como a mostrada abaixo:

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

1. No **Explorador de Soluções**, clique com o botão direito do rato no projeto e selecione **Publicar**. Escolha **Criar Nova**  >  **Publicação.**

1. Se ainda não ligou o Visual Studio à sua conta Azure, selecione **Adicionar uma conta....**

1. Siga as instruções no ecrã. É-lhe pedido que especifique um nome único para o seu serviço de aplicações, a subscrição do Azure, o grupo de recursos, o plano de hospedagem e a conta de armazenamento que pretende utilizar. Você pode criar um novo grupo de recursos, um novo plano de hospedagem, e uma conta de armazenamento se você não tiver isso. Quando terminar, **selecione Criar**

1. Depois da implementação estar concluída, note o URL do site. É o endereço da sua aplicação de função em Azure. 

1. No [portal Azure,](https://portal.azure.com)navegue para o Grupo de Recursos e procure a `EntitySearch` Função que publicou. Sob a secção **'Gerir',** deverá ver as Teclas do anfitrião. Selecione o ícone **Copiar** para a tecla de anfitrião *predefinido.*  

## <a name="test-the-function-in-azure"></a>Teste a função em Azure

Agora que tem a chave de anfitrião predefinido, teste a sua função da seguinte forma:

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
Agora que tens uma nova habilidade personalizada, podes adicioná-la à tua habilidade. O exemplo abaixo mostra como chamar a habilidade para adicionar descrições às organizações no documento (isto poderia ser estendido para também trabalhar em locais e pessoas). `[your-entity-search-app-name]`Substitua-o pelo nome da sua aplicação.

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

Aqui, contamos com a capacidade de reconhecimento de [entidades incorporadas](cognitive-search-skill-entity-recognition.md) para estar presente no skillset e ter enriquecido o documento com a lista de organizações. Para referência, aqui está uma configuração de habilidades de extração de entidades que seria suficiente para gerar os dados de que precisamos:

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
+ [Como definir um skillset](cognitive-search-defining-skillset.md)
+ [Criar Skillset (REST)](/rest/api/searchservice/create-skillset)
+ [Como mapear campos enriquecidos](cognitive-search-output-field-mapping.md)