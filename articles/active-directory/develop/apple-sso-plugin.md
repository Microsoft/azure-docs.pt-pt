---
title: Plug-in de SSO do Microsoft Enterprise para dispositivos Apple
titleSuffix: Microsoft identity platform | Azure
description: Saiba mais sobre o plug-in SSO do Azure Ative Directory da Microsoft para dispositivos iOS, iPadOS e macOS.
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/15/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 96fbf23128896f23beee70a6b3d32b4b87a454ea
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102427101"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>Microsoft Enterprise SSO plug-in para dispositivos Apple (Preview)

>[!IMPORTANT]
> Esta característica [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

O *plug-in Microsoft Enterprise SSO para dispositivos Apple* fornece um único sign-on (SSO) para contas Azure Ative Directory (Azure AD) em macOS, iOS e iPadOS em todas as aplicações que suportam a funcionalidade [Enterprise Single Sign-On](https://developer.apple.com/documentation/authenticationservices) da Apple. Isto inclui aplicações mais antigas de que o seu negócio pode depender, mas que ainda não suportam as mais recentes bibliotecas de identidade ou protocolos. A Microsoft trabalhou em estreita colaboração com a Apple para desenvolver este plug-in para aumentar a usabilidade da sua aplicação, ao mesmo tempo que forneceu a melhor proteção que a Apple e a Microsoft podem fornecer.

O plug-in Enterprise SSO está atualmente disponível como uma característica incorporada das seguintes aplicações:

* [Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) - iOS, iPadOS
* Microsoft Intune [Company Portal](/mem/intune/apps/apps-company-portal-macos) - macOS

## <a name="features"></a>Funcionalidades

O plug-in Microsoft Enterprise SSO para dispositivos Apple oferece os seguintes benefícios:

- Fornece contas SSO para Azure AD em todas as aplicações que suportam a funcionalidade Enterprise Single Sign-On da Apple.
- Pode ser ativada por qualquer solução de gestão de dispositivos móveis (MDM).
- Estende o SSO a aplicações que ainda não utilizam bibliotecas da plataforma de identidade da Microsoft.
- Estende sSO a aplicações que utilizam OAuth2, OpenID Connect e SAML.

## <a name="requirements"></a>Requisitos

Para utilizar o plug-in Microsoft Enterprise SSO para dispositivos Apple:

- O dispositivo deve **suportar** e ter uma aplicação que inclua o plug-in Microsoft Enterprise SSO para dispositivos apple **instalados:**
  - iOS 13.0+: [App Microsoft Authenticator](../user-help/user-help-auth-app-overview.md)
  - iPadOS 13.0+: [App Microsoft Authenticator](../user-help/user-help-auth-app-overview.md)
  - macOS 10.15+: [Aplicação Intune Company Portal](/mem/intune/user-help/enroll-your-device-in-intune-macos-cp)
- O dispositivo deve estar **matriculado no MDM** (por exemplo, com o Microsoft Intune).
- A configuração deve ser **empurrada para o dispositivo** para ativar o plug-in Enterprise SSO no dispositivo. Esta restrição de segurança é exigida pela Apple.

### <a name="ios-requirements"></a>Requisitos do iOS:
- O iOS 13.0 ou superior deve ser instalado no aparelho.
- Uma aplicação da Microsoft que forneça o plug-in Microsoft Enterprise SSO para dispositivos Apple deve ser instalada no dispositivo. Para visualização pública, estas aplicações são a [aplicação Microsoft Authenticator](/intune/user-help/user-help-auth-app-overview.md).


### <a name="macos-requirements"></a>requisitos do macOS:
- O macOS 10.15 ou superior deve ser instalado no aparelho. 
- Uma aplicação da Microsoft que forneça o plug-in Microsoft Enterprise SSO para dispositivos Apple deve ser instalada no dispositivo. Para visualização pública, estas aplicações incluem a [aplicação Portal da Empresa Intune.](/intune/user-help/enroll-your-device-in-intune-macos-cp.md)

## <a name="enable-the-sso-plug-in-with-mobile-device-management-mdm"></a>Ativar o plug-in SSO com gestão de dispositivos móveis (MDM)

### <a name="microsoft-intune-configuration"></a>Configuração Microsoft Intune

Se utilizar o Microsoft Intune como o seu serviço MDM, pode utilizar definições de perfis de configuração incorporadas para ativar o plug-in Microsoft Enterprise SSO.

Em primeiro lugar, configurar as definições [únicas de extensão de aplicação de assinatura](/mem/intune/configuration/device-features-configure#single-sign-on-app-extension) de um perfil de configuração e [atribuir o perfil a um utilizador ou grupo de dispositivos](/mem/intune/configuration/device-profile-assign) (se ainda não tiver sido atribuído).

As definições de perfil que permitem o plug-in SSO são automaticamente aplicadas aos dispositivos do grupo da próxima vez que cada dispositivo iniciar a verificação com o Intune.

### <a name="manual-configuration-for-other-mdm-services"></a>Configuração manual para outros serviços de MDM

Se não estiver a utilizar o Microsoft Intune para a gestão de dispositivos móveis, utilize os seguintes parâmetros para configurar o plug-in Microsoft Enterprise SSO para dispositivos Apple.

#### <a name="ios-settings"></a>Definições do iOS:

- **ID de extensão:**`com.microsoft.azureauthenticator.ssoextension`
- **Team ID**: (este campo não é necessário para iOS)

#### <a name="macos-settings"></a>Definições de macOS:

- **ID de extensão:**`com.microsoft.CompanyPortalMac.ssoextension`
- **ID da equipa:**`UBF8T346G9`

#### <a name="common-settings"></a>Configurações comuns:

- **Tipo**: Redirecionamento
  - `https://login.microsoftonline.com`
  - `https://login.microsoft.com`
  - `https://sts.windows.net`
  - `https://login.partner.microsoftonline.cn`
  - `https://login.chinacloudapi.cn`
  - `https://login.microsoftonline.de`
  - `https://login.microsoftonline.us`
  - `https://login.usgovcloudapi.net`
  - `https://login-us.microsoftonline.com`
  
### <a name="additional-configuration-options"></a>Opções de configuração adicionais
Opções de configuração adicionais podem ser adicionadas para estender a funcionalidade SSO a aplicações adicionais.

#### <a name="enable-sso-for-apps-that-dont-use-a-microsoft-identity-platform-library"></a>Ativar SSO para apps que não utilizem uma biblioteca de plataforma de identidade da Microsoft

O plug-in SSO permite que qualquer aplicação participe de um único sign-on mesmo que não tenha sido desenvolvido usando um Microsoft SDK como a Microsoft Authentication Library (MSAL).

O plug-in SSO é instalado automaticamente por dispositivos que descarregaram a aplicação Microsoft Authenticator no iOS e iPadOS ou na aplicação Intune Company Portal no macOS e registaram o seu dispositivo junto da sua organização. A sua organização provavelmente utiliza a app Authenticator hoje em dia para cenários como autenticação de vários fatores, autenticação sem palavra-passe e acesso condicional. Pode ser ligado para as suas aplicações utilizando qualquer fornecedor de MDM, embora a Microsoft tenha facilitado a configuração dentro do Microsoft Endpoint Manager do Intune. Uma lista de autorizações é usada para configurar estas aplicações para utilizar o plugin SSO.

>[!IMPORTANT]
> Apenas aplicações que usam tecnologias de rede nativas da Apple ou webviews são suportadas. Se uma aplicação envia a sua própria implementação da camada de rede, o plug-in Microsoft Enterprise SSO não é suportado.  

Utilize os seguintes parâmetros para configurar o plug-in Microsoft Enterprise SSO para aplicações que não utilizem uma biblioteca de plataforma de identidade da Microsoft:

Se quiser fornecer uma lista de aplicações específicas:

- **Chave:**`AppAllowList`
- **Tipo:**`String`
- **Valor**: Lista delimitada de IDs de pacotes de aplicações para as aplicações que podem participar no SSO
- **Exemplo:**`com.contoso.workapp, com.contoso.travelapp`

Ou se quiser fornecer uma lista de prefixos:
- **Chave:**`AppPrefixAllowList`
- **Tipo:**`String`
- **Valor**: Lista delimitada por compósitos de aplicação prefixos para as aplicações que são autorizadas a participar no SSO. Note que isto permitirá que todas as aplicações que começam com um prefixo particular participem no SSO
- **Exemplo:**`com.contoso., com.fabrikam.`

[As aplicações consentidas](./application-consent-experience.md) que são permitidas pelo administrador do MDM para participar no SSO podem obter silenciosamente um símbolo para o utilizador final. Por isso, é importante apenas adicionar aplicações fidedignas à lista de admissões. 

>[!NOTE]
> Não precisa de adicionar aplicações que utilizem a MSAL ou ASWebAuthenticationSession a esta lista. Estas aplicações são ativadas por padrão. 

##### <a name="how-to-discover-app-bundle-identifiers-on-ios-devices"></a>Como descobrir identificadores de pacotes de aplicações em dispositivos iOS

A Apple não fornece uma maneira fácil de descobrir iDs bundle da App Store. A maneira mais fácil de descobrir os IDs bundle das aplicações que querem usar para SSO é perguntar ao seu fornecedor ou desenvolvedor de aplicações. Se essa opção não estiver disponível, pode utilizar a sua configuração MDM para descobrir os IDs do Bundle. 

Ativar temporariamente a seguinte bandeira na sua configuração MDM:

- **Chave:**`admin_debug_mode_enabled`
- **Tipo:**`Integer`
- **Valor:** 1 ou 0

Quando esta bandeira estiver em início de saúde para aplicações iOS no dispositivo, pretende conhecer o Bundle ID. Em seguida, abra a aplicação Microsoft Authenticator -> Help -> Enviar registos -> Ver registos. 

No ficheiro de registo, procure a seguinte linha:

`[ADMIN MODE] SSO extension has captured following app bundle identifiers:`

Isto deve capturar todos os identificadores de pacotes de aplicações visíveis para a extensão SSO. Em seguida, pode utilizar esses identificadores para configurar o SSO para essas aplicações. 

#### <a name="allow-user-to-sign-in-from-unknown-applications-and-the-safari-browser"></a>Permita ao utilizador iniciar s presenciação a partir de aplicações desconhecidas e do navegador Safari.

Por predefinição, o plug-in Microsoft Enterprise SSO fornece SSO para aplicações autorizadas apenas quando um utilizador tenha assinado a partir de uma aplicação que utiliza uma biblioteca de plataforma de identidade microsoft como ADAL ou MSAL. O plug-in Microsoft Enterprise SSO também pode adquirir uma credencial partilhada quando é chamada por outra aplicação que utiliza uma biblioteca de plataformas de identidade da Microsoft durante uma nova aquisição.

Ativar `browser_sso_interaction_enabled` a bandeira permite que uma aplicação que não utilize uma biblioteca de plataforma de identidade da Microsoft para fazer a colocação inicial de botas e obter uma credencial partilhada. Também permite que o navegador Safari faça a primeira sapatilha e obtenha uma credencial partilhada. Se o plug-in microsoft Enterprise SSO ainda não tiver uma credencial partilhada, tentará obter uma sempre que um teste de sessão for solicitado a partir de um URL AD AD Azure dentro do navegador Safari, ASWebAuthenticationSession, SafariViewController ou outra aplicação nativa permitida.  

- **Chave:**`browser_sso_interaction_enabled`
- **Tipo:**`Integer`
- **Valor:** 1 ou 0

Para o macOS esta definição é necessária para obter uma experiência mais consistente em todas as aplicações. Para iOS e iPadOS esta definição não é necessária, uma vez que a maioria das aplicações utiliza a aplicação Microsoft Authenticator para iniciar sposição. No entanto, se tiver algumas aplicações que não utilizem o Autenticador microsoft no iOS ou iPadOS esta bandeira melhorará a experiência, pelo que recomendamos que ative a definição. Está desativado por predefinição.

#### <a name="disable-asking-for-mfa-on-initial-bootstrapping"></a>Desativar pedir MFA no bootstrapping inicial

Por predefinição, o plug-in Microsoft Enterprise SSO solicita sempre ao utilizador a autenticação multi-factor (MFA) quando faz a colocação inicial das botas e obter uma credencial partilhada, mesmo que não seja necessária para a aplicação atual que o utilizador lançou. Isto é para que a credencial partilhada possa ser facilmente usada em todas as aplicações adicionais sem avisar o utilizador se o MFA for necessário mais tarde. Isto reduz os tempos que o utilizador necessita de ser solicitado no dispositivo e é geralmente uma boa decisão.

Ativar `browser_sso_disable_mfa` o desligamento e só irá solicitar ao utilizador quando o MFA for exigido por uma aplicação ou recurso. 

- **Chave:**`browser_sso_disable_mfa`
- **Tipo:**`Integer`
- **Valor:** 1 ou 0

Recomendamos manter esta bandeira desativada, uma vez que reduz os tempos necessários para o utilizador ser solicitado no aparelho. Se a sua organização raramente usa MFA, pode querer ativar a bandeira, mas recomendamos que use MFA com mais frequência. Por esta razão, é desativado por defeito.

#### <a name="disable-oauth2-application-prompts"></a>Desativar pedidos OAuth2

O plug-in Microsoft Enterprise SSO fornece SSO através da anexação de credenciais partilhadas a pedidos de rede provenientes de aplicações permitidas. No entanto, algumas aplicações OAuth2 podem aplicar incorretamente as solicitações do utilizador final na camada de protocolo. Se isto acontecer, verá que as credenciais partilhadas são ignoradas para essas aplicações e o seu utilizador é solicitado a iniciar sedução, mesmo que o plug-in SSO da Microsoft Enterprise esteja a trabalhar para outras aplicações.  

Permitir a bandeira restringe a `disable_explicit_app_prompt` capacidade de aplicações nativas e web para forçar um pedido de utilizador final na camada de protocolo e contornar o SSO.

- **Chave:**`disable_explicit_app_prompt`
- **Tipo:**`Integer`
- **Valor:** 1 ou 0

Recomendamos que esta bandeira obtenha uma experiência mais consistente em todas as aplicações. Está desativado por predefinição. 

#### <a name="enable-sso-through-cookies-for-specific-application"></a>Ativar sSO através de cookies para aplicação específica

Um pequeno número de aplicações pode ser incompatível com a extensão SSO. Especificamente, as aplicações que têm configurações avançadas de rede podem experimentar problemas inesperados quando estão ativadas para o SSO (por exemplo, pode ver um erro que o pedido de rede foi cancelado ou interrompido). 

Se tiver problemas em iniciar sessão com o método descrito na `Enable SSO for apps that don't use MSAL` secção, poderá experimentar uma configuração alternativa para essas aplicações. 

Utilize os seguintes parâmetros para configurar o plug-in Microsoft Enterprise SSO para essas aplicações específicas:

- **Chave:**`AppCookieSSOAllowList`
- **Tipo:**`String`
- **Valor**: Lista delimitada por compósitos de aplicação prefixos para as aplicações que são autorizadas a participar no SSO. Note que isto permitirá que todas as aplicações que começam com um prefixo particular participem no SSO
- **Exemplo:**`com.contoso.myapp1, com.fabrikam.myapp2`

Note que as aplicações ativadas para o SSO utilizando este mecanismo devem ser adicionadas tanto ao `AppCookieSSOAllowList` e `AppPrefixAllowList` . .

Recomendamos experimentar esta opção apenas para aplicações que experimentem falhas inesperadas de entrada. 

#### <a name="use-intune-for-simplified-configuration"></a>Use Intune para configuração simplificada

Como indicado anteriormente, pode utilizar o Microsoft Intune como o seu serviço MDM para facilitar a configuração do plug-in Microsoft Enterprise SSO, incluindo ativar o plug-in e adicionar as suas aplicações mais antigas a uma lista de autorizações para que obtenham SSO. Para obter mais informações, consulte a documentação de [configuração intune](/intune/configuration/ios-device-features-settings).

## <a name="using-the-sso-plug-in-in-your-application"></a>Utilizando o plug-in SSO da sua aplicação

A [Microsoft Authentication Library (MSAL) para dispositivos Apple](https://github.com/AzureAD/microsoft-authentication-library-for-objc) versão 1.1.0 e mais alta suporta o plug-in Microsoft Enterprise SSO para dispositivos Apple. É a forma recomendada de adicionar suporte ao plug-in microsoft Enterprise SSO e garante que obtém todas as capacidades da plataforma de identidade da Microsoft.

Se estiver a construir uma aplicação para cenários de Frontline Worker, consulte [o modo de dispositivo partilhado para dispositivos iOS](msal-ios-shared-devices.md) para uma configuração adicional da funcionalidade.

## <a name="how-the-sso-plug-in-works"></a>Como funciona o plug-in SSO

O plug-in Microsoft Enterprise SSO baseia-se no [quadro de Sign-On único da Apple.](https://developer.apple.com/documentation/authenticationservices/asauthorizationsinglesignonprovider?language=objc) Os fornecedores de identidade que a bordo do quadro podem intercetar o tráfego da rede para os seus domínios e melhorar ou alterar a forma como esses pedidos são tratados. Por exemplo, o plug-in SSO pode mostrar UI adicional para recolher credenciais de utilizador final de forma segura, exigir MFA ou fornecer silenciosamente fichas à aplicação.

As aplicações nativas também podem implementar operações personalizadas e falar diretamente com o plug-in SSO.
Você pode aprender sobre o quadro de inscrição única neste [vídeo WWDC 2019 da Apple](https://developer.apple.com/videos/play/tech-talks/301/)

### <a name="applications-that-use-a-microsoft-identity-platform-library"></a>Aplicações que utilizam uma biblioteca de plataforma de identidade da Microsoft

A [Microsoft Authentication Library (MSAL) para dispositivos Apple](https://github.com/AzureAD/microsoft-authentication-library-for-objc) versão 1.1.0 e mais alta suporta o plug-in Microsoft Enterprise SSO para dispositivos Apple de forma nativa para contas de trabalho e escola. 

Não é necessária uma configuração especial se tiver seguido [todos os passos recomendados](./quickstart-v2-ios.md) e utilizar o [formato URI de redirecionamento](./redirect-uris-ios.md)padrão padrão . Ao executar um dispositivo que tenha o plug-in SSO presente, a MSAL irá automaticamente invocá-lo para todos os pedidos de token interativo e silencioso, bem como para a enumeração de contas e operações de remoção de contas. Uma vez que a MSAL implementa o protocolo plug-in SSO nativo que se baseia em operações personalizadas, esta configuração proporciona a experiência nativa mais suave ao utilizador final. 

Se o plug-in SSO não estiver ativado pelo MDM, mas a aplicação Microsoft Authenticator estiver presente no dispositivo, a MSAL utilizará a aplicação microsoft Authenticator para quaisquer pedidos de token interativos. O plug-in SSO partilha SSO com a aplicação Microsoft Authenticator.

### <a name="applications-that-dont-use-a-microsoft-identity-platform-library"></a>Aplicações que não usam uma biblioteca de plataforma de identidade da Microsoft

As aplicações que não utilizem uma biblioteca de plataformas de identidade da Microsoft como a MSAL ainda podem obter SSO se um administrador as adicionar explicitamente à lista de autorizações. 

Não são necessárias alterações de código nessas aplicações, desde que estejam preenchidas as seguintes condições:

- A aplicação está a usar quadros da Apple para executar pedidos de rede (por exemplo, [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview), [NSURLSession](https://developer.apple.com/documentation/foundation/nsurlsession)) 
- A aplicação está a usar protocolos padrão para comunicar com a Azure AD (por exemplo, OAuth2, SAML, WS-Federation)
- A aplicação não recolhe o nome de utilizador e a palavra-passe de texto simples na UI nativa

Neste caso, o SSO é fornecido quando a aplicação cria um pedido de rede e abre um navegador web para iniciar a inscrição do utilizador. Quando um utilizador é redirecionado para um URL de login Azure AD, o plug-in SSO valida o URL e verifica se existe uma credencial SSO disponível para esse URL. Se houver um, o plug-in SSO passa o credencial SSO para Azure AD, que autoriza a aplicação a completar o pedido de rede sem pedir ao utilizador que introduza as suas credenciais. Além disso, se o dispositivo for conhecido do Azure AD, o plug-in SSO também passará o certificado do dispositivo para satisfazer a verificação de acesso condicional baseada no dispositivo. 

Para suportar SSO para aplicações não MSAL, o plug-in SSO implementa um protocolo semelhante ao plug-in do navegador Windows descrito no [What is a Primary Refresh Token?](../devices/concept-primary-refresh-token.md#browser-sso-using-prt) 

Em comparação com as aplicações baseadas no MSAL, o plug-in SSO atua de forma mais transparente para aplicações não MSAL, integrando-se com a experiência de login do navegador existente que as aplicações fornecem. O utilizador final veria a sua experiência familiar, com o benefício de não ter de realizar insusimentos adicionais em cada uma das aplicações. Por exemplo, em vez de exibir o selecionador de contas nativo, o plug-in SSO adiciona sessões SSO à experiência de picker de conta baseada na web. 

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o modo de dispositivo partilhado no iOS, consulte [o modo de dispositivo partilhado para dispositivos iOS](msal-ios-shared-devices.md).
