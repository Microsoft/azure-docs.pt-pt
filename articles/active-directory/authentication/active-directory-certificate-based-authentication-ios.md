---
title: Autenticação baseada em certificado no iOS - Diretório Ativo Azure
description: Conheça os cenários suportados e os requisitos para configurar a autenticação baseada em certificados em soluções com dispositivos iOS
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: annaba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fd8a0c3688e5056c7941d334da8caee9f21ba82
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81407272"
---
# <a name="azure-active-directory-certificate-based-authentication-on-ios"></a>Autenticação baseada em certificado de diretório Ativo Azure no iOS

Os dispositivos iOS podem utilizar a autenticação baseada em certificados (CBA) para autenticar o Azure Ative Directory utilizando um certificado de cliente no seu dispositivo quando se ligarem a:

* Aplicações móveis do Office, tais como Microsoft Outlook e Microsoft Word
* Clientes Exchange ActiveSync (EAS)

Configurar esta funcionalidade elimina a necessidade de introduzir uma combinação de nome de utilizador e palavra-passe em determinadas aplicações de correio e microsoft office no seu dispositivo móvel.

Este tópico fornece-lhe os requisitos e os cenários suportados para configurar a CBA num dispositivo iOS para utilizadores de inquilinos no Office 365 Enterprise, Business, Education, Us Government, China e Alemanha.

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

## <a name="requirements"></a>Requisitos

A versão OS do dispositivo deve ser iOS 9 e superior

Um servidor da federação deve ser configurado.

O Microsoft Authenticator é necessário para aplicações do Office no iOS.

Para que o Diretório Ativo da Azure revogue um certificado de cliente, o token ADFS deve ter as seguintes reclamações:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>`(O número de série do certificado de cliente)
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>`(A corda para o emitente do certificado de cliente)

O Azure Ative Directory adiciona estas alegações ao token de atualização se estiverem disponíveis no token ADFS (ou em qualquer outro símbolo SAML). Quando o token de atualização precisa de ser validado, esta informação é utilizada para verificar a revogação.

Como uma boa prática, deve atualizar as páginas de erro ADFS da sua organização com as seguintes informações:

* A exigência de instalação do Autenticador Microsoft no iOS
* Instruções sobre como obter um certificado de utilizador.

Para mais informações, consulte [Personalizar as páginas de inscrição aD FS](https://technet.microsoft.com/library/dn280950.aspx).

Algumas aplicações do Office (com autenticação moderna ativada) enviam '*prompt=login*' para AD Azure a seu pedido. Por predefinição, a AD Azure traduz '*prompt=login*' no pedido à ADFS como '*wauth=usernamepassworduri*' (pede à ADFS para fazer U/P Auth) e '*wfresh=0*' (pede à ADFS que ignore o estado sso e faça uma nova autenticação). Se pretender ativar a autenticação baseada em certificados para estas aplicações, tem de modificar o comportamento da AD Azure predefinida. Basta definir o '*PromptLoginBehavior*' nas definições de domínio federado para '*Desativado*'.
Pode utilizar o [cmdlet MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) para executar esta tarefa:

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`

## <a name="exchange-activesync-clients-support"></a>Intercâmbio Suporte de clientes ActiveSync

No iOS 9 ou posterior, o cliente de correio iOS nativo é suportado. Para todas as outras aplicações Exchange ActiveSync, para determinar se esta funcionalidade é suportada, contacte o desenvolvedor da sua aplicação.

## <a name="next-steps"></a>Passos seguintes

Se pretender configurar a autenticação baseada em certificados no seu ambiente, consulte [Iniciar com autenticação baseada em certificados no Android](../authentication/active-directory-certificate-based-authentication-get-started.md) para obter instruções.

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png
