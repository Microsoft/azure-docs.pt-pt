---
title: Requisitos de dados Azure AD SSPR - Azure Ative Directory
description: Requisitos de dados para o reset da palavra-passe de autosserviço AZure AD e como satisfazê-los
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 12/09/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42f7e120745357d3bd5735cca568bdd6971ea061
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80652363"
---
# <a name="deploy-password-reset-without-requiring-end-user-registration"></a>Implementar a palavra-passe reiniciada sem exigir o registo do utilizador final

Para implementar o Azure Ative Directory (Azure AD) redefinindo a palavra-passe de autosserviço (SSPR), os dados de autenticação devem estar presentes. Algumas organizações têm os seus utilizadores a introduzir os seus dados de autenticação. Outras organizações preferem sincronizar com dados que já existem no Ative Directory. Estes dados sincronizados são disponibilizados à Azure AD e SSPR sem exigir a interação do utilizador se cumprir os seguintes requisitos:

* Formate adequadamente os dados no seu diretório no local.
* Configure [Azure AD Connect utilizando as definições expressas](../hybrid/how-to-connect-install-express.md).

Para funcionar corretamente, os números de telefone devem estar no formato *+CountryCode PhoneNumber,* por exemplo, +1 4255551234.

> [!NOTE]
> Tem de haver um espaço entre o código do país e o número de telefone.
>
> O reset da palavra-passe não suporta extensões de telefone. Mesmo no formato +1 4255551234X12345, as extensões são removidas antes da chamada ser feita.

## <a name="fields-populated"></a>Campos povoados

Se utilizar as definições predefinidas no Azure AD Connect, são feitos os seguintes mapeamentos:

| Active Directory no local | Azure AD |
| --- | --- |
| número de telefone | Telefone do escritório |
| dispositivo móvel | Número de telemóvel |

Depois de um utilizador verificar o seu número de telemóvel, o campo *Telefone* em **Autenticação contact info** em Azure AD também é preenchido com esse número.

## <a name="authentication-contact-info"></a>Informação de contacto de autenticação

Na página de **métodos de autenticação** para um utilizador Azure AD no portal Azure, um Administrador Global pode definir manualmente as informações de contacto de autenticação, como mostra o seguinte exemplo de imagem:

![Informação de contacto de autenticação em um utilizador em Azure AD][Contact]

* Se o campo **Telefone** estiver preenchido e o **telemóvel** estiver ativado na política SSPR, o utilizador vê esse número na página de registo de redefinição da palavra-passe e durante o fluxo de trabalho de redefinição da palavra-passe.
* O campo **de telefone alternativo** não é utilizado para reset de senha.
* Se o campo **de Email** estiver preenchido e o **Email** estiver ativado na política SSPR, o utilizador vê esse e-mail na página de registo de redefinição de palavra-passe e durante o fluxo de trabalho de redefinição da palavra-passe.
* Se o campo **de e-mail Alternativo** estiver preenchido e o **Email** estiver ativado na política SSPR, o utilizador **não** verá esse e-mail na página de registo de redefinição de palavra-passe, mas vê-o durante o fluxo de trabalho de redefinição da palavra-passe.

## <a name="security-questions-and-answers"></a>Perguntas e respostas de segurança

