---
title: O que são o Microsoft Flow, Logic Apps, funções e WebJobs? - Azure
description: 'Compare serviços cloud da Microsoft que estão otimizados para tarefas de integração: Microsoft Flow, Logic Apps, funções e WebJobs.'
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
ms.openlocfilehash: de83cc80d64fb1862f1fdec32b704d0948ef9fc8
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56820523"
---
# <a name="what-are-microsoft-flow-logic-apps-functions-and-webjobs"></a>O que são o Microsoft Flow, Logic Apps, funções e WebJobs?

Este artigo compara os seguintes serviços cloud do Microsoft:

* [Microsoft Flow](https://flow.microsoft.com/)
* [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Funções do Azure](https://azure.microsoft.com/services/functions/)
* [WebJobs do Serviço de Aplicações do Azure ](../app-service/webjobs-create.md)

Todos estes serviços podem resolver problemas de integração e automatizar processos comerciais. Podem todos definir entradas, ações, condições e saídas. Pode executar cada um com base numa agenda ou num acionador. Cada serviço tem vantagens únicas e este artigo explica as diferenças.

## <a name="compare-microsoft-flow-and-azure-logic-apps"></a>Comparar o Microsoft Flow e o Azure Logic Apps

Microsoft Flow e Logic Apps são ambos *designers* serviços de integração que podem criar fluxos de trabalho. Ambos os serviços integram-se com diversas aplicações empresariais e SaaS. 

Microsoft Flow baseia-se no Logic Apps. Compartilham o mesmo estruturador de fluxo de trabalho e o mesmo [conectores](../connectors/apis-list.md). 

Microsoft Flow capacita qualquer trabalhador de escritório para efetuar integrações simples (por exemplo, um processo de aprovação numa biblioteca de documentos do SharePoint) sem recorrerem a programadores ou IT. O Logic Apps também pode permitir integrações avançadas (por exemplo, processos B2B), onde a nível empresarial do Azure DevOps e práticas de segurança são necessárias. É normal um fluxo de trabalho do negócio se tornar cada vez mais complexo ao longo do tempo. Da mesma forma, pode começar com um fluxo em primeiro lugar e, em seguida, convertê-la conforme necessário para uma aplicação lógica.

A tabela seguinte ajuda-o a determinar se o Microsoft Flow ou Logic Apps é melhor para uma determinada integração:

|  | Microsoft Flow | Aplicações Lógicas |
| --- | --- | --- |
| Utilizadores |Trabalhadores do escritório, utilizadores empresariais, administradores do SharePoint |Integradores e programadores profissionais, profissionais de TI |
| Cenários |Gestão personalizada |Integrações avançadas |
| Ferramenta de design |Aplicação no browser e móvel, apenas IU |No browser e [Visual Studio](../logic-apps/logic-apps-deploy-from-vs.md), [Vista de código](../logic-apps/logic-apps-author-definitions.md) disponível |
| Gerenciamento de ciclo de vida de aplicativos (ALM) |Criar e testar em ambientes de não produção, promover para produção quando estiver pronto |Azure DevOps: da origem controlo, teste, suporte, automatização e capacidade de gerenciamento no [do Azure Resource Manager](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md) |
| Experiência de administrador |Gerir o Microsoft Flow ambientes e dados políticas prevenção de perda (DLP), controlar o licenciamento: [Centro de administração do Microsoft Flow](https://admin.flow.microsoft.com) |Gerir grupos de recursos, ligações, gestão de acesso e o registo: [Portal do Azure](https://portal.azure.com) |
| Segurança |Registos, DLP, de auditoria de segurança do Office 365 e compatibilidade [encriptação em repouso](https://wikipedia.org/wiki/Data_at_rest#Encryption) para dados confidenciais |Garantia de segurança do Azure: [Segurança do Azure](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity), [Centro de segurança do Azure](https://azure.microsoft.com/services/security-center/), [registos de auditoria](https://azure.microsoft.com/blog/azure-audit-logs-ux-refresh/) |

## <a name="compare-azure-functions-and-azure-logic-apps"></a>Comparar o Azure Functions e o Azure Logic Apps

O Functions e o Logic Apps são serviços do Azure que permitem cargas de trabalho sem servidor. As funções do Azure é um serviço de computação sem servidor, enquanto o Azure Logic Apps fornece fluxos de trabalho sem servidor. Ambos podem criar complexas *orquestrações*. Uma orquestração é uma coleção de funções ou passos, chamados *ações* no Logic Apps, que são executados para cumprir tarefas complexas. Por exemplo, para processar um lote de encomendas, poderá executar muitas instâncias de uma função em paralelo, aguardar que todas as instâncias terminem e, em seguida, executar uma função que computa um resultado agregado.

Nas Funções do Azure, vai desenvolver orquestrações ao escrever código e utilizar a [extensão Durable Functions](durable/durable-functions-concepts.md). Para o Logic Apps, crie orquestrações ao utilizar um GUI ou editar os ficheiros de configuração.

Pode combinar serviços ao criar uma orquestração, chamar funções de aplicações lógicas e chamar aplicações lógicas de funções. Selecione como criar cada orquestração com base nas capacidades dos serviços ou nas suas preferências pessoais. A seguinte tabela lista algumas das principais diferenças entre estes serviços:
 
|  | Funções Duráveis | Aplicações Lógicas |
| --- | --- | --- |
| Desenvolvimento | Baseada em código (imperativo) | Baseada em designer (declarativo) |
| Conectividade | [Cerca de uma dúzia de tipos de vinculação integrados](functions-triggers-bindings.md#supported-bindings), código de escrita para vinculações personalizadas | [Grande coleção de conectores](../connectors/apis-list.md), [Enterprise Integration Pack para cenários B2B](../logic-apps/logic-apps-enterprise-integration-overview.md), [criar conectores personalizados](../logic-apps/custom-connector-overview.md) |
| Ações | Cada atividade é uma função do Azure; escrever código para funções de atividades |[Grande coleção de ações preparadas](../logic-apps/logic-apps-workflow-actions-triggers.md)|
| Monitorização | [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) | [Portal do Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md), [registos do Azure Monitor](../logic-apps/logic-apps-monitor-your-logic-apps.md)|
| Gestão | [API REST](durable/durable-functions-http-api.md), [Visual Studio](https://docs.microsoft.com/azure/vs-azure-tools-resources-managing-with-cloud-explorer) | [Portal do Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md), [API REST](https://docs.microsoft.com/rest/api/logic/), [PowerShell](https://docs.microsoft.com/powershell/module/az.logicapp), [Visual Studio](https://docs.microsoft.com/azure/logic-apps/manage-logic-apps-with-visual-studio) |
| Contexto de execução | Pode ser executado [localmente](functions-runtime-overview.md) ou na cloud | É executado apenas na cloud|

<a name="function"></a>

## <a name="compare-functions-and-webjobs"></a>Comparar o Functions e o WebJobs

Tal como as Funções do Azure, o Serviço de Aplicações do Azure WebJobs com o SDK do WebJobs é um serviço de integração *baseado em código* e concebido para programadores. Ambos são criados no [Serviço de Aplicações do Azure](../app-service/overview.md) e suportam funcionalidades como a [integração de controlo de origem](../app-service/deploy-continuous-deployment.md), a [autenticação](../app-service/overview-authentication-authorization.md) e a [monitorização com integração do Application Insights](functions-monitoring.md).

### <a name="webjobs-and-the-webjobs-sdk"></a>WebJobs e o SDK do WebJobs

Pode utilizar o *WebJobs* funcionalidade do serviço de aplicações para executar um script ou código no contexto de um serviço de aplicações web de aplicação. O *SDK do WebJobs* é uma arquitetura concebida para o WebJobs que simplifica o código que escreveu para responder a eventos em serviços do Azure. Por exemplo, poderá responder à criação de um blob de imagem no armazenamento do Azure através da criação de uma imagem em miniatura. O SDK do WebJobs é executado como uma aplicação da consola .NET, a qual pode implementar num WebJob. 

O WebJobs e o SDK do WebJobs funcionam melhor em conjunto, mas pode utilizar o WebJobs sem o SDK do WebJobs e vice-versa. Um WebJob pode executar qualquer programa ou script que execute na sandbox do Serviço de Aplicações. Uma aplicação de consola do SDK do WebJobs pode ser executada em qualquer local onde as aplicações de consolas se executam, como nos servidores no local.

### <a name="comparison-table"></a>Tabela de comparação

As Funções do Azure são criadas no SDK do WebJobs, pelo que partilham muitos dos acionadores de eventos e das ligações a outros serviços do Azure. Eis alguns fatores a considerar quando está escolhendo entre as funções do Azure e WebJobs com o SDK de WebJobs:

|  | Funções | WebJobs com o SDK do WebJobs |
| --- | --- | --- |
|[Modelo de aplicação sem servidor](https://azure.microsoft.com/solutions/serverless/) com [dimensionamento automático](functions-scale.md#how-the-consumption-plan-works)|✔||
|[Programação e testes no browser](functions-create-first-azure-function.md) |✔||
|[Preços com pagamento por utilização](functions-scale.md#consumption-plan)|✔||
|[Integração com Logic Apps](functions-twitter-email.md)|✔||
| Eventos de acionador |[Temporizador](functions-bindings-timer.md)<br>[Filas e blobs de Armazenamento do Microsoft Azure](functions-bindings-storage-blob.md)<br>[Filas e tópicos do Azure Service Bus](functions-bindings-service-bus.md)<br>[BD do Cosmos para o Azure](functions-bindings-cosmosdb.md)<br>[Azure Event Hubs](functions-bindings-event-hubs.md)<br>[HTTP/WebHook (GitHub, Slack)](functions-bindings-http-webhook.md)<br>[Azure Event Grid](functions-bindings-event-grid.md)|[Temporizador](functions-bindings-timer.md)<br>[Filas e blobs de Armazenamento do Microsoft Azure](functions-bindings-storage-blob.md)<br>[Filas e tópicos do Azure Service Bus](functions-bindings-service-bus.md)<br>[BD do Cosmos para o Azure](functions-bindings-cosmosdb.md)<br>[Azure Event Hubs](functions-bindings-event-hubs.md)<br>[Sistema de Ficheiros](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Files/FileTriggerAttribute.cs)|
| Linguagens suportadas  |C#<br>F#<br>JavaScript<br>Java (pré-visualização) |C#<sup>1</sup>|
|Gestores de pacotes|NPM e NuGet|NuGet<sup>2</sup>|

<sup>1</sup> WebJobs (sem SDK do WebJobs) suporta C#, JavaScript, Bash,. cmd,. bat, PowerShell, PHP, TypeScript, Python e muito mais. Não se trata de uma lista abrangente. Um WebJob pode executar qualquer programa ou script que possa ser executado na sandbox do Serviço de Aplicações.

<sup>2</sup> WebJobs (sem SDK do WebJobs) suporta NPM e NuGet.

### <a name="summary"></a>Resumo

As funções do Azure oferece mais produtividade do desenvolvedor que o Azure WebJobs do serviço de aplicações. Ele também oferece mais opções para a programação de linguagens, ambientes de desenvolvimento, integração de serviço do Azure e preços. Para a maioria dos cenários, é a melhor escolha.

Eis dois cenários para os quais o WebJobs pode ser a melhor escolha:

* Precisa de ter mais controlo sobre o código que escuta eventos, o objeto `JobHost`. As Funções oferecem um número limitado de formas de personalizar o comportamento `JobHost` no ficheiro [host.json](functions-host-json.md). Por vezes, precisa de fazer coisas que não podem ser especificadas por uma cadeia num ficheiro JSON. Por exemplo, apenas o SDK do WebJobs permite configurar uma política de repetição personalizada para o Armazenamento do Microsoft Azure.
* Tem uma aplicação de serviço de aplicações para o qual pretende executar fragmentos de código e pretender geri-los em conjunto no mesmo ambiente DevOps do Azure.

Para outros cenários nos quais pretenda executar fragmentos de código para integrar serviços do Azure ou de terceiros, selecione as Funções do Azure sobre o WebJobs com o SDK do WebJobs.

<a name="together"></a>

## <a name="microsoft-flow-logic-apps-functions-and-webjobs-together"></a>Microsoft Flow, Logic Apps, funções e WebJobs em conjunto

Não tem de escolher apenas um destes serviços. Eles se integram umas com as outras, bem como que fazem com serviços externos.

Um fluxo pode chamar uma aplicação lógica. Uma aplicação lógica pode chamar uma função e uma função pode chamar uma aplicação lógica. Consulte, por exemplo, [Criar uma função que se integra no Azure Logic Apps](functions-twitter-email.md).

A integração entre o Microsoft Flow, Logic Apps e as funções continua a melhorar ao longo do tempo. Pode criar algo num serviço e utilizar noutros serviços.

Pode obter mais informações sobre serviços de integração utilizando as ligações seguintes:

* [Tirar partido das Funções do Azure e do Serviço de Aplicações do Azure para cenários de integração por Christopher Anderson](http://www.biztalk360.com/integrate-2016-resources/leveraging-azure-functions-azure-app-service-integration-scenarios/)
* [Integrações Simplificadas por Charles Lamanna](http://www.biztalk360.com/integrate-2016-resources/integrations-made-simple/)
* [Webcast de aplicações em direto de lógica](https://aka.ms/logicappslive)
* [Microsoft Flow perguntas mais frequentes](https://flow.microsoft.com/documentation/frequently-asked-questions/)

## <a name="next-steps"></a>Passos Seguintes

Comece por criar o seu primeiro fluxo, aplicação lógica ou aplicação de funções. Selecione qualquer uma das seguintes ligações:

* [Introdução ao Microsoft Flow](https://flow.microsoft.com/en-us/documentation/getting-started/)
* [Criar uma aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Criar a sua primeira função do Azure](functions-create-first-azure-function.md)
