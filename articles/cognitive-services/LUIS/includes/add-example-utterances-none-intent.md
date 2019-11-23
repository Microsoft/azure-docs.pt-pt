---
title: incluir ficheiro
description: incluir ficheiro
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 11/20/2019
ms.author: diberry
ms.openlocfilehash: e4147fbb27c8538f801f6c49f8b535a283faf50f
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325919"
---
The client application needs to know if an utterance is not meaningful or appropriate for the application. The **None** intent is added to each application as part of the creation process to determine if an utterance shouldn't be answered by the client application.

If LUIS returns the **None** intent for an utterance, your client application can ask if the user wants to end the conversation or give more directions for continuing the conversation. 

If you leave the **None** intent empty, an utterance that should be predicted outside the subject domain will be predicted in one of the existing subject domain intents. The result is that the client application, such as a chat bot, will perform incorrect operations based on an incorrect prediction. 

1. Selecione **Intents** (Intenções) no painel esquerdo.

1. Selecione a intenção **None** (Nenhuma). Add three utterances that your user might enter but are not relevant to your Pizza ordering app:

    |`None` example utterances|
    |--|
    |`Barking dogs are annoying`|
    |`Order a pizza for me`|
    |`Penguins in the ocean`|

    These examples shouldn't use words you expect in your subject domain such as `pizza`, `cheese`, `crust`, `pickup` `deliver`.