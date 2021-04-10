---
title: Quickstart - Criar e gerir fichas de acesso
titleSuffix: An Azure Communication Services quickstart
description: Saiba como gerir identidades e fichas de acesso utilizando o Azure Communication Services Identity SDK.
author: tomaschladek
manager: nmurav
services: azure-communication-services
ms.author: tchladek
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: e356219d22ee558ce3de5a96d58f24b9e7902d8a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105726622"
---
# <a name="quickstart-create-and-manage-access-tokens"></a>Quickstart: Criar e gerir fichas de acesso

Inicie-se com os Serviços de Comunicação Azure utilizando o Serviço de Identidade dos Serviços de Comunicação SDK. Permite-lhe criar identidades e gerir os seus tokens de acesso. A identidade representa a entidade da sua aplicação no Serviço de Comunicação Azure (por exemplo, utilizador ou dispositivo). Os tokens de acesso permitem que os seus Chat e Call SDKs autentem diretamente contra os Serviços de Comunicação Azure. Recomendamos gerar fichas de acesso num serviço do lado do servidor. Os tokens de acesso são então utilizados para inicializar os SDKs de Serviços de Comunicação em dispositivos clientes.

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

Created an identity with ID: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-19e0-2727-80f5-8b3a0d003502

Issued an access token with 'voip' scope that expires at 30/03/21 08:09 09 AM:
<token signature here>

Created an identity with ID: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-1ce9-31b4-54b7-a43a0d006a52

Issued an access token with 'voip' scope that expires at 30/03/21 08:09 09 AM:
<token signature here>

Successfully revoked all access tokens for identity with ID: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-19e0-2727-80f5-8b3a0d003502

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
> * Utilizar a Identidade dos Serviços de Comunicação SDK


> [!div class="nextstepaction"]
> [Adicionar chamadas de voz à sua aplicação](./voice-video-calling/getting-started-with-calling.md)

Também pode querer:

 - [Saiba mais sobre a autenticação](../concepts/authentication.md)
 - [Adicionar o chat à aplicação](./chat/get-started.md)
 - [Conheça a arquitetura do cliente e do servidor](../concepts/client-and-server-architecture.md)
