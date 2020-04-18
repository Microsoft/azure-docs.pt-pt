---
title: Autenticação baseada em certificado no iOS - Diretório Ativo Azure
description: Conheça os cenários suportados e os requisitos para configurar a autenticação baseada em certificados para o Diretório Ativo Azure em soluções com dispositivos iOS
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/17/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76c5e18a0bf84e96476eafd7ff35398049f1a492
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2020
ms.locfileid: "81639636"
---
# <a name="azure-active-directory-certificate-based-authentication-on-ios"></a>Autenticação baseada em certificado de diretório Ativo Azure no iOS

Para melhorar a segurança, os dispositivos iOS podem utilizar a autenticação baseada em certificados (CBA) para autenticar o Azure Ative Directory (Azure AD) utilizando um certificado de cliente no seu dispositivo ao ligarem-se às seguintes aplicações ou serviços:

* Aplicações móveis do Office, tais como Microsoft Outlook e Microsoft Word
* Clientes Exchange ActiveSync (EAS)

A utilização de certificados elimina a necessidade de introduzir uma combinação de nome de utilizador e palavra-passe em determinadas aplicações de correio e microsoft office no seu dispositivo móvel.

Este artigo detalha os requisitos e os cenários suportados para configurar a CBA num dispositivo iOS. CbA para iOS está disponível em nuvens públicas Azure, Microsoft Government Cloud, Microsoft Cloud Germany e Microsoft Azure China 21Vianet.

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

Para utilizar a CBA com o iOS, aplicam-se os seguintes requisitos e considerações:

* A versão OS do dispositivo deve ser iOS 9 ou superior.
* O Microsoft Authenticator é necessário para aplicações do Office no iOS.
* Deve ser criada uma preferência de identidade no chaveiro macOS que inclua o URL de autenticação do servidor ADFS. Para mais informações, consulte Criar uma preferência de [identidade no Acesso à Cadeia de Chaves no Mac](https://support.apple.com/guide/keychain-access/create-an-identity-preference-kyca6343b6c9/mac).

Aplicam-se os seguintes requisitos e considerações dos seguintes serviços e considerações da Federação de Directórioactivo (ADFS):

* O servidor ADFS deve estar ativado para autenticação de certificado e utilizar a autenticação federada.
* O certificado tem de utilizar a utilização da chave melhorada (EKU) e conter a UPN do utilizador no *Nome Alternativo sujeito (Nome Principal NT)*.

## <a name="configure-adfs"></a>Configurar ADFS

Para que a Azure AD revogue um certificado de cliente, o token ADFS deve ter as seguintes reclamações. A Azure AD adiciona estas alegações ao token de atualização se estiverem disponíveis no token ADFS (ou em qualquer outro símbolo SAML). Quando o token de atualização precisa de ser validado, esta informação é utilizada para verificar a revogação:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>`- adicione o número de série do seu certificado de cliente
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>`- adicione a corda para o emitente do seu certificado de cliente

Como uma boa prática, também deve atualizar as páginas de erro ADFS da sua organização com as seguintes informações:

* A exigência de instalação do Autenticador Microsoft no iOS.
* Instruções sobre como obter um certificado de utilizador.

Para mais informações, consulte Personalizar o sinal de [AD FS na página](https://technet.microsoft.com/library/dn280950.aspx).

## <a name="use-modern-authentication-with-office-apps"></a>Use autenticação moderna com aplicações do Office

Algumas aplicações do Office `prompt=login` com autenticação moderna permitiram enviar para a AD Azure a seu pedido. Por predefinição, a `prompt=login` Azure AD traduz-se no pedido à ADFS como `wauth=usernamepassworduri` (pede à ADFS para fazer U/P Auth) e `wfresh=0` (pede à ADFS que ignore o estado SSO e faça uma autenticação nova). Se pretender ativar a autenticação baseada em certificados para estas aplicações, modifique o comportamento do AD Azure predefinido.

Para atualizar o comportamento predefinido, detete o '*PromptLoginBehavior*' nas definições de domínio federado para *Desativado*. Pode utilizar o [cmdlet MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) para executar esta tarefa, como mostra o seguinte exemplo:

```powershell
Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled
```

## <a name="support-for-exchange-activesync-clients"></a>Suporte para clientes Exchange ActiveSync

No iOS 9 ou posterior, o cliente de correio iOS nativo é suportado. Para determinar se esta funcionalidade é suportada para todas as outras aplicações Exchange ActiveSync, contacte o desenvolvedor da sua aplicação.

## <a name="next-steps"></a>Passos seguintes

Para configurar a autenticação baseada em certificados no seu ambiente, consulte [Iniciar com autenticação baseada em certificado](active-directory-certificate-based-authentication-get-started.md) para instruções.

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png
