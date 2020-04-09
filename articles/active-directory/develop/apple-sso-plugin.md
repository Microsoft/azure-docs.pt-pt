---
title: Plug-in de SSO do Microsoft Enterprise para dispositivos Apple
titleSuffix: Microsoft identity platform | Azure
description: Conheça o plug-in SSO do Diretório Ativo Azure da Microsoft para dispositivos iOS e macOS.
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: a6af1a78582ebf62aed82bad8fa06cac37f511ea
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982587"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>Microsoft Enterprise SSO plug-in para dispositivos Apple (Pré-visualização)

> [!NOTE]
> Esta funcionalidade está em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

O *plug-in Microsoft Enterprise SSO para dispositivos Apple* fornece um único sinal de entrada (SSO) para contas de Diretório Ativo Azure (Azure AD) em todas as aplicações que suportam a funcionalidade [Enterprise Single Sign-On](https://developer.apple.com/documentation/authenticationservices) da Apple. A Microsoft trabalhou de perto com a Apple para desenvolver este plug-in para aumentar a usabilidade da sua aplicação, fornecendo a melhor proteção que a Apple e a Microsoft podem fornecer.

Neste lançamento de Pré-visualização Pública, o plug-in Enterprise SSO está disponível apenas para dispositivos iOS e está distribuído em certas aplicações da Microsoft.

A nossa primeira utilização do plug-in Enterprise SSO é com a nossa nova funcionalidade de modo de [dispositivo partilhado.](msal-ios-shared-devices.md)

## <a name="features"></a>Funcionalidades

O plug-in Microsoft Enterprise SSO para dispositivos Apple oferece os seguintes benefícios:

- Fornece contas SSO para AD Azure em todas as aplicações que suportam a funcionalidade Enterprise Single Sign-On da Apple.
- Entregue automaticamente no Autenticador microsoft e pode ser ativado por qualquer solução de gestão de dispositivos móveis (MDM).

## <a name="requirements"></a>Requisitos

Para utilizar o plug-in Microsoft Enterprise SSO para dispositivos Apple:

- O iOS 13.0 ou superior deve ser instalado no aparelho.
- Uma aplicação da Microsoft que forneça o plug-in Microsoft Enterprise SSO para dispositivos Apple deve ser instalada no dispositivo. Para pré-visualização pública, estas aplicações incluem a [aplicação Microsoft Authenticator](../user-help/user-help-auth-app-overview.md).
- O dispositivo deve estar inscrito em MDM (por exemplo, com o Microsoft Intune).
- A configuração deve ser empurrada para o dispositivo para ativar o plug-in Microsoft Enterprise SSO para dispositivos Apple no dispositivo. Esta restrição de segurança é exigida pela Apple.

## <a name="enable-the-sso-extension-with-mobile-device-management-mdm"></a>Ativar a extensão SSO com a gestão de dispositivos móveis (MDM)

Para ativar o plug-in Microsoft Enterprise SSO para dispositivos Apple, os seus dispositivos precisam de ser enviados um sinal através de um serviço MDM. Uma vez que a Microsoft inclui o plug-in Enterprise SSO na [aplicação Microsoft Authenticator,](..//user-help/user-help-auth-app-overview.md)utilize o seu MDM para configurar a aplicação para ativar o plug-in Microsoft Enterprise SSO.

Utilize os seguintes parâmetros para configurar o plug-in Microsoft Enterprise SSO para dispositivos Apple:

- **Tipo**: Redirecionar
- **ID de extensão:**`com.microsoft.azureauthenticator.ssoextension`
- **ID da equipa:**`SGGM6D27TK`
- **URLs:**
  - `https://login.microsoftonline.com`
  - `https://login.windows.net`
  - `https://login.microsoft.com`
  - `https://sts.windows.net`
  - `https://login.partner.microsoftonline.cn`
  - `https://login.chinacloudapi.cn`
  - `https://login.microsoftonline.de`
  - `https://login.microsoftonline.us`
  - `https://login.usgovcloudapi.net`
  - `https://login-us.microsoftonline.com`

Pode utilizar o Microsoft Intune como serviço MDM para facilitar a configuração do plug-in Microsoft Enterprise SSO. Para mais informações, consulte a documentação de [configuração Intune](https://docs.microsoft.com/intune/configuration/ios-device-features-settings).

## <a name="using-the-sso-extension-in-your-application"></a>Utilização da extensão SSO na sua aplicação

A [Microsoft Authentication Library (MSAL) para dispositivos Apple](https://github.com/AzureAD/microsoft-authentication-library-for-objc) versão 1.1.0 e suporta mais alto o plug-in Microsoft Enterprise SSO para dispositivos Apple.

Se quiser suportar o modo de dispositivo partilhado fornecido pelo plug-in Microsoft Enterprise SSO para dispositivos Apple, certifique-se de que as suas aplicações utilizam a versão mínima exigida do MSAL.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o modo de dispositivo partilhado no iOS, consulte o [modo de dispositivo partilhado para dispositivos iOS](msal-ios-shared-devices.md).
