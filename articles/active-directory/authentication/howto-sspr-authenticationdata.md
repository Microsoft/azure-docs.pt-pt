---
title: Requisitos de dados do Azure AD SSPR - Azure Active Directory
description: Requisitos de dados para a palavra-passe self-service do Azure AD como satisfazê-los e de reposição
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5a0d7edb6c7faafcad55e827c2d9e3d2eeea40f5
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58371371"
---
# <a name="deploy-password-reset-without-requiring-end-user-registration"></a>Implementar sem a necessidade de registo do utilizador final de reposição de palavra-passe

Para implementar a reposição de palavra-passe self-service (SSPR) do Azure Active Directory (Azure AD), dados de autenticação têm de estar presente. Algumas organizações têm seus usuários, introduza os seus próprios dados de autenticação. Mas muitas organizações preferem sincronizar com os dados que já existe no Active Directory. Os dados sincronizados são disponibilizados para o Azure AD e SSPR sem a necessidade de interação do usuário se:

* Formate corretamente os dados no seu diretório no local.
* Configurar [do Azure AD Connect com as definições express](../hybrid/how-to-connect-install-express.md).

Para funcionarem corretamente, os números de telefone tem de ser no formato *+ indicativo do país PhoneNumber*, por exemplo, + 1 4255551234.

> [!NOTE]
> Deve haver um espaço entre o código de país e o número de telefone.
>
> Reposição de palavra-passe não suporta extensões telefónicas. Até mesmo no formato + 1 4255551234 X 12345, as extensões são removidas antes da chamada é feita.

## <a name="fields-populated"></a>Campos preenchidos

Se utilizar as predefinições no Azure AD Connect, os seguintes mapeamentos são efetuados:

| Active Directory no local | Azure AD |
| --- | --- |
| telephoneNumber | Telefone do escritório |
| móvel | Número de telemóvel |

Quando o utilizador verifica o número de telemóvel, no campo de telefone em informações de contacto de autenticação no Azure AD também será preenchido com esse número.

## <a name="authentication-contact-info"></a>Informação de contacto de autenticação

Um Administrador Global pode definir manualmente as informações de contacto de autenticação para um utilizador, tal como apresentado na seguinte captura de ecrã.

![Informações sobre um usuário de contacto de autenticação no Azure AD][Contact]

Se o campo de telefone é preenchido e celular está ativado na política de SSPR, o utilizador irá ver que o número na página de registo de reposição de palavra-passe e durante a palavra-passe reposta fluxo de trabalho.

O campo de telefone alternativo não é utilizado para a reposição de palavra-passe.

Se o campo de E-Mail é preenchido e E-mail está ativado na política de SSPR, o utilizador irá ver que envie um e-mail na página de registo de reposição de palavra-passe durante a palavra-passe de reposição de fluxo de trabalho.

Se o campo de correio eletrónico alternativo é preenchido e E-mail está ativado na política de SSPR, o utilizador irá **não** ver que a página de registo de reposição de e-mail na palavra-passe, mas irão vê-lo durante a palavra-passe de reposição de fluxo de trabalho.

## <a name="security-questions-and-answers"></a>Perguntas de segurança e respostas

