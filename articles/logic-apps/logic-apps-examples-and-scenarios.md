---
title: Exemplos e cenários comuns
description: Encontre exemplos, cenários comuns, tutoriais e walkthroughs para Apps Lógicas Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: c64483f18b2deb061704a4eb7171e575fdd561e8
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "78164632"
---
# <a name="common-scenarios-examples-tutorials-and-walkthroughs-for-azure-logic-apps"></a>Cenários comuns, exemplos, tutoriais e walkthroughs para Apps Lógicas Azure

[As Aplicações Lógicas Azure](../logic-apps/logic-apps-overview.md) ajudam-no a orquestrar e integrar diferentes serviços, fornecendo [centenas de conectores prontos a usar,](../connectors/apis-list.md)desde o SQL Server ou SAP até aos Serviços Cognitivos Azure. O serviço de Aplicações Lógicas é "servidor", por isso não tem de se preocupar com escala ou instâncias. Tudo o que tem de fazer é definir o fluxo de trabalho com um gatilho e as ações que o fluxo de trabalho realiza. A plataforma subjacente lida com a escala, disponibilidade e desempenho. As Aplicações Lógicas são especialmente úteis para usar casos e cenários onde você precisa coordenar ações em vários sistemas e serviços.

Para ajudá-lo a aprender sobre as capacidades e padrões que as Aplicações Lógicas Azure suportam, este artigo descreve pontos de partida comuns, exemplos e cenários.

## <a name="common-starting-points-for-logic-app-workflows"></a>Pontos de partida comuns para fluxos de trabalho de aplicações lógicas

