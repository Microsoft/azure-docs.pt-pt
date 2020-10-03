---
title: Quickstart - Adicione chat à sua app
titleSuffix: An Azure Communication Services quickstart
description: Este quickstart mostra-lhe como adicionar o chat dos Serviços de Comunicação à sua aplicação.
author: fanche
manager: phans
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: 2d0d80be367dda3689566dec2ade6fd7fc7c01fc
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2020
ms.locfileid: "91665381"
---
# <a name="quickstart-add-chat-to-your-app"></a>Quickstart: Adicione chat à sua App

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Inicie-se com os Serviços de Comunicação Azure utilizando a biblioteca de clientes de Chat de Serviços de Comunicação para adicionar chat em tempo real à sua aplicação. Neste quickstart, usaremos a biblioteca de clientes Chat para criar linhas de chat que permitem aos utilizadores ter conversas entre si. Para saber mais sobre os conceitos chat, visite a [documentação conceptual](../../concepts/chat/concepts.md)do chat.

::: zone pivot="programming-language-javascript"
[!INCLUDE [Chat with JavaScript client library](./includes/chat-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Chat with Python client library](./includes/chat-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Chat with Java client library](./includes/chat-java.md)]
::: zone-end

::: zone pivot="programming-language-csharp"
[!INCLUDE [Chat with C# client library](./includes/chat-csharp.md)]
::: zone-end

## <a name="clean-up-resources"></a>Limpar recursos

Se pretender limpar e remover uma assinatura de Serviços de Comunicação, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos que lhe sejam associados. Saiba mais sobre [a limpeza de recursos.](../create-communication-resource.md#clean-up-resources)

## <a name="next-steps"></a>Próximas etapas

Neste arranque rápido aprendeu a:

> [!div class="checklist"]
> * Criar um cliente de chat
> * Criar um fio com dois utilizadores
> * Envie uma mensagem para o fio
> * Receber mensagens de um fio
> * Remova os utilizadores de um fio

> [!div class="nextstepaction"]
> [Experimente a App Chat Hero](../../samples/chat-hero-sample.md)

Também pode querer:

 - Saiba mais [sobre conceitos de chat](../../concepts/chat/concepts.md)
 - Familiarize-se com [a biblioteca de clientes de chat](../../concepts/chat/sdk-features.md)
