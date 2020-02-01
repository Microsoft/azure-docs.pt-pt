---
title: Exemplos e cenários comuns
description: Encontre exemplos, cenários comuns, tutoriais e walkthroughs para Apps Lógicas Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 07/31/2019
ms.openlocfilehash: 5b72ee02c2bbf811293a2bcdb15590e16e300a02
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906685"
---
# <a name="common-scenarios-examples-tutorials-and-walkthroughs-for-azure-logic-apps"></a>Cenários comuns, exemplos, tutoriais e walkthroughs para Apps Lógicas Azure

[As Aplicações Lógicas Azure](../logic-apps/logic-apps-overview.md) ajudam-no a orquestrar e integrar diferentes serviços, fornecendo [centenas de conectores prontos a usar,](../connectors/apis-list.md)desde o SQL Server ou SAP até aos Serviços Cognitivos Azure. O serviço de Aplicações Lógicas é "servidor", por isso não tem de se preocupar com escala ou instâncias. Tudo o que tem de fazer é definir o fluxo de trabalho com um gatilho e as ações que o fluxo de trabalho realiza. A plataforma subjacente lida com a escala, disponibilidade e desempenho. As Aplicações Lógicas são especialmente úteis para usar casos e cenários onde você precisa coordenar múltiplas ações em vários sistemas.

Para ajudá-lo a aprender mais sobre os muitos padrões e capacidades que as Aplicações Lógicas Azure suportam, aqui estão exemplos e cenários comuns.

## <a name="popular-starting-points-for-logic-app-workflows"></a>Pontos de partida populares para fluxos de trabalho de apps lógicas

