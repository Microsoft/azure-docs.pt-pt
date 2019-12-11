---
title: Encontrar uma API para um aplicativo personalizado | Azure
description: Como configurar as permissões necessárias para acessar uma API específica em seu aplicativo do Azure AD personalizado desenvolvido
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: ryanwi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c2b88ef00a8a40134462fdc285f1e4c3f12a0b0
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74963907"
---
# <a name="how-to-find-a-specific-api-needed-for-a-custom-developed-application"></a>Como encontrar uma API específica necessária para um aplicativo desenvolvido de forma personalizada

O acesso às APIs requer a configuração de escopos de acesso e funções. Se você quiser expor suas APIs Web do aplicativo de recursos para aplicativos cliente, será necessário configurar escopos de acesso e funções para a API. Se você quiser que um aplicativo cliente acesse uma API Web, precisará configurar permissões para acessar a API no registro do aplicativo.

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>Configurar uma aplicação de recurso para expor APIs Web

Quando você expõe sua API Web, a API é exibida na lista **selecionar uma API** ao adicionar permissões a um registro de aplicativo. Para adicionar escopos de acesso, siga as etapas descritas em [configurar um aplicativo para expor APIs Web](quickstart-configure-app-expose-web-apis.md).

## <a name="configuring-a-client-application-to-access-web-apis"></a>Configurando um aplicativo cliente para acessar APIs Web

Ao adicionar permissões ao registro do aplicativo, você pode **Adicionar acesso à API** para APIs da Web expostas. Para acessar APIs Web, siga as etapas descritas em [configurar um aplicativo cliente para acessar APIs da Web](quickstart-configure-app-access-web-apis.md).

## <a name="next-steps"></a>Passos seguintes

- [Noções básicas sobre o manifesto do aplicativo Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest)
