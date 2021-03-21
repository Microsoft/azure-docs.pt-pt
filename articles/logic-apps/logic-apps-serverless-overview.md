---
title: Visão geral - Azure Serverless para aplicações e soluções baseadas na nuvem
description: Saiba como criar apps e soluções baseadas em nuvem sem se preocupar com infraestruturas utilizando apps e funções Azure Logic
services: logic-apps
ms.suite: integration
author: jeffhollan
ms.author: jehollan
ms.reviewer: klam, estfan, logicappspm
ms.topic: article
ms.date: 03/30/2017
ms.openlocfilehash: 470857883144f4bf8c21a1921afacb7ba3f5ca00
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98788100"
---
# <a name="azure-serverless-overview-for-building-cloud-based-apps-and-solutions-with-azure-logic-apps-and-azure-functions"></a>Azure Serverless: Visão geral para a construção de apps e soluções baseadas na nuvem com apps e funções Azure Logic

[As](https://azure.microsoft.com/solutions/serverless/) aplicações sem servidor oferecem benefícios como o aumento da velocidade de desenvolvimento, código reduzido, simplicidade e escala. Este artigo cobre os diferentes atributos de soluções sem servidor e ofertas sem servidor Azure.

## <a name="what-is-serverless"></a>O que é sem servidor?

Sem servidor não significa que não existam servidores, mas sim que os desenvolvedores não tenham de se preocupar com servidores. Uma grande parte do desenvolvimento tradicional de aplicações está a responder a questões em torno de soluções de escala, hospedagem e monitorização para atender às exigências da aplicação. Sem servidor, estas questões são cuidadas como parte da solução. Além disso, as aplicações sem servidor são faturadas num plano baseado no consumo. Se a aplicação nunca for utilizada, não é cobrado qualquer custo. Estas funcionalidades ajudam os desenvolvedores a concentrarem-se apenas na lógica de negócio de uma solução.

Os principais serviços Azure para sem servidor são [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) e [Azure Functions](https://azure.microsoft.com/services/functions/). Ambas as soluções seguem os princípios anteriormente descritos e ajudam os desenvolvedores a construir aplicações em nuvem robustas com código mínimo.

## <a name="what-is-azure-logic-apps"></a>O que é o Azure Logic Apps?

[A Azure Logic Apps](logic-apps-overview.md) fornece uma forma de simplificar e implementar integrações escaláveis e fluxos de trabalho na nuvem. Este serviço fornece um designer visual para modelar e automatizar o seu processo como uma série de passos chamado fluxo de trabalho. Existem [muitos conectores](../connectors/apis-list.md) em serviços na nuvem e sistemas no local que conectam rapidamente uma aplicação sem servidor a outras APIs. Cada aplicação lógica começa com um gatilho, como "Quando uma conta é adicionada ao Dynamics CRM". Após o disparo, o fluxo de trabalho pode executar combinações de ações, conversões e lógica condicional. As Aplicações Lógicas são uma ótima escolha ao orquestrar diferentes Funções Azure num processo, especialmente quando o processo requer interagir com um sistema externo ou API.

Para começar com as Aplicações Lógicas, comece por [criar a sua primeira aplicação lógica.](quickstart-create-first-logic-app-workflow.md) Para obter mais informações técnicas sobre as Aplicações Lógicas, consulte a referência do [programador](logic-apps-workflow-definition-language.md).

## <a name="what-is-azure-functions"></a>O que são as Funções do Azure?

Azure Functions é um serviço para executar facilmente peças de código ou "funções" na nuvem. Pode escrever apenas o código necessário para o problema atual, sem se preocupar com uma aplicação inteira ou com a infraestrutura necessária. As funções podem tornar o desenvolvimento ainda mais produtivo, e você pode usar a sua linguagem de desenvolvimento de eleição, tais como C#, F#, Node.js, Python ou PHP. Paga-se apenas pelo tempo que o seu código for executado e as escalas Azure, se necessário.

Para começar com as Funções Azure, comece por [Criar a sua primeira Função Azure](../azure-functions/functions-get-started.md). Para obter mais informações técnicas sobre funções, consulte a referência do [programador](../azure-functions/functions-reference.md).

## <a name="how-can-i-build-and-deploy-serverless-apps-in-azure"></a>Como posso construir e implementar aplicações sem servidor no Azure?

O Azure fornece ferramentas ricas para desenvolver, implementar e gerir aplicações sem servidor. Pode construir aplicações diretamente no portal Azure, com [ferramentas no Visual Studio,](logic-apps-serverless-get-started-vs.md)ou [Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md). Depois de construir a sua aplicação, pode [implementar essa aplicação rapidamente com modelos do Azure Resource Manager.](logic-apps-deploy-azure-resource-manager-templates.md) O Azure também fornece monitorização, a que pode aceder através do portal Azure, através da API ou SDKs, ou com ferramentas integradas para registos do Monitor Azure e Insights de Aplicação.

## <a name="next-steps"></a>Passos seguintes

* [Construa uma aplicação sem servidor no Visual Studio](logic-apps-serverless-get-started-vs.md)
* [Crie um dashboard de insights de cliente com servidor sem servidor](logic-apps-scenario-social-serverless.md)
* [Automatizar a implementação de uma aplicação lógica](logic-apps-azure-resource-manager-templates-overview.md)