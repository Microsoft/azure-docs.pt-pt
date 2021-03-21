---
title: Plug-in de SSO do Microsoft Enterprise para dispositivos Apple
titleSuffix: Microsoft identity platform | Azure
description: Saiba mais sobre o plug-in SSO do Azure Ative Directory para dispositivos iOS, iPadOS e macOS.
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
ms.openlocfilehash: 24a538686e101d40daba008f30a72ffc5078047a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104674536"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>Microsoft Enterprise SSO plug-in para dispositivos Apple (pré-visualização)

>[!IMPORTANT]
> Esta característica [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

O *plug-in Microsoft Enterprise SSO para dispositivos Apple* fornece um único sign-on (SSO) para contas Azure Ative Directory (Azure AD) em macOS, iOS e iPadOS em todas as aplicações que suportam a funcionalidade [única de sign-on da](https://developer.apple.com/documentation/authenticationservices) Apple. O plug-in fornece SSO para aplicações antigas de que o seu negócio pode depender, mas que ainda não suporta as mais recentes bibliotecas de identidade ou protocolos. A Microsoft trabalhou em estreita colaboração com a Apple para desenvolver este plug-in para aumentar a usabilidade da sua aplicação, ao mesmo tempo que forneceu a melhor proteção disponível.

O plug-in Enterprise SSO é atualmente uma característica incorporada das seguintes aplicações:

* [Microsoft Authenticator](../user-help/user-help-auth-app-overview.md): iOS, iPadOS
* Microsoft Intune [Company Portal](/mem/intune/apps/apps-company-portal-macos): macOS

## <a name="features"></a>Funcionalidades

O plug-in Microsoft Enterprise SSO para dispositivos Apple oferece os seguintes benefícios:

- Fornece contas SSO para Azure AD em todas as aplicações que suportam a funcionalidade SSO da Apple Enterprise.
- Pode ser ativado por qualquer solução de gestão de dispositivos móveis (MDM).
- Estende o SSO a aplicações que ainda não utilizam bibliotecas da plataforma de identidade da Microsoft.
- Estende sSO a aplicações que utilizam OAuth 2, OpenID Connect e SAML.

## <a name="requirements"></a>Requisitos

Para utilizar o plug-in Microsoft Enterprise SSO para dispositivos Apple:

- O dispositivo deve *suportar* e ter uma aplicação instalada que tenha o plug-in Microsoft Enterprise SSO para dispositivos Apple:
  - iOS 13.0 e mais tarde: [App Microsoft Authenticator](../user-help/user-help-auth-app-overview.md)
  - iPadOS 13.0 e mais tarde: [Microsoft Authenticator app](../user-help/user-help-auth-app-overview.md)
  - macOS 10.15 e mais tarde: [Intune Company Portal app](/mem/intune/user-help/enroll-your-device-in-intune-macos-cp)
- O dispositivo deve ser *matriculado em MDM*, por exemplo, através do Microsoft Intune.
- A configuração deve ser *empurrada para o dispositivo* para ativar o plug-in Enterprise SSO. A Apple requer esta restrição de segurança.

Requisitos do iOS:
- O iOS 13.0 ou posterior deve ser instalado no aparelho.
- Uma aplicação da Microsoft que forneça o plug-in Microsoft Enterprise SSO para dispositivos Apple deve ser instalada no dispositivo. Durante a pré-visualização pública, esta aplicação é a [aplicação Microsoft Authenticator](/intune/user-help/user-help-auth-app-overview.md).


requisitos do macOS:
- O macOS 10.15 ou posterior deve ser instalado no aparelho. 
- Uma aplicação da Microsoft que forneça o plug-in Microsoft Enterprise SSO para dispositivos Apple deve ser instalada no dispositivo. Durante a pré-visualização pública, esta aplicação é a [aplicação Intune Company Portal](/intune/user-help/enroll-your-device-in-intune-macos-cp.md).

## <a name="enable-the-sso-plug-in"></a>Ativar o plug-in SSO

Utilize as seguintes informações para ativar o plug-in SSO utilizando o MDM.
### <a name="microsoft-intune-configuration"></a>Configuração Microsoft Intune

Se utilizar o Microsoft Intune como o seu serviço MDM, pode utilizar definições de perfis de configuração incorporadas para ativar o plug-in Microsoft Enterprise SSO:

1. Configure as definições de extensão da [aplicação SSO](/mem/intune/configuration/device-features-configure#single-sign-on-app-extension) de um perfil de configuração. 
1. Se o perfil ainda não estiver atribuído, [atribua o perfil a um utilizador ou grupo de dispositivos](/mem/intune/configuration/device-profile-assign).

As definições de perfil que permitem o plug-in SSO são automaticamente aplicadas aos dispositivos do grupo da próxima vez que cada dispositivo iniciar a verificação com o Intune.

### <a name="manual-configuration-for-other-mdm-services"></a>Configuração manual para outros serviços de MDM

Se não utilizar o Intune para MDM, utilize os seguintes parâmetros para configurar o plug-in Microsoft Enterprise SSO para dispositivos Apple.

Definições do iOS:

- **ID de extensão:**`com.microsoft.azureauthenticator.ssoextension`
- **Team ID**: Este campo não é necessário para iOS.

Definições de macOS:

- **ID de extensão:**`com.microsoft.CompanyPortalMac.ssoextension`
- **ID da equipa:**`UBF8T346G9`

Configurações comuns:

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
  
### <a name="more-configuration-options"></a>Mais opções de configuração
Pode adicionar mais opções de configuração para estender a funcionalidade SSO a outras aplicações.

#### <a name="enable-sso-for-apps-that-dont-use-a-microsoft-identity-platform-library"></a>Ativar SSO para apps que não utilizem uma biblioteca de plataforma de identidade da Microsoft

O plug-in SSO permite que qualquer aplicação participe no SSO mesmo que não tenha sido desenvolvida utilizando um Microsoft SDK como a Microsoft Authentication Library (MSAL).

O plug-in SSO é instalado automaticamente por dispositivos que têm:
* Descarregou a aplicação Authenticator no iOS ou iPadOS, ou descarregou a aplicação Do Portal da Empresa Intune no macOS.
* Registou o dispositivo deles com a sua organização. 

A sua organização provavelmente utiliza a aplicação Authenticator para cenários como autenticação multifactor (MFA), autenticação sem palavras-passe e acesso condicional. Ao utilizar um fornecedor DEDM, pode ligar o plug-in SSO para as suas aplicações. A Microsoft facilitou a configuração do plug-in dentro do Microsoft Endpoint Manager em Intune. Um allowlist é utilizado para configurar estas aplicações para utilizar o plug-in SSO.

>[!IMPORTANT]
> O plug-in Microsoft Enterprise SSO suporta apenas aplicações que utilizam tecnologias de rede nativas da Apple ou webviews. Não suporta aplicações que enviam a sua própria implementação da camada de rede.  

Utilize os seguintes parâmetros para configurar o plug-in Microsoft Enterprise SSO para aplicações que não utilizem uma biblioteca de plataforma de identidade da Microsoft.

Para fornecer uma lista de aplicações específicas, utilize estes parâmetros:

- **Chave:**`AppAllowList`
- **Tipo:**`String`
- **Valor**: Lista delimitada por compósitos de aplicações para as aplicações que podem participar no SSO.
- **Exemplo:**`com.contoso.workapp, com.contoso.travelapp`

Para fornecer uma lista de prefixos, utilize estes parâmetros:
- **Chave:**`AppPrefixAllowList`
- **Tipo:**`String`
- **Valor**: Lista delimitada por compósitos de aplicação prefixos para as aplicações que são autorizadas a participar em SSO. Este parâmetro permite que todas as aplicações que começam com um prefixo particular participem no SSO.
- **Exemplo:**`com.contoso., com.fabrikam.`

[As aplicações consentidas](./application-consent-experience.md) que o administrador do MDM permite participar no SSO podem obter silenciosamente um símbolo para o utilizador final. Por isso, adicione apenas aplicações fidedignas à lista de admissões. 

>[!NOTE]
> Não precisa de adicionar aplicações que utilizem MSAL ou ASWebAuthenticationSession à lista de aplicações que podem participar no SSO. Estas aplicações são ativadas por padrão. 

##### <a name="find-app-bundle-identifiers-on-ios-devices"></a>Encontre identificadores de pacotes de aplicativos em dispositivos iOS

A Apple não fornece uma maneira fácil de obter iDs de pacote da App Store. A maneira mais fácil de obter os IDs de pacote das aplicações que você quer usar para SSO é perguntar ao seu fornecedor ou desenvolvedor de aplicações. Se essa opção não estiver disponível, pode utilizar a sua configuração MDM para encontrar os IDs do pacote: 

1. Ativar temporariamente a seguinte bandeira na sua configuração MDM:

    - **Chave:**`admin_debug_mode_enabled`
    - **Tipo:**`Integer`
    - **Valor:** 1 ou 0
1. Quando esta bandeira estiver acesa, inscreva-se nas aplicações do iOS no dispositivo para o qual pretende conhecer o iD do pacote. 
1. Na aplicação Authenticator, selecione **Ajuda**  >  **Enviar registos**  >  **Ver registos**. 
1. No ficheiro de registo, procure a seguinte linha: `[ADMIN MODE] SSO extension has captured following app bundle identifiers` . Esta linha deve capturar todos os IDs do pacote de aplicações que são visíveis para a extensão SSO. 

Utilize os IDs do pacote para configurar o SSO para as aplicações. 

#### <a name="allow-users-to-sign-in-from-unknown-applications-and-the-safari-browser"></a>Permitir que os utilizadores entrem em aplicações desconhecidas e no navegador Safari

Por predefinição, o plug-in Microsoft Enterprise SSO fornece SSO para aplicações autorizadas apenas quando um utilizador tenha assinado a partir de uma aplicação que utiliza uma biblioteca de plataformas de identidade microsoft como a MSAL ou a Azure Ative Directory Authentication Library (ADAL). O plug-in Microsoft Enterprise SSO também pode adquirir uma credencial partilhada quando é chamada por outra aplicação que utiliza uma biblioteca de plataformas de identidade da Microsoft durante uma nova aquisição.

Quando ativar a `browser_sso_interaction_enabled` bandeira, as aplicações que não usam uma biblioteca de plataformas de identidade da Microsoft podem fazer a primeira sapatilha e obter uma credencial partilhada. O navegador Safari também pode fazer a primeira sapatilha e obter uma credencial partilhada. 

Se o plug-in microsoft Enterprise SSO ainda não tiver uma credencial partilhada, tentará obter uma sempre que um teste de sessão for solicitado a partir de um URL AD Azure dentro do navegador Safari, ASWebAuthenticationSession, SafariViewController ou outra aplicação nativa permitida. 

Utilize estes parâmetros para permitir a bandeira: 

- **Chave:**`browser_sso_interaction_enabled`
- **Tipo:**`Integer`
- **Valor:** 1 ou 0

O macOS requer esta definição para que possa proporcionar uma experiência consistente em todas as aplicações. O iOS e o iPadOS não requerem esta definição porque a maioria das aplicações utiliza a aplicação Authenticator para iniciar sposição. Mas recomendamos que ative esta definição porque se algumas das suas aplicações não utilizarem a aplicação Authenticator no iOS ou iPadOS, esta bandeira melhorará a experiência. A definição é desativada por predefinição.

#### <a name="disable-asking-for-mfa-during-initial-bootstrapping"></a>Desativar pedir MFA durante a primeira sapatilha

Por predefinição, o plug-in Microsoft Enterprise SSO solicita sempre ao utilizador para MFA durante a fase inicial de arranque e, ao mesmo tempo, obter uma credencial partilhada. O utilizador é solicitado para MFA mesmo que não seja necessário para a aplicação que o utilizador abriu. Este comportamento permite que a credencial partilhada seja facilmente utilizada em todas as outras aplicações sem a necessidade de solicitar ao utilizador se o MFA for necessário mais tarde. Como o utilizador recebe menos solicitações em geral, esta configuração é geralmente uma boa decisão.

Ativar `browser_sso_disable_mfa` desliga mfa durante a primeira armadilhagem de botas e ao obter a credencial partilhada. Neste caso, o utilizador só é solicitado quando o MFA é exigido por uma aplicação ou recurso. 

Para ativar a bandeira, utilize estes parâmetros:

- **Chave:**`browser_sso_disable_mfa`
- **Tipo:**`Integer`
- **Valor:** 1 ou 0

Recomendamos manter esta bandeira desativada porque reduz o número de vezes que o utilizador é solicitado a iniciar sing. Se a sua organização raramente usa MFA, talvez queira ativar a bandeira. Mas recomendamos que utilize mFA com mais frequência. Por esta razão, a bandeira é desativada por defeito.

#### <a name="disable-oauth-2-application-prompts"></a>Desativar pedidos de aplicação OAuth 2

O plug-in Microsoft Enterprise SSO fornece SSO através da anexação de credenciais partilhadas a pedidos de rede que vêm de aplicações permitidas. No entanto, algumas aplicações OAuth 2 podem aplicar incorretamente as solicitações do utilizador final na camada de protocolo. Se vir este problema, também verá que as credenciais partilhadas são ignoradas para essas aplicações. O utilizador é solicitado a iniciar seduções mesmo que o plug-in Microsoft Enterprise SSO funcione para outras aplicações.  

Permitir a `disable_explicit_app_prompt` bandeira restringe a capacidade de aplicações nativas e aplicações web para forçar um pedido de utilizador final na camada de protocolo e contornar sSO. Para ativar a bandeira, utilize estes parâmetros:

- **Chave:**`disable_explicit_app_prompt`
- **Tipo:**`Integer`
- **Valor:** 1 ou 0

Recomendamos que esta bandeira obtenha uma experiência consistente em todas as aplicações. É desativado por defeito. 

#### <a name="enable-sso-through-cookies-for-a-specific-application"></a>Ativar sSO através de cookies para uma aplicação específica

Algumas aplicações podem ser incompatíveis com a extensão SSO. Especificamente, as aplicações que têm configurações avançadas de rede podem experimentar problemas inesperados quando estão ativadas para SSO. Por exemplo, pode ver um erro que indique que o pedido de rede foi cancelado ou interrompido. 

Se tiver problemas de sessão através do método descrito nas Aplicações que não utilizam a secção [MSAL,](#applications-that-dont-use-msal) experimente uma configuração alternativa. Utilize estes parâmetros para configurar o plug-in:

- **Chave:**`AppCookieSSOAllowList`
- **Tipo:**`String`
- **Valor**: Lista delimitada por compósitos de aplicação prefixos para as aplicações que são autorizadas a participar no SSO. Todas as aplicações que começarem com os prefixos listados serão autorizadas a participar no SSO.
- **Exemplo:**`com.contoso.myapp1, com.fabrikam.myapp2`

As aplicações ativadas para o SSO utilizando esta configuração devem ser adicionadas a ambos `AppCookieSSOAllowList` e `AppPrefixAllowList` .

Experimente esta configuração apenas para aplicações que tenham falhas inesperadas de entrada. 

#### <a name="use-intune-for-simplified-configuration"></a>Use Intune para configuração simplificada

Pode utilizar o Intune como serviço MDM para facilitar a configuração do plug-in Microsoft Enterprise SSO. Por exemplo, pode utilizar o Intune para ativar o plug-in e adicionar aplicações antigas a uma lista de admissões para que obtenham SSO. 

Para obter mais informações, consulte a documentação de [configuração intune](/intune/configuration/ios-device-features-settings).

## <a name="use-the-sso-plug-in-in-your-application"></a>Utilize o plug-in SSO da sua aplicação

[MSAL para as versões](https://github.com/AzureAD/microsoft-authentication-library-for-objc) 1.1.0 dos dispositivos da Apple e mais tarde suporta o plug-in Microsoft Enterprise SSO para dispositivos Apple. É a forma recomendada de adicionar suporte para o plug-in Microsoft Enterprise SSO. Garante que obtém todas as capacidades da plataforma de identidade da Microsoft.

Se estiver a construir uma aplicação para cenários de trabalho na linha da frente, consulte [o modo de dispositivo partilhado para dispositivos iOS](msal-ios-shared-devices.md) para obter informações de configuração.

## <a name="understand-how-the-sso-plug-in-works"></a>Entenda como funciona o plug-in SSO

O plug-in Microsoft Enterprise SSO baseia-se no [quadro SSO da Apple Enterprise](https://developer.apple.com/documentation/authenticationservices/asauthorizationsinglesignonprovider?language=objc). Os fornecedores de identidade que aderirem ao quadro podem intercetar o tráfego da rede para os seus domínios e melhorar ou alterar a forma como esses pedidos são tratados. Por exemplo, o plug-in SSO pode mostrar mais UIs para recolher credenciais de utilizador final de forma segura, exigir MFA ou fornecer silenciosamente fichas à aplicação.

As aplicações nativas também podem implementar operações personalizadas e comunicar diretamente com o plug-in SSO. Para mais informações, consulte este [vídeo da World Developer Conference 2019 da Apple.](https://developer.apple.com/videos/play/tech-talks/301/)

### <a name="applications-that-use-msal"></a>Aplicações que utilizam o MSAL

[MSAL para as versões](https://github.com/AzureAD/microsoft-authentication-library-for-objc) 1.1.0 dos dispositivos da Apple e, mais tarde, suporta o plug-in Microsoft Enterprise SSO para dispositivos apple de forma nativa para contas de trabalho e escola. 

Não precisa de nenhuma configuração especial se seguiu [todos os passos recomendados](./quickstart-v2-ios.md) e utilizou o [formato URI de redirecionamento](./redirect-uris-ios.md)padrão padrão . Nos dispositivos que têm o plug-in SSO, a MSAL invoca-o automaticamente para todos os pedidos de token interativo e silencioso. Invoca-o igualmente para as operações de enumeração de contas e de remoção de contas. Uma vez que a MSAL implementa um protocolo plug-in SSO nativo que se baseia em operações personalizadas, esta configuração proporciona a experiência nativa mais suave ao utilizador final. 

Se o plug-in SSO não for ativado pelo MDM, mas a aplicação Microsoft Authenticator estiver presente no dispositivo, a MSAL utiliza a aplicação Authenticator para quaisquer pedidos de token interativos. O plug-in SSO partilha SSO com a aplicação Authenticator.

### <a name="applications-that-dont-use-msal"></a>Aplicações que não usam MSAL

As aplicações que não utilizem uma biblioteca de plataformas de identidade da Microsoft, como a MSAL, ainda podem obter SSO se um administrador adicionar estas aplicações à lista de admissões. 

Não é necessário alterar o código nessas aplicações, desde que estejam preenchidas as seguintes condições:

- A aplicação utiliza quadros da Apple para executar pedidos de rede. Estes quadros incluem [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview) e [NSURLSession,](https://developer.apple.com/documentation/foundation/nsurlsession)por exemplo. 
- A aplicação utiliza protocolos padrão para comunicar com a Azure AD. Estes protocolos incluem, por exemplo, OAuth 2, SAML e WS-Federation.
- A aplicação não recolhe nomes de utilizadores de texto simples e palavras-passe na UI nativa.

Neste caso, o SSO é fornecido quando a aplicação cria um pedido de rede e abre um navegador web para iniciar a inscrição do utilizador. Quando um utilizador é redirecionado para um URL de entrada AD Azure, o plug-in SSO valida o URL e verifica uma credencial SSO para esse URL. Se encontrar a credencial, o plug-in SSO passa-o para a Azure AD, que autoriza a aplicação a completar o pedido de rede sem pedir ao utilizador que introduza credenciais. Além disso, se o dispositivo for conhecido do Azure AD, o plug-in SSO passa o certificado do dispositivo para satisfazer a verificação de acesso condicional baseada no dispositivo. 

Para suportar SSO para aplicações não MSAL, o plug-in SSO implementa um protocolo semelhante ao plug-in do navegador Windows descrito no [Que é um token de atualização primária?](../devices/concept-primary-refresh-token.md#browser-sso-using-prt) 

Em comparação com as aplicações baseadas no MSAL, o plug-in SSO atua de forma mais transparente para aplicações não MSAL. Integra-se com a experiência de inscrição do navegador existente que as aplicações fornecem. 

O utilizador final vê a experiência familiar e não tem de voltar a inscrever-se em cada aplicação. Por exemplo, em vez de exibir o selecionador de contas nativo, o plug-in SSO adiciona sessões SSO à experiência de picker de conta baseada na web. 

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [o modo de dispositivo partilhado para dispositivos iOS](msal-ios-shared-devices.md).
