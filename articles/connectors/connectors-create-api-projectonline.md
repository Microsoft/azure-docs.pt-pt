---
title: Conectar-se ao Project online do aplicativo lógico do Azure | Microsoft Docs
description: Automatizar fluxos de trabalho que monitoram, criam e gerenciam projetos, tarefas e recursos do Project online usando aplicativos lógicos do Azure
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.suite: integration
ms.topic: article
ms.assetid: 40ce621e-4925-4653-93bb-71ab9abcbdf1
tags: connectors
ms.date: 08/24/2018
ms.openlocfilehash: fe571209d28fe098ce9b507cb67b0a9a5abd25a3
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050856"
---
# <a name="manage-project-online-projects-tasks-and-resources-by-using-azure-logic-apps"></a>Gerenciar projetos, tarefas e recursos do Project online usando os aplicativos lógicos do Azure

Com os aplicativos lógicos do Azure e o conector do Project online, você pode criar tarefas e fluxos de trabalho automatizados para seus projetos, tarefas e recursos no Project online por meio do Office 365. Seus fluxos de trabalho podem executar essas ações e outros, por exemplo:

* Monitore quando novos projetos, tarefas ou recursos são criados. Ou então, monitore quando novos projetos são publicados.
* Crie novos projetos, tarefas ou recursos.
* Listar projetos ou tarefas existentes.
* Faça check-out, faça check-in ou publique projetos.

O Project online ajuda você a planejar, priorizar e gerenciar projetos e investimentos de portfólio de projetos de praticamente qualquer lugar em praticamente qualquer dispositivo, fornecendo poderosos recursos de gerenciamento de projetos. Você pode usar gatilhos do Project online que obtêm respostas do Project online e disponibilizam a saída para outras ações. Você pode usar ações em seus aplicativos lógicos para executar várias tarefas no Project online. Se você for novo em aplicativos lógicos, examine [o que são os aplicativos lógicos do Azure?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/). 

* Project online, disponível por meio de uma [conta do Office 365](https://www.office.com/), 

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* O aplicativo lógico no qual você deseja acessar os dados do seu projeto online. Para começar com um gatilho do Project online, [crie um aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para usar ações do Project online, inicie seu aplicativo lógico com outro gatilho, por exemplo, o gatilho de recorrência.

## <a name="connect-to-project-online"></a>Conectar-se ao Project online

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Entre no [portal do Azure](https://portal.azure.com)e abra seu aplicativo lógico no designer de aplicativo lógico, se ainda não estiver aberto.

1. Escolha um caminho: 

   * Para aplicativos lógicos em branco, na caixa de pesquisa, digite "Project online" como filtro. 
   Na lista de gatilhos, selecione o gatilho desejado. 

     -ou-

   * Para aplicativos lógicos existentes, na etapa em que você deseja adicionar uma ação, escolha **nova etapa**. Na caixa de pesquisa, digite "Project online" como seu filtro. Na lista ações, selecione a ação desejada.

1. Se você for solicitado a entrar no Project online, entre agora.

   Suas credenciais autorizam seu aplicativo lógico a criar uma conexão com o Project online e acessar seus dados.

1. Forneça os detalhes necessários para o gatilho ou ação selecionado e continue criando o fluxo de trabalho do aplicativo lógico.

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, que são descritos pela descrição de OpenAPI (anteriormente, Swagger) do conector, examine a [página de referência](/connectors/projectonline/)do conector.

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre outros conectores de [aplicativos lógicos](../connectors/apis-list.md)