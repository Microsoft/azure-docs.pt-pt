---
title: Exemplos e cenários comuns - Azure Logic Apps | Documentos da Microsoft
description: Exemplos, cenários, tutoriais e orientações passo a passo para o Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.assetid: e06311bc-29eb-49df-9273-1f05bbb2395c
ms.date: 01/31/2018
ms.openlocfilehash: 89e0294db3178cedd3b14aada0b505787b17c75e
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56671799"
---
# <a name="common-scenarios-examples-tutorials-and-walkthroughs-for-azure-logic-apps"></a>Comuns cenários, exemplos, tutoriais e orientações passo a passo para o Azure Logic Apps

[O Azure Logic Apps](../logic-apps/logic-apps-overview.md) ajuda-o a organizar e integrar serviços diferentes, fornecendo [conectores de prontos para utilizar mais de 100](../connectors/apis-list.md), que varia entre SQL Server no local ou SAP para serviços cognitivos da Microsoft. O serviço de aplicações lógicas é "sem servidor", para que não tenha de se preocupar sobre dimensionamento ou instâncias. Tudo que precisa fazer é definir o fluxo de trabalho com um acionador e as ações que executa o fluxo de trabalho. A plataforma subjacente processa o dimensionamento, disponibilidade e desempenho. O Logic Apps é especialmente útil para cenários em que precisar de coordenar várias ações em vários sistemas e casos de utilização.

Para o ajudar a saber mais sobre os vários padrões e capacidades que [do Azure Logic Apps](../logic-apps/logic-apps-overview.md) suporta, seguem-se exemplos e cenários comuns.

## <a name="popular-starting-points-for-logic-app-workflows"></a>Pontos de partida para fluxos de trabalho de aplicação de lógica populares

