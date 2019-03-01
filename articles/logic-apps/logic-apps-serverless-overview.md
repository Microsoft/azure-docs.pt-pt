---
title: Descrição geral do Azure sem servidor | Documentos da Microsoft
description: Saiba mais sobre a criação de poderosas soluções na cloud sem preocupar com infraestruturas
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: jeffhollan
ms.author: jehollan
ms.reviewer: klam, estfan, LADocs
ms.custom: vs-azure
ms.topic: article
ms.date: 03/30/2017
ms.openlocfilehash: 9cd1946d8fa670764bcc95b82298d07cc70417bd
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57191328"
---
# <a name="overview-azure-serverless-with-azure-logic-apps-and-azure-functions"></a>Descrição geral: Azure sem servidor com o Azure Logic Apps e as funções do Azure

[Sem servidor](https://azure.microsoft.com/solutions/serverless/) aplicativos oferecem benefícios de um aumento na velocidade do desenvolvimento, redução no código necessário e a simplicidade com dimensionamento.  Neste artigo vai para os atributos diferentes das soluções sem servidor e as ofertas sem servidor do Azure.

## <a name="what-is-serverless"></a>O que é sem servidor?

Sem servidor não significa que não existirem servidores – isso apenas significa que o desenvolvedor não precisa se preocupar sobre servidores.  A maior parte do desenvolvimento de aplicativos tradicionais é responder às perguntas sobre o dimensionamento, alojamento e monitorização de soluções para satisfazer as necessidades do aplicativo.  Com sem servidor, estas perguntas cuidam da como parte da solução.  Além disso, as aplicações sem servidor são faturadas num plano com base no consumo.  Se o aplicativo nunca for utilizado, um custo nunca é incorrido.  Esses recursos permitem aos desenvolvedores se concentrarem exclusivamente na lógica de negócios da solução.

São os principais serviços no Azure em torno de sem servidor [as funções do Azure](https://azure.microsoft.com/services/functions/) e [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/).  Essas duas soluções a seguem os princípios acima e permitem aos programadores criar aplicações na cloud robusta com pouco código.

## <a name="what-are-azure-functions"></a>Quais são as funções do Azure?

Funções do Azure é uma solução para uma fácil execução de pequenos blocos de código, ou "funções", na nuvem. É possível escrever apenas o código que necessita para o problema em questão, sem ter de se preocupar com toda a aplicação ou a infraestrutura para executá-la. As funções podem tornar o desenvolvimento ainda mais produtivo e pode utilizar o desenvolvimento linguagem de sua escolha, como C#, F#, node. js, Python ou PHP. Pague apenas o tempo de que seu código é executado e o Azure pode ser dimensionada conforme necessário.

Se quiser iniciar de imediato e utilizar as Funções do Azure, comece por [Criar a sua primeira Função do Azure](../azure-functions/functions-create-first-azure-function.md). Se estiver à procura de mais informações técnicas acerca das Funções, consulte a [referência para programadores](../azure-functions/functions-reference.md).

## <a name="what-are-azure-logic-apps"></a>O que são do Azure Logic Apps?

O Azure Logic Apps fornece uma forma de simplificar e implementar integrações escaláveis e fluxos de trabalho na cloud. Ele fornece um designer visual para modelar e automatizar o processo como uma série de passos chamado um fluxo de trabalho.  Existem [muitos conetores](../connectors/apis-list.md) em todos os serviços cloud e no local para ligar-se rapidamente uma aplicação sem servidor para outras APIs.  Uma aplicação lógica começa com um acionador (por exemplo, «Quando uma conta é adicionada ao Dynamics CRM») e depois de acionadas podem iniciar muitas ações de combinações, conversões e lógica de condição.  O Logic Apps é uma ótima opção quando orquestrar diferentes funções do Azure num processo - especialmente quando o processo exige a interagir com um sistema externo ou uma API.

Para começar a utilizar com o Logic Apps, comece com [criando sua primeira aplicação lógica](quickstart-create-first-logic-app-workflow.md).  Se estiver à procura de mais informações técnicas sobre o Logic Apps, consulte a [referência para programadores](logic-apps-workflow-actions-triggers.md).

## <a name="how-can-i-build-and-deploy-serverless-applications-in-azure"></a>Como criar e implementar aplicações sem servidor no Azure?

O Azure fornece um conjunto avançado de ferramentas em desenvolvimento, implementação e gestão de aplicações sem servidor.  As aplicações podem ser criadas diretamente no portal do Azure ou com [ferramentas do Visual Studio](logic-apps-serverless-get-started-vs.md).  Uma vez que foi desenvolvido um aplicativo pode ser [implementado instantaneamente](logic-apps-create-deploy-template.md).  O Azure também fornece monitorização para aplicações sem servidor.  Esta monitorização pode ser acessado do portal do Azure, através da API ou SDKs, ou com ferramentas integradas para registos do Azure Monitor e o Application Insights.

## <a name="next-steps"></a>Passos Seguintes

* [Comece a criar uma aplicação sem servidor no Visual Studio](logic-apps-serverless-get-started-vs.md)
* [Criar um dashboard de informações do cliente com sem servidor](logic-apps-scenario-social-serverless.md)
* [Criar um modelo de implementação para uma aplicação lógica](logic-apps-create-deploy-template.md)
