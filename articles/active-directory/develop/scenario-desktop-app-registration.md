---
title: Registar aplicações de desktop que chamam APIs web - Plataforma de identidade da Microsoft Rio Azure
description: Saiba como construir uma aplicação de desktop que chame APIs web (registo de aplicações)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/09/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 787380dbf6f739d4b88f18a836da2146b06024c3
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94443148"
---
# <a name="desktop-app-that-calls-web-apis-app-registration"></a>Aplicativo de desktop que chama APIs web: Registo de aplicações

Este artigo cobre as especificidades do registo da aplicação para uma aplicação de ambiente de trabalho.

## <a name="supported-account-types"></a>Tipos de conta suportados

Os tipos de conta suportados numa aplicação de ambiente de trabalho dependem da experiência que pretende iluminar. Por causa desta relação, os tipos de conta suportados dependem dos fluxos que pretende utilizar.

### <a name="audience-for-interactive-token-acquisition"></a>Público para aquisição de fichas interativas

Se a sua aplicação para desktop utilizar a autenticação interativa, pode iniciar sação nos utilizadores a partir de qualquer tipo de [conta](quickstart-register-app.md).

### <a name="audience-for-desktop-app-silent-flows"></a>Público para aplicativo de ambiente de trabalho fluxos silenciosos

- Para utilizar a Autenticação Integrada do Windows ou um nome de utilizador e uma palavra-passe, a sua aplicação precisa de iniciar sação nos utilizadores no seu próprio inquilino, por exemplo, se for um programador de linha de negócios (LOB). Ou, nas organizações do Azure Ative Directory, a sua aplicação precisa de assinar nos utilizadores no seu próprio inquilino se for um cenário ISV. Estes fluxos de autenticação não são suportados para contas pessoais da Microsoft.
- Se assinar em utilizadores com identidades sociais que passam por uma autoridade e política de negócios para comércio (B2C), só pode utilizar a autenticação interativa e de nome de utilizador-password.

## <a name="redirect-uris"></a>Redirecionar URIs

Os URIs de redirecionamento para utilizar numa aplicação de ambiente de trabalho dependem do fluxo que pretende utilizar.

- Se utilizar a autenticação interativa ou o fluxo de código do dispositivo, utilize `https://login.microsoftonline.com/common/oauth2/nativeclient` - Para obter esta configuração, selecione o URL correspondente na secção **autenticação** para a sua aplicação.

  > [!IMPORTANT]
  > Hoje em dia, MSAL.NET usa outro URI de redirecionamento por padrão em aplicações de desktop que funcionam no Windows `urn:ietf:wg:oauth:2.0:oob` (). No futuro, vamos querer alterar este padrão, por isso recomendamos que `https://login.microsoftonline.com/common/oauth2/nativeclient` utilize.

- Se construir uma aplicação nativa Objective-C ou Swift para macOS, registe o URI de redirecionamento com base no identificador do pacote da sua aplicação no seguinte formato: `msauth.<your.app.bundle.id>://auth` . `<your.app.bundle.id>`Substitua-o pelo identificador do pacote da sua aplicação.
- Se a sua aplicação utilizar apenas a Autenticação Integrada do Windows ou um nome de utilizador e uma palavra-passe, não precisa de registar um URI de redirecionamento para a sua aplicação. Estes fluxos fazem uma ida e volta para a plataforma de identidade da Microsoft v2.0. A sua candidatura não será chamada de volta em nenhum URI específico.
- Para distinguir o [fluxo de código do dispositivo](scenario-desktop-acquire-token.md#device-code-flow), [Autenticação Integrada do Windows,](scenario-desktop-acquire-token.md#integrated-windows-authentication)nome de [utilizador e palavra-passe](scenario-desktop-acquire-token.md#username-and-password) de uma aplicação confidencial do cliente utilizando um fluxo de credencial do cliente utilizado em [aplicações daemon](scenario-daemon-overview.md), nenhum dos quais requer um URI de redirecionamento, é necessário configurá-lo como uma aplicação de cliente público. Para alcançar esta configuração:

    1. No [portal Azure](https://portal.azure.com), selecione a sua aplicação nas **inscrições da App** e, em seguida, selecione **Autenticação**.
    1. Em **definições avançadas,** o tipo de cliente  >  **padrão** Tratar a  >  **aplicação como cliente público** , selecione **Sim**.

        :::image type="content" source="media/scenarios/default-client-type.png" alt-text="Ativar a configuração do cliente público no painel de autenticação no portal Azure":::

## <a name="api-permissions"></a>Permissões de API

As aplicações para desktop chamam APIs para o utilizador inscrito. Precisam pedir permissões delegadas. Não podem solicitar permissões de pedidos, que são tratadas apenas em [aplicações daemon](scenario-daemon-overview.md).

## <a name="next-steps"></a>Passos seguintes

Passe para o próximo artigo neste cenário, [configuração do Código de Aplicação](scenario-desktop-app-configuration.md).
