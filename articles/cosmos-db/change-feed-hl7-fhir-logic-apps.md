---
title: Alterar feed para recursos de HL7 FHIR - Azure Cosmos DB
description: Saiba como configurar notificações de alteração para HL7 FHIR dos cuidados de saúde os registos dos pacientes através do Azure Logic Apps, o Azure Cosmos DB e o Service Bus.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/08/2017
ms.author: sngun
ms.openlocfilehash: 765596500e3ac294dc79f0785b12b03370fa652a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60893914"
---
# <a name="notifying-patients-of-hl7-fhir-health-care-record-changes-using-logic-apps-and-azure-cosmos-db"></a>Notificar os pacientes de alterações de cuidados de saúde registo HL7 FHIR com Logic Apps e do Azure Cosmos DB

Recentemente, fui contatado por uma organização de cuidados de saúde que gostaria de adicionar uma nova funcionalidade para o portal do doente Edidin de Howard MVP do Azure. Eles necessários para enviar notificações para pacientes, quando o respetivo registo de estado de funcionamento foi atualizado e que precisavam de pacientes para poder subscrever estas atualizações. 

Este artigo explica o solução de notificação criada para esta organização de cuidados de saúde com o Azure Cosmos DB, o Logic Apps e o Service Bus do feed de alterações. 

