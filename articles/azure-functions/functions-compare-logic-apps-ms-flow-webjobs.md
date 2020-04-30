---
title: Opções de plataforma de integração e automação no Azure
description: 'Compare os serviços de nuvem da Microsoft otimizados para tarefas de integração: Power Automate, Logic Apps, Functions e WebJobs.'
ms.topic: overview
ms.date: 04/09/2018
ms.custom: mvc
ms.openlocfilehash: 40293056a73fd88e9ad6b3922aebfe0ba71f07dd
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80878141"
---
# <a name="choose-the-right-integration-and-automation-services-in-azure"></a>Escolha os serviços de integração e automação certos em Azure

Este artigo compara os seguintes serviços cloud do Microsoft:

* [Microsoft Power Automate](https://flow.microsoft.com/) (foi Microsoft Flow)
* [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Funções do Azure](https://azure.microsoft.com/services/functions/)
* [WebJobs do Serviço de Aplicações do Azure ](../app-service/webjobs-create.md)

Todos estes serviços podem resolver problemas de integração e automatizar processos comerciais. Podem todos definir entradas, ações, condições e saídas. Pode executar cada um com base numa agenda ou num acionador. Cada serviço tem vantagens únicas, e este artigo explica as diferenças. 

Se procura uma comparação mais geral entre as Funções Azure e outras opções de computação Azure, consulte Critérios para escolher um serviço de [computação Azure](/azure/architecture/guide/technology-choices/compute-comparison) e escolher uma opção de [computação Azure para microserviços.](/azure/architecture/microservices/design/compute-options)

## <a name="compare-microsoft-power-automate-and-azure-logic-apps"></a>Compare aplicações microsoft Power Automate e Azure Logic

Power Automate e Logic Apps são ambos serviços de integração *de design-primeiro* que podem criar fluxos de trabalho. Ambos os serviços integram-se com diversas aplicações empresariais e SaaS. 

Power Automate é construído em cima de Aplicações Lógicas. Partilham o mesmo designer de fluxode trabalho e os [mesmos conectores.](../connectors/apis-list.md) 

A Power Automate capacita qualquer trabalhador de escritório para realizar integrações simples (por exemplo, um processo de aprovação numa Biblioteca de Documentos SharePoint) sem passar por desenvolvedores ou TI. As Aplicações Lógicas também podem permitir integrações avançadas (por exemplo, processos B2B) onde são necessários Azure DevOps e práticas de segurança ao nível da empresa. É normal um fluxo de trabalho do negócio se tornar cada vez mais complexo ao longo do tempo. Assim, pode começar com um fluxo no início e depois convertê-lo para uma aplicação lógica, conforme necessário.

A tabela seguinte ajuda-o a determinar se power automate ou Logic Apps é o melhor para uma determinada integração:

|  | Power Automate | Aplicações Lógicas |
| --- | --- | --- |
| **Utilizadores** |Trabalhadores do escritório, utilizadores empresariais, administradores do SharePoint |Integradores e programadores profissionais, profissionais de TI |
| **Cenários** |Gestão personalizada |Integrações avançadas |
| **Ferramenta de design** |Aplicação no browser e móvel, apenas IU |No browser e [Visual Studio](../logic-apps/logic-apps-deploy-from-vs.md), [Vista de código](../logic-apps/logic-apps-author-definitions.md) disponível |
| **Gestão do ciclo de vida da aplicação (ALM)** |Conceção e teste em ambientes não produtivos, promover à produção quando estiver pronto |Azure DevOps: controlo de fontes, testes, suporte, automação e gestão no Gestor de [Recursos do Azure](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md) |
| **Experiência de administrador** |Gerir ambientes power automate e políticas de prevenção de perdas de dados (DLP), licenciamento de pista: [Centro de Administração](https://admin.flow.microsoft.com) |Gerir grupos de recursos, conexões, gestão de acesso e exploração madeireira: [Portal Azure](https://portal.azure.com) |
| **Segurança** |Registos de auditoria de segurança e conformidade do Office 365, DLP, [encriptação em repouso](https://wikipedia.org/wiki/Data_at_rest#Encryption) para dados sensíveis |Garantia de segurança do Azure: [Segurança Azure,](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity) [Centro de Segurança Azure,](https://azure.microsoft.com/services/security-center/) [registos de auditoria](https://azure.microsoft.com/blog/azure-audit-logs-ux-refresh/) |

## <a name="compare-azure-functions-and-azure-logic-apps"></a>Comparar o Azure Functions e o Azure Logic Apps

O Functions e o Logic Apps são serviços do Azure que permitem cargas de trabalho sem servidor. O Azure Functions é um serviço de computação sem servidores, enquanto as Aplicações Lógicas Azure fornecem fluxos de trabalho sem servidores. Ambos podem criar *orquestrações complexas.* Uma orquestração é uma coleção de funções ou passos, chamados *ações* no Logic Apps, que são executados para cumprir tarefas complexas. Por exemplo, para processar um lote de encomendas, pode executar muitos casos de uma função paralela, esperar que todas as instâncias terminem e, em seguida, executar uma função que calcule um resultado no agregado.

Nas Funções do Azure, vai desenvolver orquestrações ao escrever código e utilizar a [extensão Durable Functions](durable/durable-functions-overview.md). Para o Logic Apps, crie orquestrações ao utilizar um GUI ou editar os ficheiros de configuração.

Pode combinar serviços ao criar uma orquestração, chamar funções de aplicações lógicas e chamar aplicações lógicas de funções. Selecione como criar cada orquestração com base nas capacidades dos serviços ou nas suas preferências pessoais. A tabela que se segue enumera algumas das principais diferenças entre estas:

|  | Funções Duráveis | Aplicações Lógicas |
| --- | --- | --- |
| **Desenvolvimento** | Baseada em código (imperativo) | Baseada em designer (declarativo) |
| **Conectividade** | [Cerca de uma dúzia de tipos de vinculação integrados](functions-triggers-bindings.md#supported-bindings), código de escrita para vinculações personalizadas | [Grande coleção de conectores](../connectors/apis-list.md), [Enterprise Integration Pack para cenários B2B](../logic-apps/logic-apps-enterprise-integration-overview.md), [criar conectores personalizados](../logic-apps/custom-connector-overview.md) |
| **Ações** | Cada atividade é uma função do Azure; escrever código para funções de atividades |[Grande coleção de ações preparadas](../logic-apps/logic-apps-workflow-actions-triggers.md)|
| **Monitorização** | [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) | [Portal Azure,](../logic-apps/quickstart-create-first-logic-app-workflow.md) [Registos do Monitor Azure](../logic-apps/logic-apps-monitor-your-logic-apps.md)|
| **Gestão** | [API REST](durable/durable-functions-http-api.md), [Visual Studio](https://docs.microsoft.com/azure/vs-azure-tools-resources-managing-with-cloud-explorer) | [Portal do Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md), [API REST](https://docs.microsoft.com/rest/api/logic/), [PowerShell](https://docs.microsoft.com/powershell/module/az.logicapp), [Visual Studio](https://docs.microsoft.com/azure/logic-apps/manage-logic-apps-with-visual-studio) |
| **Contexto de execução** | Pode correr [localmente](functions-runtime-overview.md) ou na nuvem | São executadas apenas na cloud|

<a name="function"></a>

## <a name="compare-functions-and-webjobs"></a>Comparar o Functions e o WebJobs

Tal como as Funções do Azure, o Serviço de Aplicações do Azure WebJobs com o SDK do WebJobs é um serviço de integração *baseado em código* e concebido para programadores. Ambos são criados no [Serviço de Aplicações do Azure](../app-service/overview.md) e suportam funcionalidades como a [integração de controlo de origem](../app-service/deploy-continuous-deployment.md), a [autenticação](../app-service/overview-authentication-authorization.md) e a [monitorização com integração do Application Insights](functions-monitoring.md).

### <a name="webjobs-and-the-webjobs-sdk"></a>WebJobs e o SDK do WebJobs

Pode utilizar a funcionalidade *WebJobs* do App Service para executar um script ou código no contexto de uma aplicação web do App Service. O *SDK do WebJobs* é uma arquitetura concebida para o WebJobs que simplifica o código que escreveu para responder a eventos em serviços do Azure. Por exemplo, você pode responder à criação de uma bolha de imagem no Armazenamento Azure criando uma imagem de miniatura. O SDK do WebJobs é executado como uma aplicação da consola .NET, a qual pode implementar num WebJob. 

O WebJobs e o SDK do WebJobs funcionam melhor em conjunto, mas pode utilizar o WebJobs sem o SDK do WebJobs e vice-versa. Um WebJob pode executar qualquer programa ou script que execute na sandbox do Serviço de Aplicações. Uma aplicação de consola do SDK do WebJobs pode ser executada em qualquer local onde as aplicações de consolas se executam, como nos servidores no local.

### <a name="comparison-table"></a>Tabela de comparação

As Funções do Azure são criadas no SDK do WebJobs, pelo que partilham muitos dos acionadores de eventos e das ligações a outros serviços do Azure. Aqui estão alguns fatores a ter em conta quando está a escolher entre funções Azure e WebJobs com o WebJobs SDK:

|  | Funções | WebJobs com o SDK do WebJobs |
| --- | --- | --- |
|[Modelo de aplicação sem servidor](https://azure.microsoft.com/solutions/serverless/) com [dimensionamento automático](functions-scale.md#how-the-consumption-and-premium-plans-work)|✔||
|[Programação e testes no browser](functions-create-first-azure-function.md) |✔||
|[Preços com pagamento por utilização](functions-scale.md#consumption-plan)|✔||
|[Integração com Logic Apps](functions-twitter-email.md)|✔||
| Eventos de acionador |[Temporizador](functions-bindings-timer.md)<br>[Filas e blobs de Armazenamento do Microsoft Azure](functions-bindings-storage-blob.md)<br>[Filas e tópicos do Azure Service Bus](functions-bindings-service-bus.md)<br>[Azure Cosmos DB](functions-bindings-cosmosdb.md)<br>[Azure Event Hubs](functions-bindings-event-hubs.md)<br>[HTTP/WebHook (GitHub, Slack)](functions-bindings-http-webhook.md)<br>[Azure Event Grid](functions-bindings-event-grid.md)|[Temporizador](functions-bindings-timer.md)<br>[Filas e blobs de Armazenamento do Microsoft Azure](functions-bindings-storage-blob.md)<br>[Filas e tópicos do Azure Service Bus](functions-bindings-service-bus.md)<br>[Azure Cosmos DB](functions-bindings-cosmosdb.md)<br>[Azure Event Hubs](functions-bindings-event-hubs.md)<br>[Sistema de ficheiros](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Files/FileTriggerAttribute.cs)|
| Linguagens suportadas  |C#<br>F#<br>JavaScript<br>Java<br>Python<br>PowerShell |C#<sup>1</sup>|
|Gestores de pacotes|NPM e NuGet|NuGet<sup>2</sup>|

<sup>1</sup> WebJobs (sem o WebJobs SDK) suporta C#, Java, JavaScript, Bash, .cmd, .bat, PowerShell, PHP, TypeScript, Python, e muito mais. Esta não é uma lista completa. Um WebJob pode executar qualquer programa ou script que possa ser executado na sandbox do Serviço de Aplicações.

<sup>2</sup> WebJobs (sem o WebJobs SDK) suporta NPM e NuGet.

### <a name="summary"></a>Resumo

A Azure Functions oferece mais produtividade para os desenvolvedores do que o Azure App Service WebJobs. Oferece também mais opções para linguagem de programação, ambientes de desenvolvimento, integração de serviços Azure e preços. Para a maioria dos cenários, é a melhor escolha.

Eis dois cenários para os quais o WebJobs pode ser a melhor escolha:

* Precisa de ter mais controlo sobre o código que escuta eventos, o objeto `JobHost`. As Funções oferecem um número limitado de formas de personalizar o comportamento `JobHost` no ficheiro [host.json](functions-host-json.md). Por vezes, precisa de fazer coisas que não podem ser especificadas por uma cadeia num ficheiro JSON. Por exemplo, apenas o SDK do WebJobs permite configurar uma política de repetição personalizada para o Armazenamento do Microsoft Azure.
* Você tem uma aplicação de Serviço de Aplicações para a qual você quer executar códigos de snippets, e você quer geri-los juntos no mesmo ambiente Azure DevOps.

Para outros cenários nos quais pretenda executar fragmentos de código para integrar serviços do Azure ou de terceiros, selecione as Funções do Azure sobre o WebJobs com o SDK do WebJobs.

<a name="together"></a>

## <a name="power-automate-logic-apps-functions-and-webjobs-together"></a>Power Automate, Logic Apps, Funções e WebJobs juntos

Não tens de escolher apenas um destes serviços. Integram-se entre si, assim como com os serviços externos.

Um fluxo pode chamar uma aplicação lógica. Uma aplicação lógica pode chamar uma função e uma função pode chamar uma aplicação lógica. Consulte, por exemplo, [Criar uma função que se integra no Azure Logic Apps](functions-twitter-email.md).

A integração entre power automate, Logic Apps e Functions continua a melhorar ao longo do tempo. Pode criar algo num serviço e utilizar noutros serviços.

Pode obter mais informações sobre serviços de integração utilizando os seguintes links:

* [Tirar partido das Funções do Azure e do Serviço de Aplicações do Azure para cenários de integração por Christopher Anderson](https://www.biztalk360.com/integrate-2016-resources/leveraging-azure-functions-azure-app-service-integration-scenarios/)
* [Integrações Simplificadas por Charles Lamanna](https://www.biztalk360.com/integrate-2016-resources/integrations-made-simple/)
* [Aplicativos lógicos Webcast ao vivo](https://aka.ms/logicappslive)
* [Power Automate frequentemente fez perguntas](/power-automate/frequently-asked-questions)

## <a name="next-steps"></a>Passos seguintes

Comece por criar o seu primeiro fluxo, aplicação lógica ou aplicação de funções. Selecione qualquer um dos seguintes links:

* [Começar com power automate](/power-automate/getting-started)
* [Criar uma aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Crie a sua primeira função Azure](functions-create-first-azure-function.md)
