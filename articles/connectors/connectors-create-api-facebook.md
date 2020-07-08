---
title: Conecte-se ao Facebook
description: Automatizar tarefas e fluxos de trabalho que gerem a sua linha de tempo e página do Facebook utilizando apps Azure Logic
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: 803453291b5cab2c51fec6641f8b096f62336325
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75665807"
---
# <a name="manage-your-facebook-timeline-and-page-by-using-azure-logic-apps"></a>Gerencie a sua linha de tempo e página do Facebook utilizando apps Azure Logic

Conecte-se ao Facebook e publique uma linha do tempo, obtenha um feed de página, e muito mais. Com o Facebook, pode:

* Construa o seu fluxo de negócio com base nos dados que obtém do Facebook. 
* Utilize um gatilho quando um novo post for recebido.
* Use ações que publiquem na sua linha do tempo, obtenha um feed de página, e muito mais. Estas ações obtêm uma resposta e, em seguida, disponibilizam a produção para outras ações. Por exemplo, quando houver uma nova publicação na sua linha temporal, pode pegar nesse post e empurrá-lo para o seu feed do Twitter. 

Pode começar por criar agora uma aplicação lógica, ver [Criar uma aplicação lógica.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-a-connection-to-facebook"></a>Criar uma ligação ao Facebook

Ao adicionar este conector às suas aplicações lógicas, tem de autorizar aplicações lógicas para se ligarem ao seu Facebook.

1. Inscreva-se na sua conta do Facebook.

2. **Selecione Authorize**, e permita que as suas aplicações lógicas conectem e utilizem o seu Facebook. 

> [!INCLUDE [Steps to create a connection to Facebook](../../includes/connectors-create-api-facebook.md)]
> 

## <a name="connector-reference"></a>Referência do conector

Para mais detalhes técnicos, tais como gatilhos, ações e limites, conforme descrito pelo ficheiro OpenAPI (ex-Swagger) do conector, consulte a [página de referência do conector](/connectors/facebook/).

## <a name="next-steps"></a>Próximos passos

* Saiba mais sobre [outros conectores de Apps Lógicas](../connectors/apis-list.md)