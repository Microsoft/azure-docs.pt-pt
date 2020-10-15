---
title: Quickstart - Criar e gerir fichas de acesso
titleSuffix: An Azure Communication Services quickstart
description: Saiba como gerir identidades e fichas de acesso utilizando a biblioteca de clientes da Administração de Serviços de Comunicação Azure.
author: tomaschladek
manager: jken
services: azure-communication-services
ms.author: tchladek
ms.date: 08/20/2020
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: e323f1f50fe6c67a841c300fcbec1eed3afc4497
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92074129"
---
# <a name="quickstart-create-and-manage-access-tokens"></a>Quickstart: Criar e gerir fichas de acesso

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Inicie-se com os Serviços de Comunicação da Azure utilizando a biblioteca de clientes da Administração de Serviços de Comunicação para provisão e gestão dos seus tokens de acesso. Os tokens de acesso permitem que as suas bibliotecas de clientes Chat e Call autentem diretamente contra os Serviços de Comunicação Azure. Estes tokens são gerados num serviço de fornecimento de fichas do lado do servidor que implementa. São então usados para rubricar as bibliotecas de clientes dos Serviços de Comunicação em dispositivos clientes.

Note que quaisquer preços vistos em imagens ao longo deste tutorial são apenas para fins.

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

Issued a access token with 'voip' scope for identity with ID: 8:acs:fecfaddf-bf2c-4a0e-b52f-7d918c9536e6_65012b-1400da9050:
<token signature here>

Issued a access token with 'chat' scope for identity with ID: 8:acs:fecfaddf-bf2c-4a0e-b52f-7d918c9536e6_65012b-1400da9050:
<token signature here>

Successfully deleted the identity with ID: 8:acs:fecfaddf-bf2c-4a0e-b52f-7d918c9536e6_65012b-1400da9050

Deleted the identity with ID: 8:acs:fecfaddf-bf2c-4a0e-b52f-7d918c9536e6_65012b-1400da9050
```
<!---cSpell:enable --->

## <a name="clean-up-resources"></a>Limpar recursos

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
