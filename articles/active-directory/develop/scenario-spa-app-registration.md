---
title: Registrar aplicativos de página única-plataforma Microsoft Identity | Azure
description: Saiba como criar um aplicativo de página única (registro de aplicativo)
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 5b18b10748e0587920c6965f1d235376da928469
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701897"
---
# <a name="single-page-application-app-registration"></a>Aplicativo de página única: registro de aplicativo

Esta página explica as especificações de registro do aplicativo para um aplicativo de página única (SPA).

Siga as etapas para [registrar um novo aplicativo com a plataforma de identidade da Microsoft](quickstart-register-app.md)e selecione as contas com suporte para seu aplicativo. O cenário SPA pode dar suporte à autenticação com contas em sua organização ou em qualquer organização e contas pessoais da Microsoft.

Em seguida, Aprenda os aspectos específicos do registro de aplicativo que se aplicam a aplicativos de página única.

## <a name="register-a-redirect-uri"></a>Registrar um URI de redirecionamento

O fluxo implícito envia os tokens em um redirecionamento para o aplicativo de página única em execução em um navegador da Web. Portanto, é importante registrar um URI de redirecionamento no qual seu aplicativo pode receber os tokens. Verifique se o URI de redirecionamento corresponde exatamente ao URI do seu aplicativo.

No [portal do Azure](https://go.microsoft.com/fwlink/?linkid=2083908), vá para o aplicativo registrado. Na página **autenticação** do aplicativo, selecione a plataforma **da Web** . Insira o valor do URI de redirecionamento para seu aplicativo no campo **URI de redirecionamento** .

## <a name="enable-the-implicit-flow"></a>Habilitar o fluxo implícito

Na mesma página de **autenticação** , em **Configurações avançadas**, você também deve habilitar a **concessão implícita**. Se seu aplicativo estiver apenas entrando em usuários e obtendo tokens de ID, será suficiente marcar a caixa de seleção **tokens de ID** .

Se seu aplicativo também precisar obter tokens de acesso para chamar APIs, certifique-se de marcar a caixa de seleção **tokens de acesso** também. Para obter mais informações, consulte [tokens de ID](./id-tokens.md) e [tokens de acesso](./access-tokens.md).

## <a name="api-permissions"></a>Permissões de API

Aplicativos de página única podem chamar APIs em nome do usuário conectado. Eles precisam solicitar permissões delegadas. Para obter detalhes, consulte [adicionar permissões para acessar APIs da Web](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Configuração de código do aplicativo](scenario-spa-app-configuration.md)
