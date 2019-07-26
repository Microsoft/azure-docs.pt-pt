---
title: Exemplos & cenários comuns – aplicativos lógicos do Azure | Microsoft Docs
description: Exemplos, cenários, tutoriais e orientações para os aplicativos lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.assetid: e06311bc-29eb-49df-9273-1f05bbb2395c
ms.date: 01/31/2018
ms.openlocfilehash: 95eca4c7f3e8170f6559799fc4c706e95df70e9e
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385515"
---
# <a name="common-scenarios-examples-tutorials-and-walkthroughs-for-azure-logic-apps"></a>Cenários comuns, exemplos, tutoriais e orientações para os aplicativos lógicos do Azure

Os [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md) ajudam a orquestrar e integrar serviços diferentes fornecendo [centenas de conectores prontos para uso](../connectors/apis-list.md), desde o SQL Server local ou o SAP para serviços cognitivas do Azure. O serviço de aplicativos lógicos é "sem servidor", portanto, você não precisa se preocupar com escala ou instâncias. Tudo o que você precisa fazer é definir o fluxo de trabalho com um gatilho e as ações que o fluxo de trabalho executa. A plataforma subjacente lida com escala, disponibilidade e desempenho. Os aplicativos lógicos são especialmente úteis para casos de uso e cenários em que você precisa coordenar várias ações em vários sistemas.

Para ajudá-lo a saber mais sobre os vários padrões e recursos aos quais o [aplicativo lógico do Azure](../logic-apps/logic-apps-overview.md) dá suporte, aqui estão exemplos e cenários comuns.

## <a name="popular-starting-points-for-logic-app-workflows"></a>Pontos de partida populares para fluxos de trabalho de aplicativo lógico

