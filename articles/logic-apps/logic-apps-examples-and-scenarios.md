---
title: Exemplos & cenários comuns
description: Encontre exemplos, cenários comuns, tutoriais e walkthroughs para Apps Azure Logic
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: 207b597bc865c8234d447759ab8b0f53dc35413c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87090285"
---
# <a name="common-scenarios-examples-tutorials-and-walkthroughs-for-azure-logic-apps"></a>Cenários comuns, exemplos, tutoriais e walkthroughs para apps Azure Logic

[O Azure Logic Apps](../logic-apps/logic-apps-overview.md) ajuda-o a orquestrar e a integrar diferentes serviços, fornecendo [centenas de conectores prontos a usar,](../connectors/apis-list.md)desde o SQL Server ou o SAP até à Azure Cognitive Services. O serviço De aplicações lógicas é "sem servidor", pelo que não tem de se preocupar com escala ou instâncias. Tudo o que tem de fazer é definir o fluxo de trabalho com um gatilho e as ações que o fluxo de trabalho realiza. A plataforma subjacente lida com escala, disponibilidade e desempenho. As Aplicações Lógicas são especialmente úteis para a utilização de casos e cenários onde é necessário coordenar ações em vários sistemas e serviços.

Para ajudá-lo a aprender sobre as capacidades e padrões que a Azure Logic Apps suporta, este artigo descreve pontos de partida comuns, exemplos e cenários.

## <a name="common-starting-points-for-logic-app-workflows"></a>Pontos de partida comuns para fluxos de trabalho de aplicações lógicas

