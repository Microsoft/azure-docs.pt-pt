---
title: Integração empresarial B2B
description: Saiba mais sobre como criar fluxos de trabalho B2B automatizados para a integração corporativa usando os aplicativos lógicos do Azure e Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, logicappspm
ms.topic: overview
ms.date: 08/01/2019
ms.openlocfilehash: 141f1a7fc8f966132cb3570fd9d9cfa911127eed
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792449"
---
# <a name="b2b-enterprise-integration-solutions-with-azure-logic-apps-and-enterprise-integration-pack"></a>Soluções de integração corporativa B2B com aplicativos lógicos do Azure e Enterprise Integration Pack

Para Soluções B2B (entre empresas) e comunicação direta entre organizações, você pode criar fluxos de trabalho de integração empresarial escalonáveis automatizados usando o Enterprise Integration Pack (EIP) com [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md). Embora as organizações usem diferentes protocolos e formatos, elas podem trocar mensagens eletronicamente. O EIP transforma formatos diferentes em um formato que os sistemas de sua organização podem processar e dá suporte a protocolos padrão do setor, incluindo [AS2](../logic-apps/logic-apps-enterprise-integration-as2.md), [X12](logic-apps-enterprise-integration-x12.md)e [EDIFACT](../logic-apps/logic-apps-enterprise-integration-edifact.md). Você também pode proteger mensagens com criptografia e assinaturas digitais. O EIP dá suporte a esses [conectores de integração corporativa](../connectors/apis-list.md#integration-account-connectors) e estes padrões do setor:

* EDI (intercâmbio eletrônico de dados)
* Integração de aplicativos empresariais (EAI)

Se você estiver familiarizado com o Microsoft BizTalk Server ou os serviços BizTalk do Azure, o EIP seguirá conceitos semelhantes, tornando os recursos fáceis de usar. No entanto, uma grande diferença é que o EIP é baseado em arquiteturas em "contas de integração" para simplificar o armazenamento e o gerenciamento de artefatos usados em comunicações B2B. Essas contas são contêineres baseados em nuvem que armazenam todos os seus artefatos, como parceiros, contratos, esquemas, mapas e certificados. 

## <a name="why-use-the-enterprise-integration-pack"></a>Por que usar o Enterprise Integration Pack?

* Com o EIP, você pode armazenar todos os seus artefatos em um local – sua conta de integração.

* Você pode criar fluxos de trabalho B2B e integrá-los a aplicativos de software como serviço (SaaS) de terceiros, aplicativos locais e aplicativos personalizados usando os aplicativos e conectores lógicos do Azure.

* Você pode criar código personalizado para seus aplicativos lógicos com o Azure functions.

## <a name="how-do-i-get-started"></a>Como começo?

Antes de começar a criar fluxos de trabalho do aplicativo lógico B2B com o EIP, você precisará destes itens:

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* Uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) com os artefatos que você deseja usar

* Para criar mapas e esquemas, você pode usar o [Microsoft Azure aplicativos lógicos Enterprise Integration ferramentas para visual studio 2015 2,0](https://aka.ms/vsmapsandschemas) e visual Studio 2015.

Depois de criar uma conta de integração e adicionar seus artefatos, você pode começar a criar fluxos de trabalho B2B com esses artefatos criando um aplicativo lógico no portal do Azure. Se você for novo em aplicativos lógicos, tente [criar um aplicativo lógico básico](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para trabalhar com esses artefatos, certifique-se de primeiro vincular sua conta de integração ao seu aplicativo lógico. Depois disso, seu aplicativo lógico pode acessar sua conta de integração. Você também pode criar, gerenciar e implantar aplicativos lógicos usando o Visual Studio ou o [PowerShell](https://docs.microsoft.com/powershell/module/az.logicapp).

Aqui estão as etapas de alto nível para começar a criar aplicativos lógicos B2B:

![Pré-requisitos para a criação de aplicativos lógicos B2B](./media/logic-apps-enterprise-integration-overview/overview.png)  

## <a name="try-now"></a>Experimente agora

[Implantar um aplicativo lógico de exemplo totalmente operacional que envia e recebe mensagens AS2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-as2-send-receive)

## <a name="next-steps"></a>Passos seguintes

* [Criar parceiros comerciais](logic-apps-enterprise-integration-partners.md)
* [Criar contratos](../logic-apps/logic-apps-enterprise-integration-agreements.md)
* [Adicionar esquemas](logic-apps-enterprise-integration-schemas.md)
* [Adicionar mapas](../logic-apps/logic-apps-enterprise-integration-maps.md)
* [Migrar dos serviços BizTalk](../logic-apps/logic-apps-move-from-mabs.md)
