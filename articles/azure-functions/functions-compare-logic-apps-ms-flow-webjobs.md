---
title: O que são Microsoft Flow, aplicativos lógicos, funções e trabalhos Web? - Azure
description: 'Compare os serviços em nuvem da Microsoft que são otimizados para tarefas de integração: Microsoft Flow, aplicativos lógicos, funções e trabalhos Web.'
services: functions, logic-apps
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: microsoft flow, flow, fluxo, logic apps, aplicações lógicas, funções do azure, funções, azure webjobs, webjobs, processamento de eventos, computação dinâmica, arquitetura sem servidor
ms.service: azure-functions
ms.devlang: multiple
ms.topic: overview
ms.date: 04/09/2018
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 7bfe586294aec5938e2245c38fbe88aa5e57a66c
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68839494"
---
# <a name="what-are-microsoft-flow-logic-apps-functions-and-webjobs"></a>O que são Microsoft Flow, aplicativos lógicos, funções e trabalhos Web?

Este artigo compara os seguintes serviços cloud do Microsoft:

* [Microsoft Flow](https://flow.microsoft.com/)
* [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Funções do Azure](https://azure.microsoft.com/services/functions/)
* [WebJobs do Serviço de Aplicações do Azure ](../app-service/webjobs-create.md)

Todos estes serviços podem resolver problemas de integração e automatizar processos comerciais. Podem todos definir entradas, ações, condições e saídas. Pode executar cada um com base numa agenda ou num acionador. Cada serviço tem vantagens exclusivas, e este artigo explica as diferenças.

## <a name="compare-microsoft-flow-and-azure-logic-apps"></a>Comparar o Microsoft Flow e o Azure Logic Apps

Os aplicativos Microsoft Flow e lógicos são os serviços de integração do *Designer primeiro* que podem criar fluxos de trabalho. Ambos os serviços integram-se com diversas aplicações empresariais e SaaS. 

Microsoft Flow é criado sobre os aplicativos lógicos. Eles compartilham o mesmo designer de fluxo de trabalho [](../connectors/apis-list.md)e os mesmos conectores. 

Microsoft Flow capacita qualquer operador do Office a executar integrações simples (por exemplo, um processo de aprovação em uma biblioteca de documentos do SharePoint) sem passar pelos desenvolvedores ou por ele. Os aplicativos lógicos também podem habilitar integrações avançadas (por exemplo, processos B2B) em que as práticas de segurança e DevOps do Azure de nível empresarial são necessárias. É normal um fluxo de trabalho do negócio se tornar cada vez mais complexo ao longo do tempo. Da mesma forma, você pode começar com um fluxo primeiro e, em seguida, convertê-lo em um aplicativo lógico, conforme necessário.

A tabela a seguir ajuda a determinar se Microsoft Flow ou aplicativos lógicos são melhores para uma determinada integração:

|  | Microsoft Flow | Aplicações Lógicas |
| --- | --- | --- |
| Utilizadores |Trabalhadores do escritório, utilizadores empresariais, administradores do SharePoint |Integradores e programadores profissionais, profissionais de TI |
| Cenários |Gestão personalizada |Integrações avançadas |
| Ferramenta de design |Aplicação no browser e móvel, apenas IU |No browser e [Visual Studio](../logic-apps/logic-apps-deploy-from-vs.md), [Vista de código](../logic-apps/logic-apps-author-definitions.md) disponível |
| Gerenciamento do ciclo de vida do aplicativo (ALM) |Projete e teste em ambientes de não produção, promova para produção quando estiver pronto |Azure DevOps: controle do código-fonte, teste, suporte, automação e capacidade de gerenciamento no [Azure Resource Manager](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md) |
| Experiência de administrador |Gerenciar ambientes de Microsoft Flow e políticas de prevenção de perda de dados (DLP), acompanhar o licenciamento: [Centro de administração do Microsoft Flow](https://admin.flow.microsoft.com) |Gerenciar grupos de recursos, conexões, gerenciamento de acesso e registro em log: [Azure portal](https://portal.azure.com) |
| Segurança |Logs de auditoria de conformidade e segurança do Office 365, DLP, [criptografia em repouso](https://wikipedia.org/wiki/Data_at_rest#Encryption) para dados confidenciais |Garantia de segurança do Azure: [Segurança do Azure](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity), [central de segurança do Azure](https://azure.microsoft.com/services/security-center/), [logs de auditoria](https://azure.microsoft.com/blog/azure-audit-logs-ux-refresh/) |

## <a name="compare-azure-functions-and-azure-logic-apps"></a>Comparar o Azure Functions e o Azure Logic Apps

O Functions e o Logic Apps são serviços do Azure que permitem cargas de trabalho sem servidor. Azure Functions é um serviço de computação sem servidor, enquanto os aplicativos lógicos do Azure fornecem fluxos de trabalho sem servidor. Ambos podem criar *orquestrações*complexas. Uma orquestração é uma coleção de funções ou passos, chamados *ações* no Logic Apps, que são executados para cumprir tarefas complexas. Por exemplo, para processar um lote de pedidos, você pode executar muitas instâncias de uma função em paralelo, aguardar até que todas as instâncias sejam concluídas e, em seguida, executar uma função que computa um resultado na agregação.

Nas Funções do Azure, vai desenvolver orquestrações ao escrever código e utilizar a [extensão Durable Functions](durable/durable-functions-concepts.md). Para o Logic Apps, crie orquestrações ao utilizar um GUI ou editar os ficheiros de configuração.

Pode combinar serviços ao criar uma orquestração, chamar funções de aplicações lógicas e chamar aplicações lógicas de funções. Selecione como criar cada orquestração com base nas capacidades dos serviços ou nas suas preferências pessoais. A seguinte tabela lista algumas das principais diferenças entre estes serviços:
 
|  | Durable Functions | Aplicações Lógicas |
| --- | --- | --- |
| Desenvolvimento | Baseada em código (imperativo) | Baseada em designer (declarativo) |
| Conectividade | [Cerca de uma dúzia de tipos de vinculação integrados](functions-triggers-bindings.md#supported-bindings), código de escrita para vinculações personalizadas | [Grande coleção de conectores](../connectors/apis-list.md), [Enterprise Integration Pack para cenários B2B](../logic-apps/logic-apps-enterprise-integration-overview.md), [criar conectores personalizados](../logic-apps/custom-connector-overview.md) |
| Ações | Cada atividade é uma função do Azure; escrever código para funções de atividades |[Grande coleção de ações preparadas](../logic-apps/logic-apps-workflow-actions-triggers.md)|
| Monitorização | [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) | Logs de [portal do Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md), [Azure monitor](../logic-apps/logic-apps-monitor-your-logic-apps.md)|
| Gestão | [API REST](durable/durable-functions-http-api.md), [Visual Studio](https://docs.microsoft.com/azure/vs-azure-tools-resources-managing-with-cloud-explorer) | [Portal do Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md), [API REST](https://docs.microsoft.com/rest/api/logic/), [PowerShell](https://docs.microsoft.com/powershell/module/az.logicapp), [Visual Studio](https://docs.microsoft.com/azure/logic-apps/manage-logic-apps-with-visual-studio) |
| Contexto de execução | Pode ser executado [localmente](functions-runtime-overview.md) ou na nuvem | É executado somente na nuvem|

<a name="function"></a>

## <a name="compare-functions-and-webjobs"></a>Comparar o Functions e o WebJobs

Tal como as Funções do Azure, o Serviço de Aplicações do Azure WebJobs com o SDK do WebJobs é um serviço de integração *baseado em código* e concebido para programadores. Ambos são criados no [Serviço de Aplicações do Azure](../app-service/overview.md) e suportam funcionalidades como a [integração de controlo de origem](../app-service/deploy-continuous-deployment.md), a [autenticação](../app-service/overview-authentication-authorization.md) e a [monitorização com integração do Application Insights](functions-monitoring.md).

### <a name="webjobs-and-the-webjobs-sdk"></a>WebJobs e o SDK do WebJobs

Você pode usar o recurso webjobs do serviço de aplicativo para executar um script ou código no contexto de um aplicativo Web do serviço de aplicativo. O *SDK do WebJobs* é uma arquitetura concebida para o WebJobs que simplifica o código que escreveu para responder a eventos em serviços do Azure. Por exemplo, você pode responder à criação de um blob de imagem no armazenamento do Azure criando uma imagem em miniatura. O SDK do WebJobs é executado como uma aplicação da consola .NET, a qual pode implementar num WebJob. 

O WebJobs e o SDK do WebJobs funcionam melhor em conjunto, mas pode utilizar o WebJobs sem o SDK do WebJobs e vice-versa. Um WebJob pode executar qualquer programa ou script que execute na sandbox do Serviço de Aplicações. Uma aplicação de consola do SDK do WebJobs pode ser executada em qualquer local onde as aplicações de consolas se executam, como nos servidores no local.

### <a name="comparison-table"></a>Tabela de comparação

As Funções do Azure são criadas no SDK do WebJobs, pelo que partilham muitos dos acionadores de eventos e das ligações a outros serviços do Azure. Aqui estão alguns fatores a serem considerados quando você estiver escolhendo entre Azure Functions e trabalhos Web com o SDK de trabalhos Web:

|  | Functions | WebJobs com o SDK do WebJobs |
| --- | --- | --- |
|[Modelo de aplicação sem servidor](https://azure.microsoft.com/solutions/serverless/) com [dimensionamento automático](functions-scale.md#how-the-consumption-and-premium-plans-work)|✔||
|[Programação e testes no browser](functions-create-first-azure-function.md) |✔||
|[Preços com pagamento por utilização](functions-scale.md#consumption-plan)|✔||
|[Integração com Logic Apps](functions-twitter-email.md)|✔||
| Eventos de acionador |[Temporizador](functions-bindings-timer.md)<br>[Filas e blobs de Armazenamento do Microsoft Azure](functions-bindings-storage-blob.md)<br>[Filas e tópicos do Azure Service Bus](functions-bindings-service-bus.md)<br>[BD do Cosmos para o Azure](functions-bindings-cosmosdb.md)<br>[Azure Event Hubs](functions-bindings-event-hubs.md)<br>[HTTP/WebHook (GitHub, Slack)](functions-bindings-http-webhook.md)<br>[Azure Event Grid](functions-bindings-event-grid.md)|[Temporizador](functions-bindings-timer.md)<br>[Filas e blobs de Armazenamento do Microsoft Azure](functions-bindings-storage-blob.md)<br>[Filas e tópicos do Azure Service Bus](functions-bindings-service-bus.md)<br>[BD do Cosmos para o Azure](functions-bindings-cosmosdb.md)<br>[Azure Event Hubs](functions-bindings-event-hubs.md)<br>[Sistema de Ficheiros](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Files/FileTriggerAttribute.cs)|
| Idiomas suportados  |C#<br>F#<br>JavaScript<br>Java<br>Python (versão prévia) |C#<sup>1</sup>|
|Gestores de pacotes|NPM e NuGet|NuGet<sup>2</sup>|

<sup>1</sup> trabalhos Web (sem o SDK de trabalhos Web) C#dão suporte a, Java, JavaScript, Bash,. cmd,. bat, PowerShell, PHP, TypeScript, Python e muito mais. Essa não é uma lista abrangente. Um WebJob pode executar qualquer programa ou script que possa ser executado na sandbox do Serviço de Aplicações.

<sup>2</sup> trabalhos Web (sem o SDK de trabalhos Web) dão suporte a NPM e NuGet.

### <a name="summary"></a>Resumo

O Azure Functions oferece mais produtividade de desenvolvedor do que o serviço webjobs do Azure App. Ele também oferece mais opções para linguagens de programação, ambientes de desenvolvimento, integração de serviços do Azure e preços. Para a maioria dos cenários, é a melhor escolha.

Eis dois cenários para os quais o WebJobs pode ser a melhor escolha:

* Precisa de ter mais controlo sobre o código que escuta eventos, o objeto `JobHost`. As Funções oferecem um número limitado de formas de personalizar o comportamento `JobHost` no ficheiro [host.json](functions-host-json.md). Por vezes, precisa de fazer coisas que não podem ser especificadas por uma cadeia num ficheiro JSON. Por exemplo, apenas o SDK do WebJobs permite configurar uma política de repetição personalizada para o Armazenamento do Microsoft Azure.
* Você tem um aplicativo do serviço de aplicativo para o qual deseja executar trechos de código e deseja gerenciá-los juntos no mesmo ambiente de DevOps do Azure.

Para outros cenários nos quais pretenda executar fragmentos de código para integrar serviços do Azure ou de terceiros, selecione as Funções do Azure sobre o WebJobs com o SDK do WebJobs.

<a name="together"></a>

## <a name="microsoft-flow-logic-apps-functions-and-webjobs-together"></a>Microsoft Flow, aplicativos lógicos, funções e trabalhos Web juntos

Você não precisa escolher apenas um desses serviços. Eles se integram entre si e também com serviços externos.

Um fluxo pode chamar uma aplicação lógica. Uma aplicação lógica pode chamar uma função e uma função pode chamar uma aplicação lógica. Consulte, por exemplo, [Criar uma função que se integra no Azure Logic Apps](functions-twitter-email.md).

A integração entre Microsoft Flow, aplicativos lógicos e funções continua a melhorar com o passar do tempo. Pode criar algo num serviço e utilizar noutros serviços.

Você pode obter mais informações sobre o Integration Services usando os seguintes links:

* [Tirar partido das Funções do Azure e do Serviço de Aplicações do Azure para cenários de integração por Christopher Anderson](https://www.biztalk360.com/integrate-2016-resources/leveraging-azure-functions-azure-app-service-integration-scenarios/)
* [Integrações Simplificadas por Charles Lamanna](https://www.biztalk360.com/integrate-2016-resources/integrations-made-simple/)
* [Webcast ao vivo de aplicativos lógicos](https://aka.ms/logicappslive)
* [Microsoft Flow perguntas frequentes](https://flow.microsoft.com/documentation/frequently-asked-questions/)

## <a name="next-steps"></a>Passos Seguintes

Comece por criar o seu primeiro fluxo, aplicação lógica ou aplicação de funções. Selecione qualquer um dos seguintes links:

* [Introdução ao Microsoft Flow](https://flow.microsoft.com/en-us/documentation/getting-started/)
* [Criar uma aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Criar sua primeira função do Azure](functions-create-first-azure-function.md)