## <a name="project-requirements"></a>Requisitos do projeto
- Fornecedores de enviam a arquitetura de documento consolidado Clinical HL7 (C-CDA) documentos em formato XML. Documentos de C-CDA englobam praticamente todos os tipos de documento clínica, incluindo documentos clinical como entretenimento e registos de immunization, bem como administrativos, o fluxo de trabalho e documentos financeiros. 
- Documentos de C-CDA são convertidos em [HL7 FHIR recursos](https://hl7.org/fhir/2017Jan/resourcelist.html) no formato JSON.
- Documentos de recursos FHIR modificados são enviados por e-mail no formato JSON.

## <a name="solution-workflow"></a>Fluxo de trabalho da solução 

Num alto nível, o projeto necessários os seguintes passos de fluxo de trabalho: 
1. Converta os documentos de C-CDA aos recursos FHIR.
2. Execute acionador periódico de consulta para obter recursos FHIR modificados. 
2. Chame uma aplicação personalizada, FhirNotificationApi, para ligar ao Azure Cosmos DB e consulta de documentos novos ou modificados.
3. Guarde a resposta para a fila do Service Bus.
4. Consulta para novas mensagens numa fila do Service Bus.
5. Envie notificações por e-mail pacientes.

## <a name="solution-architecture"></a>Arquitetura da solução
Esta solução requer que as três aplicações de lógica cumprir os requisitos acima e concluir o fluxo de trabalho da solução. As três aplicações de lógica são:
1. **Aplicação de mapeamento de HL7 FHIR**: Recebe o documento da HL7 C-CDA, transforma-a para o recurso de FHIR e, em seguida, guarda-o no Azure Cosmos DB.
2. **Aplicação EHR**: O repositório do Azure Cosmos DB FHIR de consulta e salva a resposta a uma fila do Service Bus. Esta aplicação lógica utiliza um [aplicação API](#api-app) para obter documentos novos e alterados.
3. **Aplicação de notificação de processo**: Envia uma notificação por e-mail com os documentos de recurso FHIR no corpo.

![O Logic Apps três utilizados nesta solução de cuidados de saúde HL7 FHIR](./media/change-feed-hl7-fhir-logic-apps/health-care-solution-hl7-fhir.png)



### <a name="azure-services-used-in-the-solution"></a>Serviços do Azure utilizados na solução

#### <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB SQL API
O Azure Cosmos DB é o repositório para os recursos FHIR, conforme mostrado na figura a seguir.

![Conta do Azure Cosmos DB utilizada neste tutorial de cuidados de saúde HL7 FHIR](./media/change-feed-hl7-fhir-logic-apps/account.png)

#### <a name="logic-apps"></a>Aplicações Lógicas
Aplicações lógicas manipulam o processo de fluxo de trabalho. As capturas de ecrã seguintes mostram as aplicações de lógica que criou para esta solução. 


1. **Aplicação de mapeamento de HL7 FHIR**: Receber o documento da HL7 C-CDA e transformá-los a um recurso FHIR com o Enterprise Integration Pack para o Logic Apps. O Enterprise Integration Pack processa o mapeamento de C-CDA para recursos FHIR.

    ![A aplicação de lógica utilizada para receber registos HL7 FHIR de cuidados de saúde](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-logic-apps-json-transform.png)


2. **Aplicação EHR**: Consultar o repositório do Azure Cosmos DB FHIR e guardar a resposta a uma fila do Service Bus. O código da aplicação de GetNewOrModifiedFHIRDocuments é abaixo.

    ![A aplicação lógica utilizada para consultar o Azure Cosmos DB](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-logic-apps-api-app.png)

3. **Aplicação de notificação de processo**: Envie uma notificação por e-mail com os documentos de recurso FHIR no corpo.

    ![A aplicação lógica que envia um e-mail de doentes com o recurso de HL7 FHIR no corpo](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-logic-apps-send-email.png)

#### <a name="service-bus"></a>Service Bus
A figura seguinte mostra os pacientes fila. O valor da propriedade Tag é utilizado para o assunto do e-mail.

![A fila de barramento de serviço utilizado neste tutorial HL7 FHIR](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-service-bus-queue.png)

<a id="api-app"></a>

#### <a name="api-app"></a>Aplicação de API
Uma aplicação API estabelece ligação ao Azure Cosmos DB e consultas para documentos FHIR novos ou modificados por tipo de recurso. Esta aplicação tem um controlador, **FhirNotificationApi** com uma operação de um **GetNewOrModifiedFhirDocuments**, consulte [origem para a aplicação de API](#api-app-source).

Estamos a utilizar o [ `CreateDocumentChangeFeedQuery` ](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentchangefeedquery.aspx) classe a partir da API de .NET de SQL do Azure Cosmos DB. Para obter mais informações, consulte a [artigo de feed de alterações](change-feed.md). 

##### <a name="getnewormodifiedfhirdocuments-operation"></a>Operação de GetNewOrModifiedFhirDocuments

**entradas**
- DatabaseId
- CollectionId
- Nome do tipo de recurso do HL7 FHIR
- Booleano: Começar do início
- INT: Número de documentos devolvidos

**Saídas**
- Êxito: Código de estado: 200, resposta: Lista de documentos (matriz JSON)
- Falha de: Código de estado: 404, resposta: "Documentos não encontrada para '*nome do recurso"* tipo de recurso "

<a id="api-app-source"></a>

**Origem da aplicação de API**

```csharp

    using System.Collections.Generic;
    using System.Linq;
    using System.Net;
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Web.Http;
    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Swashbuckle.Swagger.Annotations;
    using TRex.Metadata;
    
    namespace FhirNotificationApi.Controllers
    {
        /// <summary>
        ///     FHIR Resource Type Controller
        /// </summary>
        /// <seealso cref="System.Web.Http.ApiController" />
        public class FhirResourceTypeController : ApiController
        {
            /// <summary>
            ///     Gets the new or modified FHIR documents from Last Run Date 
            ///     or create date of the collection
            /// </summary>
            /// <param name="databaseId"></param>
            /// <param name="collectionId"></param>
            /// <param name="resourceType"></param>
            /// <param name="startfromBeginning"></param>
            /// <param name="maximumItemCount">-1 returns all (default)</param>
            /// <returns></returns>
            [Metadata("Get New or Modified FHIR Documents",
                "Query for new or modified FHIR Documents By Resource Type " +
                "from Last Run Date or Beginning of Collection creation"
            )]
            [SwaggerResponse(HttpStatusCode.OK, type: typeof(Task<dynamic>))]
            [SwaggerResponse(HttpStatusCode.NotFound, "No New or Modified Documents found")]
            [SwaggerOperation("GetNewOrModifiedFHIRDocuments")]
            public async Task<dynamic> GetNewOrModifiedFhirDocuments(
                [Metadata("Database Id", "Database Id")] string databaseId,
                [Metadata("Collection Id", "Collection Id")] string collectionId,
                [Metadata("Resource Type", "FHIR resource type name")] string resourceType,
                [Metadata("Start from Beginning ", "Change Feed Option")] bool startfromBeginning,
                [Metadata("Maximum Item Count", "Number of documents returned. '-1 returns all' (default)")] int maximumItemCount = -1
            )
            {
                var collectionLink = UriFactory.CreateDocumentCollectionUri(databaseId, collectionId);
    
                var context = new DocumentDbContext();  
    
                var docs = new List<dynamic>();
    
                var partitionKeyRanges = new List<PartitionKeyRange>();
                FeedResponse<PartitionKeyRange> pkRangesResponse;
    
                do
                {
                    pkRangesResponse = await context.Client.ReadPartitionKeyRangeFeedAsync(collectionLink);
                    partitionKeyRanges.AddRange(pkRangesResponse);
                } while (pkRangesResponse.ResponseContinuation != null);
    
                foreach (var pkRange in partitionKeyRanges)
                {
                    var changeFeedOptions = new ChangeFeedOptions
                    {
                        StartFromBeginning = startfromBeginning,
                        RequestContinuation = null,
                        MaxItemCount = maximumItemCount,
                        PartitionKeyRangeId = pkRange.Id
                    };
    
                    using (var query = context.Client.CreateDocumentChangeFeedQuery(collectionLink, changeFeedOptions))
                    {
                        do
                        {
                            if (query != null)
                            {
                                var results = await query.ExecuteNextAsync<dynamic>().ConfigureAwait(false);
                                if (results.Count > 0)
                                    docs.AddRange(results.Where(doc => doc.resourceType == resourceType));
                            }
                            else
                            {
                                throw new HttpResponseException(new HttpResponseMessage(HttpStatusCode.NotFound));
                            }
                        } while (query.HasMoreResults);
                    }
                }
                if (docs.Count > 0)
                    return docs;
                var msg = new StringContent("No documents found for " + resourceType + " Resource");
                var response = new HttpResponseMessage
                {
                    StatusCode = HttpStatusCode.NotFound,
                    Content = msg
                };
                return response;
            }
        }
    }
    
```

### <a name="testing-the-fhirnotificationapi"></a>Teste o FhirNotificationApi 

A imagem seguinte demonstra como swagger foi utilizado para testar a [FhirNotificationApi](#api-app-source).

![O ficheiro de Swagger utilizado para testar a aplicação de API](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-testing-app.png)


### <a name="azure-portal-dashboard"></a>Dashboard do portal do Azure

A imagem seguinte mostra todos os serviços do Azure para esta solução em execução no portal do Azure.

![Portal do Azure mostra todos os serviços utilizados neste tutorial HL7 FHIR](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-portal.png)


## <a name="summary"></a>Resumo

- Sabe que o Azure Cosmos DB tem suporte nativo para notificações para documentos novos ou modificados e como é fácil de utilizar. 
- Ao tirar partido das aplicações lógicas, pode criar fluxos de trabalho sem escrever nenhum código.
- Utilizar filas do Service Bus do Azure para processar a distribuição para os documentos de HL7 FHIR.

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre o Azure Cosmos DB, consulte a [home page do Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). Para obter mais informações sobre o Logic Apps, consulte [Logic Apps](https://azure.microsoft.com/services/logic-apps/).


