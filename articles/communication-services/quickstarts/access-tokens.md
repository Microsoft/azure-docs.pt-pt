---
title: Quickstart - Criar e gerir fichas de acesso
titleSuffix: An Azure Communication Services quickstart
description: Saiba como gerir identidades e fichas de acesso utilizando a biblioteca de clientes da Administração de Serviços de Comunicação Azure.
author: tomaschladek
manager: nmurav
services: azure-communication-services
ms.author: tchladek
ms.date: 08/20/2020
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: b67d0808643797d88628b626403c1b9d97cf1cad
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94506247"
---
# <a name="quickstart-create-and-manage-access-tokens"></a>Quickstart: Criar e gerir fichas de acesso

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Inicie com os Serviços de Comunicação Azure utilizando a biblioteca de clientes da Administração de Serviços de Comunicação. Permite-lhe criar identidades e gerir os seus tokens de acesso. A identidade representa a entidade da sua aplicação no Serviço de Comunicação Azure (por exemplo, utilizador ou dispositivo). Os tokens de acesso permitem que as suas bibliotecas de clientes Chat e Call autentem diretamente contra os Serviços de Comunicação Azure. Recomendamos gerar fichas de acesso num serviço do lado do servidor. Os tokens de acesso são então utilizados para inicializar as bibliotecas de clientes dos Serviços de Comunicação em dispositivos clientes.

Os preços vistos em imagens ao longo deste tutorial são apenas para fins de demonstração.

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/user-access-token-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/user-access-token-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/user-access-token-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/user-access-token-java.md)]
::: zone-end

A saída da aplicação descreve cada ação que é concluída:
<!---cSpell:disable --->
```console
Azure Communication Services - Access Tokens Quickstart

Created an identity: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-19e0-2727-80f5-8b3a0d003502

Issued a access token with 'voip' scope for identity with ID: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-19e0-2727-80f5-8b3a0d003502:
<token signature here>

Successfully deleted the identity with ID: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-19e0-2727-80f5-8b3a0d003502

Deleted the identity with ID: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-19e0-2727-80f5-8b3a0d003502
```
<!---cSpell:enable --->

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretender limpar e remover uma assinatura de Serviços de Comunicação, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos que lhe sejam associados. Saiba mais sobre [a limpeza de recursos.](./create-communication-resource.md#clean-up-resources)


## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, aprendeu a:

> [!div class="checklist"]
> * Gerir identidades
> * Emitir fichas de acesso
> * Utilizar a biblioteca de clientes da Administração de Serviços de Comunicação


> [!div class="nextstepaction"]
> [Adicionar chamadas de voz à sua aplicação](./voice-video-calling/getting-started-with-calling.md)

Também pode querer:

 - [Saiba mais sobre a autenticação](../concepts/authentication.md)
 - [Adicionar o chat à aplicação](./chat/get-started.md)
 - [Conheça a arquitetura do cliente e do servidor](../concepts/client-and-server-architecture.md)
 