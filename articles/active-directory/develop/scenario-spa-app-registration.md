---
title: Aplicação de página única (registo da aplicação) - plataforma de identidade da Microsoft
description: Saiba como criar uma aplicação de página única (registo da aplicação)
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: b1faf4669dca2aaaf3f873e66f859473ccd99f10
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074834"
---
# <a name="single-page-application---app-registration"></a>Aplicação de página única - registo de aplicações

Esta página explica as especificações de registo de aplicação para uma aplicação de página única (SPA).

Siga os passos para [registar uma nova aplicação com a plataforma de identidade do Microsoft](quickstart-register-app.md)e selecione as contas de suporte para a sua aplicação. O cenário SPA pode suportar a autenticação com contas na sua organização ou de qualquer organização e de contas Microsoft pessoais.

Em seguida, saiba os aspectos específicos do registo de aplicação que se aplicam a aplicativos de única página.

## <a name="register-a-redirect-uri"></a>Registe-se de um URI de redirecionamento

O fluxo implícito envia os tokens num redirecionamento para o aplicativo de página única em execução num navegador da web. Portanto, é um requisito importante para registar um URI onde a sua aplicação pode receber os tokens de redirecionamento. Certifique-se que o redirecionamento que URI corresponde exatamente com o URI para a sua aplicação.

No [portal do Azure](https://go.microsoft.com/fwlink/?linkid=2083908), navegue para a aplicação registada, no **autenticação** página da aplicação, selecione o **Web** plataforma e introduza o valor do URI de redirecionamento da sua aplicação na **URI de redirecionamento** campo.

## <a name="enable-the-implicit-flow"></a>Ativar o fluxo implícito

No mesmo **autenticação** página, em **definições avançadas**, também tem de ativar o **concessão implícita**. Se seu aplicativo só está a efetuar o início de sessão de utilizadores e obter os tokens de ID, é suficiente permitir **tokens de ID** caixa de verificação.

Se o aplicativo também precisa de obter os tokens de acesso para chamar as APIs, certifique-se ativar a **tokens de acesso** também a caixa de verificação. Para obter mais informações, consulte [tokens de ID](./id-tokens.md) e [tokens de acesso](./access-tokens.md).

## <a name="api-permissions"></a>Permissões de API

Aplicativos de única página podem chamar APIs em nome do utilizador com sessão iniciada. Eles precisam solicitar permissões delegadas. Para obter detalhes, consulte [adicionar permissões para aceder a web APIs](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Configuração de código da aplicação](scenario-spa-app-configuration.md)
