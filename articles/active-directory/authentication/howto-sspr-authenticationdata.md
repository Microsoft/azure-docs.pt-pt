---
title: Requisitos de dados do Azure AD SSPR-Azure Active Directory
description: Requisitos de dados para redefinição de senha de autoatendimento do Azure AD e como atender a eles
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1c00d0f4ba365442762df6e041f02ea0a39f099
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74847308"
---
# <a name="deploy-password-reset-without-requiring-end-user-registration"></a>Implantar a redefinição de senha sem exigir o registro do usuário final

Para Azure Active Directory implantar a SSPR (redefinição de senha de autoatendimento) do Microsoft Azure AD), os dados de autenticação precisam estar presentes. Algumas organizações têm seus usuários para inserir seus próprios dados de autenticação. Mas muitas organizações preferem sincronizar com os dados que já existem no Active Directory. Os dados sincronizados são disponibilizados para o Azure AD e o SSPR sem a necessidade de interação do usuário se você:

* Formate corretamente os dados em seu diretório local.
* Configure [Azure ad Connect usando as configurações expressas](../hybrid/how-to-connect-install-express.md).

Para funcionar corretamente, os números de telefone devem estar no formato *+ CountryCode PhoneNumber*, por exemplo, + 1 4255551234.

> [!NOTE]
> Precisa haver um espaço entre o código do país e o número de telefone.
>
> A redefinição de senha não dá suporte a extensões de telefone. Mesmo no formato + 1 4255551234X12345, as extensões são removidas antes de a chamada ser colocada.

## <a name="fields-populated"></a>Campos preenchidos

Se você usar as configurações padrão no Azure AD Connect, os seguintes mapeamentos serão feitos:

| Active Directory no local | Azure AD |
| --- | --- |
| telephoneNumber | Telefone do escritório |
| móvel | Número de telemóvel |

Depois que um usuário verificar seu número de telefone celular, o campo telefone em informações de contato de autenticação no Azure AD também será preenchido com esse número.

## <a name="authentication-contact-info"></a>Informações de contato de autenticação

Um administrador global pode definir manualmente as informações de contato de autenticação para um usuário, conforme exibido na captura de tela a seguir.

![Informações de contato de autenticação em um usuário no Azure AD][Contact]

Se o campo telefone for preenchido e o telefone celular estiver habilitado na política SSPR, o usuário verá esse número na página de registro de redefinição de senha e durante o fluxo de trabalho de redefinição de senha.

O campo telefone alternativo não é usado para redefinição de senha.

Se o campo de email for preenchido e o email estiver habilitado na política SSPR, o usuário verá esse email na página de registro de redefinição de senha e durante o fluxo de trabalho de redefinição de senha.

Se o campo email alternativo for preenchido e o email estiver habilitado na política SSPR, o usuário **não** verá esse email na página de registro de redefinição de senha, mas ele o verá durante o fluxo de trabalho de redefinição de senha.

## <a name="security-questions-and-answers"></a>Perguntas e respostas de segurança

As perguntas e respostas de segurança são armazenadas com segurança no seu locatário do Azure AD e só podem ser acessadas por usuários por meio do [portal de registro do SSPR](https://aka.ms/ssprsetup). Os administradores não podem ver, definir ou modificar o conteúdo das perguntas e respostas de outros usuários.

## <a name="what-happens-when-a-user-registers"></a>O que acontece quando um usuário registra

Quando um usuário é registrado, a página de registro define os seguintes campos:

* **Telefone de autenticação**
* **Email de autenticação**
* **Perguntas e respostas de segurança**

Se você tiver fornecido um valor para **celular ou** **email alternativo**, os usuários poderão usar esses valores imediatamente para redefinir suas senhas, mesmo que não tenham se registrado no serviço. Além disso, os usuários veem esses valores quando se registram pela primeira vez e podem modificá-los se desejarem. Depois que eles se registrarem com êxito, esses valores serão persistidos nos campos **telefone de autenticação** e **email de autenticação** , respectivamente.

## <a name="set-and-read-the-authentication-data-through-powershell"></a>Definir e ler os dados de autenticação por meio do PowerShell

Os campos a seguir podem ser definidos por meio do PowerShell:

* **Email alternativo**
* **Número de telemóvel**
* **Telefone comercial**: só poderá ser definido se você não estiver sincronizando com um diretório local

### <a name="use-powershell-version-1"></a>Usar o PowerShell versão 1

Para começar, você precisa [baixar e instalar o módulo do PowerShell do Azure ad](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule). Depois de instalá-lo, você pode usar as etapas a seguir para configurar cada campo.

#### <a name="set-the-authentication-data-with-powershell-version-1"></a>Definir os dados de autenticação com o PowerShell versão 1

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

#### <a name="read-the-authentication-phone-and-authentication-email-options"></a>Ler as opções de telefone de autenticação e email de autenticação

Para ler o **telefone de autenticação** e o **email de autenticação** ao usar o PowerShell versão 1, use os seguintes comandos:

```PowerShell
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

### <a name="use-powershell-version-2"></a>Usar o PowerShell versão 2

Para começar, você precisa [baixar e instalar o módulo PowerShell da versão 2 do Azure ad](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0). Depois de instalá-lo, você pode usar as etapas a seguir para configurar cada campo.

Para instalar rapidamente de versões recentes do PowerShell que oferecem suporte a install-Module, execute os comandos a seguir. (A primeira linha verifica se o módulo já está instalado.)

```PowerShell
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

#### <a name="set-the-authentication-data-with-powershell-version-2"></a>Definir os dados de autenticação com o PowerShell versão 2

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

## <a name="next-steps"></a>Passos seguintes

* [Como posso concluir uma implementação com êxito da SSPR?](howto-sspr-deployment.md)
* [Repor ou alterar a palavra-passe](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registar-se na reposição personalizada de palavra-passe](../user-help/active-directory-passwords-reset-register.md)
* [Tem alguma pergunta sobre licenciamento?](concept-sspr-licensing.md)
* [Que métodos de autenticação estão disponíveis para os utilizadores?](concept-sspr-howitworks.md#authentication-methods)
* [Quais são as opções de política da SSPR?](concept-sspr-policy.md)
* [O que é a repetição de escrita de palavras-passe e por que me deve interessar?](howto-sspr-writeback.md)
* [Como posso comunicar a atividade da SSPR?](howto-sspr-reporting.md)
* [Quais são todas as opções na SSPR e o que significam?](concept-sspr-howitworks.md)
* [Acho que algo está quebrado. Como fazer solucionar problemas de SSPR?](active-directory-passwords-troubleshoot.md)
* [Tenho uma pergunta que ainda não foi abordada](active-directory-passwords-faq.md)

[Contact]: ./media/howto-sspr-authenticationdata/user-authentication-contact-info.png "Os administradores globais podem modificar as informações de contato de autenticação de um usuário"