Todas as aplicações lógicas começa com uma [ *acionador*](../logic-apps/logic-apps-overview.md#logic-app-concepts)e apenas um acionador, que inicia o fluxo de trabalho de aplicação lógica e passa em todos os dados como parte do que acionador. Alguns conectores disponibilizam acionadores, que são fornecidos em destes tipos:

* *Acionadores de consultas*: Verifica regularmente o ponto final de serviço para novos dados. Quando existem novos dados, o acionador cria e executa uma nova instância de fluxo de trabalho com os dados como entrada.

* *Acionadores push*: Está à escuta de dados num ponto de extremidade de serviço e aguarda até que um evento específico acontece. Quando o evento ocorre, o acionador é acionado imediatamente, criação e execução de uma nova instância de fluxo de trabalho que utiliza todos os dados disponíveis como entrada.

Aqui estão alguns exemplos de Acionador popular:

* Consulta: 

  * [**Agendamento - periodicidade** acionador](../connectors/connectors-native-recurrence.md) permite-lhe definir a data de início e hora mais a periodicidade para trabalharem a sua aplicação lógica. 
  Por exemplo, pode selecionar os dias da semana e horas do dia para acionar a sua aplicação lógica.

  * O acionador "Quando é recebido um e-mail" permite que a aplicação lógica Verifique para novo e-mail de qualquer fornecedor de e-mail que é suportado pelo Logic Apps, por exemplo, [Outlook do Office 365](../connectors/connectors-create-api-office365-outlook.md), [Gmail](https://docs.microsoft.com/connectors/gmail/), [ Outlook.com](https://docs.microsoft.com/connectors/outlook/)e assim por diante.

  * O [ **HTTP** acionador](../connectors/connectors-native-http.md) permite que a aplicação lógica verifique um ponto de extremidade de serviço especificado através da comunicação através de HTTP.
  
* Push:

  * O [ **pedido / resposta - pedido** acionador](../connectors/connectors-native-reqres.md) permite à aplicação de lógica receber pedidos HTTP e responder em tempo real para eventos de alguma forma.

  * O [ **Webhook de HTTP** acionador](../connectors/connectors-native-webhook.md) assina um ponto de extremidade de serviço ao se registrar um *URL de retorno de chamada* com esse serviço. 
  Dessa forma, o serviço pode apenas notificar o acionador quando ocorre o evento especificado, para que o acionador não necessita de consultar o serviço.

Depois de receber uma notificação sobre novos dados ou um evento, o acionador é acionado, cria uma nova instância de fluxo de trabalho de aplicação de lógica e executa as ações no fluxo de trabalho. Pode acessar todos os dados de Acionador em todo o fluxo de trabalho. Por exemplo, o acionador "num novo tweet" transmite o conteúdo do tweet para a execução da aplicação lógica. 

## <a name="respond-to-triggers-and-extend-actions"></a>Responder a acionadores e ações de expandir

Para sistemas e serviços que não podem ter publicado conectores, também pode expandir as aplicações lógicas.

* [Criar acionadores personalizados ou de ações](../logic-apps/logic-apps-create-api-app.md)
* [Configurar as ações de execução longa para execuções de fluxo de trabalho](../logic-apps/logic-apps-create-api-app.md)
* [Responder a eventos externos e ações com webhooks](../logic-apps/logic-apps-create-api-app.md)
* [Chamar, acionar, ou aninhar fluxos de trabalho com síncronas respostas a pedidos de HTTP](../logic-apps/logic-apps-http-endpoint.md)
* [Tutorial: Criar um dashboard de rede social com tecnologia de IA em minutos com o Logic Apps e o Power BI](https://aka.ms/logicappsdemo)
* [Vídeo: Responder a webhooks de Twilio SMS e enviar uma resposta de texto](https://channel9.msdn.com/Blogs/Windows-Azure/Azure-Logic-Apps-Walkthrough-Webhook-Functions-and-an-SMS-Bot)

## <a name="control-flow-error-handling-and-logging-capabilities"></a>Fluxo de controle, tratamento de erros e capacidades de registo

O Logic apps inclui capacidades avançadas para o fluxo de controle avançado, como condições, comutadores, ciclos e âmbitos. Para garantir soluções resilientes, pode também implementar processar em seus fluxos de trabalho de exceções e erros. Para a notificação e registos de diagnóstico de estado de execução de fluxo de trabalho, Azure Logic Apps também fornece monitorização e alertas.

* Executar ações diferentes com base na [instruções condicionais](../logic-apps/logic-apps-control-flow-conditional-statement.md) e [declarações de comutador](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Repita os passos ou itens de processo em matrizes e coleções com loops](../logic-apps/logic-apps-control-flow-loops.md)
* [Ações de grupo, juntamente com âmbitos](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* [Erro de autor e de processamento num fluxo de trabalho de exceções](../logic-apps/logic-apps-exception-handling.md)
* [Utilize as maiúsculas e minúsculas: Como uma empresa de cuidados de saúde utiliza para fluxos de trabalho da HL7 FHIR de manipulação de exceção de aplicação de lógica](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Ativar monitorização, registo e alertas para o logic apps existentes](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [Ativar o registo de diagnóstico e monitorização ao criar aplicações lógicas](../logic-apps/logic-apps-monitor-your-logic-apps-oms.md)

## <a name="deploy-and-manage-logic-apps"></a>Implementar e gerir aplicações lógicas

Totalmente pode desenvolver e implementar aplicações lógicas com o Visual Studio, os DevOps do Azure, ou qualquer outro controlo de código fonte e ferramentas automatizadas de compilação. Para suportar a implementação para fluxos de trabalho e ligações dependentes num modelo de recursos, aplicações lógicas utilizam modelos de implementação de recursos do Azure. O Visual Studio tools gerar automaticamente estes modelos, que pode verificar controle de origem para controlo de versões.

* [Criar e implementar aplicações lógicas com o Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Ativar monitorização, registo e alertas para o logic apps existentes](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [Criar um modelo de implementação automatizada](../logic-apps/logic-apps-create-deploy-template.md)

## <a name="content-types-conversions-and-transformations-within-a-run"></a>Tipos de conteúdo, conversões e transformações dentro de uma execução

Pode acessar, converter e transformar vários tipos de conteúdo com muitas funções no Azure Logic Apps [linguagem de definição de fluxo de trabalho](https://aka.ms/logicappsdocs). Por exemplo, é possível converter entre uma cadeia de caracteres JSON e XML com o `@json()` e `@xml()` expressões de fluxo de trabalho. O motor do Logic Apps preserva os tipos de conteúdo para oferecer suporte a transferência de conteúdo de forma sem perdas entre serviços.

* [Como expressões de fluxo de trabalho funcionam no logic apps](../logic-apps/logic-apps-author-definitions.md)
* [Lidar com tipos de conteúdo não JSON](../logic-apps/logic-apps-content-type.md), como `application/xml`, `application/octet-stream`, e `multipart/formdata`
* [Esquema de linguagem de definição de fluxo de trabalho para o Azure Logic Apps](https://aka.ms/logicappsdocs)

## <a name="other-integrations-and-capabilities"></a>Outras integrações e capacidades

As aplicações lógicas oferecem também a integração com vários serviços, como as funções do Azure, gestão de API do Azure, serviços de aplicações do Azure e pontos finais HTTP personalizados, por exemplo, REST e SOAP.

* [Criar um dashboard em tempo real de redes sociais com sem servidor do Azure](../logic-apps/logic-apps-scenario-social-serverless.md)
* [Chamar as funções do Azure a partir de aplicações lógicas](../logic-apps/logic-apps-azure-functions.md)
* [Tutorial: Acionador logic apps com as funções do Azure](../logic-apps/logic-apps-scenario-function-sb-trigger.md)
* [Tutorial: Monitorizar alterações de máquina virtual com o Azure Event Grid e Logic Apps](../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md)
* [Tutorial: Criar uma função que se integra no Azure Logic Apps e os serviços cognitivos da Microsoft para analisar sentimentos de publicação do Twitter](../azure-functions/functions-twitter-email.md)
* [Tutorial: A monitorização remota do IoT e notificações com o Azure Logic Apps, ligar o seu IoT hub e caixa de correio](../iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps.md)
* [Blog: Chamar pontos finais SOAP a partir de aplicações lógicas](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)

## <a name="end-to-end-scenarios"></a>Cenários ponto a ponto

* [Documento técnico: Integração da gestão de casos de ponto a ponto com serviços do Azure, como o Logic Apps](https://aka.ms/enterprise-integration-e2e-case-management-utilities-logic-apps)

## <a name="customer-stories"></a>Histórias dos clientes

Saiba como Azure Logic Apps, juntamente com outros serviços do Azure e produtos da Microsoft, ajudou [estas empresas](https://aka.ms/logic-apps-customer-stories) melhorar a agilidade e concentrar-se nos negócios principais ao simplificando, organizar, automatizar e orquestrar processos complexos.

## <a name="next-steps"></a>Passos Seguintes

* [Criar em definições de aplicação lógica com JSON](../logic-apps/logic-apps-author-definitions.md)
* [Lidar com erros e exceções no logic apps](../logic-apps/logic-apps-exception-handling.md)
* [Envie seus comentários, dúvidas, comentários ou sugestões para aprimorar o Azure Logic Apps](https://feedback.azure.com/forums/287593-logic-apps)
