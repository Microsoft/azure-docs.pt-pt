---
title: Adicione o conector do Twilio nas suas Azure Logic apps | Microsoft Docs
description: Descrição geral do conector do Twilio com parâmetros de REST API
services: logic-apps
documentationcenter: ''
author: ecfan
manager: jeconnoc
editor: ''
tags: connectors
ms.assetid: 43116187-4a2f-42e5-9852-a0d62f08c5fc
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/19/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 8bcf69a7c8e04cb45d795fd0d6f20d477c15865d
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/11/2018
ms.locfileid: "35296140"
---
# <a name="get-started-with-the-twilio-connector"></a>Começar a utilizar o conector do Twilio
Ligar ao Twilio para enviar e receber mensagens IP, SMS e MMS global. Com Twilio, pode:

* Crie o fluxo de negócio com base nos dados que aproveitar Twilio. 
* Utilize ações obter uma mensagem, mensagens de lista e muito mais. Estas ações obter uma resposta e, em seguida, torne a saída disponível para outras ações. Por exemplo, quando receber uma nova mensagem do Twilio, pode efetuar esta mensagem e utilizá-lo um fluxo de trabalho do Service Bus. 

Começar através da criação de uma aplicação lógica; consulte [criar uma aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-twilio"></a>Criar uma ligação ao Twilio
Ao adicionar este conector para as logic apps, introduza os seguintes valores do Twilio:

| Propriedade | Necessário | Descrição |
| --- | --- | --- |
| ID da Conta |Sim |Introduza o seu ID de conta do Twilio |
| Token de Acesso |Sim |Introduza o seu token de acesso do Twilio |

> [!INCLUDE [Steps to create a connection to Twilio](../../includes/connectors-create-api-twilio.md)]
> 
> 

Se não tiver um token de acesso do Twilio, consulte [identidade de utilizador e Tokens de acesso](https://www.twilio.com/docs/api/chat/guides/identity).

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Ver todos os acionadores e ações definidas no swagger e consulte também os limites no [detalhes do conector](/connectors/twilio/).

## <a name="more-connectors"></a>Mais conectores
Volte à [lista APIs](apis-list.md).