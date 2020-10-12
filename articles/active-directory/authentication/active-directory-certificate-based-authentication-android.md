---
title: Autenticação baseada em certificados Android - Azure Ative Directory
description: Conheça os cenários suportados e os requisitos para configurar a autenticação baseada em certificados em soluções com dispositivos Android
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: annaba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7dd4c95c3c02f4b4a807b5238aa61e76ecb56e3e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88716423"
---
# <a name="azure-active-directory-certificate-based-authentication-on-android"></a>Autenticação baseada em certificados Azure Ative Para Android

Os dispositivos Android podem utilizar a autenticação baseada em certificados (CBA) para autenticar o Azure Ative Directory utilizando um certificado de cliente no seu dispositivo quando se conecta a:

* Aplicações móveis do Office, como o Microsoft Outlook e o Microsoft Word
* Clientes Exchange ActiveSync (EAS)

Configurar esta funcionalidade elimina a necessidade de introduzir um nome de utilizador e uma combinação de palavra-passe em determinadas aplicações de correio e Microsoft Office no seu dispositivo móvel.

Este tópico fornece-lhe os requisitos e os cenários suportados para configurar a CBA num dispositivo Android para utilizadores de inquilinos no Office 365 Enterprise, Business, Education, US Government, China e Alemanha.

Esta funcionalidade está disponível em pré-visualização no Office 365 US Government Defense and Federal plans.

## <a name="microsoft-mobile-applications-support"></a>Suporte a aplicações móveis da Microsoft

| Apps | Suporte |
| --- | --- |
| App Azure Information Protection |![Marca de verificação significa apoio para esta aplicação][1] |
| Intune Portal da Empresa |![Marca de verificação significa apoio para esta aplicação][1] |
| Microsoft Stream |![Marca de verificação significa apoio para esta aplicação][1] |
| OneNote |![Marca de verificação significa apoio para esta aplicação][1] |
| OneDrive |![Marca de verificação significa apoio para esta aplicação][1] |
| Outlook |![Marca de verificação significa apoio para esta aplicação][1] |
| Power BI |![Marca de verificação significa apoio para esta aplicação][1] |
| Skype para Empresas |![Marca de verificação significa apoio para esta aplicação][1] |
| Palavra / Excel / PowerPoint |![Marca de verificação significa apoio para esta aplicação][1] |
| Yammer |![Marca de verificação significa apoio para esta aplicação][1] |

### <a name="implementation-requirements"></a>Requisitos de implementação

A versão SO do dispositivo deve ser o Android 5.0 (Lollipop) e acima.

Um servidor da federação deve ser configurado.

Para que o Azure Ative Directory revogue um certificado de cliente, o token ADFS deve ter as seguintes alegações:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>` (Número de série do certificado de cliente)
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>` (O fio para o emitente do certificado de cliente)

O Azure Ative Directory adiciona estas reclamações ao token de atualização se estiverem disponíveis no token ADFS (ou em qualquer outro token SAML). Quando o token de atualização precisa de ser validado, esta informação é utilizada para verificar a revogação.

Como melhor prática, deve atualizar as páginas de erro ADFS da sua organização com as seguintes informações:

* A exigência de instalação do Microsoft Authenticator no Android.
* Instruções sobre como obter um certificado de utilizador.

Para obter mais informações, consulte [personalizar as páginas de inscrição do AD FS](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn280950(v=ws.11)).

Algumas aplicações do Office (com autenticação moderna ativada) enviam '*prompt=login*' para Azure AD no seu pedido. Por padrão, a Azure AD traduz o *' prompt=login*' no pedido à ADFS como '*wauth=usernamepassworduri*' (pede à ADFS para fazer U/P Auth) e '*wfresh=0*' (pede à ADFS que ignore o estado SSO e faça uma nova autenticação). Se pretender ativar a autenticação baseada em certificados para estas aplicações, tem de modificar o comportamento AD Azure predefinido. Defina o '*PromptLoginBehavior*' nas definições de domínio federado para '*Desativado*'.
Pode utilizar o [CMDlet MSOLDomainFederations](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) para executar esta tarefa:

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`

## <a name="exchange-activesync-clients-support"></a>Suporte de clientes Exchange ActiveSync

Algumas aplicações do Exchange ActiveSync no Android 5.0 (Lollipop) ou posteriormente são suportadas. Para determinar se a sua aplicação de e-mail suporta esta funcionalidade, contacte o seu programador de aplicações.

## <a name="next-steps"></a>Passos seguintes

Se pretender configurar a autenticação baseada em certificados no seu ambiente, consulte [Começar com autenticação baseada em certificados no Android](active-directory-certificate-based-authentication-get-started.md) para obter instruções.

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-android/ic195031.png