As perguntas de segurança e respostas são armazenadas em segurança no seu inquilino do Azure AD e só podem ser acedidas por utilizadores através da [portal de registo SSPR](https://aka.ms/ssprsetup). Os administradores não é possível ver, definir ou modificar o conteúdo de perguntas e respostas dos utilizadores de outra.

## <a name="what-happens-when-a-user-registers"></a>O que acontece quando um utilizador se regista

Quando um utilizador se regista, a página de registo define os seguintes campos:

* **Telefone de autenticação**
* **E-Mail de autenticação**
* **Perguntas de segurança e respostas**

Se lhe fornecido um valor para **número de telemóvel** ou **e-mail alternativo**, os utilizadores imediatamente podem utilizar esses valores para repor as palavras-passe, mesmo se elas ainda não está registado para o serviço. Além disso, os utilizadores veem esses valores quando eles se registram pela primeira vez, e eles poderão modificá-los se desejarem. Depois de serem registrados com êxito, estes valores irão manter-se na **telefone de autenticação** e **E-Mail de autenticação** campos, respectivamente.

## <a name="set-and-read-the-authentication-data-through-powershell"></a>Definir e ler os dados de autenticação através do PowerShell

Os seguintes campos podem ser definidos através do PowerShell:

* **Correio eletrónico alternativo**
* **Número de telemóvel**
* **Telefone do escritório**: Só pode ser definida se não estiver sincronizando com um diretório no local

### <a name="use-powershell-version-1"></a>Utilizar o PowerShell versão 1

Para começar, precisa [transferir e instalar o módulo Azure AD PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule). Depois de ter instalado, pode utilizar os passos seguintes para configurar cada campo.

#### <a name="set-the-authentication-data-with-powershell-version-1"></a>Defina os dados de autenticação com o PowerShell versão 1

```PowerShell
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 1234567890" -PhoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-1"></a>Ler os dados de autenticação com o PowerShell versão 1

```PowerShell
Connect-MsolService

Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber

Get-MsolUser | select DisplayName,UserPrincipalName,AlternateEmailAddresses,MobilePhone,PhoneNumber | Format-Table
```

#### <a name="read-the-authentication-phone-and-authentication-email-options"></a>Leia as opções de telefone de autenticação e o E-Mail de autenticação

Para ler os **telefone de autenticação** e **E-Mail de autenticação** quando utilizar o PowerShell versão 1, utilize os seguintes comandos:

```PowerShell
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

### <a name="use-powershell-version-2"></a>Utilizar o PowerShell versão 2

Para começar, precisa [transferir e instalar o módulo do PowerShell versão 2 do Azure AD](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0). Depois de ter instalado, pode utilizar os passos seguintes para configurar cada campo.

Para instalar rapidamente a partir de versões recentes do PowerShell que suportam o Install-Module, execute os seguintes comandos. (A primeira linha verifica se o módulo já está instalado.)

```PowerShell
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

#### <a name="set-the-authentication-data-with-powershell-version-2"></a>Defina os dados de autenticação com o PowerShell versão 2

```PowerShell
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 2345678901"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 1234567890"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 1234567890" -TelephoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-2"></a>Ler os dados de autenticação com o PowerShell versão 2

```PowerShell
Connect-AzureAD

Get-AzureADUser -ObjectID user@domain.com | select otherMails
Get-AzureADUser -ObjectID user@domain.com | select Mobile
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber

Get-AzureADUser | select DisplayName,UserPrincipalName,otherMails,Mobile,TelephoneNumber | Format-Table
```

## <a name="next-steps"></a>Passos Seguintes

* [Como posso concluir uma implementação com êxito da SSPR?](howto-sspr-deployment.md)
* [Repor ou alterar a palavra-passe](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registar-se na reposição personalizada de palavra-passe](../user-help/active-directory-passwords-reset-register.md)
* [Tem alguma pergunta sobre licenciamento?](concept-sspr-licensing.md)
* [Que métodos de autenticação estão disponíveis para os utilizadores?](concept-sspr-howitworks.md#authentication-methods)
* [Quais são as opções de política da SSPR?](concept-sspr-policy.md)
* [O que é a repetição de escrita de palavras-passe e por que me deve interessar?](howto-sspr-writeback.md)
* [Como posso comunicar a atividade da SSPR?](howto-sspr-reporting.md)
* [Quais são todas as opções na SSPR e o que significam?](concept-sspr-howitworks.md)
* [Creio que algo está a funcionar incorretamente. Como posso resolver problemas da SSPR?](active-directory-passwords-troubleshoot.md)
* [Tenho uma pergunta que ainda não foi abordada](active-directory-passwords-faq.md)

[Contact]: ./media/howto-sspr-authenticationdata/user-authentication-contact-info.png "Os administradores globais podem modificar informações de contacto de autenticação de um utilizador"
