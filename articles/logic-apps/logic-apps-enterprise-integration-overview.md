---
title: Integração empresarial B2B
description: Saiba mais sobre a construção de fluxos de trabalho B2B automatizados para integração empresarial utilizando apps Azure Logic e Pacote de Integração Empresarial
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, logicappspm
ms.topic: overview
ms.date: 03/24/2021
ms.openlocfilehash: 09d253aae4c054db5efdc252f62986044e1d366b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771862"
---
# <a name="b2b-enterprise-integration-solutions-with-azure-logic-apps-and-enterprise-integration-pack"></a>Soluções de integração empresarial B2B com apps Azure Logic e Pacote de Integração Empresarial

Para soluções business-to-business (B2B) e comunicação perfeita entre organizações, pode construir fluxos de trabalho de integração de empresas escaláveis automatizados utilizando o Pacote de Integração Empresarial (EIP) com [apps Azure Logic.](../logic-apps/logic-apps-overview.md) Embora as organizações utilizem diferentes protocolos e formatos, podem trocar mensagens eletronicamente. O EIP transforma diferentes formatos num formato que os sistemas das suas organizações podem processar e suportar protocolos padrão da indústria, incluindo [AS2,](../logic-apps/logic-apps-enterprise-integration-as2.md) [X12](logic-apps-enterprise-integration-x12.md)e [EDIFACT.](../logic-apps/logic-apps-enterprise-integration-edifact.md) Também pode melhorar a segurança das mensagens utilizando assinaturas de encriptação e digitais. O EIP apoia estes [conectores de integração empresarial](../connectors/managed.md#enterprise-connectors) e estes padrões do setor:

* Intercâmbio eletrónico de dados (EDI)
* Integração de Aplicações Empresariais (EAI)

Se estiver familiarizado com o Microsoft BizTalk Server ou com os Serviços Azure BizTalk, o EIP segue conceitos semelhantes, tornando as funcionalidades fáceis de usar. No entanto, uma grande diferença é que o EIP se baseia em "contas de integração" para simplificar o armazenamento e a gestão dos artefactos utilizados nas comunicações B2B. Estas contas são contentores baseados em nuvem que armazenam todos os seus artefactos, tais como parceiros, acordos, esquemas, mapas e certificados. 

## <a name="why-use-the-enterprise-integration-pack"></a>Porquê usar o Pacote de Integração Empresarial?

* Com o EIP, pode armazenar todos os seus artefactos num só local - a sua conta de integração.

* Você pode construir fluxos de trabalho B2B e integrar-se com aplicações de software de terceiros como serviço (SaaS), aplicações no local e aplicativos personalizados usando Apps e conectores Azure Logic.

* Pode criar código personalizado para as suas aplicações lógicas com funções Azure.

## <a name="how-do-i-get-started"></a>Como posso começar?

Antes de começar a construir fluxos de trabalho de aplicações lógicas B2B com o EIP, precisa destes itens:

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* Uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) com os artefactos que quer usar

* Para criar mapas e esquemas, pode utilizar as [Ferramentas de Integração Empresarial do Microsoft Azure Logic Apps para o Visual Studio 2015 2.0](https://aka.ms/vsmapsandschemas) e Visual Studio 2015.

Depois de criar uma conta de integração e adicionar os seus artefactos, pode começar a construir fluxos de trabalho B2B com estes artefactos, criando uma aplicação lógica no portal Azure. Se é novo em aplicações lógicas, tente [criar uma aplicação lógica básica.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Para trabalhar com estes artefactos, certifique-se de que primeiro liga a sua conta de integração à sua aplicação lógica. Depois disso, a sua aplicação lógica pode aceder à sua conta de integração. Também pode criar, gerir e implementar aplicações lógicas utilizando o Visual Studio ou [o PowerShell.](/powershell/module/az.logicapp)

Aqui estão os passos de alto nível para começar a construir aplicações lógicas B2B:

![Pré-requisitos para a criação de aplicações lógicas B2B](./media/logic-apps-enterprise-integration-overview/overview.png)  

## <a name="try-now"></a>Experimente agora

[Implementar uma aplicação lógica de amostra totalmente operacional que envia e recebe mensagens AS2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-as2-send-receive)

## <a name="next-steps"></a>Passos seguintes

* [Criar parceiros de negociação](logic-apps-enterprise-integration-partners.md)
* [Criar acordos](../logic-apps/logic-apps-enterprise-integration-agreements.md)
* [Adicionar esquemas](logic-apps-enterprise-integration-schemas.md)
* [Adicionar mapas](../logic-apps/logic-apps-enterprise-integration-maps.md)
* [Migrar dos Serviços BizTalk](../logic-apps/logic-apps-move-from-mabs.md)