Cada aplicação lógica começa com um [*gatilho*](../logic-apps/logic-apps-overview.md#logic-app-concepts), e apenas um gatilho, que inicia o fluxo de trabalho da aplicação lógica e passa em qualquer dado como parte desse gatilho. Alguns conectores fornecem gatilhos, que vêm neste tipo:

* *Disparos de sondagens*: Verifica regularmente um ponto final de serviço para novos dados. Quando existem novos dados, o gatilho cria e executa uma nova instância de fluxo de trabalho com os dados como entrada.

* *Aciona os gatilhos*: Ouve os dados num ponto final de serviço e aguarda até que um evento específico aconteça. Quando o evento acontece, o gatilho dispara imediatamente, criando e executando uma nova instância de fluxo de trabalho que utiliza todos os dados disponíveis como entrada.

Aqui estão exemplos que descrevem os gatilhos comumente usados:

* *As sondagens* disparam:

  * [O gatilho **de recorrência** ](../connectors/connectors-native-recurrence.md) permite-lhe definir a data e hora de início mais a recorrência para disparar a sua aplicação lógica. Por exemplo, pode selecionar os dias da semana e as horas do dia para ativar a sua aplicação lógica. Para obter mais informações, veja estes tópicos:<p>

    * [Agendar e executar tarefas, processos e fluxos de trabalho automatizados com o Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)
    * [Tutorial: Criar fluxos de trabalho automáticos e recorrentes baseados em horários utilizando apps Azure Logic](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)

  * O gatilho **Quando um e-mail é recebido** permite que a sua aplicação lógica verifique por um novo e-mail de qualquer fornecedor de correio que seja suportado por Aplicações Lógicas, por exemplo, Office [365 Outlook](../connectors/connectors-create-api-office365-outlook.md), [Gmail](/connectors/gmail/), [Outlook.com](/connectors/outlook/), e assim por diante.

    > [!IMPORTANT]
    > Se quiser utilizar o conector do Gmail, apenas as contas de negócios da G-Suite podem utilizar este conector sem restrições em aplicações lógicas. Se tiver uma conta de consumo do Gmail, pode utilizar este conector apenas com serviços específicos aprovados pela Google, ou pode [criar uma aplicação para clientes da Google para utilizar para autenticação com o seu conector Gmail.](/connectors/gmail/#authentication-and-bring-your-own-application) Para obter mais informações, consulte [as políticas de segurança de dados e privacidade para conectores google em Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md).

    Para obter mais informações, veja estes tópicos:<p>

    * [Tutorial: Criar fluxos de trabalho automatizados baseados em aprovação utilizando apps Azure Logic](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
    * [Tutorial: Automatizar tarefas para processar e-mails utilizando apps Azure Logic, Funções Azure e Armazenamento Azure](../logic-apps/tutorial-process-email-attachments-workflow.md)

  * O gatilho [ **HTTP** ](../connectors/connectors-native-http.md) pode chamar um ponto final de serviço em HTTP ou HTTPS. Para obter mais informações, consulte [fluxos de trabalho de chamada, gatilho ou ninho utilizando pontos finais HTTP](../logic-apps/logic-apps-http-endpoint.md).

* *Acionadores:*

  * O gatilho [ **'Pedido'** ](../connectors/connectors-native-reqres.md) pode receber pedidos HTTPS de entrada.

  * O gatilho [ **HTTP Webhook** ](../connectors/connectors-native-webhook.md) subscreve um ponto final de serviço registando um *URL de retorno* com esse serviço. Dessa forma, o serviço pode simplesmente notificar o gatilho quando o evento especificado acontece, para que o gatilho não precise de sondar o serviço.

Após o evento especificado acontecer, o gatilho dispara, o que cria uma nova instância lógica de fluxo de trabalho de aplicações e executa as ações no fluxo de trabalho. Pode aceder a quaisquer dados do gatilho durante todo o fluxo de trabalho. Por exemplo, o Twitter Num novo gatilho **de tweet** passa o conteúdo do tweet para a aplicação lógica executada. Para começar com a Azure Logic Apps, experimente estes tópicos de arranque rápido:

* [Quickstart: Crie o seu primeiro fluxo de trabalho automatizado utilizando aplicações Azure Logic - portal Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Quickstart: Criar tarefas, processos e fluxos de trabalho automatizados utilizando apps Azure Logic - Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Quickstart: Criar e gerir fluxos de trabalho de aplicações lógicas automatizadas utilizando o Código do Estúdio Visual](../logic-apps/quickstart-create-logic-apps-visual-studio-code.md)

## <a name="control-flow-and-error-handling-capabilities"></a>Capacidades de controlo do fluxo e do tratamento de erros

As aplicações lógicas incluem capacidades ricas para fluxo avançado de controlo, tais como condições, interruptores, loops e âmbitos. Para garantir soluções resilientes, também pode implementar o tratamento de erros e exceções nos seus fluxos de trabalho.

* Realizar diferentes ações com base em [declarações condicionais](../logic-apps/logic-apps-control-flow-conditional-statement.md) e [declarações de switch](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Repita etapas ou itens de processo em matrizes e coleções com loops](../logic-apps/logic-apps-control-flow-loops.md)
* [Ações de grupo juntamente com âmbitos](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* [Adicione erro e tratamento de exceções a um fluxo de trabalho](../logic-apps/logic-apps-exception-handling.md)
* [Caso de utilização: Como uma empresa de cuidados de saúde usa o tratamento de exceção de aplicativos lógicos para fluxos de trabalho HL7 FHIR](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)

## <a name="create-custom-apis-and-connectors"></a>Criar APIs e conectores personalizados

Para sistemas e serviços que não tenham conectores publicados, também pode estender aplicações lógicas.

* [Crie APIs personalizados para ligar a partir de Azure Logic Apps](../logic-apps/logic-apps-create-api-app.md)
* [Verifique regularmente novos dados ou eventos utilizando o padrão de disparo de sondagens](../logic-apps/logic-apps-create-api-app.md#polling-triggers)
* [Aguarde e ouça novos dados ou eventos usando o padrão de gatilho webhook](../logic-apps/logic-apps-create-api-app.md#webhook-triggers)
* [Realizar tarefas de longo prazo utilizando o padrão de ação de votação](../logic-apps/logic-apps-create-api-app.md#async-pattern)
* [Execute tarefas de longa duração utilizando o padrão de ação webhook](../logic-apps/logic-apps-create-api-app.md#webhook-actions)
* [Conectores personalizados em Apps LógicaS Azure](../logic-apps/custom-connector-overview.md)

## <a name="build-business-to-business-b2b-solutions"></a>Construir soluções business-to-business (B2B)

Para soluções de integração empresarial e comunicação perfeita entre organizações, pode construir fluxos de trabalho escaláveis automatizados para estes cenários utilizando o Pack de Integração Empresarial (EIP) com Apps Azure Logic. Embora as organizações utilizem diferentes protocolos e formatos, podem trocar mensagens eletronicamente. O EIP transforma diferentes formatos num formato que os sistemas das suas organizações podem processar e suportar protocolos padrão da indústria, incluindo AS2, X12, EDIFACT e RosettaNet. Para construir estas soluções, cria uma conta de integração, que é um recurso Azure separado que fornece um recipiente seguro, escalável e manejável para os artefactos que define e utiliza com os fluxos de trabalho da sua aplicação lógica. Por exemplo, os artefactos incluem parceiros comerciais, acordos, mapas, esquemas, certificados e configurações de lote.

* [Visão geral: Soluções de integração empresarial B2B com apps lógicas Azure e Pacote de Integração Empresarial](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Criar e gerir contas de integração para integrações empresariais B2B no Azure Logic Apps](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md).

## <a name="access-azure-virtual-network-resources"></a>Aceder recursos de rede virtual Azure

Por vezes, as suas aplicações lógicas e contas de integração precisam de acesso a recursos seguros, como máquinas virtuais (VMs) e outros sistemas ou serviços, que estão numa rede virtual Azure. Para configurar este acesso, pode criar um ambiente de serviço de integração (ISE) onde pode construir e executar as suas aplicações lógicas. Um ISE é uma instância privada e isolada do serviço Logic Apps que utiliza recursos dedicados como armazenamento, e funciona separadamente do público, serviço de Apps Lógicas multi-inquilinos. Separar o seu caso privado isolado e o caso público global também ajuda a reduzir o impacto que outros inquilinos do Azure podem ter no desempenho das suas apps, que também é conhecido como o efeito "vizinhos barulhentos".

* [Visão geral: Acesso aos recursos de rede virtual Azure a partir de Apps Azure Logic](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [Ligue-se a redes virtuais Azure a partir de Apps Azure Logic](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

## <a name="deploy-manage-and-monitor-logic-apps"></a>Implementar, gerir e monitorizar aplicações lógicas

Pode desenvolver e implementar totalmente aplicações lógicas com o Visual Studio, Azure DevOps ou qualquer outro controlo de origem e ferramentas de construção automatizadas. Para suportar a implementação de fluxos de trabalho e conexões dependentes num modelo de recurso, as aplicações lógicas usam modelos de implementação de recursos Azure. As ferramentas visual Studio geram automaticamente estes modelos, que pode fazer check-in no controlo de origem para a versão. Para registos de notificação e diagnóstico para o estado de execução do fluxo de trabalho, a Azure Logic Apps também fornece monitorização e alertas.

### <a name="deploy"></a>Implementação

* [Quickstart: Criar tarefas, processos e fluxos de trabalho automatizados utilizando apps Azure Logic - Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Visão geral: Automatizar a implementação de aplicativos de lógica](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Criar modelos do Azure Resource Manager para automatizar a implementação do Azure Logic Apps](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
* [Implementar modelos do Azure Resource Manager para o Azure Logic Apps](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)
* [Amostra: Ligue-se às filas de autocarros da Azure Service de Azure Logic Apps e implemente com a Azure Pipelines em Azure DevOps](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Amostra: Ligue-se às contas de armazenamento Azure a partir de Azure Logic Apps e implemente com a Azure Pipelines em Azure DevOps](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Amostra: Crie uma ação de aplicação de função para apps Azure Logic e implemente com a Azure Pipelines em Azure DevOps](/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Amostra: Ligue-se a uma conta de integração a partir de Azure Logic Apps e implemente com a Azure Pipelines em Azure DevOps](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)
* [Amostra: Orquestrar gasodutos Azure usando apps Azure Logic](/samples/azure-samples/azure-logic-apps-pipeline-orchestration/azure-devops-orchestration-with-logic-apps/)

### <a name="manage"></a>Gerir

* [Gerir aplicativos lógicos utilizando o Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)
* [Criar e gerir contas de integração para integrações empresariais B2B](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)
* [Gerir o ambiente de serviço de integração (ISE) em Azure Logic Apps](../logic-apps/ise-manage-integration-service-environment.md)

### <a name="monitor"></a>Monitorizar

* [Monitorizar o estado de execução, rever o histórico do acionador e configurar alertas para o Azure Logic Apps](../logic-apps/monitor-logic-apps.md)
* [Configurar registos do Azure Monitor e recolher dados de diagnóstico para apps Azure Logic](../logic-apps/monitor-logic-apps-log-analytics.md)
* [Configurar os registos do Azure Monitor e recolher dados de diagnóstico para mensagens B2B no Azure Logic Apps](../logic-apps/monitor-b2b-messages-log-analytics.md)
* [Ver e criar consultas para monitorização e rastreio em registos do Azure Monitor para Apps Azure Logic](../logic-apps/create-monitoring-tracking-queries.md)

## <a name="handle-content-types-conversions-and-transformations"></a>Lidar com tipos de conteúdo, conversões e transformações

Pode aceder, converter e transformar vários tipos de conteúdo utilizando as muitas funções no [idioma](https://aka.ms/logicappsdocs)de definição de fluxo de trabalho Azure Logic Apps . Por exemplo, pode converter entre uma corda, JSON e XML com as `@json()` expressões de fluxo de `@xml()` trabalho. O motor Logic Apps preserva os tipos de conteúdo para suportar a transferência de conteúdos de forma sem perdas entre os serviços.

* [Lidar com tipos de conteúdo em Azure Logic Apps](../logic-apps/logic-apps-content-type.md), tais `application/` `application/octet-stream` como, e `multipart/formdata`
* [Guia de referência para a utilização de funções em expressões para Apps lógicas Azure e Automatização de Energia](../logic-apps/workflow-definition-language-functions-reference.md)
* [Esquema de linguagem de definição de fluxo de trabalho para apps lógicas Azure](../logic-apps/logic-apps-workflow-definition-language.md)

## <a name="other-integrations-and-capabilities"></a>Outras integrações e capacidades

A Azure Logic Apps integra-se com muitos serviços, tais como Azure Functions, Azure API Management, Azure App Service e pontos finais http personalizados, por exemplo, REST e SOAP.

* [Ligue para as funções Azure de Azure Logic Apps](../logic-apps/logic-apps-azure-functions.md)
* [Tutorial: Ligue ou desencadeie aplicações lógicas utilizando as funções Azure e o Azure Service Bus](../logic-apps/logic-apps-scenario-function-sb-trigger.md)
* [Tutorial: Criar um painel de insights de clientes em streaming com apps Azure Logic e Funções Azure](../logic-apps/logic-apps-scenario-social-serverless.md)
* [Tutorial: Criar uma função que se integre com Azure Logic Apps e Azure Cognitive Services para analisar o sentimento de publicação do Twitter](../azure-functions/functions-twitter-email.md)
* [Tutorial: Construa um dashboard social movido a IA utilizando aplicações de lógica Power BI e Azure Logic](https://aka.ms/logicappsdemo)
* [Tutorial: Monitorizar alterações de máquina virtual através do Azure Event Grid e do Logic Apps](../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md)
* [Tutorial: Monitorização remota IoT e notificações com Azure Logic Apps que ligam o seu hub IoT e caixa de correio](../iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps.md)
* [Blog: Serviços de SABONETE de Chamada utilizando apps Azure Logic](/archive/blogs/logicapps/using-soap-services-with-logic-apps)

## <a name="end-to-end-scenarios"></a>Cenários ponto a ponto

* [Whitepaper: Integração de gestão de casos de ponta a ponta com serviços Azure, tais como Apps Lógicas](https://aka.ms/enterprise-integration-e2e-case-management-utilities-logic-apps)

## <a name="customer-stories"></a>Histórias dos clientes

Saiba como a Azure Logic Apps, juntamente com outros serviços Azure e produtos da Microsoft, ajudou [estas empresas](https://aka.ms/logic-apps-customer-stories) a melhorar a sua agilidade e a concentrarem-se nos seus negócios principais, simplificando, organizando, automatizando e orquestrando processos complexos.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais [sobre conectores para Apps Lógicas](../connectors/apis-list.md)
* Conheça os [cenários de integração empresarial B2B com a Azure Logic Apps](../logic-apps/logic-apps-enterprise-integration-overview.md)
