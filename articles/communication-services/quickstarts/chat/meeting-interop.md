---
title: Começar com equipas interop nos Serviços de Comunicação Azure
titleSuffix: An Azure Communication Services quickstart
description: Neste arranque rápido, você vai aprender a se juntar a uma reunião de equipas com a biblioteca de clientes Azure Communication Chat
author: askaur
ms.author: askaur
ms.date: 12/08/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 336b12f7000b70c424f1165976370300bc8c85e0
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101656127"
---
# <a name="quickstart-join-your-chat-app-to-a-teams-meeting"></a>Quickstart: Junte-se à sua app de chat para uma reunião de Equipas

> [!IMPORTANT]
> Para permitir/desativar [a interoperabilidade do inquilino das equipas,](../concepts/teams-interop.md)preencha [este formulário](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR21ouQM6BHtHiripswZoZsdURDQ5SUNQTElKR0VZU0VUU1hMOTBBMVhESS4u).

Inicie-se com os Serviços de Comunicação Azure ligando a sua solução de chat às Equipas Microsoft utilizando a biblioteca de clientes JavaScript. 

## <a name="prerequisites"></a>Pré-requisitos 

1. Uma [colocação de equipas.](/deployoffice/teams-install) 
2. Uma [aplicação](./get-started.md)de chat de trabalho. 

## <a name="enable-teams-interoperability"></a>Ativar a interoperabilidade das equipas 

Um utilizador dos Serviços de Comunicação que se junte a uma reunião de Equipas como utilizador convidado só pode aceder ao chat da reunião quando se juntar à chamada de reunião das Equipas. Consulte a documentação [do interop das Equipas](../voice-video-calling/get-started-teams-interop.md) para saber como adicionar um utilizador de Serviços de Comunicação a uma chamada de reunião de Equipas.

Deve ser membro da organização proprietária de ambas as entidades para utilizar esta funcionalidade.

[!INCLUDE [Join Teams meetings](./includes/meeting-interop-javascript.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretender limpar e remover uma assinatura de Serviços de Comunicação, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos que lhe sejam associados. Saiba mais sobre [a limpeza de recursos.](../create-communication-resource.md#clean-up-resources)

## <a name="next-steps"></a>Próximos passos

Para obter mais informações, veja os seguintes artigos:

- Confira a nossa [amostra de herói de chat](../../samples/chat-hero-sample.md)
- Saiba mais sobre [como funciona o chat](../../concepts/chat/concepts.md)