---
title: Requisitos de dados da Azure AD SSPR - Diretório Ativo Azure
description: Requisitos de dados para reset de senha de autosserviço da AD Azure e como satisfazê-los
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: a14338e552250ac63c344365099a16f20616ea9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74964038"
---
# <a name="deploy-password-reset-without-requiring-end-user-registration"></a>Implementar reset de palavra-passe sem exigir o registo do utilizador final

Para implementar o reset de autosserviço do Azure Ative Directory (Azure AD), os dados de autenticação têm de estar presentes. Algumas organizações têm os seus utilizadores a introduzir os seus próprios dados de autenticação. Outras organizações preferem sincronizar com dados que já existem no Diretório Ativo. Estes dados sincronizados são disponibilizados à Azure AD e sSPR sem necessitar de interação do utilizador se cumprir os seguintes requisitos:

* Forme adequadamente os dados do seu diretório no local.
* Configure [o Azure AD Connect utilizando as definições expressas](../hybrid/how-to-connect-install-express.md).

Para funcionar corretamente, os números de telefone devem estar no formato *+CountryCode PhoneNumber*, por exemplo, +1 4255551234.

> [!NOTE]
> Tem de haver um espaço entre o código do país e o número de telefone.
>
> O reset da palavra-passe não suporta extensões de telefone. Mesmo no formato +1 4255551234X12345, as extensões são removidas antes da chamada ser colocada.

## <a name="fields-populated"></a>Campos povoados

Se utilizar as definições predefinidas no Azure AD Connect, são feitos os seguintes mapeamentos:

| Active Directory no local | Azure AD |
| --- | --- |
| telefoneNúmero | Telefone do escritório |
| dispositivo móvel | Número de telemóvel |

Depois de um utilizador verificar o seu número de telemóvel, o campo *Telefone* em informações de contacto de **Autenticação** em Azure AD também é preenchido com esse número.

## <a name="authentication-contact-info"></a>Informações de contacto de autenticação

Na página dos métodos de **Autenticação** para um utilizador da AD Azure no portal Azure, um Administrador Global pode definir manualmente as informações de contacto de autenticação, como mostra o seguinte exemplo:

![Informação de contacto de autenticação de um utilizador em Azure AD][Contact]

* Se o campo **Phone** estiver povoado e o **telemóvel** estiver ativado na política sspr, o utilizador vê esse número na página de registo de redefinição da palavra-passe e durante o fluxo de trabalho de redefinição da palavra-passe.
* O campo **telefónico alternativo** não é utilizado para redefinir palavras-passe.
* Se o campo **de e-mail** estiver povoado e o **Email** estiver ativado na política sspr, o utilizador vê esse e-mail na página de registo de redefinição da palavra-passe e durante o fluxo de trabalho de redefinição da palavra-passe.
* Se o campo de **e-mail Alternativo** estiver povoado e o **Email** estiver ativado na política de SSPR, o utilizador **não** verá esse e-mail na página de registo de redefinição de palavra-passe, mas vê-o durante o fluxo de trabalho de redefinição da palavra-passe.

## <a name="security-questions-and-answers"></a>Perguntas e respostas de segurança

