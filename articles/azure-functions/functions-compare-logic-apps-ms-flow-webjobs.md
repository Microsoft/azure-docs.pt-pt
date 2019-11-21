---
title: What are Microsoft Flow, Logic Apps, Functions, and WebJobs? - Azure
description: 'Compare Microsoft cloud services that are optimized for integration tasks: Microsoft Flow, Logic Apps, Functions, and WebJobs.'
ms.topic: overview
ms.date: 04/09/2018
ms.custom: mvc
ms.openlocfilehash: 6cf820af8a5b97de536a9d07e49ec822ffb76681
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230879"
---
# <a name="what-are-microsoft-flow-logic-apps-functions-and-webjobs"></a>What are Microsoft Flow, Logic Apps, Functions, and WebJobs?

This article compares the following Microsoft cloud 
* [Microsoft Flow](https://flow.microsoft.com/)
* [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Funções do Azure](https://azure.microsoft.com/services/functions/)
* [WebJobs do Serviço de Aplicações do Azure ](../app-service/webjobs-create.md)

Todos estes serviços podem resolver problemas de integração e automatizar processos comerciais. Podem todos definir entradas, ações, condições e saídas. Pode executar cada um com base numa agenda ou num acionador. Each service has unique advantages, and this article explains the differences. 

If you're looking for a more general comparison between Azure Functions and other Azure compute options, see [Criteria for choosing an Azure compute service](/azure/architecture/guide/technology-choices/compute-comparison) and [Choosing an Azure compute option for microservices](/azure/architecture/microservices/design/compute-options).

## <a name="compare-microsoft-flow-and-azure-logic-apps"></a>Comparar o Microsoft Flow e o Azure Logic Apps

Microsoft Flow and Logic Apps are both *designer-first* integration services that can create workflows. Ambos os serviços integram-se com diversas aplicações empresariais e SaaS. 

Microsoft Flow is built on top of Logic Apps. They share the same workflow designer and the same [connectors](../connectors/apis-list.md). 

Microsoft Flow empowers any office worker to perform simple integrations (for example, an approval process on a SharePoint Document Library) without going through developers or IT. Logic Apps can also enable advanced integrations (for example, B2B processes) where enterprise-level Azure DevOps and security practices are required. É normal um fluxo de trabalho do negócio se tornar cada vez mais complexo ao longo do tempo. Accordingly, you can start with a flow at first, and then convert it to a logic app as needed.

The following table helps you determine whether Microsoft Flow or Logic Apps is best for a particular integration:

|  | Microsoft Flow | Logic Apps |
| --- | --- | --- |
| Utilizadores |Trabalhadores do escritório, utilizadores empresariais, administradores do SharePoint |Integradores e programadores profissionais, profissionais de TI |
| Cenários |Self-service |Integrações avançadas |
| Design tool |Aplicação no browser e móvel, apenas IU |No browser e [Visual Studio](../logic-apps/logic-apps-deploy-from-vs.md), [Vista de código](../logic-apps/logic-apps-author-definitions.md) disponível |
| Application lifecycle management (ALM) |Design and test in non-production environments, promote to production when ready |Azure DevOps: source control, testing, support, automation, and manageability in [Azure Resource Manager](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md) |
| Admin experience |Manage Microsoft Flow environments and data loss prevention (DLP) policies, track licensing: [Microsoft Flow Admin Center](https://admin.flow.microsoft.com) |Manage resource groups, connections, access management, and logging: [Azure portal](https://portal.azure.com) |
| Segurança |Office 365 Security and Compliance audit logs, DLP, [encryption at rest](https://wikipedia.org/wiki/Data_at_rest#Encryption) for sensitive data |Security assurance of Azure: [Azure security](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity), [Azure Security Center](https://azure.microsoft.com/services/security-center/), [audit logs](https://azure.microsoft.com/blog/azure-audit-logs-ux-refresh/) |

## <a name="compare-azure-functions-and-azure-logic-apps"></a>Comparar o Azure Functions e o Azure Logic Apps

O Functions e o Logic Apps são serviços do Azure que permitem cargas de trabalho sem servidor. Azure Functions is a serverless compute service, whereas Azure Logic Apps provides serverless workflows. Both can create complex *orchestrations*. Uma orquestração é uma coleção de funções ou passos, chamados *ações* no Logic Apps, que são executados para cumprir tarefas complexas. For example, to process a batch of orders, you might execute many instances of a function in parallel, wait for all instances to finish, and then execute a function that computes a result on the aggregate.

Nas Funções do Azure, vai desenvolver orquestrações ao escrever código e utilizar a [extensão Durable Functions](durable/durable-functions-overview.md). Para o Logic Apps, crie orquestrações ao utilizar um GUI ou editar os ficheiros de configuração.

Pode combinar serviços ao criar uma orquestração, chamar funções de aplicações lógicas e chamar aplicações lógicas de funções. Selecione como criar cada orquestração com base nas capacidades dos serviços ou nas suas preferências pessoais. The following table lists some of the key differences between these  
|  | Funções Duráveis | Logic Apps |
| --- | --- | --- |
| Desenvolvimento | Baseada em código (imperativo) | Baseada em designer (declarativo) |
| Ligação | [Cerca de uma dúzia de tipos de vinculação integrados](functions-triggers-bindings.md#supported-bindings), código de escrita para vinculações personalizadas | [Grande coleção de conectores](../connectors/apis-list.md), [Enterprise Integration Pack para cenários B2B](../logic-apps/logic-apps-enterprise-integration-overview.md), [criar conectores personalizados](../logic-apps/custom-connector-overview.md) |
| Ações | Cada atividade é uma função do Azure; escrever código para funções de atividades |[Grande coleção de ações preparadas](../logic-apps/logic-apps-workflow-actions-triggers.md)|
| Monitorização | [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) | [Azure portal](../logic-apps/quickstart-create-first-logic-app-workflow.md), [Azure Monitor logs](../logic-apps/logic-apps-monitor-your-logic-apps.md)|
| Gestão | [API REST](durable/durable-functions-http-api.md), [Visual Studio](https://docs.microsoft.com/azure/vs-azure-tools-resources-managing-with-cloud-explorer) | [Portal do Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md), [API REST](https://docs.microsoft.com/rest/api/logic/), [PowerShell](https://docs.microsoft.com/powershell/module/az.logicapp), [Visual Studio](https://docs.microsoft.com/azure/logic-apps/manage-logic-apps-with-visual-studio) |
| Contexto de execução | Can run [locally](functions-runtime-overview.md) or in the cloud | Runs only in the cloud|

<a name="function"></a>

## <a name="compare-functions-and-webjobs"></a>Comparar o Functions e o WebJobs

Tal como as Funções do Azure, o Serviço de Aplicações do Azure WebJobs com o SDK do WebJobs é um serviço de integração *baseado em código* e concebido para programadores. Ambos são criados no [Serviço de Aplicações do Azure](../app-service/overview.md) e suportam funcionalidades como a [integração de controlo de origem](../app-service/deploy-continuous-deployment.md), a [autenticação](../app-service/overview-authentication-authorization.md) e a [monitorização com integração do Application Insights](functions-monitoring.md).

### <a name="webjobs-and-the-webjobs-sdk"></a>WebJobs e o SDK do WebJobs

You can use the *WebJobs* feature of App Service to run a script or code in the context of an App Service web app. O *SDK do WebJobs* é uma arquitetura concebida para o WebJobs que simplifica o código que escreveu para responder a eventos em serviços do Azure. For example, you might respond to the creation of an image blob in Azure Storage by creating a thumbnail image. O SDK do WebJobs é executado como uma aplicação da consola .NET, a qual pode implementar num WebJob. 

O WebJobs e o SDK do WebJobs funcionam melhor em conjunto, mas pode utilizar o WebJobs sem o SDK do WebJobs e vice-versa. Um WebJob pode executar qualquer programa ou script que execute na sandbox do Serviço de Aplicações. Uma aplicação de consola do SDK do WebJobs pode ser executada em qualquer local onde as aplicações de consolas se executam, como nos servidores no local.

### <a name="comparison-table"></a>Tabela de comparação

As Funções do Azure são criadas no SDK do WebJobs, pelo que partilham muitos dos acionadores de eventos e das ligações a outros serviços do Azure. Here are some factors to consider when you're choosing between Azure Functions and WebJobs with the WebJobs SDK:

|  | Funções | WebJobs com o SDK do WebJobs |
| --- | --- | --- |
|[Modelo de aplicação sem servidor](https://azure.microsoft.com/solutions/serverless/) com [dimensionamento automático](functions-scale.md#how-the-consumption-and-premium-plans-work)|✔||
|[Programação e testes no browser](functions-create-first-azure-function.md) |✔||
|[Preços com pagamento por utilização](functions-scale.md#consumption-plan)|✔||
|[Integração com Logic Apps](functions-twitter-email.md)|✔||
| Eventos de acionador |[Temporizador](functions-bindings-timer.md)<br>[Filas e blobs de Armazenamento do Microsoft Azure](functions-bindings-storage-blob.md)<br>[Filas e tópicos do Azure Service Bus](functions-bindings-service-bus.md)<br>[Azure Cosmos DB](functions-bindings-cosmosdb.md)<br>[Azure Event Hubs](functions-bindings-event-hubs.md)<br>[HTTP/WebHook (GitHub, Slack)](functions-bindings-http-webhook.md)<br>[Azure Event Grid](functions-bindings-event-grid.md)|[Temporizador](functions-bindings-timer.md)<br>[Filas e blobs de Armazenamento do Microsoft Azure](functions-bindings-storage-blob.md)<br>[Filas e tópicos do Azure Service Bus](functions-bindings-service-bus.md)<br>[Azure Cosmos DB](functions-bindings-cosmosdb.md)<br>[Azure Event Hubs](functions-bindings-event-hubs.md)<br>[Sistema de Ficheiros](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Files/FileTriggerAttribute.cs)|
| Linguagens suportadas  |C#<br>F#<br>JavaScript<br>Java<br>Python |C#<sup>1</sup>|
|Gestores de pacotes|NPM e NuGet|NuGet<sup>2</sup>|

<sup>1</sup> WebJobs (without the WebJobs SDK) supports C#, Java, JavaScript, Bash, .cmd, .bat, PowerShell, PHP, TypeScript, Python, and more. This is not a comprehensive list. Um WebJob pode executar qualquer programa ou script que possa ser executado na sandbox do Serviço de Aplicações.

<sup>2</sup> WebJobs (without the WebJobs SDK) supports NPM and NuGet.

### <a name="summary"></a>Resumo

Azure Functions offers more developer productivity than Azure App Service WebJobs does. It also offers more options for programming languages, development environments, Azure service integration, and pricing. Para a maioria dos cenários, é a melhor escolha.

Eis dois cenários para os quais o WebJobs pode ser a melhor escolha:

* Precisa de ter mais controlo sobre o código que escuta eventos, o objeto `JobHost`. As Funções oferecem um número limitado de formas de personalizar o comportamento `JobHost` no ficheiro [host.json](functions-host-json.md). Por vezes, precisa de fazer coisas que não podem ser especificadas por uma cadeia num ficheiro JSON. Por exemplo, apenas o SDK do WebJobs permite configurar uma política de repetição personalizada para o Armazenamento do Microsoft Azure.
* You have an App Service app for which you want to run code snippets, and you want to manage them together in the same Azure DevOps environment.

Para outros cenários nos quais pretenda executar fragmentos de código para integrar serviços do Azure ou de terceiros, selecione as Funções do Azure sobre o WebJobs com o SDK do WebJobs.

<a name="together"></a>

## <a name="microsoft-flow-logic-apps-functions-and-webjobs-together"></a>Microsoft Flow, Logic Apps, Functions, and WebJobs together

You don't have to choose just one of these services. They integrate with each other as well as they do with external services.

Um fluxo pode chamar uma aplicação lógica. Uma aplicação lógica pode chamar uma função e uma função pode chamar uma aplicação lógica. Consulte, por exemplo, [Criar uma função que se integra no Azure Logic Apps](functions-twitter-email.md).

The integration between Microsoft Flow, Logic Apps, and Functions continues to improve over time. Pode criar algo num serviço e utilizar noutros serviços.

You can get more information on integration services by using the following links:

* [Tirar partido das Funções do Azure e do Serviço de Aplicações do Azure para cenários de integração por Christopher Anderson](https://www.biztalk360.com/integrate-2016-resources/leveraging-azure-functions-azure-app-service-integration-scenarios/)
* [Integrações Simplificadas por Charles Lamanna](https://www.biztalk360.com/integrate-2016-resources/integrations-made-simple/)
* [Logic Apps Live webcast](https://aka.ms/logicappslive)
* [Microsoft Flow frequently asked questions](/power-automate/frequently-asked-questions)

## <a name="next-steps"></a>Passos seguintes

Comece por criar o seu primeiro fluxo, aplicação lógica ou aplicação de funções. Select any of the following links:

* [Introdução ao Microsoft Flow](/power-automate/getting-started)
* [Criar uma aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Create your first Azure function](functions-create-first-azure-function.md)
