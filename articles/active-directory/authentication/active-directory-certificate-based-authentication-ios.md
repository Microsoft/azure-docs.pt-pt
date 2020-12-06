---
title: Autenticação baseada em certificados no iOS - Azure Ative Directory
description: Conheça os cenários suportados e os requisitos para configurar a autenticação baseada em certificados para o Azure Ative Directory em soluções com dispositivos iOS
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/17/2020
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: ffddac13009b84aa8253955d265f11aefe2ce5dd
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2020
ms.locfileid: "96744419"
---
# <a name="azure-active-directory-certificate-based-authentication-on-ios"></a>Autenticação baseada em certificados Azure Ative No iOS

Para melhorar a segurança, os dispositivos iOS podem utilizar a autenticação baseada em certificados (CBA) para autenticar o Azure Ative Directory (Azure AD) utilizando um certificado de cliente no seu dispositivo ao ligarem-se às seguintes aplicações ou serviços:

* Aplicações móveis do Office, como o Microsoft Outlook e o Microsoft Word
* Clientes Exchange ActiveSync (EAS)

A utilização de certificados elimina a necessidade de introduzir um nome de utilizador e uma combinação de palavra-passe em determinados emails e aplicações do Microsoft Office no seu dispositivo móvel.

Este artigo detalha os requisitos e os cenários suportados para configurar a CBA num dispositivo iOS. CBA para iOS está disponível em nuvens públicas Azure, Microsoft Government Cloud, Microsoft Cloud Germany e Microsoft Azure China 21Vianet.

## <a name="microsoft-mobile-applications-support"></a>Suporte a aplicações móveis da Microsoft

| Aplicações | Suporte |
| --- | --- |
| App Azure Information Protection |![Marca de verificação significa apoio para esta aplicação][1] |
| Intune Portal da Empresa |![Marca de verificação significa apoio para esta aplicação][1] |
| Microsoft Stream |![Marca de verificação significa apoio para esta aplicação][1] |
| Escritório (móvel) |![Marca de verificação significa apoio para esta aplicação][1] |
| OneNote |![Marca de verificação significa apoio para esta aplicação][1] |
| OneDrive |![Marca de verificação significa apoio para esta aplicação][1] |
| Outlook |![Marca de verificação significa apoio para esta aplicação][1] |
| Power BI |![Marca de verificação significa apoio para esta aplicação][1] |
| Skype para Empresas |![Marca de verificação significa apoio para esta aplicação][1] |
| Palavra / Excel / PowerPoint |![Marca de verificação significa apoio para esta aplicação][1] |
| Yammer |![Marca de verificação significa apoio para esta aplicação][1] |

## <a name="requirements"></a>Requisitos

Para utilizar a CBA com o iOS, aplicam-se os seguintes requisitos e considerações:

* A versão SO do dispositivo deve ser iOS 9 ou superior.
* O Microsoft Authenticator é necessário para aplicações do Office no iOS.
* Deve ser criada uma preferência de identidade no keychain do macOS que inclua o URL de autenticação do servidor ADFS. Para obter mais informações, consulte [Criar uma preferência de identidade no Keychain Access no Mac.](https://support.apple.com/guide/keychain-access/create-an-identity-preference-kyca6343b6c9/mac)

Aplicam-se os seguintes requisitos e considerações dos Serviços e considerações da Federação do Diretório Ativo (ADFS):

* O servidor ADFS deve ser ativado para autenticação de certificados e utilizar a autenticação federada.
* O certificado tem de utilizar a utilização da chave melhorada (EKU) e conter a UPN do utilizador no *Nome Alternativo do Sujeito (Nome Principal NT)*.

## <a name="configure-adfs"></a>Configure ADFS

Para que a Azure AD revogue um certificado de cliente, o token ADFS deve ter as seguintes reclamações. A Azure AD adiciona estas alegações ao token de atualização se estiverem disponíveis no token ADFS (ou em qualquer outro token SAML). Quando o token de atualização precisa de ser validado, esta informação é utilizada para verificar a revogação:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>` - adicionar o número de série do seu certificado de cliente
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>` - adicione a cadeia para o emitente do seu certificado de cliente

Como melhor prática, também deve atualizar as páginas de erro ADFS da sua organização com as seguintes informações:

* A exigência de instalação do Autenticador Microsoft no iOS.
* Instruções sobre como obter um certificado de utilizador.

Para obter mais informações, consulte [personalizar o sinal de AD FS na página.](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn280950(v=ws.11))

## <a name="use-modern-authentication-with-office-apps"></a>Use a autenticação moderna com aplicações do Office

Algumas aplicações do Office com autenticação moderna habilitados a enviar `prompt=login` para a Azure AD no seu pedido. Por padrão, a Azure AD traduz-se `prompt=login` no pedido à ADFS como `wauth=usernamepassworduri` (pede à ADFS para fazer U/P Auth) e `wfresh=0` (pede à ADFS que ignore o estado SSO e faça uma autenticação fresca). Se pretender ativar a autenticação baseada em certificados para estas aplicações, modifique o comportamento AD AZure predefinido.

Para atualizar o comportamento predefinido, defina o '*PromptLoginBehavior*' nas definições de domínio federados para *Desativar*. Pode utilizar o [CMDlet MSOLDomainFederations](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) para executar esta tarefa, como mostra o seguinte exemplo:

```powershell
Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled
```

## <a name="support-for-exchange-activesync-clients"></a>Suporte para clientes Exchange ActiveSync

No iOS 9 ou posteriormente, o cliente de correio nativo do iOS é suportado. Para determinar se esta funcionalidade é suportada para todas as outras aplicações do Exchange ActiveSync, contacte o seu programador de aplicações.

## <a name="next-steps"></a>Passos seguintes

Para configurar a autenticação baseada em certificados no seu ambiente, consulte [Começar com autenticação baseada em certificados](active-directory-certificate-based-authentication-get-started.md) para obter instruções.

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png