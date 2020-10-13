---
title: Plug-in de SSO do Microsoft Enterprise para dispositivos Apple
titleSuffix: Microsoft identity platform | Azure
description: Saiba mais sobre o plug-in SSO do Azure Ative Directory da Microsoft para dispositivos iOS e macOS.
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
ms.openlocfilehash: ec0ab4601e15129ecd8917e0e750a3e1661dc558
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91530702"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>Microsoft Enterprise SSO plug-in para dispositivos Apple (Preview)

> [!NOTE]
> Esta funcionalidade está em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O *plug-in Microsoft Enterprise SSO para dispositivos Apple* fornece contas únicas de sign-on (SSO) para contas Azure Ative Directory (Azure AD) em todas as aplicações que suportam a funcionalidade [Enterprise Single Sign-On](https://developer.apple.com/documentation/authenticationservices) da Apple. A Microsoft trabalhou em estreita colaboração com a Apple para desenvolver este plug-in para aumentar a usabilidade da sua aplicação, ao mesmo tempo que forneceu a melhor proteção que a Apple e a Microsoft podem fornecer.

Nesta versão de Visualização Pública, o plug-in Enterprise SSO está disponível apenas para dispositivos iOS e é distribuído em certas aplicações da Microsoft.

## <a name="features"></a>Funcionalidades

O plug-in Microsoft Enterprise SSO para dispositivos Apple oferece os seguintes benefícios:

- Fornece contas SSO para Azure AD em todas as aplicações que suportam a funcionalidade Enterprise Single Sign-On da Apple.
- Entregue automaticamente no Microsoft Authenticator e pode ser ativado por qualquer solução de gestão de dispositivos móveis (MDM).

## <a name="requirements"></a>Requisitos

Para utilizar o plug-in Microsoft Enterprise SSO para dispositivos Apple:

- O iOS 13.0 ou superior deve ser instalado no aparelho.
- Uma aplicação da Microsoft que forneça o plug-in Microsoft Enterprise SSO para dispositivos Apple deve ser instalada no dispositivo. Para visualização pública, estas aplicações incluem a [aplicação Microsoft Authenticator](../user-help/user-help-auth-app-overview.md).
- O dispositivo deve estar matriculado no MDM (por exemplo, com o Microsoft Intune).
- A configuração deve ser empurrada para o dispositivo para permitir o plug-in Microsoft Enterprise SSO para dispositivos apple no dispositivo. Esta restrição de segurança é exigida pela Apple.

## <a name="enable-the-sso-plug-in-with-mobile-device-management-mdm"></a>Ativar o plug-in SSO com gestão de dispositivos móveis (MDM)

Para ativar o plug-in Microsoft Enterprise SSO para dispositivos Apple, os seus dispositivos precisam de ser enviados um sinal através de um serviço MDM. Uma vez que a Microsoft inclui o plug-in Enterprise SSO na [aplicação Microsoft Authenticator,](..//user-help/user-help-auth-app-overview.md)utilize o seu MDM para configurar a aplicação para ativar o plug-in Microsoft Enterprise SSO.

Utilize os seguintes parâmetros para configurar o plug-in Microsoft Enterprise SSO para dispositivos Apple:

- **Tipo**: Redirecionamento
- **ID de extensão:**`com.microsoft.azureauthenticator.ssoextension`
- **Team ID**: (este campo não é necessário para iOS)
- **URLs:**
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

#### <a name="enable-sso-for-apps-that-dont-use-msal"></a>Ativar SSO para apps que não utilizem o MSAL

O plug-in SSO permite que qualquer aplicação participe de um único sign-on mesmo que não tenha sido desenvolvido usando um Microsoft SDK como a Microsoft Authentication Library (MSAL).

O plug-in SSO é instalado automaticamente por dispositivos que descarregaram a aplicação Microsoft Authenticator e registaram o seu dispositivo junto da sua organização. A sua organização provavelmente utiliza a app Authenticator hoje em dia para cenários como autenticação de vários fatores, autenticação sem palavra-passe e acesso condicional. Pode ser ligado para as suas aplicações utilizando qualquer fornecedor de MDM, embora a Microsoft tenha facilitado a configuração dentro do Microsoft Endpoint Manager do Intune. Uma lista de permitis é utilizada para configurar estas aplicações para utilizar o plugin SSO instalado pela aplicação Authenticator.

Apenas aplicações que usam tecnologias de rede nativas da Apple ou webviews são suportadas. Se uma aplicação envia a sua própria implementação da camada de rede, o plug-in Microsoft Enterprise SSO não é suportado.  

Utilize os seguintes parâmetros para configurar o plug-in Microsoft Enterprise SSO para aplicações que não utilizem o MSAL:

- **Chave:**`AppAllowList`
- **Tipo:**`String`
- **Valor**: Lista delimitada de IDs de pacotes de aplicações para as aplicações que podem participar no SSO
- **Exemplo:**`com.contoso.workapp, com.contoso.travelapp`

[As aplicações consentidas](./application-consent-experience.md) que são permitidas pelo administrador do MDM para participar no SSO podem obter silenciosamente um símbolo para o utilizador final. Por isso, é importante apenas adicionar aplicações fidedignas à lista de admissões. 

Não precisa de adicionar aplicações que utilizem a MSAL ou ASWebAuthenticationSession a esta lista. Estas aplicações são ativadas por padrão. 

#### <a name="allow-creating-sso-session-from-any-application"></a>Permitir a criação de sessão SSO a partir de qualquer aplicação

Por predefinição, o plug-in Microsoft Enterprise SSO fornece SSO para aplicações autorizadas apenas quando o plug-in SSO já tem uma credencial partilhada. O plug-in Microsoft Enterprise SSO pode adquirir uma credencial partilhada quando é chamada por outra aplicação baseada em ADAL ou MSAL durante a aquisição de token. A maioria das aplicações da Microsoft usam o Microsoft Authenticator ou o SSO plug-in. Isto significa que, por padrão, o SSO fora dos fluxos de aplicações nativos é o melhor esforço.  

Ativar `browser_sso_interaction_enabled` a bandeira permite que aplicações não-MSAL e navegador Safari façam a primeira sapatilha e obtenham uma credencial partilhada. Se o plug-in microsoft Enterprise SSO ainda não tiver uma credencial partilhada, tentará obter uma sempre que um teste de sessão for solicitado a partir de um URL AD AD Azure dentro do navegador Safari, ASWebAuthenticationSession, SafariViewController ou outra aplicação nativa permitida.  

- **Chave:**`browser_sso_interaction_enabled`
- **Tipo:**`Integer`
- **Valor:** 1 ou 0

Recomendamos que esta bandeira obtenha uma experiência mais consistente em todas as aplicações. Está desativado por predefinição. 

#### <a name="disable-oauth2-application-prompts"></a>Desativar pedidos OAuth2

O plug-in Microsoft Enterprise SSO fornece SSO através da anexação de credenciais partilhadas a pedidos de rede provenientes de aplicações permitidas. Algumas aplicações OAuth2 podem estar a impor o pedido do utilizador final na camada do protocolo. A credencial partilhada seria ignorada para essas aplicações.  

Permitir a bandeira restringe a `disable_explicit_app_prompt` capacidade de aplicações nativas e web para forçar um pedido de utilizador final na camada de protocolo e contornar o SSO.

- **Chave:**`disable_explicit_app_prompt`
- **Tipo:**`Integer`
- **Valor:** 1 ou 0

Recomendamos que esta bandeira obtenha uma experiência mais consistente em todas as aplicações. Está desativado por predefinição. 

#### <a name="use-intune-for-simplified-configuration"></a>Use Intune para configuração simplificada

Pode utilizar o Microsoft Intune como o seu serviço MDM para facilitar a configuração do plug-in Microsoft Enterprise SSO. Para obter mais informações, consulte a documentação de [configuração intune](/intune/configuration/ios-device-features-settings).

## <a name="using-the-sso-plug-in-in-your-application"></a>Utilizando o plug-in SSO da sua aplicação

A [Microsoft Authentication Library (MSAL) para dispositivos Apple](https://github.com/AzureAD/microsoft-authentication-library-for-objc) versão 1.1.0 e mais alta suporta o plug-in Microsoft Enterprise SSO para dispositivos Apple.

Se estiver a construir uma aplicação para cenários de Frontline Worker, consulte [o modo de dispositivo partilhado para dispositivos iOS](msal-ios-shared-devices.md) para uma configuração adicional da funcionalidade.

## <a name="how-the-sso-plug-in-works"></a>Como funciona o plug-in SSO

O plug-in Microsoft Enterprise SSO baseia-se no [quadro de Sign-On único da Apple.](https://developer.apple.com/documentation/authenticationservices/asauthorizationsinglesignonprovider?language=objc) Os fornecedores de identidade que a bordo do quadro podem intercetar o tráfego da rede para os seus domínios e melhorar ou alterar a forma como esses pedidos são tratados. Por exemplo, o plug-in SSO pode mostrar UI adicional para recolher credenciais de utilizador final de forma segura, exigir MFA ou fornecer silenciosamente fichas à aplicação.

As aplicações nativas também podem implementar operações personalizadas e falar diretamente com o plug-in SSO.
Você pode aprender sobre o quadro de inscrição única neste [vídeo WWDC 2019 da Apple](https://developer.apple.com/videos/play/tech-talks/301/)

### <a name="applications-that-use-msal"></a>Aplicações que utilizam o MSAL

A [Microsoft Authentication Library (MSAL) para dispositivos Apple](https://github.com/AzureAD/microsoft-authentication-library-for-objc) versão 1.1.0 e mais alta suporta o plug-in Microsoft Enterprise SSO para dispositivos Apple de forma nativa para contas de trabalho e escola. 

Não é necessária uma configuração especial se tiver seguido [todos os passos recomendados](./quickstart-v2-ios.md) e utilizar o [formato URI de redirecionamento](./redirect-uris-ios.md)padrão padrão . Ao executar um dispositivo que tenha o plug-in SSO presente, a MSAL irá automaticamente invocá-lo para todos os pedidos de token interativo e silencioso, bem como para a enumeração de contas e operações de remoção de contas. Uma vez que a MSAL implementa o protocolo plug-in SSO nativo que se baseia em operações personalizadas, esta configuração proporciona a experiência nativa mais suave ao utilizador final. 

Se o plug-in SSO não estiver ativado pelo MDM, mas a aplicação Microsoft Authenticator estiver presente no dispositivo, a MSAL utilizará a aplicação microsoft Authenticator para quaisquer pedidos de token interativos. O plug-in SSO partilha SSO com a aplicação Microsoft Authenticator.

### <a name="applications-that-dont-use-msal"></a>Aplicações que não usam MSAL

As aplicações que não usam o MSAL ainda podem obter SSO se um administrador as adicionar explicitamente à lista de autorizações. 

Não são necessárias alterações de código nessas aplicações, desde que estejam preenchidas as seguintes condições:

- A aplicação está a usar quadros da Apple para executar pedidos de rede (por exemplo, [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview), [NSURLSession](https://developer.apple.com/documentation/foundation/nsurlsession)) 
- A aplicação está a usar protocolos padrão para comunicar com a Azure AD (por exemplo, OAuth2, SAML, WS-Federation)
- A aplicação não recolhe o nome de utilizador e a palavra-passe de texto simples na UI nativa

Neste caso, o SSO é fornecido quando a aplicação cria um pedido de rede e abre um navegador web para iniciar a inscrição do utilizador. Quando um utilizador é redirecionado para um URL de login Azure AD, o plug-in SSO valida o URL e verifica se existe uma credencial SSO disponível para esse URL. Se houver um, o plug-in SSO passa o credencial SSO para Azure AD, que autoriza a aplicação a completar o pedido de rede sem pedir ao utilizador que introduza as suas credenciais. Além disso, se o dispositivo for conhecido do Azure AD, o plug-in SSO também passará o certificado do dispositivo para satisfazer a verificação de acesso condicional baseada no dispositivo. 

Para suportar SSO para aplicações não MSAL, o plug-in SSO implementa um protocolo semelhante ao plug-in do navegador Windows descrito no [What is a Primary Refresh Token?](../devices/concept-primary-refresh-token.md#browser-sso-using-prt) 

Em comparação com as aplicações baseadas no MSAL, o plug-in SSO atua de forma mais transparente para aplicações não MSAL, integrando-se com a experiência de login do navegador existente que as aplicações fornecem. O utilizador final veria a sua experiência familiar, com o benefício de não ter de realizar insusimentos adicionais em cada uma das aplicações. Por exemplo, em vez de exibir o selecionador de contas nativo, o plug-in SSO adiciona sessões SSO à experiência de picker de conta baseada na web. 

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o modo de dispositivo partilhado no iOS, consulte [o modo de dispositivo partilhado para dispositivos iOS](msal-ios-shared-devices.md).
