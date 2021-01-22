---
title: Opções de plataforma de integração e automação em Azure
description: 'Compare os serviços de cloud da Microsoft que estão otimizados para tarefas de integração: Power Automamate, Logic Apps, Functions e WebJobs.'
ms.topic: overview
ms.date: 04/09/2018
ms.custom: mvc
ms.openlocfilehash: 95167791efe13526b0a70c28fa89771542a9d220
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98685566"
---
# <a name="choose-the-right-integration-and-automation-services-in-azure"></a>Choose the right integration and automation services in Azure (Escolher os serviços de integração e automatização certos no Azure)

Este artigo compara os seguintes serviços cloud do Microsoft:

* [Microsoft Power Automamate](https://flow.microsoft.com/) (foi Microsoft Flow)
* [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Funções do Azure](https://azure.microsoft.com/services/functions/)
* [WebJobs do Serviço de Aplicações do Azure](../app-service/webjobs-create.md)

Todos estes serviços podem resolver problemas de integração e automatizar processos comerciais. Podem todos definir entradas, ações, condições e saídas. Pode executar cada um com base numa agenda ou num acionador. Cada serviço tem vantagens únicas, e este artigo explica as diferenças. 

Se procura uma comparação mais geral entre as Funções Azure e outras opções de computação Azure, consulte [Critérios para escolher um serviço de computação Azure](/azure/architecture/guide/technology-choices/compute-comparison) e [escolher uma opção de computação Azure para microserviços.](/azure/architecture/microservices/design/compute-options)

## <a name="compare-microsoft-power-automate-and-azure-logic-apps"></a>Compare as aplicações de automação de energia da Microsoft e a Azure Logic

Power Automamate e Logic Apps são ambos serviços de integração *de designers* que podem criar fluxos de trabalho. Ambos os serviços integram-se com diversas aplicações empresariais e SaaS. 

Power Automamate é construído em cima de Aplicações Lógicas. Partilham o mesmo designer de fluxo de trabalho e os [mesmos conectores.](../connectors/apis-list.md) 

A Power Automamate capacita qualquer trabalhador de escritório para realizar integrações simples (por exemplo, um processo de aprovação numa Biblioteca de Documentos SharePoint) sem passar por desenvolvedores ou TI. As Aplicações Lógicas também podem permitir integrações avançadas (por exemplo, processos B2B) onde são necessárias práticas de segurança a nível empresarial Azure DevOps e de segurança. É normal um fluxo de trabalho do negócio se tornar cada vez mais complexo ao longo do tempo. Assim, pode começar com um fluxo no início e depois convertê-lo numa aplicação lógica, conforme necessário.

A tabela a seguir ajuda-o a determinar se o Power Automamate ou o Logic Apps são os melhores para uma determinada integração:

|  | Power Automate | Logic Apps |
| --- | --- | --- |
| **Utilizadores** |Trabalhadores do escritório, utilizadores empresariais, administradores do SharePoint |Integradores e programadores profissionais, profissionais de TI |
| **Cenários** |Gestão personalizada |Integrações avançadas |
| **Ferramenta de design** |Aplicação no browser e móvel, apenas IU |No browser e [Visual Studio](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), [Vista de código](../logic-apps/logic-apps-author-definitions.md) disponível |
| **Gestão do ciclo de vida da aplicação (ALM)** |Conceber e testar em ambientes não produtivos, promover a produção quando estiver pronto |Azure DevOps: controlo de fontes, testes, suporte, automação e gestão no [Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) |
| **Experiência de administração** |Gerir ambientes de automatização de energia e políticas de prevenção de perdas de dados (DLP), acompanhar o licenciamento: [Centro de Administração](https://admin.flow.microsoft.com) |Gerir grupos de recursos, conexões, gestão de acessos e registos: [Portal Azure](https://portal.azure.com) |
| **Segurança** |Microsoft 365 registos de auditoria de segurança, DLP, [encriptação em repouso](https://wikipedia.org/wiki/Data_at_rest#Encryption) para dados sensíveis |Garantia de segurança da Azure: [Segurança Azure,](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity) [Azure Security Center,](https://azure.microsoft.com/services/security-center/) [registos de auditoria](https://azure.microsoft.com/blog/azure-audit-logs-ux-refresh/) |

## <a name="compare-azure-functions-and-azure-logic-apps"></a>Comparar o Azure Functions e o Azure Logic Apps

O Functions e o Logic Apps são serviços do Azure que permitem cargas de trabalho sem servidor. Azure Functions é um serviço de computação sem servidor, enquanto a Azure Logic Apps fornece fluxos de trabalho sem servidor. Ambos podem criar *orquestrações complexas.* Uma orquestração é uma coleção de funções ou passos, chamados *ações* no Logic Apps, que são executados para cumprir tarefas complexas. Por exemplo, para processar um lote de encomendas, pode executar muitas instâncias de uma função em paralelo, esperar que todas as instâncias terminem e, em seguida, executar uma função que calcula um resultado no agregado.

Nas Funções do Azure, vai desenvolver orquestrações ao escrever código e utilizar a [extensão Durable Functions](durable/durable-functions-overview.md). Para o Logic Apps, crie orquestrações ao utilizar um GUI ou editar os ficheiros de configuração.

Pode combinar serviços ao criar uma orquestração, chamar funções de aplicações lógicas e chamar aplicações lógicas de funções. Selecione como criar cada orquestração com base nas capacidades dos serviços ou nas suas preferências pessoais. A tabela que se segue enumera algumas das principais diferenças entre estes:

|  | Funções Duráveis | Logic Apps |
| --- | --- | --- |
| **Desenvolvimento** | Baseado em código (imperativo) | Baseado no estruturador (declarativo) |
| **Conetividade** | [Cerca de uma dúzia de tipos de encadernação incorporados,](functions-triggers-bindings.md#supported-bindings)código de escrita para encadernações personalizadas | [Grande coleção de conectores](../connectors/apis-list.md), Pacote de [Integração Empresarial para cenários B2B,](../logic-apps/logic-apps-enterprise-integration-overview.md) [construa conectores personalizados](../logic-apps/custom-connector-overview.md) |
| **Ações** | Cada atividade é uma função do Azure; escrever código para funções de atividades |[Grande coleção de ações prontas a utilizar](../logic-apps/logic-apps-workflow-actions-triggers.md)|
| **Monitorização** | [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) | [Portal Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md), [registos do Monitor Azure](../logic-apps/monitor-logic-apps.md)|
| **Gestão** | [REST API](durable/durable-functions-http-api.md), [Estúdio Visual](/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view=vs-2019) | [Portal Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md), [REST API,](/rest/api/logic/) [PowerShell,](/powershell/module/az.logicapp) [Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md) |
| **Contexto de execução** | Pode correr [localmente](functions-runtime-overview.md) ou na nuvem | São executadas apenas na cloud|

<a name="function"></a>

## <a name="compare-functions-and-webjobs"></a>Comparar o Functions e o WebJobs

Tal como as Funções do Azure, o Serviço de Aplicações do Azure WebJobs com o SDK do WebJobs é um serviço de integração *baseado em código* e concebido para programadores. Ambos são criados no [Serviço de Aplicações do Azure](../app-service/overview.md) e suportam funcionalidades como a [integração de controlo de origem](../app-service/deploy-continuous-deployment.md), a [autenticação](../app-service/overview-authentication-authorization.md) e a [monitorização com integração do Application Insights](functions-monitoring.md).

### <a name="webjobs-and-the-webjobs-sdk"></a>WebJobs e o SDK de WebJobs

Pode utilizar a funcionalidade *WebJobs* do Serviço de Aplicações para executar um script ou código no contexto de uma aplicação web do Serviço de Aplicações. O *SDK do WebJobs* é uma arquitetura concebida para o WebJobs que simplifica o código que escreveu para responder a eventos em serviços do Azure. Por exemplo, pode responder à criação de uma bolha de imagem no Azure Storage criando uma imagem de miniatura. O SDK do WebJobs é executado como uma aplicação da consola .NET, a qual pode implementar num WebJob. 

O WebJobs e o SDK do WebJobs funcionam melhor em conjunto, mas pode utilizar o WebJobs sem o SDK do WebJobs e vice-versa. Um WebJob pode executar qualquer programa ou script que execute na sandbox do Serviço de Aplicações. Uma aplicação de consola do SDK do WebJobs pode ser executada em qualquer local onde as aplicações de consolas se executam, como nos servidores no local.

### <a name="comparison-table"></a>Tabela de comparação

As Funções do Azure são criadas no SDK do WebJobs, pelo que partilham muitos dos acionadores de eventos e das ligações a outros serviços do Azure. Aqui ficam alguns fatores a ter em conta quando estiver a escolher entre as Funções Azure e webJobs com o WebJobs SDK:

|  | Funções | WebJobs com o SDK do WebJobs |
| --- | --- | --- |
|**[Modelo de aplicativo sem servidor](https://azure.microsoft.com/solutions/serverless/) com escala [automática](event-driven-scaling.md)**|✔||
|**[Desenvolver e testar no navegador](./functions-get-started.md)** |✔||
|**[Preços com pagamento por utilização](consumption-plan.md)**|✔||
|**[Integração com o Logic Apps](functions-twitter-email.md)**|✔||
| **Eventos de acionador** |[Temporizador](functions-bindings-timer.md)<br>[Filas e blobs de Armazenamento do Microsoft Azure](functions-bindings-storage-blob.md)<br>[Filas e tópicos do Azure Service Bus](functions-bindings-service-bus.md)<br>[BD do Cosmos para o Azure](functions-bindings-cosmosdb.md)<br>[Azure Event Hubs](functions-bindings-event-hubs.md)<br>[HTTP/WebHook (GitHub, Slack)](functions-bindings-http-webhook.md)<br>[Azure Event Grid](functions-bindings-event-grid.md)|[Temporizador](functions-bindings-timer.md)<br>[Filas e blobs de Armazenamento do Microsoft Azure](functions-bindings-storage-blob.md)<br>[Filas e tópicos do Azure Service Bus](functions-bindings-service-bus.md)<br>[BD do Cosmos para o Azure](functions-bindings-cosmosdb.md)<br>[Azure Event Hubs](functions-bindings-event-hubs.md)<br>[Sistema de Ficheiros](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Files/FileTriggerAttribute.cs)|
| **Idiomas suportados**  |C#<br>F#<br>JavaScript<br>Java<br>Python<br>PowerShell |C#<sup>1</sup>|
|**Gestores de pacotes**|NPM e NuGet|NuGet<sup>2</sup>|

<sup>1</sup> WebJobs (sem o WebJobs SDK) suporta C#, Java, JavaScript, Bash, .cmd, .bat, PowerShell, PHP, TypeScript, Python, entre outros. Esta não é uma lista completa. Um WebJob pode executar qualquer programa ou script que possa ser executado na sandbox do Serviço de Aplicações.

<sup>2</sup> WebJobs (sem o WebJobs SDK) suporta NPM e NuGet.

### <a name="summary"></a>Resumo

O Azure Functions oferece mais produtividade do programador do que o Azure App Service WebJobs. Também oferece mais opções para a programação de linguagens, ambientes de desenvolvimento, integração de serviços Azure e preços. Para a maioria dos cenários, é a melhor escolha.

Eis dois cenários para os quais o WebJobs pode ser a melhor escolha:

* Precisa de ter mais controlo sobre o código que escuta eventos, o objeto `JobHost`. As Funções oferecem um número limitado de formas de personalizar o comportamento `JobHost` no ficheiro [host.json](functions-host-json.md). Por vezes, precisa de fazer coisas que não podem ser especificadas por uma cadeia num ficheiro JSON. Por exemplo, apenas o SDK do WebJobs permite configurar uma política de repetição personalizada para o Armazenamento do Microsoft Azure.
* Tem uma aplicação de Serviço de Aplicações para a qual pretende executar código snippets, e quer geri-los juntos no mesmo ambiente Azure DevOps.

Para outros cenários nos quais pretenda executar fragmentos de código para integrar serviços do Azure ou de terceiros, selecione as Funções do Azure sobre o WebJobs com o SDK do WebJobs.

<a name="together"></a>

## <a name="power-automate-logic-apps-functions-and-webjobs-together"></a>Power Automamate, Logic Apps, Functions e WebJobs juntos

Não tens de escolher apenas um destes serviços. Integram-se uns aos outros, assim como com serviços externos.

Um fluxo pode chamar uma aplicação lógica. Uma aplicação lógica pode chamar uma função e uma função pode chamar uma aplicação lógica. Consulte, por exemplo, [Criar uma função que se integra no Azure Logic Apps](functions-twitter-email.md).

A integração entre Power Automamate, Logic Apps e Functions continua a melhorar ao longo do tempo. Pode criar algo num serviço e utilizar noutros serviços.

Pode obter mais informações sobre serviços de integração utilizando os seguintes links:

* [Tirar partido das Funções do Azure e do Serviço de Aplicações do Azure para cenários de integração por Christopher Anderson](https://www.biztalk360.com/integrate-2016-resources/leveraging-azure-functions-azure-app-service-integration-scenarios/)
* [Integrações Simplificadas por Charles Lamanna](https://www.biztalk360.com/integrate-2016-resources/integrations-made-simple/)
* [Webcast logic Apps Live](https://aka.ms/logicappslive)
* [Power Automamate frequentemente fez perguntas](/power-automate/frequently-asked-questions)

## <a name="next-steps"></a>Passos seguintes

Comece por criar o seu primeiro fluxo, aplicação lógica ou aplicação de funções. Selecione qualquer um dos seguintes links:

* [Introdução ao Power Automate](/power-automate/getting-started)
* [Criar uma aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Crie a sua primeira função Azure](./functions-get-started.md)