Cada aplicação lógica começa com um [*gatilho*](../logic-apps/logic-apps-overview.md#logic-app-concepts), e apenas um gatilho, que inicia o fluxo de trabalho da sua aplicação lógica e passa em qualquer dado como parte desse gatilho. Alguns conectores fornecem gatilhos, que vêm nestes tipos:

* *Desencadeadores de sondagens*: Verifica regularmente um ponto final de serviço para novos dados. Quando existem novos dados, o gatilho cria e executa uma nova instância de fluxo de trabalho com os dados como entrada.

* *Premir os gatilhos*: Ouve os dados num ponto final do serviço e aguarda até que aconteça um evento específico. Quando o evento acontece, o gatilho dispara imediatamente, criando e executando uma nova instância de fluxo de trabalho que usa quaisquer dados disponíveis como entrada.

Aqui estão apenas alguns exemplos populares:

* Sondagem:

  * O gatilho de [ **recorrência** ](../connectors/connectors-native-recurrence.md) permite definir a data e a hora de início mais a recorrência para disparar a sua aplicação lógica. Por exemplo, pode selecionar os dias da semana e as horas do dia para desencadear a sua aplicação lógica. Para mais informações, consulte estes tópicos:

    * [Agendar e executar tarefas automatizadas recorrentes, processos e fluxos de trabalho com aplicações lógicas azure](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)
    * [Tutorial: Verifique o tráfego em horário com apps da Azure Logic](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)

  * O gatilho "Quando um e-mail é recebido" permite que a sua aplicação lógica verifique por um novo e-mail de qualquer fornecedor de correio que seja suportado por Aplicações Lógicas, por exemplo, [Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md), [Gmail,](https://docs.microsoft.com/connectors/gmail/) [Outlook.com](https://docs.microsoft.com/connectors/outlook/), e assim por diante. Para mais informações, consulte estes tópicos: 

    * [Tutorial: Gerir pedidos de lista de correio com aplicações da Azure Logic](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
    * [Tutorial: Automatizar e-mails e anexos com aplicações da Lógica Azure](../logic-apps/tutorial-process-email-attachments-workflow.md)

  * O gatilho [ **HTTP** ](../connectors/connectors-native-http.md) permite que a sua aplicação lógica verifique um ponto final de serviço especificado comunicando através do HTTP.
  
* Empurre:

  * O gatilho [ **do Pedido** ](../connectors/connectors-native-reqres.md) permite que a sua aplicação lógica receba pedidos HTTP e responda em tempo real a eventos de alguma forma.

  * O gatilho [ **HTTP Webhook** ](../connectors/connectors-native-webhook.md) subscreve um ponto final de serviço registando um URL de *callback* com esse serviço. Dessa forma, o serviço pode apenas notificar o gatilho quando o evento especificado acontecer, para que o gatilho não precise de sondar o serviço.

Depois de receber uma notificação sobre novos dados ou um evento, o gatilho dispara, cria uma nova instância de fluxo de aplicações lógica, e executa as ações no fluxo de trabalho. Pode aceder a quaisquer dados a partir do gatilho durante todo o fluxo de trabalho. Por exemplo, o gatilho "On a new tweet" passa o conteúdo do tweet para a aplicação lógica. Para começar com as Aplicações Lógicas Azure, experimente estes tópicos de arranque rápido:

* [Quickstart: Crie o seu primeiro fluxo de trabalho automatizado com aplicações da Azure Logic no portal Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Quickstart: Criar tarefas automatizadas, processos e fluxos de trabalho com aplicações da Lógica Azure utilizando o Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Quickstart: Criar e gerir fluxos de trabalho de aplicações lógicaautomatizadas utilizando o Código do Estúdio Visual](../logic-apps/quickstart-create-logic-apps-visual-studio-code.md)

## <a name="respond-to-triggers-and-extend-actions"></a>Responder aos gatilhos e alargar as ações

Para sistemas e serviços que podem não ter publicado conectores, também pode alargar aplicações lógicas.

* [Criar gatilhos ou ações personalizadas](../logic-apps/logic-apps-create-api-app.md)
* [Criar ações de longo prazo para corridas de fluxos de trabalho](../logic-apps/logic-apps-create-api-app.md)
* [Responder a eventos e ações externas com webhooks](../logic-apps/logic-apps-create-api-app.md)
* [Call, trigger ou nest workflows com respostas sincronizadas aos pedidos de HTTP](../logic-apps/logic-apps-http-endpoint.md)
* [Tutorial: Construa um dashboard social movido a IA em minutos com Aplicações Lógicas e Power BI](https://aka.ms/logicappsdemo)
* [Vídeo: Responda aos webhooks Twilio SMS e envie uma resposta de texto](https://channel9.msdn.com/Blogs/Windows-Azure/Azure-Logic-Apps-Walkthrough-Webhook-Functions-and-an-SMS-Bot)

## <a name="control-flow-error-handling-and-logging-capabilities"></a>Fluxo de controlo, manipulação de erros e capacidades de exploração madeireira

As aplicações lógicas incluem capacidades ricas para um fluxo avançado de controlo, como condições, interruptores, loops e âmbitos. Para garantir soluções resilientes, também pode implementar erros e manipulação de exceções nos seus fluxos de trabalho. Para registos de notificação e diagnóstico para o estado do fluxo de trabalho, as Aplicações Lógicas Azure também fornecem monitorização e alertas.

* Realizar diferentes ações com base em [declarações condicionadas](../logic-apps/logic-apps-control-flow-conditional-statement.md) e [declarações de switch](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Repita passos ou itens de processo em matrizes e coleções com loops](../logic-apps/logic-apps-control-flow-loops.md)
* [Ações de grupo em conjunto com âmbitos](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* [Erro do autor e manipulação de exceção num fluxo de trabalho](../logic-apps/logic-apps-exception-handling.md)
* [Caso de utilização: Como uma empresa de cuidados de saúde usa o tratamento de exceção lógica de aplicações para fluxos de trabalho HL7 FHIR](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Ligue a monitorização, o registo e os alertas para aplicações lógicas existentes](../logic-apps/monitor-logic-apps.md)
* [Ligue a monitorização e diagnóstico saqueamento ao criar aplicações lógicas](../logic-apps/monitor-logic-apps-log-analytics.md)

## <a name="deploy-and-manage-logic-apps"></a>Implementar e gerir aplicações lógicas

Pode desenvolver e implementar aplicações lógicas com o Visual Studio, o Azure DevOps ou qualquer outro controlo de origem e ferramentas de construção automatizadas. Para suportar a implementação de fluxos de trabalho e ligações dependentes num modelo de recurso, as aplicações lógicas utilizam modelos de implementação de recursos Azure. As ferramentas do Estúdio Visual geram automaticamente estes modelos, que pode fazer o check-in no controlo de origem para a versão.

* [Criar e implementar aplicações lógicas com o Estúdio Visual](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Ligue a monitorização, o registo e os alertas para aplicações lógicas existentes](../logic-apps/monitor-logic-apps.md)
* [Automatizar a implantação do aplicativo lógico](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Exemplo: conectar-se a filas do barramento de serviço do Azure de aplicativos lógicos do Azure e implantar com Azure Pipelines no Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Exemplo: conectar-se a contas de armazenamento do Azure de aplicativos lógicos do Azure e implantar com Azure Pipelines no Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Exemplo: configurar uma ação de aplicativo de funções para aplicativos lógicos do Azure e implantar com Azure Pipelines no Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Exemplo: conectar-se a uma conta de integração de aplicativos lógicos do Azure e implantar com Azure Pipelines no Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)

## <a name="content-types-conversions-and-transformations-within-a-run"></a>Tipos de conteúdo, conversões e transformações dentro de uma corrida

Pode aceder, converter e transformar vários tipos de conteúdo utilizando as muitas funções na linguagem de [definição](https://aka.ms/logicappsdocs)de fluxo de trabalho das Aplicações Lógicas Azure . Por exemplo, pode converter entre uma cadeia, JSON e XML com as expressões de fluxo de trabalho `@json()` e `@xml()`. O motor Logic Apps preserva os tipos de conteúdo para suportar a transferência de conteúdos de forma sem perdas entre os serviços.

* [Como as expressões de fluxo de trabalho funcionam em aplicações lógicas](../logic-apps/logic-apps-author-definitions.md)
* [Manuseie tipos de conteúdo não JSON](../logic-apps/logic-apps-content-type.md), como `application/xml`, `application/octet-stream`e `multipart/formdata`
* [Esquema de linguagem de definição de fluxo de trabalho para aplicações lógicas azure](https://aka.ms/logicappsdocs)

## <a name="other-integrations-and-capabilities"></a>Outras integrações e capacidades

As aplicações lógicas também oferecem integração com muitos serviços, como funções Azure, Gestão Azure API, Serviços de Aplicações Azure e pontos finais personalizados http, por exemplo, REST e SOAP.

* [Crie um dashboard social em tempo real com o Azure Serverless](../logic-apps/logic-apps-scenario-social-serverless.md)
* [Ligue para as funções do Azure a partir de aplicações lógicas](../logic-apps/logic-apps-azure-functions.md)
* [Tutorial: Aplicativos lógicos de gatilho com funções azure](../logic-apps/logic-apps-scenario-function-sb-trigger.md)
* [Tutorial: Monitorize alterações de máquinas virtuais com grelha de eventos azure e aplicações lógicas](../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md)
* [Tutorial: Criar uma função que se integre com apps da Lógica Azure e Serviços Cognitivos Azure para analisar o sentimento de post do Twitter](../azure-functions/functions-twitter-email.md)
* [Tutorial: Monitorização remota ioT e notificações com Aplicações Lógicas Azure que ligam o seu hub IoT e caixa de correio](../iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps.md)
* [Blog: Call SOAP endpoints from logic apps](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)

## <a name="end-to-end-scenarios"></a>Cenários ponto a ponto

* [Whitepaper: Integração de gestão de casos de ponta a ponta com serviços Azure, tais como Aplicações Lógicas](https://aka.ms/enterprise-integration-e2e-case-management-utilities-logic-apps)

## <a name="customer-stories"></a>Histórias de clientes

Saiba como as Aplicações Lógicas Azure, juntamente com outros serviços Azure e produtos da Microsoft, ajudaram [estas empresas](https://aka.ms/logic-apps-customer-stories) a melhorar a sua agilidade e a focar-se nos seus negócios centrais, simplificando, organizando, automatizando e orquestrando processos complexos.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [conectores para aplicações lógicas](../connectors/apis-list.md)
* Conheça [cenários de integração empresarial B2B com aplicações da Lógica Azure](../logic-apps/logic-apps-enterprise-integration-overview.md)
