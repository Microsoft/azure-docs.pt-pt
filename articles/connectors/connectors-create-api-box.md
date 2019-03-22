---
title: Ligue-se ao Box - Azure Logic Apps | Documentos da Microsoft
description: Criar e gerenciar arquivos com APIs de REST de caixa e o Azure Logic Apps
author: ecfan
ms.author: estfan
ms.date: 11/07/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 971d38fa0fbd47f0deb815577033bbe684aac32f
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58310681"
---
# <a name="create-and-manage-files-in-box-with-azure-logic-apps"></a>Criar e gerenciar arquivos na caixa de com o Azure Logic Apps

Este artigo mostra como pode criar e gerir os seus ficheiros na caixa de dentro de uma aplicação lógica com o conector de caixa. Dessa forma, pode criar aplicações lógicas que automatizam as tarefas e fluxos de trabalho para gerir os seus ficheiros e outras ações, por exemplo:

* Crie o seu fluxo de negócios com base nos dados que obtém da caixa.

* Quando um ficheiro é criado ou atualizado, acione o fluxo de trabalho e tarefas automatizadas.

* Execute as ações que copia um ficheiro, elimina um ficheiro e muito mais.

  Quando estas ações obtém uma resposta, eles tornam a saída disponível para outras ações. 
  Por exemplo, quando um ficheiro for alterado na caixa, pode enviar esse ficheiro por e-mail com o Office 365.

## <a name="prerequisites"></a>Pré-requisitos

* A [caixa conta](https://www.box.com/home)

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se para obter uma conta do Azure gratuita</a>. 

* A aplicação de lógica onde pretende aceder à sua conta de caixa. Para começar a sua aplicação lógica com um acionador de caixa, tem um [aplicação lógica em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Se estiver familiarizado com aplicações lógicas, reveja [o que é o Azure Logic Apps](../logic-apps/logic-apps-overview.md).

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos, como disparadores, ações e limites, conforme descrito pelo OpenAPI do conector (anteriormente Swagger) de ficheiros, consulte a [página de referência do conector](/connectors/box/).

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre outras [conectores do Logic Apps](../connectors/apis-list.md)