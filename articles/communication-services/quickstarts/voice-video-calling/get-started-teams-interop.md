---
title: Quickstart - Equipas interop nos Serviços de Comunicação Azure
titleSuffix: An Azure Communication Services quickstart
description: Neste arranque rápido, você vai aprender a se juntar a uma reunião de equipas com o Azure Communication Calling SDK.
author: chpalm
ms.author: chpalm
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-plat-web-ios-android
ms.openlocfilehash: 0e75d2b480a9cbfd2977d9d449c1ea12bdfe4920
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106095616"
---
# <a name="quickstart-join-your-calling-app-to-a-teams-meeting"></a>Quickstart: Junte-se à sua app de chamadas para uma reunião de equipas

[!INCLUDE [Public Preview](../../includes/public-preview-include-document.md)]

> [!IMPORTANT]
> Para permitir/desativar [a interoperabilidade do inquilino das equipas,](../../concepts/teams-interop.md)preencha [este formulário](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR21ouQM6BHtHiripswZoZsdURDQ5SUNQTElKR0VZU0VUU1hMOTBBMVhESS4u).

Inicie-se com os Serviços de Comunicação Azure ligando a sua solução de chamada às Equipas microsoft utilizando o JavaScript SDK.

::: zone pivot="platform-web"
[!INCLUDE [Calling with JavaScript](./includes/teams-interop-javascript.md)]
::: zone-end

::: zone pivot="platform-android"
[!INCLUDE [Calling with Android](./includes/teams-interop-android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Calling with iOS](./includes/teams-interop-ios.md)]
::: zone-end

A funcionalidade descrita neste documento utiliza a versão General Availablity dos SDKs dos Serviços de Comunicação. A interoperabilidade das equipas requer a versão Beta dos SDKs dos Serviços de Comunicação. Os SDKs Beta podem ser explorados na página de [notas de lançamento.](https://github.com/Azure/Communication/tree/master/releasenotes)

Ao executar o passo "Instalar pacote" com os SDKs Beta, modifique a versão do seu pacote para a versão beta mais recente especificando a versão `@1.0.0-beta.10` (versão no momento da escrita deste artigo) no nome do `communication-calling` pacote. Não precisa modificar o comando do `communication-common` pacote. Por exemplo:

```console
npm install @azure/communication-calling@1.0.0-beta.10 --save
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretender limpar e remover uma assinatura de Serviços de Comunicação, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos que lhe sejam associados. Saiba mais sobre [a limpeza de recursos.](../create-communication-resource.md#clean-up-resources)

## <a name="next-steps"></a>Próximos passos

Para obter mais informações, veja os seguintes artigos:

- Veja a nossa [amostra de herói chamando](../../samples/calling-hero-sample.md)
- Saiba mais [sobre as capacidades de chamar sDK](./calling-client-samples.md)
- Saiba mais sobre [como funciona a chamada](../../concepts/voice-video-calling/about-call-types.md)
