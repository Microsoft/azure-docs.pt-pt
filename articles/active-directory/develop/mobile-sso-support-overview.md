---
title: Suporte políticas únicas de segurança de assinatura e aplicações em aplicações móveis que desenvolva | Rio Azure
titleSuffix: Microsoft identity platform
description: Explicação e visão geral da construção de aplicações móveis que suportam políticas únicas de segurança de entrada e aplicações utilizando a plataforma de identidade da Microsoft e integrando-se com o Azure Ative Directory.
services: active-directory
author: knicholasa
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/14/2020
ms.author: nichola
ms.openlocfilehash: 4f0588667df6acb11a43e8c3469c67f65ed3cdd9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98165183"
---
# <a name="support-single-sign-on-and-app-protection-policies-in-mobile-apps-you-develop"></a>Apoie políticas únicas de segurança de assinatura e aplicações em aplicações móveis que desenvolver

O sign-on único (SSO) é uma oferta chave da plataforma de identidade da Microsoft e do Azure Ative Directory, fornecendo logins fáceis e seguros para os utilizadores da sua aplicação. Além disso, as políticas de proteção de aplicações (APP) permitem o suporte das principais políticas de segurança que mantêm os dados do utilizador seguros. Em conjunto, estas funcionalidades permitem logins seguros dos utilizadores e gestão dos dados da sua aplicação.

