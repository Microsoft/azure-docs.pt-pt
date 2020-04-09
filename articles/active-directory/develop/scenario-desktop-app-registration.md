---
title: Registe aplicações de desktop que liguem para a web APIs - plataforma de identidade da Microsoft Azure
description: Saiba como construir uma aplicação de desktop que chama APIs web (registo de aplicações)
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
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885162"
---
# <a name="desktop-app-that-calls-web-apis-app-registration"></a>Aplicação de desktop que chama APIs web: Registo de aplicações

Este artigo cobre as especificidades de registo da aplicação para uma aplicação de ambiente de trabalho.

## <a name="supported-account-types"></a>Tipos de conta suportados

Os tipos de conta suportados numa aplicação de ambiente de trabalho dependem da experiência que pretende iluminar. Devido a esta relação, os tipos de conta suportados dependem dos fluxos que pretende utilizar.

### <a name="audience-for-interactive-token-acquisition"></a>Público para aquisição interativa de token

Se a sua aplicação de ambiente de trabalho utilizar a autenticação interativa, pode inscrever utilizadores a partir de qualquer tipo de [conta](quickstart-register-app.md#register-a-new-application-using-the-azure-portal).

### <a name="audience-for-desktop-app-silent-flows"></a>Público para desktop app fluxos silenciosos

- Para utilizar a Autenticação Integrada do Windows ou um nome de utilizador e uma palavra-passe, a sua aplicação precisa de iniciar sessão de utilizadores no seu próprio inquilino, por exemplo, caso seja um programador de linha de negócios (LOB). Ou, nas organizações azure Ative Directy, a sua aplicação precisa de contratar utilizadores no seu próprio inquilino se for um cenário ISV. Estes fluxos de autenticação não são suportados para contas pessoais da Microsoft.
- Se pretender utilizar o fluxo de código do dispositivo, ainda não é possível inscrever utilizadores com as suas contas pessoais da Microsoft.
- Se assinar em utilizadores com identidades sociais que passam uma autoridade e política de negócios-para-comércio (B2C), só pode utilizar a autenticação interativa e username-password.

## <a name="redirect-uris"></a>Redirecionamento de URIs

Os URIs redirecionados para utilização numa aplicação de ambiente de trabalho dependem do fluxo que pretende utilizar.

- Se utilizar a autenticação interativa `https://login.microsoftonline.com/common/oauth2/nativeclient`ou o fluxo de código do dispositivo, utilize . Para obter esta configuração, selecione o URL correspondente na secção **Autenticação** para a sua aplicação.
  
  > [!IMPORTANT]
  > Hoje em dia, MSAL.NET usa outro URI redirecionado`urn:ietf:wg:oauth:2.0:oob`por padrão em aplicações de ambiente de trabalho que funcionam no Windows ( ). No futuro, vamos querer alterar este padrão, por isso `https://login.microsoftonline.com/common/oauth2/nativeclient`recomendamos que use .

- Se construir uma aplicação nativa Object-C ou Swift para macOS, registe o URI redirecionamento com base no identificador de pacote da sua aplicação no seguinte formato: msauth.<your.app.bundle.id>://auth. Substitua <your.app.bundle.id> com o identificador de pacote da sua aplicação.
- Se a sua aplicação utilizar apenas a Autenticação Integrada do Windows ou um nome de utilizador e uma palavra-passe, não precisa de registar um URI redirecionado para a sua aplicação. Estes fluxos fazem uma viagem de ida e volta à plataforma de identidade da Microsoft v2.0 endpoint. A sua candidatura não será chamada de volta a nenhum URI específico.
- Para distinguir o fluxo de código do dispositivo, a Autenticação Integrada do Windows e um nome de utilizador e uma palavra-passe de um fluxo de aplicação de cliente confidencial que também não tem URIs redirecionados (o fluxo credencial do cliente utilizado nas aplicações daemon), precisa de expressar que a sua aplicação é uma aplicação de cliente público. Para obter esta configuração, vá à secção **de Autenticação** para a sua aplicação. Na subsecção **de configurações Avançadas,** no parágrafo **do tipo de cliente Predefinido,** selecione **Sim** para **Tratar aplicação como cliente público**.

  ![Permitir cliente público](media/scenarios/default-client-type.png)

## <a name="api-permissions"></a>Permissões de API

As aplicações de ambiente de trabalho chamam APIs para o utilizador inscrito. Precisam de pedir permissões delegadas. Não podem solicitar permissões de pedido, que são tratadas apenas em [aplicações daemon](scenario-daemon-overview.md).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Aplicativo de ambiente de trabalho: Configuração de aplicativo](scenario-desktop-app-configuration.md)
