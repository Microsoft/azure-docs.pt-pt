---
title: Autenticação baseada em certificado no iOS-Azure Active Directory
description: Saiba mais sobre os cenários com suporte e os requisitos para configurar a autenticação baseada em certificado em soluções com dispositivos iOS
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 01/15/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: annaba
ms.collection: M365-identity-device-management
ms.openlocfilehash: d2f9e7d71ab660c4df6f65d6bebe1d3854086bdd
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848804"
---
# <a name="azure-active-directory-certificate-based-authentication-on-ios"></a>Azure Active Directory a autenticação baseada em certificado no iOS

os dispositivos iOS podem usar a CBA (autenticação baseada em certificado) para autenticar para Azure Active Directory usando um certificado de cliente em seu dispositivo ao se conectarem a:

* Aplicativos móveis do Office, como o Microsoft Outlook e o Microsoft Word
* Clientes do Exchange ActiveSync (EAS)

A configuração desse recurso elimina a necessidade de inserir uma combinação de nome de usuário e senha em determinados emails e Microsoft Office aplicativos em seu dispositivo móvel.

Este tópico fornece os requisitos e os cenários com suporte para configurar o CBA em um dispositivo iOS (Android) para usuários de locatários nos planos do Office 365 Enterprise, Business, Education, governo dos EUA, China e Alemanha.

Esse recurso está disponível em versão prévia nos planos federal e de defesa do governo dos EUA do Office 365.

## <a name="microsoft-mobile-applications-support"></a>Suporte a aplicativos móveis da Microsoft

| Aplicações | Suporte |
| --- | --- |
| Aplicativo de proteção de informações do Azure |![Marca de seleção que significa suporte para este aplicativo][1] |
| Portal da Empresa do Intune |![Marca de seleção que significa suporte para este aplicativo][1] |
| Microsoft Teams |![Marca de seleção que significa suporte para este aplicativo][1] |
| OneNote |![Marca de seleção que significa suporte para este aplicativo][1] |
| OneDrive |![Marca de seleção que significa suporte para este aplicativo][1] |
| Outlook |![Marca de seleção que significa suporte para este aplicativo][1] |
| Power BI |![Marca de seleção que significa suporte para este aplicativo][1] |
| Skype para Empresas |![Marca de seleção que significa suporte para este aplicativo][1] |
| Word/Excel/PowerPoint |![Marca de seleção que significa suporte para este aplicativo][1] |
| Yammer |![Marca de seleção que significa suporte para este aplicativo][1] |

## <a name="requirements"></a>Requisitos

A versão do sistema operacional do dispositivo deve ser iOS 9 e superior

Um servidor de Federação deve ser configurado.

Microsoft Authenticator é necessário para aplicativos do Office no iOS.

Para Azure Active Directory revogar um certificado de cliente, o token do ADFS deve ter as seguintes declarações:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>` (o número de série do certificado do cliente)
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>` (a cadeia de caracteres do emissor do certificado do cliente)

Azure Active Directory adiciona essas declarações ao token de atualização se elas estiverem disponíveis no token do ADFS (ou qualquer outro token SAML). Quando o token de atualização precisa ser validado, essas informações são usadas para verificar a revogação.

Como prática recomendada, você deve atualizar as páginas de erro do ADFS de sua organização com as seguintes informações:

* O requisito para instalar o Microsoft Authenticator no iOS
* Instruções sobre como obter um certificado de usuário.

Para obter mais informações, consulte [Personalizando as páginas de entrada do AD FS](https://technet.microsoft.com/library/dn280950.aspx).

Alguns aplicativos do Office (com autenticação moderna habilitada) enviam '*prompt = logon*' ao Azure AD em sua solicitação. Por padrão, o Azure AD traduz '*prompt = logon*' na solicitação para ADFS como '*wauth = usernamepassworduri*' (solicita que o ADFS faça a autenticação U/P) e '*wfresh = 0*' (solicita que o ADFS ignore o estado do SSO e faça uma nova autenticação). Se você quiser habilitar a autenticação baseada em certificado para esses aplicativos, precisará modificar o comportamento padrão do AD do Azure. Basta definir '*PromptLoginBehavior*' nas configurações de domínio federado como '*Disabled*'.
Você pode usar o cmdlet [MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) para executar esta tarefa:

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`

## <a name="exchange-activesync-clients-support"></a>Suporte a clientes do Exchange ActiveSync

No iOS 9 ou posterior, há suporte para o cliente de correio iOS nativo. Para todos os outros aplicativos do Exchange ActiveSync, para determinar se há suporte para esse recurso, contate o desenvolvedor do aplicativo.

## <a name="next-steps"></a>Passos seguintes

Se você quiser configurar a autenticação baseada em certificado em seu ambiente, consulte Introdução [à autenticação baseada em certificado no Android](../authentication/active-directory-certificate-based-authentication-get-started.md) para obter instruções.

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png
