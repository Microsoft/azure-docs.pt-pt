---
title: Autenticação baseada em certificado no Android - Azure Active Directory
description: Saiba mais sobre os cenários suportados e os requisitos de configuração da autenticação baseada em certificados em soluções com dispositivos Android
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 01/15/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: annaba
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0f4a648227e62642bb071a76cbd1f3a50bf78ae
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58314507"
---
# <a name="azure-active-directory-certificate-based-authentication-on-android"></a>Autenticação do Azure Active Directory com base em certificado no Android

Dispositivos Android podem utilizar a autenticação baseada em certificados (CBA) para autenticar para o Azure Active Directory com um certificado de cliente nos respetivos dispositivos ao ligar a:

* Aplicações móveis do Office como o Microsoft Outlook e o Microsoft Word
* Clientes do Exchange ActiveSync (EAS)

Configurar esta funcionalidade elimina a necessidade de inserir uma combinação de nome de utilizador e palavra-passe em determinados email e aplicativos do Microsoft Office no seu dispositivo móvel.

Este tópico fornece os requisitos e os cenários suportados para configurar CBA num dispositivo iOS(Android) para os utilizadores de inquilinos no Office 365 Enterprise, empresas, ensino, Governo dos EUA, China e Alemanha planos.

Esta funcionalidade está disponível em pré-visualização nos planos do Office 365 US Government defesa e Federal.

## <a name="microsoft-mobile-applications-support"></a>Suporte de aplicações móveis da Microsoft

| Aplicações | Suporte |
| --- | --- |
| Azure Information Protection app |![Marcar][1] |
| Portal da empresa do Intune |![Marcar][1] |
| Microsoft Teams |![Marcar][1] |
| OneNote |![Marcar][1] |
| OneDrive |![Marcar][1] |
| Outlook |![Marcar][1] |
| Power BI |![Marcar][1] |
| Skype para Empresas |![Marcar][1] |
| Word / Excel / PowerPoint |![Marcar][1] |
| Yammer |![Marcar][1] |

### <a name="implementation-requirements"></a>Requisitos de implementação

A versão de SO do dispositivo tem de ser Android 5.0 (Lollipop) e superior.

Tem de ser configurado um servidor de Federação.

Para o Azure Active Directory revogar um certificado de cliente, o token do AD FS tem de ter as seguintes declarações:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>` (O número de série do certificado de cliente)
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>` (A cadeia para o emissor do certificado de cliente)

O Azure Active Directory adiciona essas declarações para o token de atualização se estiverem disponíveis no token do AD FS (ou qualquer outro token SAML). Quando o token de atualização tem de ser validado, estas informações são utilizadas para verificar a revogação.

Como melhor prática, deve atualizar páginas de erro do AD FS da sua organização com as seguintes informações:

* O requisito para instalar o Microsoft Authenticator em dispositivos Android.
* Instruções sobre como obter um certificado de utilizador.

Para obter mais informações, consulte [personalizar as páginas de início de sessão do AD FS-](https://technet.microsoft.com/library/dn280950.aspx).

Algumas aplicações do Office (com autenticação moderna ativada) enviar '*perguntar = o início de sessão*' para o Azure AD no seu pedido. Por predefinição, o Azure AD traduz '*perguntar = o início de sessão*"no pedido para ADFS como'*wauth = usernamepassworduri*' (solicita o ADFS para fazer a autenticação de U/P) e"*wfresh = 0*' (solicita o ADFS para Ignorar o estado SSO e fazer uma nova autenticação). Se pretender ativar a autenticação baseada em certificado para estas aplicações, terá de modificar o comportamento do padrão do Azure AD. Defina a '*PromptLoginBehavior*"nas definições do domínio federado para'*desativado*'.
Pode utilizar o [MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) cmdlet para efetuar esta tarefa:

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`

## <a name="exchange-activesync-clients-support"></a>Suporte de clientes do Exchange ActiveSync

São suportados determinados aplicativos do Exchange ActiveSync no Android 5.0 (Lollipop) ou posterior. Para determinar se a aplicação de e-mail de suportar esta funcionalidade, contacte o seu desenvolvedor de aplicativos.

## <a name="next-steps"></a>Passos Seguintes

Se quiser configurar a autenticação baseada em certificado no seu ambiente, consulte [introdução à autenticação baseada em certificados em dispositivos Android](active-directory-certificate-based-authentication-get-started.md) para obter instruções.

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-android/ic195031.png
