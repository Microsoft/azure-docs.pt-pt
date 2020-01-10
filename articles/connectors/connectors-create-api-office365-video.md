---
title: Conectar-se ao vídeo do Office 365
description: Automatizar tarefas e fluxos de trabalho que gerenciam vídeos no vídeo do Office 365 usando aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 05/18/2016
tags: connectors
ms.openlocfilehash: 8ac6b7b411e7f42dd076c5b16e7b500a819c617f
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2020
ms.locfileid: "75665790"
---
# <a name="manage-videos-in-office365-video-by-using-azure-logic-apps"></a>Gerenciar vídeos no vídeo do Office365 usando aplicativos lógicos do Azure

Conecte-se ao vídeo do Office 365 para obter informações sobre um vídeo do Office 365, obter uma lista de vídeos e muito mais. Com o vídeo do Office 365, você pode:

* Crie seu fluxo de negócios com base nos dados obtidos do vídeo do Office 365. 

* Use as ações que verificam o status do portal de vídeo, obtenha uma lista de todos os vídeos em um canal e muito mais. Essas ações obtêm uma resposta e tornam a saída disponível para outras ações. 

Por exemplo, você pode usar o conector de Pesquisa do Bing para pesquisar vídeos do Office 365 e, em seguida, usar o conector de vídeo do Office 365 para obter informações sobre esse vídeo. Se o vídeo atender aos seus requisitos, você poderá postar este vídeo no Facebook.

Você pode começar criando um aplicativo lógico agora, consulte [criar um aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-office365-video"></a>Conectar ao vídeo do Office365

Ao adicionar esse conector aos seus aplicativos lógicos, você deve entrar na sua conta de vídeo do Office 365 e permitir que os aplicativos lógicos se conectem à sua conta.

> [!INCLUDE [Steps to create a connection to Office 365 Video](../../includes/connectors-create-api-office365video.md)]

Depois de criar a conexão, insira as propriedades de vídeo do Office 365, como o nome do locatário ou a ID do canal. 

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Exiba todos os gatilhos e ações definidos no Swagger e também veja os limites nos [detalhes do conector](/connectors/office365videoconnector/).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores de aplicativos lógicos](../connectors/apis-list.md)