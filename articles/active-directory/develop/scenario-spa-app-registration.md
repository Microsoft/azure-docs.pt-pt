---
title: Registe aplicações de página única - plataforma de identidade da Microsoft Azure
description: Saiba como construir uma aplicação de uma única página (registo de aplicações)
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 6f690a8b3436a45d434ccad2bbaa7d2a1b0b76aa
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882153"
---
# <a name="single-page-application-app-registration"></a>Aplicação de página única: Registo de aplicativos

Esta página explica as especificidades do registo da aplicação para uma aplicação de uma página única (SPA).

Siga os passos para [registar uma nova aplicação com a plataforma de identidade da Microsoft](quickstart-register-app.md)e selecione as contas suportadas para a sua aplicação. O cenário SPA pode suportar a autenticação com contas na sua organização ou em qualquer organização e contas pessoais da Microsoft.

Em seguida, conheça os aspetos específicos do registo de candidaturas aplicáveis às aplicações de uma página única.

## <a name="register-a-redirect-uri"></a>Registe um URI redirecionado

O fluxo implícito envia as fichas num redirecionamento para a aplicação de uma página única que funciona num navegador web. Por isso, é importante registar um URI redirecionado onde a sua aplicação pode receber os tokens. Certifique-se de que o URI redireciona exatamente o URI para a sua aplicação.

No [portal Azure,](https://go.microsoft.com/fwlink/?linkid=2083908)vá ao seu pedido registado. Na página de **Autenticação** da aplicação, selecione a plataforma **Web.** Insira o valor do URI redirecionamento para a sua aplicação no campo **Redirect URI.**

## <a name="enable-the-implicit-flow"></a>Ativar o fluxo implícito

Na mesma página de **Autenticação,** em **definições Avançadas,** deve também ativar a **concessão implícita**. Se a sua aplicação estiver apenas a iniciar sessão nos utilizadores e a obter fichas de identificação, é suficiente selecionar a caixa de verificação de **fichas de identificação.**

Se a sua aplicação também necessitar de obter fichas de acesso para ligar para APIs, certifique-se de selecionar também a caixa de verificação de fichas de **acesso.** Para mais informações, consulte [fichas de identificação](./id-tokens.md) e fichas de [acesso.](./access-tokens.md)

## <a name="api-permissions"></a>Permissões de API

As aplicações de página única podem ligar para APIs em nome do utilizador inscrito. Precisam de pedir permissões delegadas. Para mais detalhes, consulte [Adicionar permissões para aceder a APIs web](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Configuração de código da aplicação](scenario-spa-app-configuration.md)