As questões e respostas de segurança são armazenadas de forma segura no seu inquilino Azure AD e só são acessíveis aos utilizadores através do portal de [registo SSPR](https://aka.ms/ssprsetup). Os administradores não podem ver, definir ou modificar o conteúdo das perguntas e respostas de outros utilizadores.

## <a name="what-happens-when-a-user-registers"></a>O que acontece quando um utilizador se regista

Quando um utilizador se regista, a página de registo define os seguintes campos:

* **Telefone de autenticação**
* **Email de autenticação**
* **Perguntas e Respostas de Segurança**

Se forneceu um valor para **telemóvel** ou **e-mail alternativo,** os utilizadores podem utilizar imediatamente esses valores para redefinir as suas palavras-passe, mesmo que não tenham registado o serviço. Além disso, os utilizadores vêem esses valores quando se registam pela primeira vez, e podem modificá-los se quiserem. Depois de se registarem com sucesso, estes valores são persistidos nos campos de **Autenticação Do Telefone** e do Email de **Autenticação,** respectivamente.

## <a name="set-and-read-the-authentication-data-through-powershell"></a>Definir e ler os dados de autenticação através do PowerShell

Os seguintes campos podem ser definidos através do PowerShell:

* **E-mail alternativo**
* **Número de telemóvel**
* **Telefone de escritório**: Só pode ser definido se não estiver sincronizando com um diretório no local

### <a name="use-powershell-version-1"></a>Utilizar a versão PowerShell 1

Para começar, precisa [de descarregar e instalar o módulo PowerShell Da AD Azure](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule). Depois de o ter instalado, pode utilizar os passos que se seguem para configurar cada campo.

#### <a name="set-the-authentication-data-with-powershell-version-1"></a>Detete os dados de autenticação com a versão 1 da PowerShell

```PowerShell
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 1234567890" -PhoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-1"></a>Leia os dados de autenticação com a versão 1 da PowerShell

```PowerShell
Connect-MsolService

Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber

Get-MsolUser | select DisplayName,UserPrincipalName,AlternateEmailAddresses,MobilePhone,PhoneNumber | Format-Table
```

#### <a name="read-the-authentication-phone-and-authentication-email-options"></a>Leia as opções de email do Telefone de Autenticação e autenticação

Para ler o **Telefone de Autenticação** e o Email de **Autenticação** quando utilizar a versão PowerShell 1, utilize os seguintes comandos:

```PowerShell
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

### <a name="use-powershell-version-2"></a>Utilizar a versão PowerShell 2

Para começar, precisa de descarregar e instalar o módulo PowerShell da versão 2 da [Versão AD Azure.](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0) Depois de o ter instalado, pode utilizar os passos que se seguem para configurar cada campo.

Para instalar rapidamente a partir de versões recentes da PowerShell que suportam o Módulo de Instalação, execute os seguintes comandos. (A primeira linha verifica se o módulo já está instalado.)

```PowerShell
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

#### <a name="set-the-authentication-data-with-powershell-version-2"></a>Detete os dados de autenticação com a versão 2 da PowerShell

```PowerShell
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 2345678901"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 1234567890"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 1234567890" -TelephoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-2"></a>Leia os dados de autenticação com a versão 2 da PowerShell

```PowerShell
Connect-AzureAD

Get-AzureADUser -ObjectID user@domain.com | select otherMails
Get-AzureADUser -ObjectID user@domain.com | select Mobile
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber

Get-AzureADUser | select DisplayName,UserPrincipalName,otherMails,Mobile,TelephoneNumber | Format-Table
```

## <a name="next-steps"></a>Passos seguintes

* [Como posso concluir uma implementação com êxito da SSPR?](howto-sspr-deployment.md)
* [Redefinir ou alterar a sua palavra-passe](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registar-se na reposição personalizada de palavra-passe](../user-help/active-directory-passwords-reset-register.md)
* [Tem alguma pergunta sobre licenciamento?](concept-sspr-licensing.md)
* [Que métodos de autenticação estão disponíveis para os utilizadores?](concept-sspr-howitworks.md#authentication-methods)
* [Quais são as opções de política da SSPR?](concept-sspr-policy.md)
* [O que é a repetição de escrita de palavras-passe e por que me deve interessar?](howto-sspr-writeback.md)
* [Como posso comunicar a atividade da SSPR?](howto-sspr-reporting.md)
* [Quais são todas as opções na SSPR e o que significam?](concept-sspr-howitworks.md)
* [Acho que algo está partido. Como posso resolver problemas com a SSPR?](active-directory-passwords-troubleshoot.md)
* [Tenho uma pergunta que ainda não foi abordada](active-directory-passwords-faq.md)

[Contact]: ./media/howto-sspr-authenticationdata/user-authentication-contact-info.png "Administradores globais podem modificar informações de contacto de autenticação de um utilizador"
