---
title: Conectar ao box
description: Criar e gerenciar arquivos com APIs REST do box e aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: 38e061e918d445de07961af1789891f44c59090a
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789877"
---
# <a name="create-and-manage-files-in-box-with-azure-logic-apps"></a>Criar e gerenciar arquivos no box com os aplicativos lógicos do Azure

Este artigo mostra como você pode criar e gerenciar seus arquivos no box de dentro de um aplicativo lógico com o conector do box. Dessa forma, você pode criar aplicativos lógicos que automatizam tarefas e fluxos de trabalho para gerenciar seus arquivos e outras ações, por exemplo:

* Crie seu fluxo de negócios com base nos dados obtidos do box.

* Disparar tarefas automatizadas e fluxo de trabalho quando um arquivo for criado ou atualizado.

* Executar uma ação que copia um arquivo ou exclui um arquivo.

  Quando essas ações recebem uma resposta, elas tornam a saída disponível para outras ações. 
  Por exemplo, quando um arquivo é alterado no box, você pode enviar esse arquivo por email usando o Office 365.

## <a name="prerequisites"></a>Pré-requisitos

* Uma [conta do box](https://www.box.com/home)

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/). 

* O aplicativo lógico no qual você deseja acessar sua conta do box. Para iniciar seu aplicativo lógico com um gatilho de caixa, você precisa de um [aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Se você for novo em aplicativos lógicos, examine [o que são os aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md).

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos, como gatilhos, ações e limites, conforme descrito pelo arquivo OpenAPI (anteriormente Swagger) do conector, consulte a [página de referência do conector](/connectors/box/).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores de aplicativos lógicos](../connectors/apis-list.md)