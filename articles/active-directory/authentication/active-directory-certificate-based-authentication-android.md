---
title: Autenticação baseada em certificadoandroid - Diretório Ativo Azure
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
ms.openlocfilehash: 8e0427398d05cbe9f76249ec8f7c25568d566d5d
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654385"
---
# <a name="azure-active-directory-certificate-based-authentication-on-android"></a>Autenticação baseada em certificado de diretório Ativo Azure no Android

Os dispositivos Android podem utilizar a autenticação baseada em certificados (CBA) para autenticar o Azure Ative Directory utilizando um certificado de cliente no seu dispositivo quando se ligarem a:

* Aplicações móveis do Office, tais como Microsoft Outlook e Microsoft Word
* Clientes Exchange ActiveSync (EAS)

Configurar esta funcionalidade elimina a necessidade de introduzir uma combinação de nome de utilizador e palavra-passe em determinadas aplicações de correio e microsoft office no seu dispositivo móvel.

Este tópico fornece-lhe os requisitos e os cenários suportados para configurar cba num dispositivo iOS (Android) para utilizadores de inquilinos no Office 365 Enterprise, Business, Education, Governo dos EUA, China e Alemanha.

Esta funcionalidade está disponível em pré-visualização no Office 365 US Government Defense and Federal plans.

## <a name="microsoft-mobile-applications-support"></a>Suporte a aplicações móveis da Microsoft

| Aplicações | Suporte |
| --- | --- |
| Aplicativo de proteção de informação Azure |![Verifique o suporte de sinalização de marca para esta aplicação][1] |
| Intune Portal da Empresa |![Verifique o suporte de sinalização de marca para esta aplicação][1] |
| Microsoft Teams |![Verifique o suporte de sinalização de marca para esta aplicação][1] |
| OneNote |![Verifique o suporte de sinalização de marca para esta aplicação][1] |
| OneDrive |![Verifique o suporte de sinalização de marca para esta aplicação][1] |
| Outlook |![Verifique o suporte de sinalização de marca para esta aplicação][1] |
| Power BI |![Verifique o suporte de sinalização de marca para esta aplicação][1] |
| Skype para Empresas |![Verifique o suporte de sinalização de marca para esta aplicação][1] |
| Palavra / Excel / PowerPoint |![Verifique o suporte de sinalização de marca para esta aplicação][1] |
| Yammer |![Verifique o suporte de sinalização de marca para esta aplicação][1] |

### <a name="implementation-requirements"></a>Requisitos de implementação

A versão OS do dispositivo deve ser Android 5.0 (Lollipop) e acima.

Um servidor da federação deve ser configurado.

Para que o Diretório Ativo da Azure revogue um certificado de cliente, o token ADFS deve ter as seguintes reclamações:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>`(O número de série do certificado de cliente)
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>`(A corda para o emitente do certificado de cliente)

O Azure Ative Directory adiciona estas alegações ao token de atualização se estiverem disponíveis no token ADFS (ou em qualquer outro símbolo SAML). Quando o token de atualização precisa de ser validado, esta informação é utilizada para verificar a revogação.

Como uma boa prática, deve atualizar as páginas de erro ADFS da sua organização com as seguintes informações:

* A exigência de instalação do Autenticador Microsoft no Android.
* Instruções sobre como obter um certificado de utilizador.

Para mais informações, consulte [Personalizar as páginas de inscrição aD FS](https://technet.microsoft.com/library/dn280950.aspx).

Algumas aplicações do Office (com autenticação moderna ativada) enviam '*prompt=login*' para AD Azure a seu pedido. Por predefinição, a AD Azure traduz '*prompt=login*' no pedido à ADFS como '*wauth=usernamepassworduri*' (pede à ADFS para fazer U/P Auth) e '*wfresh=0*' (pede à ADFS que ignore o estado sso e faça uma nova autenticação). Se pretender ativar a autenticação baseada em certificados para estas aplicações, tem de modificar o comportamento da AD Azure predefinida. Desative o '*Comportamento de ProntoLogin '* nas definições de domínio federado para '*Desativado*'.
Pode utilizar o [cmdlet MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) para executar esta tarefa:

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`

## <a name="exchange-activesync-clients-support"></a>Intercâmbio Suporte de clientes ActiveSync

Algumas aplicações Exchange ActiveSync no Android 5.0 (Lollipop) ou posteriormente são suportadas. Para determinar se a sua aplicação de e-mail suporta esta funcionalidade, contacte o programador da sua aplicação.

## <a name="next-steps"></a>Passos seguintes

Se pretender configurar a autenticação baseada em certificados no seu ambiente, consulte [Iniciar com autenticação baseada em certificados no Android](active-directory-certificate-based-authentication-get-started.md) para obter instruções.

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-android/ic195031.png