As questões e respostas de segurança são armazenadas de forma segura no seu inquilino Azure AD e só são acessíveis aos utilizadores através do [portal de registo SSPR](https://aka.ms/ssprsetup). Os administradores não conseguem ver, definir ou modificar o conteúdo das perguntas e respostas de outros utilizadores.

## <a name="what-happens-when-a-user-registers"></a>O que acontece quando um utilizador se regista

Quando um utilizador se regista, a página de registo define os seguintes campos:

* **Telefone de autenticação**
* **E-mail de autenticação**
* **Perguntas e Respostas de Segurança**

Se tiver fornecido um valor para **telemóvel** ou **e-mail alternativo,** os utilizadores podem utilizar imediatamente esses valores para redefinir as suas palavras-passe, mesmo que não tenham registado o serviço. Além disso, os utilizadores vêem esses valores quando se registam pela primeira vez, e podem modificá-los se quiserem. Depois de se registarem com sucesso, estes valores são percamados nos campos **de Email de Autenticação** e **Autenticação,** respectivamente.

## <a name="set-and-read-the-authentication-data-through-powershell"></a>Desajuste e leia os dados de autenticação através do PowerShell

Os seguintes campos podem ser definidos através do PowerShell:

* **E-mail alternativo**
* **Número de telemóvel**
* **Telefone de escritório**: Só pode ser definido se não estiver sincronizado com um diretório no local

### <a name="use-powershell-version-1"></a>Utilizar a versão 1 do PowerShell

Para começar, é necessário [descarregar e instalar o módulo Azure AD PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule). Depois de o instalar, pode utilizar os passos que se seguem para configurar cada campo.

#### <a name="set-the-authentication-data-with-powershell-version-1"></a>Desa estação os dados de autenticação com a versão 1 do PowerShell

```PowerShell
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 1234567890" -PhoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-1"></a>Leia os dados de autenticação com a versão 1 do PowerShell

```PowerShell
Connect-MsolService

Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber

Get-MsolUser | select DisplayName,UserPrincipalName,AlternateEmailAddresses,MobilePhone,PhoneNumber | Format-Table
```

#### <a name="read-the-authentication-phone-and-authentication-email-options"></a>Leia as opções de Telefone de Autenticação e Autenticação

Para ler o **Telefone de Autenticação** e **O Email de Autenticação** quando utilizar a versão 1 do PowerShell, utilize os seguintes comandos:

```PowerShell
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

### <a name="use-powershell-version-2"></a>Utilizar a versão 2 do PowerShell

Para começar, é necessário [descarregar e instalar o módulo PowerShell da versão AD Azure.](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0) Depois de o instalar, pode utilizar os passos que se seguem para configurar cada campo.

Para instalar rapidamente a partir de versões recentes do PowerShell que suportam o Módulo de Instalação, execute os seguintes comandos. (A primeira linha verifica se o módulo já está instalado.)

```PowerShell
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

#### <a name="set-the-authentication-data-with-powershell-version-2"></a>Desa estação os dados de autenticação com a versão 2 do PowerShell

```PowerShell
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 2345678901"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 1234567890"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 1234567890" -TelephoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-2"></a>Leia os dados de autenticação com a versão 2 do PowerShell

```PowerShell
Connect-AzureAD

Get-AzureADUser -ObjectID user@domain.com | select otherMails
Get-AzureADUser -ObjectID user@domain.com | select Mobile
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber

Get-AzureADUser | select DisplayName,UserPrincipalName,otherMails,Mobile,TelephoneNumber | Format-Table
```

## <a name="next-steps"></a>Próximos passos

* [Como posso concluir uma implementação com êxito da SSPR?](howto-sspr-deployment.md)
* [Redefinir ou alterar a sua palavra-passe](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registar-se na reposição personalizada de palavra-passe](../user-help/active-directory-passwords-reset-register.md)
* [Tem alguma pergunta sobre licenciamento?](concept-sspr-licensing.md)
* [Que métodos de autenticação estão disponíveis para os utilizadores?](concept-sspr-howitworks.md#authentication-methods)
* [Quais são as opções de política da SSPR?](concept-sspr-policy.md)
* [O que é a repetição de escrita de palavras-passe e por que me deve interessar?](howto-sspr-writeback.md)
* [Como posso comunicar a atividade da SSPR?](howto-sspr-reporting.md)
* [Quais são todas as opções na SSPR e o que significam?](concept-sspr-howitworks.md)
* [Acho que algo está partido. Como é que resolvo a SSPR?](active-directory-passwords-troubleshoot.md)
* [Tenho uma pergunta que ainda não foi abordada](active-directory-passwords-faq.md)

[Contact]: ./media/howto-sspr-authenticationdata/user-authentication-contact-info.png "Os administradores globais podem modificar as informações de contacto de autenticação de um utilizador"
