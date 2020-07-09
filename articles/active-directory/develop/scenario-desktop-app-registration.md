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
ms.openlocfilehash: 599603ba867e21694392e38e9692280f010e08eb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80885162"
---
# <a name="desktop-app-that-calls-web-apis-app-registration"></a>Aplicativo de desktop que chama APIs web: Registo de aplicações

Este artigo cobre as especificidades do registo da aplicação para uma aplicação de ambiente de trabalho.

## <a name="supported-account-types"></a>Tipos de conta suportados

Os tipos de conta suportados numa aplicação de ambiente de trabalho dependem da experiência que pretende iluminar. Por causa desta relação, os tipos de conta suportados dependem dos fluxos que pretende utilizar.

### <a name="audience-for-interactive-token-acquisition"></a>Público para aquisição de fichas interativas

Se a sua aplicação para desktop utilizar a autenticação interativa, pode iniciar sação nos utilizadores a partir de qualquer tipo de [conta](quickstart-register-app.md#register-a-new-application-using-the-azure-portal).

### <a name="audience-for-desktop-app-silent-flows"></a>Público para aplicativo de ambiente de trabalho fluxos silenciosos

- Para utilizar a Autenticação Integrada do Windows ou um nome de utilizador e uma palavra-passe, a sua aplicação precisa de iniciar sação nos utilizadores no seu próprio inquilino, por exemplo, se for um programador de linha de negócios (LOB). Ou, nas organizações do Azure Ative Directory, a sua aplicação precisa de assinar nos utilizadores no seu próprio inquilino se for um cenário ISV. Estes fluxos de autenticação não são suportados para contas pessoais da Microsoft.
- Se pretender utilizar o fluxo de código do dispositivo, ainda não pode iniciar sôms nas suas contas pessoais da Microsoft.
- Se assinar em utilizadores com identidades sociais que passam por uma autoridade e política de negócios para comércio (B2C), só pode utilizar a autenticação interativa e de nome de utilizador-password.

## <a name="redirect-uris"></a>Redirecionar URIs

Os URIs de redirecionamento para utilizar numa aplicação de ambiente de trabalho dependem do fluxo que pretende utilizar.

- Se utilizar a autenticação interativa ou o fluxo de código do dispositivo, utilize `https://login.microsoftonline.com/common/oauth2/nativeclient` - Para obter esta configuração, selecione o URL correspondente na secção **autenticação** para a sua aplicação.
  
  > [!IMPORTANT]
  > Hoje em dia, MSAL.NET usa outro URI de redirecionamento por padrão em aplicações de desktop que funcionam no Windows `urn:ietf:wg:oauth:2.0:oob` (). No futuro, vamos querer alterar este padrão, por isso recomendamos que `https://login.microsoftonline.com/common/oauth2/nativeclient` utilize.

- Se construir uma aplicação nativa Objective-C ou Swift para macOS, registe o redirect URI com base no identificador do pacote da sua aplicação no seguinte formato: msauth.<your.app.bundle.id>://auth. Substitua <your.app.bundle.id> pelo identificador do pacote da sua aplicação.
- Se a sua aplicação utilizar apenas a Autenticação Integrada do Windows ou um nome de utilizador e uma palavra-passe, não precisa de registar um URI de redirecionamento para a sua aplicação. Estes fluxos fazem uma ida e volta para a plataforma de identidade da Microsoft v2.0. A sua candidatura não será chamada de volta em nenhum URI específico.
- Para distinguir o fluxo de código do dispositivo, a Autenticação Integrada do Windows e um nome de utilizador e uma palavra-passe de um fluxo de aplicação confidencial do cliente que também não tenha URIs redirecionado (o fluxo de credencial do cliente utilizado nas aplicações daemon), é necessário expressar que a sua aplicação é uma aplicação de cliente público. Para obter esta configuração, aceda à secção **de Autenticação** para a sua aplicação. Na subsecção **de definições Avançadas,** no parágrafo padrão do **tipo cliente,** selecione **Sim** para Tratar **a aplicação como cliente público**.

  ![Permitir cliente público](media/scenarios/default-client-type.png)

## <a name="api-permissions"></a>Permissões de API

As aplicações para desktop chamam APIs para o utilizador inscrito. Precisam pedir permissões delegadas. Não podem solicitar permissões de pedidos, que são tratadas apenas em [aplicações daemon](scenario-daemon-overview.md).

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Aplicativo de ambiente de trabalho: Configuração de aplicativo](scenario-desktop-app-configuration.md)