Cada aplicação lógica começa com um [*gatilho*](../logic-apps/logic-apps-overview.md#logic-app-concepts), e apenas um gatilho, que inicia o fluxo de trabalho da sua aplicação lógica e passa em qualquer dado como parte desse gatilho. Alguns conectores fornecem gatilhos, que vêm nestes tipos:

* *Desencadeadores de sondagens*: Verifica regularmente um ponto final de serviço para novos dados. Quando existem novos dados, o gatilho cria e executa uma nova instância de fluxo de trabalho com os dados como entrada.

* *Premir os gatilhos*: Ouve os dados num ponto final do serviço e aguarda até que aconteça um evento específico. Quando o evento acontece, o gatilho dispara imediatamente, criando e executando uma nova instância de fluxo de trabalho que usa quaisquer dados disponíveis como entrada.

Aqui estão exemplos que descrevem gatilhos comumente usados:

* Os gatilhos *das sondagens:*

  * O gatilho de [ **recorrência** ](../connectors/connectors-native-recurrence.md) permite definir a data e a hora de início mais a recorrência para disparar a sua aplicação lógica. Por exemplo, pode selecionar os dias da semana e as horas do dia para desencadear a sua aplicação lógica. Para mais informações, consulte estes tópicos:<p>

    * [Agendar e executar tarefas, processos e fluxos de trabalho automatizados com o Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)
    * [Tutorial: Criar fluxos de trabalho recorrentes automatizados e baseados em horários utilizando aplicações lógicas azure](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)

  * O **Trigger Quando um e-mail é recebido** permite que a sua aplicação lógica verifique por qualquer novo e-mail de qualquer fornecedor de correio que seja suportado por Apps Lógicas, por exemplo, Office [365 Outlook](../connectors/connectors-create-api-office365-outlook.md), [Gmail,](https://docs.microsoft.com/connectors/gmail/) [Outlook.com](https://docs.microsoft.com/connectors/outlook/), e assim por diante. Para mais informações, consulte estes tópicos:<p>

    * [Tutorial: Criar fluxos de trabalho automatizados baseados em aprovação utilizando aplicações da Lógica Azure](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
    * [Tutorial: Automatizar tarefas para processar e-mails utilizando aplicações da Lógica Azure, Funções Azure e Armazenamento Azure](../logic-apps/tutorial-process-email-attachments-workflow.md)

  * O gatilho [ **HTTP** ](../connectors/connectors-native-http.md) pode ligar para um ponto final de serviço em HTTP ou HTTPS. Para mais informações, consulte [call, trigger ou nest workflows utilizando pontos finais HTTP](../logic-apps/logic-apps-http-endpoint.md).

* *Gatilhos de impulso:*

  * O gatilho [ **do Pedido** ](../connectors/connectors-native-reqres.md) pode receber pedidos HTTPS recebidos.

  * O gatilho [ **HTTP Webhook** ](../connectors/connectors-native-webhook.md) subscreve um ponto final de serviço registando um URL de *callback* com esse serviço. Dessa forma, o serviço pode apenas notificar o gatilho quando o evento especificado acontecer, para que o gatilho não precise de sondar o serviço.

Após o evento especificado acontecer, o gatilho dispara, o que cria uma nova instância de fluxo de aplicações lógica seletiva e executa as ações no fluxo de trabalho. Pode aceder a quaisquer dados a partir do gatilho durante todo o fluxo de trabalho. Por exemplo, o Twitter **Num novo tweet** transmite o conteúdo do tweet para a aplicação lógica. Para começar com as Aplicações Lógicas Azure, experimente estes tópicos de arranque rápido:

* [Quickstart: Crie o seu primeiro fluxo de trabalho automatizado utilizando aplicações da Azure Logic - portal Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Quickstart: Criar tarefas automatizadas, processos e fluxos de trabalho utilizando aplicações lógicas azure - Estúdio Visual](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Quickstart: Criar e gerir fluxos de trabalho de aplicações lógicaautomatizadas utilizando o Código do Estúdio Visual](../logic-apps/quickstart-create-logic-apps-visual-studio-code.md)

## <a name="control-flow-and-error-handling-capabilities"></a>Capacidades de controlo do fluxo e do manuseamento de erros

As aplicações lógicas incluem capacidades ricas para um fluxo de controlo avançado, tais como condições, interruptores, loops e âmbitos. Para garantir soluções resilientes, também pode implementar erros e manipulação de exceções nos seus fluxos de trabalho.

* Realizar diferentes ações com base em [declarações condicionadas](../logic-apps/logic-apps-control-flow-conditional-statement.md) e [declarações de switch](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Repita passos ou itens de processo em matrizes e coleções com loops](../logic-apps/logic-apps-control-flow-loops.md)
* [Ações de grupo em conjunto com âmbitos](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* [Adicione o erro e o manuseamento de exceções a um fluxo de trabalho](../logic-apps/logic-apps-exception-handling.md)
* [Caso de utilização: Como uma empresa de cuidados de saúde usa o tratamento de exceção lógica de aplicações para fluxos de trabalho HL7 FHIR](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)

## <a name="create-custom-apis-and-connectors"></a>Criar APIs e conectores personalizados

Para sistemas e serviços que não tenham publicado conectores, também pode alargar aplicações lógicas.

* [Criar APIs personalizados para ligar de Aplicações Lógicas Azure](../logic-apps/logic-apps-create-api-app.md)
* [Verifique regularmente novos dados ou eventos utilizando o padrão de gatilho de sondagens](../logic-apps/logic-apps-create-api-app.md#polling-triggers)
* [Aguarde e ouça novos dados ou eventos utilizando o padrão de gatilho webhook](../logic-apps/logic-apps-create-api-app.md#webhook-triggers)
* [Execute longas tarefas de corrida utilizando o padrão de ação de sondagens](../logic-apps/logic-apps-create-api-app.md#async-pattern)
* [Execute tarefas de longo prazo utilizando o padrão de ação webhook](../logic-apps/logic-apps-create-api-app.md#webhook-actions)
* [Conectores personalizados em Aplicações Lógicas Azure](../logic-apps/custom-connector-overview.md)

## <a name="build-business-to-business-b2b-solutions"></a>Construir soluções business-to-business (B2B)

Para soluções de integração empresarial e comunicação perfeita entre organizações, pode construir fluxos de trabalho automatizados escaláveis para estes cenários utilizando o Enterprise Integration Pack (EIP) com apps azure logic. Embora as organizações utilizem diferentes protocolos e formatos, podem trocar mensagens eletronicamente. O EIP transforma diferentes formatos num formato que os sistemas das suas organizações podem processar e apoiar protocolos padrão da indústria, incluindo AS2, X12, EDIFACT e RosettaNet. Para construir estas soluções, cria-se uma conta de integração, que é um recurso Azure separado que fornece um recipiente seguro, escalável e manejável para os artefactos que define e utiliza com os fluxos de trabalho da sua aplicação lógica. Por exemplo, os artefactos incluem parceiros comerciais, acordos, mapas, schemas, certificados e configurações de lotes.

* [Visão geral: Soluções de integração empresarial B2B com Aplicações Da Lógica Azure e Pacote de Integração Empresarial](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Criar e gerir contas de integração para integrações empresariais B2B em Aplicações Lógicas Azure](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

## <a name="access-azure-virtual-network-resources"></a>Acesso aos recursos da rede virtual Azure

Por vezes, as suas aplicações lógicas e contas de integração precisam de acesso a recursos seguros, como máquinas virtuais (VMs) e outros sistemas ou serviços, que se encontram numa rede virtual Azure. Para configurar este acesso, pode criar um ambiente de serviço de integração (ISE) onde pode construir e executar as suas aplicações lógicas. O ISE é uma instância privada e isolada do serviço De Aplicações Lógicas que utiliza recursos dedicados, como armazenamento, e funciona separadamente do público, serviço "global", multi-inquilino Logic Apps. Separar a sua instância privada isolada e a instância global pública também ajuda a reduzir o impacto que outros inquilinos do Azure podem ter no desempenho das suas apps, que também é conhecido como o efeito "vizinhos barulhentos".

* [Visão geral: Acesso aos recursos da rede virtual Azure a partir de Aplicações Lógicas Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [Ligue-se a redes virtuais Azure a partir de Aplicações Lógicas Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

## <a name="deploy-manage-and-monitor-logic-apps"></a>Implementar, gerir e monitorizar aplicações lógicas

Pode desenvolver e implementar aplicações lógicas com o Visual Studio, o Azure DevOps ou qualquer outro controlo de origem e ferramentas de construção automatizadas. Para suportar a implementação de fluxos de trabalho e ligações dependentes num modelo de recurso, as aplicações lógicas utilizam modelos de implementação de recursos Azure. As ferramentas do Estúdio Visual geram automaticamente estes modelos, que pode fazer o check-in no controlo de origem para a versão. Para registos de notificação e diagnóstico para o estado do fluxo de trabalho, as Aplicações Lógicas Azure também fornecem monitorização e alertas.

### <a name="deploy"></a>Implementação

* [Quickstart: Criar tarefas automatizadas, processos e fluxos de trabalho utilizando aplicações lógicas azure - Estúdio Visual](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Visão geral: Implementação de aplicações lógicas Automate](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Crie modelos de Gestor de Recursos Azure para automatizar implementação para aplicações lógicas azure](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
* [Implementar modelos de Gestor de Recursos Azure para aplicações lógicas azure](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)
* [Amostra: Ligue-se às filas de ônibus de serviço azure de Aplicações Da Lógica Azure e implemente com pipelines Azure em Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Amostra: Ligue-se a contas de armazenamento azure de Aplicações Lógicas Azure e implante com pipelines Azure em Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Amostra: Criar uma aplicação de função para apps azure logic e implementar com pipelines Azure em Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Amostra: Ligue-se a uma conta de integração da Azure Logic Apps e implemente com pipelines Azure em Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)
* [Amostra: Orquestrar gasodutos Azure utilizando aplicações da Azure Logic](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-pipeline-orchestration/azure-devops-orchestration-with-logic-apps/)

### <a name="manage"></a>Gerir

* [Gerir aplicações lógicas usando o Estúdio Visual](../logic-apps/manage-logic-apps-with-visual-studio.md)
* [Criar e gerir contas de integração para integrações empresariais B2B](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)
* [Gerir o ambiente de serviço de integração (ISE) em Aplicações Lógicas Azure](../logic-apps/ise-manage-integration-service-environment.md)

### <a name="monitor"></a>Monitorizar

* [Monitorize o estado da execução, reveja o histórico do gatilho e instale alertas para aplicações lógicas do Azure](../logic-apps/monitor-logic-apps.md)
* [Configurar registos do Monitor Azure e recolher dados de diagnóstico para aplicações lógicas do Azure](../logic-apps/monitor-logic-apps-log-analytics.md)
* [Configurar registos do Monitor Azure e recolher dados de diagnóstico para mensagens B2B em Aplicações Lógicas Azure](../logic-apps/monitor-b2b-messages-log-analytics.md)
* [Ver e criar consultas para monitorização e rastreio em registos do Monitor Azure para aplicações lógicas azure](../logic-apps/create-monitoring-tracking-queries.md)

## <a name="handle-content-types-conversions-and-transformations"></a>Lidar com tipos de conteúdo, conversões e transformações

Pode aceder, converter e transformar vários tipos de conteúdo utilizando as muitas funções na linguagem de [definição](https://aka.ms/logicappsdocs)de fluxo de trabalho das Aplicações Lógicas Azure . Por exemplo, pode converter entre uma cadeia, JSON e XML com as expressões de fluxo de trabalho `@json()` e `@xml()`. O motor Logic Apps preserva os tipos de conteúdo para suportar a transferência de conteúdos de forma sem perdas entre os serviços.

* [Manuseie tipos de conteúdo em Aplicações Lógicas Azure](../logic-apps/logic-apps-content-type.md), como `application/`, `application/octet-stream`e `multipart/formdata`
* [Guia de referência para a utilização de funções em expressões para aplicações lógicas azure e automatização de potência](../logic-apps/workflow-definition-language-functions-reference.md)
* [Esquema de linguagem de definição de fluxo de trabalho para aplicações lógicas azure](../logic-apps/logic-apps-workflow-definition-language.md)

## <a name="other-integrations-and-capabilities"></a>Outras integrações e capacidades

As Aplicações Lógicas Azure integram-se com muitos serviços, como funções Azure, Gestão API Azure, Serviço de Aplicações Azure e pontos finais personalizados http, por exemplo, REST e SOAP.

* [Ligue para as funções azure de aplicações lógicas azure](../logic-apps/logic-apps-azure-functions.md)
* [Tutorial: Ligue ou desencadeie aplicações lógicas usando funções Azure e ônibus de serviço Azure](../logic-apps/logic-apps-scenario-function-sb-trigger.md)
* [Tutorial: Criar um dashboard de informação de clientes em streaming com aplicações da Lógica Azure e funções azure](../logic-apps/logic-apps-scenario-social-serverless.md)
* [Tutorial: Criar uma função que se integre com apps da Lógica Azure e Serviços Cognitivos Azure para analisar o sentimento de post do Twitter](../azure-functions/functions-twitter-email.md)
* [Tutorial: Construa um dashboard social movido a IA utilizando aplicações lógicas power bi e azure](https://aka.ms/logicappsdemo)
* [Tutorial: Monitorize as mudanças de máquinavirtual utilizando a Grelha de Eventos Azure e aplicações lógicas](../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md)
* [Tutorial: Monitorização remota ioT e notificações com Aplicações Lógicas Azure que ligam o seu hub IoT e caixa de correio](../iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps.md)
* [Blog: Call SOAP serviços utilizando aplicações da Lógica Azure](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)

## <a name="end-to-end-scenarios"></a>Cenários ponto a ponto

* [Whitepaper: Integração de gestão de casos de ponta a ponta com serviços Azure, tais como Aplicações Lógicas](https://aka.ms/enterprise-integration-e2e-case-management-utilities-logic-apps)

## <a name="customer-stories"></a>Histórias dos clientes

Saiba como as Aplicações Lógicas Azure, juntamente com outros serviços Azure e produtos da Microsoft, ajudaram [estas empresas](https://aka.ms/logic-apps-customer-stories) a melhorar a sua agilidade e a focar-se nos seus negócios centrais, simplificando, organizando, automatizando e orquestrando processos complexos.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [conectores para aplicações lógicas](../connectors/apis-list.md)
* Conheça [cenários de integração empresarial B2B com aplicações da Lógica Azure](../logic-apps/logic-apps-enterprise-integration-overview.md)
