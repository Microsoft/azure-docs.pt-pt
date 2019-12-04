---
title: Conectar-se ao Facebook
description: Gerenciar sua linha do tempo e página com APIs REST do Facebook e aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: 1aa936f902dc17c9a401959c19824f6c581547b1
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789838"
---
# <a name="manage-your-facebook-timeline-and-page-by-using-azure-logic-apps"></a>Gerenciar sua linha do tempo e página do Facebook usando aplicativos lógicos do Azure

Conecte-se ao Facebook e poste em uma linha do tempo, obtenha um feed de página e muito mais. Com o Facebook, você pode:

* Crie seu fluxo de negócios com base nos dados obtidos do Facebook. 
* Use um gatilho quando uma nova postagem for recebida.
* Use as ações postadas em sua linha do tempo, obtenha um feed de página e muito mais. Essas ações obtêm uma resposta e tornam a saída disponível para outras ações. Por exemplo, quando há uma nova postagem em sua linha do tempo, você pode pegar essa postagem e enviá-la por push para o seu feed do Twitter. 

Você pode começar criando um aplicativo lógico agora, consulte [criar um aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-facebook"></a>Criar uma conexão com o Facebook

Ao adicionar esse conector aos seus aplicativos lógicos, você deve autorizar que os aplicativos lógicos se conectem ao Facebook.

1. Entre na sua conta do Facebook.

2. Selecione **autorizar**e permita que seus aplicativos lógicos se conectem e usem o Facebook. 

> [!INCLUDE [Steps to create a connection to Facebook](../../includes/connectors-create-api-facebook.md)]
> 

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos, como gatilhos, ações e limites, conforme descrito pelo arquivo OpenAPI (anteriormente Swagger) do conector, consulte a [página de referência do conector](/connectors/facebook/).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores de aplicativos lógicos](../connectors/apis-list.md)