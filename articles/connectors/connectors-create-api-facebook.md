---
title: Conectar-se ao Facebook – aplicativos lógicos do Azure
description: Gerenciar sua linha do tempo e página com APIs REST do Facebook e aplicativos lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: 83431184d7e9c5970ece6af143ee9b5166da96d5
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050947"
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

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre outros conectores de [aplicativos lógicos](../connectors/apis-list.md)