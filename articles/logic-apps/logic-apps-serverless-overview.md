---
title: Visão geral - Azure Serverless para aplicações e soluções baseadas na nuvem
description: Saiba como criar aplicações e soluções baseadas na nuvem sem se preocupar com infraestruturas utilizando aplicações da Lógica Azure e funções azure
services: logic-apps
ms.suite: integration
author: jeffhollan
ms.author: jehollan
ms.reviewer: klam, estfan, logicappspm
ms.topic: article
ms.date: 03/30/2017
ms.openlocfilehash: 0f20bb5fb249ad6bac862afe2b0e8eee4b32e2a9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75666555"
---
# <a name="azure-serverless-overview-for-building-cloud-based-apps-and-solutions-with-azure-logic-apps-and-azure-functions"></a>Azure Serverless: Visão geral para a construção de aplicações e soluções baseadas na nuvem com aplicações lógicas azure e funções azure

As aplicações [sem servidor](https://azure.microsoft.com/solutions/serverless/) oferecem benefícios como aumento da velocidade de desenvolvimento, código reduzido, simplicidade e escala. Este artigo cobre os diferentes atributos de soluções sem servidor e ofertas sem servidor estonteantes do Azure.

## <a name="what-is-serverless"></a>O que é servidor?

Servidor não significa que não existam servidores, mas os desenvolvedores não têm que se preocupar com servidores. Grande parte do desenvolvimento tradicional de aplicações está a responder a questões em torno da escala, hospedagem e monitorização de soluções para atender às exigências da aplicação. Com o servidor, estas questões são cuidadas como parte da solução. Além disso, as aplicações sem servidor são faturadas num plano baseado no consumo. Se a aplicação nunca for utilizada, não é incorrido qualquer custo. Estas funcionalidades ajudam os desenvolvedores a focar-se apenas na lógica de negócio de uma solução.

Os principais serviços Azure para servidores são [as Aplicações Lógicas Azure](https://azure.microsoft.com/services/logic-apps/) e [as Funções Azure.](https://azure.microsoft.com/services/functions/) Ambas as soluções seguem os princípios anteriormente descritos e ajudam os desenvolvedores a construir aplicações robustas em nuvem com código mínimo.

## <a name="what-is-azure-logic-apps"></a>O que é o Azure Logic Apps?

[As Aplicações Lógicas Azure](logic-apps-overview.md) fornecem uma forma de simplificar e implementar integrações e fluxos de trabalho escaláveis na nuvem. Este serviço fornece um designer visual para modelar e automatizar o seu processo como uma série de passos chamados fluxo de trabalho. Existem muitos [conectores](../connectors/apis-list.md) em serviços na nuvem e sistemas no local que rapidamente conectam uma aplicação sem servidor estoirar a outras APIs. Todas as aplicações lógicas começam com um gatilho, como "Quando uma conta é adicionada ao CRM da Dynamics". Após os incêndios de gatilho, o fluxo de trabalho pode executar combinações de ações, conversões e lógica condicional. Logic Apps é uma ótima escolha ao orquestrar diferentes Funções Azure num processo, especialmente quando o processo requer interagir com um sistema externo ou API.

Para começar com apps lógicas, comece por [criar a sua primeira aplicação lógica.](quickstart-create-first-logic-app-workflow.md) Para obter informações mais técnicas sobre Apps Lógicas, consulte a referência do [desenvolvedor.](logic-apps-workflow-definition-language.md)

## <a name="what-is-azure-functions"></a>O que são as Funções do Azure?

As Funções Azure são um serviço para executar facilmente peças de código ou "funções" na nuvem. Só pode escrever o código necessário para o problema atual, sem se preocupar com toda uma aplicação ou com a infraestrutura necessária. As funções podem tornar o desenvolvimento ainda mais produtivo, e você pode usar a sua linguagem de desenvolvimento de eleição, tais como C#, F#, Node.js, Python ou PHP. Paga-se apenas pelo tempo que o seu código funciona e as escalas azure, se necessário.

Para começar com as Funções Azure, comece com [criar a sua primeira Função Azure.](../azure-functions/functions-create-first-azure-function.md) Para obter mais informações técnicas sobre funções, consulte a referência do [desenvolvedor](../azure-functions/functions-reference.md).

## <a name="how-can-i-build-and-deploy-serverless-apps-in-azure"></a>Como posso construir e implementar aplicações sem servidores no Azure?

O Azure fornece ferramentas ricas para desenvolver, implementar e gerir aplicações sem servidores. Pode construir aplicações diretamente no portal Azure, com [ferramentas em Visual Studio](logic-apps-serverless-get-started-vs.md), ou [Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md). Depois de construir a sua aplicação, pode [implementar essa aplicação rapidamente com modelos do Gestor de Recursos Azure.](logic-apps-deploy-azure-resource-manager-templates.md) O Azure também fornece monitorização, a que pode aceder através do portal Azure, através da API ou SDKs, ou com ferramentas integradas para registos e Insights de Aplicação do Monitor Azure.

## <a name="next-steps"></a>Passos seguintes

* [Construa uma aplicação sem servidores no Estúdio Visual](logic-apps-serverless-get-started-vs.md)
* [Criar um dashboard de insights do cliente com servidor](logic-apps-scenario-social-serverless.md)
* [Automatizar a implementação de uma aplicação lógica](logic-apps-azure-resource-manager-templates-overview.md)