> [!VIDEO https://www.youtube.com/embed/JpeMeTjQJ04]

Este artigo explica porque é que o SSO e a APP são importantes e fornece orientações de alto nível para a construção de aplicações móveis que suportam estas funcionalidades. Isto aplica-se tanto para aplicações de telefone como para tablets. Se você é um administrador de TI que quer implementar SSO através do inquilino do Azure Ative Directory da sua organização, consulte a nossa [orientação para o planeamento de uma única implantação de sinal-on](../manage-apps/plan-sso-deployment.md)

## <a name="about-single-sign-on-and-app-protection-policies"></a>Sobre políticas únicas de s-on e proteção de aplicações

[O único sinal de ssto (SSO)](../manage-apps/plan-sso-deployment.md) permite que um utilizador faça o singso uma vez e obtenha acesso a outras aplicações sem reentrar em credenciais. Isto facilita o acesso às aplicações e elimina a necessidade de os utilizadores se lembrarem de longas listas de nomes de utilizadores e palavras-passe. Implementá-lo na sua aplicação facilita o acesso e utilização da sua app.

Além disso, ativar um único login na sua aplicação desbloqueia novos mecanismos de autenticação que vêm com a autenticação moderna, como [logins sem palavras-passe](../authentication/concept-authentication-passwordless.md). Os nomes de utilizador e as palavras-passe são um dos vetores de ataque mais populares contra aplicações, e permitir que o SSO permita mitigar este risco, aplicando acessos condicional ou logins sem palavras-passe que adicionam segurança adicional ou dependem de mecanismos de autenticação mais seguros. Por último, a ativação de um único sinal permite uma [única sação.](v2-protocols-oidc.md#single-sign-out) Isto é útil em situações como aplicações de trabalho que serão usadas em dispositivos partilhados.

[As políticas de proteção de aplicações (APP)](/mem/intune/apps/app-protection-policy) garantem que os dados de uma organização permanecem seguros e contidos. Permitem que as empresas gerem e protejam os seus dados dentro de uma app e permitem controlar quem pode aceder à app e aos seus dados. Implementar políticas de proteção de aplicações permite que a sua app conecte os utilizadores a recursos protegidos pelas políticas de Acesso Condicional e transfira de forma segura dados de e para outras aplicações protegidas. Os cenários desbloqueados pelas políticas de proteção de apps incluem exigir que um PIN abra uma app, controlar a partilha de dados entre apps e impedir que os dados das aplicações da empresa sejam guardados para locais de armazenamento pessoal.

## <a name="implementing-single-sign-on"></a>Implementação de um único sinal

Recomendamos o seguinte para permitir que a sua aplicação aproveite o único sign-on.

### <a name="use-the-microsoft-authentication-library-msal"></a>Utilize a Biblioteca de Autenticação microsoft (MSAL)

A melhor escolha para implementar um único sinal de sing na sua aplicação é utilizar [a Microsoft Authentication Library (MSAL)](msal-overview.md). Ao utilizar o MSAL, pode adicionar autenticação à sua aplicação com o código mínimo e chamadas API, obter todas as funcionalidades da [plataforma de identidade da Microsoft](./index.yml)– e deixar a Microsoft lidar com a manutenção de uma solução de autenticação segura. Por predefinição, a MSAL adiciona suporte SSO para a sua aplicação. Além disso, usar o MSAL é um requisito se também planeia implementar políticas de proteção de aplicações.

> [!NOTE]
> É possível configurar a MSAL para utilizar uma vista web incorporada. Isto evitará uma única inscrição. Utilize o comportamento padrão (isto é, o navegador web do sistema) para garantir que o SSO funcionará.

Se está atualmente a utilizar a [biblioteca ADAL](../azuread-dev/active-directory-authentication-libraries.md) na sua aplicação, recomendamos vivamente que [a emigreie para a MSAL,](msal-migration.md)uma vez que [a ADAL está a ser depreciada.](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363)

Para aplicações iOS, temos um [quickstart](quickstart-v2-ios.md) que mostra como configurar insuser usando MSAL, bem como [orientações para configurar o MSAL para vários cenários SSO](single-sign-on-macos-ios.md).

Para aplicações Android, temos um [quickstart](quickstart-v2-android.md) que mostra como configurar inserções usando MSAL, e orientações para [como ativar o SSO cross-app no Android usando o MSAL.](msal-android-single-sign-on.md)

### <a name="use-the-system-web-browser"></a>Use o navegador web do sistema

É necessário um navegador web para a autenticação interativa. Para aplicações móveis que utilizem bibliotecas de autenticação modernas que não sejam a MSAL (ou seja, outras bibliotecas OpenID Connect ou SAML), ou se implementar o seu próprio código de autenticação, deverá utilizar o navegador do sistema como superfície de autenticação para ativar o SSO.

A Google tem orientação para o fazer em Aplicações Android: [Chrome Custom Tabs - Google Chrome](https://developer.chrome.com/multidevice/android/customtabs).

A Apple tem orientação para o fazer em aplicações iOS: [Autenticar um utilizador através de um serviço web | Documentação do Desenvolvedor de Maçãs.](https://developer.apple.com/documentation/authenticationservices/authenticating_a_user_through_a_web_service)

> [!TIP]
> O [plug-in SSO para dispositivos Apple](apple-sso-plugin.md) permite sSO para aplicações iOS que usam visualizações web incorporadas em dispositivos geridos usando o Intune. Recomendamos o MSAL e o navegador do sistema como a melhor opção para o desenvolvimento de apps que permitem sSO para todos os utilizadores, mas isso irá permitir sSO em alguns cenários onde de outra forma não é possível.

## <a name="enable-app-protection-policies"></a>Ativar políticas de proteção de aplicações

Para ativar as políticas de proteção de aplicações, utilize a [Biblioteca de Autenticação da Microsoft (MSAL)](msal-overview.md). A MSAL é a biblioteca de autenticação e autorização da plataforma de identidade da Microsoft e o Intune SDK é desenvolvido para trabalhar em conjunto com ela.

Além disso, deve utilizar uma aplicação de corretor para autenticação. O corretor exige que a app forneça informações sobre aplicações e dispositivos para garantir o cumprimento da aplicação. Os utilizadores do iOS utilizarão a [aplicação Microsoft Authenticator](../user-help/user-help-auth-app-sign-in.md) e os utilizadores Android utilizarão a aplicação Microsoft Authenticator ou a [aplicação Portal da Empresa](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal) para [autenticação intermediada.](./msal-android-single-sign-on.md) Por predefinição, a MSAL utiliza um corretor como primeira escolha para cumprir um pedido de autenticação, pelo que a utilização do corretor para autenticar será ativada automaticamente para a sua aplicação quando utilizar o MSAL fora da caixa.

Por fim, [adicione o Intune SDK](/mem/intune/developer/app-sdk-get-started) à sua app para ativar políticas de proteção de aplicações. O SDK segue, na sua maioria, um modelo de interceção e aplicará automaticamente políticas de proteção de aplicações para determinar se as ações que a aplicação está a tomar são permitidas ou não. Existem também APIs que pode ligar manualmente para dizer à aplicação se existem restrições em determinadas ações.

## <a name="additional-resources"></a>Recursos adicionais

- [Planeie uma implantação única de sinalização do Azure Ative Directory](../manage-apps/plan-sso-deployment.md)
- [Como: Configurar SSO em macOS e iOS](single-sign-on-macos-ios.md)
- [Microsoft Enterprise SSO plug-in para dispositivos Apple (Preview)](apple-sso-plugin.md)
- [Autenticação intermediada no Android](./msal-android-single-sign-on.md)
- [Agentes de autorização e como habilitá-los](./msal-android-single-sign-on.md)
- [Introdução ao SDK da Aplicação do Microsoft Intune](/mem/intune/developer/app-sdk-get-started)
- [Configurar as definições para o SDK da Aplicação do Intune](/mem/intune/developer/app-sdk-ios#configure-settings-for-the-intune-app-sdk)
- [Aplicativos protegidos microsoft Intune](/mem/intune/apps/apps-supported-intune-apps)
