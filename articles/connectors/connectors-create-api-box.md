---
title: Ligar à Caixa
description: Automatizar tarefas e fluxos de trabalho que criam e gerem ficheiros em Box utilizando aplicações lógicas azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: c7f97ff33742eb545cbfbd7521ba135584851e5e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75666776"
---
# <a name="create-and-manage-files-in-box-by-using-azure-logic-apps"></a>Criar e gerir ficheiros em Box utilizando aplicações da Lógica Azure

Este artigo mostra como pode criar e gerir os seus ficheiros em Box a partir de dentro de uma aplicação lógica com o conector Box. Desta forma, pode criar aplicações lógicas que automatizam tarefas e fluxos de trabalho para gerir os seus ficheiros e outras ações, por exemplo:

* Construa o seu fluxo de negócio saem com base nos dados que obtém da Box.

* Desencadear tarefas automatizadas e fluxo de trabalho quando um ficheiro é criado ou atualizado.

* Execute uma ação que copie um ficheiro ou apague um ficheiro.

  Quando estas ações obtêm uma resposta, disponibilizam a produção para outras ações. 
  Por exemplo, quando um ficheiro é alterado na Caixa, pode enviar esse ficheiro por e-mail utilizando o Office 365.

## <a name="prerequisites"></a>Pré-requisitos

* Uma [conta caixa](https://www.box.com/home)

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/). 

* A aplicação lógica onde pretende aceder à sua conta Box. Para iniciar a sua aplicação lógica com um gatilho Box, precisa de uma [aplicação lógica em branco.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Conhecimento básico sobre [como criar aplicações lógicas.](../logic-apps/quickstart-create-first-logic-app-workflow.md)
Se você é novo em aplicativos lógicos, reveja [o que é Azure Logic Apps](../logic-apps/logic-apps-overview.md).

## <a name="connector-reference"></a>Referência do conector

Para detalhes técnicos, tais como gatilhos, ações e limites, conforme descrito pelo ficheiro OpenAPI (ex-Swagger) do conector, consulte a página de [referência do conector](/connectors/box/).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores de Aplicações Lógicas](../connectors/apis-list.md)