Cada aplicativo lógico começa com um [*gatilho*](../logic-apps/logic-apps-overview.md#logic-app-concepts)e apenas um gatilho, que inicia o fluxo de trabalho do aplicativo lógico e passa dados como parte desse gatilho. Alguns conectores fornecem gatilhos, que são fornecidos nesses tipos:

* *Gatilhos*de sondagem: Verifica regularmente se há novos dados em um ponto de extremidade de serviço. Quando novos dados existem, o gatilho cria e executa uma nova instância de fluxo de trabalho com os dados como entrada.

* *Gatilhos por push*: Escuta dados em um ponto de extremidade de serviço e aguarda até que ocorra um evento específico. Quando o evento acontece, o gatilho é acionado imediatamente, criando e executando uma nova instância de fluxo de trabalho que usa quaisquer dados disponíveis como entrada.

Aqui estão apenas alguns exemplos populares de gatilho:

* Poll 

  * [ **Agenda-** o gatilho](../connectors/connectors-native-recurrence.md) de recorrência permite que você defina a data e a hora de início mais a recorrência para acionar seu aplicativo lógico. 
  Por exemplo, você pode selecionar os dias da semana e as horas do dia para disparar seu aplicativo lógico.

  * O gatilho "quando um email é recebido" permite que seu aplicativo lógico Verifique se há novos emails de qualquer provedor de email com suporte dos aplicativos lógicos, por exemplo, [Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md), [gmail](https://docs.microsoft.com/connectors/gmail/), [Outlook.com](https://docs.microsoft.com/connectors/outlook/)e assim por diante.

  * O [gatilho **http** ](../connectors/connectors-native-http.md) permite que seu aplicativo lógico verifique um ponto de extremidade de serviço especificado comunicando-se por http.
  
* Pressionado

  * O [gatilho solicitação **/resposta-solicitação** ](../connectors/connectors-native-reqres.md) permite que seu aplicativo lógico receba solicitações HTTP e responda em tempo real a eventos de alguma forma.

  * O [gatilho de webhook **http** ](../connectors/connectors-native-webhook.md) assina um ponto de extremidade de serviço registrando uma *URL de retorno de chamada* com esse serviço. 
  Dessa forma, o serviço pode apenas notificar o gatilho quando o evento especificado ocorrer, para que o gatilho não precise sondar o serviço.

Depois de receber uma notificação sobre novos dados ou um evento, o gatilho é disparado, cria uma nova instância de fluxo de trabalho do aplicativo lógico e executa as ações no fluxo de trabalho. Você pode acessar quaisquer dados do gatilho em todo o fluxo de trabalho. Por exemplo, o gatilho "em um novo tweet" passa o conteúdo do tweet para a execução do aplicativo lógico. 

## <a name="respond-to-triggers-and-extend-actions"></a>Responder a gatilhos e estender ações

Para sistemas e serviços que podem não ter conectores publicados, você também pode estender os aplicativos lógicos.

* [Criar gatilhos ou ações personalizadas](../logic-apps/logic-apps-create-api-app.md)
* [Configurar ações de execução longa para execuções de fluxo de trabalho](../logic-apps/logic-apps-create-api-app.md)
* [Responder a eventos externos e ações com WebHooks](../logic-apps/logic-apps-create-api-app.md)
* [Chamar, disparar ou aninhar fluxos de trabalho com respostas síncronas a solicitações HTTP](../logic-apps/logic-apps-http-endpoint.md)
* [Tutorial: Crie um painel social com alimentação de ia em minutos com aplicativos lógicos e Power BI](https://aka.ms/logicappsdemo)
* [Vídeo: Responder a WebHooks do SMS do twilio e enviar uma resposta de texto](https://channel9.msdn.com/Blogs/Windows-Azure/Azure-Logic-Apps-Walkthrough-Webhook-Functions-and-an-SMS-Bot)

## <a name="control-flow-error-handling-and-logging-capabilities"></a>Fluxo de controle, tratamento de erros e recursos de log

Os aplicativos lógicos incluem recursos avançados para o fluxo de controle avançado, como condições, comutadores, loops e escopos. Para garantir soluções resilientes, você também pode implementar o tratamento de erros e exceções em seus fluxos de trabalho. Para logs de notificação e diagnóstico para o status de execução do fluxo de trabalho, os aplicativos lógicos do Azure também fornecem monitoramento e alertas.

* Executar ações diferentes com base em [instruções condicionais](../logic-apps/logic-apps-control-flow-conditional-statement.md) e [instruções switch](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Repetir etapas ou processar itens em matrizes e coleções com loops](../logic-apps/logic-apps-control-flow-loops.md)
* [Agrupar ações em conjunto com escopos](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* [Tratamento de erros e exceções de autor em um fluxo de trabalho](../logic-apps/logic-apps-exception-handling.md)
* [Caso de utilização: Como uma empresa de saúde usa a manipulação de exceções de aplicativos lógicos para fluxos de trabalho do HL7 FHIR](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Ativar monitoramento, registro em log e alertas para aplicativos lógicos existentes](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [Ativar o monitoramento e o log de diagnóstico ao criar aplicativos lógicos](../logic-apps/logic-apps-monitor-your-logic-apps-oms.md)

## <a name="deploy-and-manage-logic-apps"></a>Implantar e gerenciar aplicativos lógicos

Você pode desenvolver e implantar aplicativos lógicos com o Visual Studio, o Azure DevOps ou qualquer outro controle de código-fonte e ferramentas de Build automatizadas. Para dar suporte à implantação de fluxos de trabalho e conexões dependentes em um modelo de recurso, os aplicativos lógicos usam modelos de implantação de recursos do Azure. As ferramentas do Visual Studio geram automaticamente esses modelos, que você pode fazer check-in no controle do código-fonte para controlar versões.

* [Criar e implantar aplicativos lógicos com o Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Ativar monitoramento, registro em log e alertas para aplicativos lógicos existentes](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [Automatizar a implantação do aplicativo lógico](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)

## <a name="content-types-conversions-and-transformations-within-a-run"></a>Tipos de conteúdo, conversões e transformações em uma execução

Você pode acessar, converter e transformar vários tipos de conteúdo usando as muitas funções na [linguagem de definição de fluxo de trabalho](https://aka.ms/logicappsdocs)dos aplicativos lógicos do Azure. Por exemplo, você pode converter entre uma cadeia de caracteres, JSON e XML com `@json()` as `@xml()` expressões de fluxo de trabalho e. O mecanismo de aplicativos lógicos preserva os tipos de conteúdo para dar suporte à transferência de conteúdo de maneira sem perdas entre serviços.

* [Como as expressões de fluxo de trabalho funcionam nos aplicativos lógicos](../logic-apps/logic-apps-author-definitions.md)
* [Manipule tipos de conteúdo não JSON](../logic-apps/logic-apps-content-type.md), como `application/xml`, `application/octet-stream`e`multipart/formdata`
* [Esquema de linguagem de definição de fluxo de trabalho para aplicativos lógicos do Azure](https://aka.ms/logicappsdocs)

## <a name="other-integrations-and-capabilities"></a>Outras integrações e recursos

Os aplicativos lógicos também oferecem integração com vários serviços, como Azure Functions, gerenciamento de API do Azure, serviços de Azure App e pontos de extremidade HTTP personalizados, por exemplo, REST e SOAP.

* [Crie um painel social em tempo real com o Azure sem servidor](../logic-apps/logic-apps-scenario-social-serverless.md)
* [Chamar Azure Functions de aplicativos lógicos](../logic-apps/logic-apps-azure-functions.md)
* [Tutorial: Disparar aplicativos lógicos com Azure Functions](../logic-apps/logic-apps-scenario-function-sb-trigger.md)
* [Tutorial: Monitorar alterações de máquina virtual com a grade de eventos do Azure e aplicativos lógicos](../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md)
* [Tutorial: Criar uma função que se integre com os aplicativos lógicos do Azure e os serviços cognitivas do Azure para analisar as opiniões de postagem no Twitter](../azure-functions/functions-twitter-email.md)
* [Tutorial: Monitoramento remoto de IoT e notificações com aplicativos lógicos do Azure conectando seu hub IoT e caixa de correio](../iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps.md)
* [Blog: Chamar pontos de extremidade SOAP de aplicativos lógicos](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)

## <a name="end-to-end-scenarios"></a>Cenários ponto a ponto

* [Artigos Integração de gerenciamento de casos de ponta a ponta com serviços do Azure, como aplicativos lógicos](https://aka.ms/enterprise-integration-e2e-case-management-utilities-logic-apps)

## <a name="customer-stories"></a>Histórias dos clientes

Saiba como os aplicativos lógicos do Azure, juntamente com outros serviços do Azure e produtos da Microsoft, ajudaram [essas empresas](https://aka.ms/logic-apps-customer-stories) a melhorarem sua agilidade e se concentrarem em seus principais negócios ao simplificar, organizar, automatizar e orquestrar processos complexos.

## <a name="next-steps"></a>Passos Seguintes

* [Criar definições de aplicativo lógico com JSON](../logic-apps/logic-apps-author-definitions.md)
* [Tratar erros e exceções em aplicativos lógicos](../logic-apps/logic-apps-exception-handling.md)
* [Envie seus comentários, perguntas, comentários ou sugestões para melhorar os aplicativos lógicos do Azure](https://feedback.azure.com/forums/287593-logic-apps)
