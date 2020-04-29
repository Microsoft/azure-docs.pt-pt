---
title: Integração empresarial B2B
description: Saiba mais sobre a construção de fluxos de trabalho b2B automatizados para integração empresarial utilizando aplicações da Azure Logic e pacote de integração empresarial
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, logicappspm
ms.topic: overview
ms.date: 08/01/2019
ms.openlocfilehash: 9356cecb22672ae15505d14b5ac73d5baeef09e0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "77191378"
---
# <a name="b2b-enterprise-integration-solutions-with-azure-logic-apps-and-enterprise-integration-pack"></a>Soluções de integração empresarial B2B com Aplicações Lógicas Azure e Pacote de Integração Empresarial

Para soluções business-to-business (B2B) e comunicação perfeita entre organizações, você pode construir fluxos de trabalho automatizados de integração empresarial escalável usando o Enterprise Integration Pack (EIP) com [Aplicações Lógicas Azure](../logic-apps/logic-apps-overview.md). Embora as organizações utilizem diferentes protocolos e formatos, podem trocar mensagens eletronicamente. O EIP transforma diferentes formatos num formato que os sistemas das suas organizações podem processar e apoiar protocolos padrão da indústria, incluindo [AS2,](../logic-apps/logic-apps-enterprise-integration-as2.md) [X12](logic-apps-enterprise-integration-x12.md)e [EDIFACT.](../logic-apps/logic-apps-enterprise-integration-edifact.md) Também pode melhorar a segurança das mensagens utilizando encriptação e assinaturas digitais. O EIP apoia estes [conectores](../connectors/apis-list.md#integration-account-connectors) de integração empresarial e estas normas do setor:

* Intercâmbio eletrónico de dados (EDI)
* Integração de Aplicações Empresariais (EAI)

Se estiver familiarizado com o Microsoft BizTalk Server ou com os Serviços Azure BizTalk, o EIP segue conceitos semelhantes, tornando as funcionalidades fáceis de usar. No entanto, uma grande diferença é que o PiIp se baseia em "contas de integração" para simplificar o armazenamento e a gestão dos artefactos utilizados nas comunicações B2B. Estas contas são recipientes baseados em nuvem que armazenam todos os seus artefactos, tais como parceiros, acordos, schemas, mapas e certificados. 

## <a name="why-use-the-enterprise-integration-pack"></a>Porquê usar o Pacote de Integração Empresarial?

* Com o EIP, pode armazenar todos os seus artefactos num só local - a sua conta de integração.

* Você pode construir fluxos de trabalho B2B e integrar-se com aplicações de software de terceiros (SaaS), aplicações no local e aplicações personalizadas usando Apps e conectores Azure Logic.

* Pode criar código personalizado para as suas aplicações lógicas com funções Azure.

## <a name="how-do-i-get-started"></a>Como posso começar?

Antes de começar a construir fluxos de aplicações lógicaB2B com o EIP, precisa destes itens:

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* Uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) com os artefactos que pretende usar

* Para criar mapas e schemas, pode utilizar as Ferramentas de Integração Empresarial da [Microsoft Azure Logic Apps para o Visual Studio 2015 2.0](https://aka.ms/vsmapsandschemas) e visual Studio 2015.

Depois de criar uma conta de integração e adicionar os seus artefactos, pode começar a construir fluxos de trabalho B2B com estes artefactos criando uma aplicação lógica no portal Azure. Se você é novo em aplicativos lógicos, tente [criar uma aplicação lógica básica](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para trabalhar com estes artefactos, certifique-se de que liga a sua conta de integração à sua aplicação lógica. Depois disso, a sua aplicação lógica pode aceder à sua conta de integração. Também pode criar, gerir e implementar aplicações lógicas utilizando o Visual Studio ou [o PowerShell.](https://docs.microsoft.com/powershell/module/az.logicapp)

Aqui estão os passos de alto nível para começar a construir aplicações lógicas B2B:

![Pré-requisitos para a criação de aplicações lógicas B2B](./media/logic-apps-enterprise-integration-overview/overview.png)  

## <a name="try-now"></a>Experimente agora

[Implemente uma aplicação lógica de amostra totalmente operacional que envia e recebe mensagens AS2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-as2-send-receive)

## <a name="next-steps"></a>Passos seguintes

* [Criar parceiros comerciais](logic-apps-enterprise-integration-partners.md)
* [Criar acordos](../logic-apps/logic-apps-enterprise-integration-agreements.md)
* [Adicionar esquemas](logic-apps-enterprise-integration-schemas.md)
* [Adicionar mapas](../logic-apps/logic-apps-enterprise-integration-maps.md)
* [Migrar dos Serviços BizTalk](../logic-apps/logic-apps-move-from-mabs.md)
