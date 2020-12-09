---
title: Pré-povoar informações de contacto para reset de senha de autosserviço - Azure Ative Directory
description: Saiba como pré-povoar informações de contacto para os utilizadores do Azure Ative Directory self-service reset (SSPR) para que possam utilizar a funcionalidade sem completar um processo de registo.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 10/05/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ed93944c2a94370250fb75a679c1ac37a2a8418c
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861158"
---
# <a name="pre-populate-user-authentication-contact-information-for-azure-active-directory-self-service-password-reset-sspr"></a>Pré-povoar informações de contacto de autenticação do utilizador para reposição da palavra-passe de autosserviço do Azure Ative Directory (SSPR)

Para utilizar o Azure Ative Directory (Azure AD) redefinindo a palavra-passe de autosserviço (SSPR), devem estar presentes dados de contacto de autenticação para um utilizador. Algumas organizações têm utilizadores que registam os seus dados de autenticação. Outras organizações preferem sincronizar a partir de dados de autenticação que já existem nos Serviços de Domínio do Diretório Ativo (DS AD). Estes dados sincronizados são disponibilizados à Azure AD e SSPR sem que seja necessária interação do utilizador. Quando os utilizadores precisam de alterar ou redefinir a sua palavra-passe, podem fazê-lo mesmo que não tenham registado previamente as suas informações de contacto.

Pode pré-povoar informações de contacto de autenticação se cumprir os seguintes requisitos:

* Formata os dados no seu diretório no local.
* Você configura a [Azure AD Connect](../hybrid/how-to-connect-install-express.md) para o seu inquilino AD AZure.

Os números de telefone devem estar no formato *+CountryCode PhoneNumber,* tais como *+1 4251234567*.

> [!NOTE]
> Deve haver um espaço entre o código do país e o número de telefone.
>
> O reset da palavra-passe não suporta extensões de telefone. Mesmo no formato *+1 4251234567X12345,* as extensões são removidas antes da chamada ser feita.

## <a name="fields-populated"></a>Campos povoados

Se utilizar as definições predefinidas no Azure AD Connect, são feitos os seguintes mapeamentos para preencher informações de contacto de autenticação para SSPR:

| Active Directory no local | Azure AD     |
|------------------------------|--------------|
| telephoneNumber              | Telefone do escritório |
| dispositivo móvel                       | Número de telemóvel |

Depois de um utilizador verificar o seu número de telemóvel, o campo *Telefone* em **Autenticação contact info** em Azure AD também é preenchido com esse número.

## <a name="authentication-contact-info"></a>Informação de contacto de autenticação

Na página de **métodos de autenticação** para um utilizador Azure AD no portal Azure, um Administrador Global pode definir manualmente as informações de contacto de autenticação. Pode rever os métodos existentes na secção *métodos de autenticação utilizável* ou **+Adicionar métodos de autenticação,** como mostra o seguinte exemplo de screenshot:

:::image type="content" source="media/howto-sspr-authenticationdata/user-authentication-contact-info.png" alt-text="Gerir métodos de autenticação a partir do portal Azure":::

Aplicam-se as seguintes considerações a esta informação de contacto de autenticação:

* Se o campo *Telefone* estiver preenchido e o *telemóvel* estiver ativado na política SSPR, o utilizador vê esse número na página de registo de redefinição da palavra-passe e durante o fluxo de trabalho de redefinição da palavra-passe.
* Se o campo *de Email* estiver preenchido e o *Email* estiver ativado na política SSPR, o utilizador vê esse e-mail na página de registo de redefinição de palavra-passe e durante o fluxo de trabalho de redefinição da palavra-passe.

## <a name="security-questions-and-answers"></a>Perguntas e respostas de segurança

As questões e respostas de segurança são armazenadas de forma segura no seu inquilino Azure AD e só são acessíveis aos utilizadores através do [portal de registo SSPR](https://aka.ms/ssprsetup). Os administradores não conseguem ver, definir ou modificar o conteúdo das perguntas e respostas de outros utilizadores.

## <a name="what-happens-when-a-user-registers"></a>O que acontece quando um utilizador se regista

Quando um utilizador se regista, a página de registo define os seguintes campos:

* **Telefone de autenticação**
* **E-mail de autenticação**
* **Perguntas e Respostas de Segurança**

Se forneceu um valor para *telemóvel* ou *e-mail alternativo,* os utilizadores podem utilizar imediatamente esses valores para redefinir as suas palavras-passe, mesmo que não tenham registado o serviço.

Os utilizadores também vêem esses valores quando se registam pela primeira vez, e podem modificá-los se quiserem. Depois de se registarem com sucesso, estes valores são persistidos nos campos *de Email de Autenticação* e *Autenticação,* respectivamente.

## <a name="set-and-read-the-authentication-data-through-powershell"></a>Desajuste e leia os dados de autenticação através do PowerShell

Os seguintes campos podem ser definidos através do PowerShell:

* *E-mail alternativo*
* *Número de telemóvel*
* *Telefone do escritório*
    * Só pode ser definido se não estiver sincronizado com um diretório no local.

> [!IMPORTANT]
> Há uma conhecida falta de paridade nas características de comando entre PowerShell v1 e PowerShell v2. O [Microsoft Graph REST API (beta) para métodos de autenticação](/graph/api/resources/authenticationmethods-overview) é o foco atual de engenharia para proporcionar interação moderna.

### <a name="use-powershell-version-1"></a>Utilizar a versão 1 do PowerShell

Para começar, [descarregue e instale o módulo Azure AD PowerShell](/previous-versions/azure/jj151815(v=azure.100)#bkmk_installmodule). Depois de instalado, utilize os seguintes passos para configurar cada campo.

#### <a name="set-the-authentication-data-with-powershell-version-1"></a>Desa estação os dados de autenticação com a versão 1 do PowerShell

```PowerShell
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 4251234567"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 4252345678"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 4251234567" -PhoneNumber "+1 4252345678"
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

Para começar, [descarregue e instale o módulo PowerShell da versão AD AZure](/powershell/module/azuread/).

Para instalar rapidamente a partir de versões recentes do PowerShell que suportam `Install-Module` , executar os seguintes comandos. A primeira linha verifica se o módulo já está instalado:

```PowerShell
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

Depois de instalado o módulo, utilize os seguintes passos para configurar cada campo.

#### <a name="set-the-authentication-data-with-powershell-version-2"></a>Desa estação os dados de autenticação com a versão 2 do PowerShell

```PowerShell
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 4251234567"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 4252345678"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 4251234567" -TelephoneNumber "+1 4252345678"
```

#### <a name="read-the-authentication-data-with-powershell-version-2"></a>Leia os dados de autenticação com a versão 2 do PowerShell

```PowerShell
Connect-AzureAD

Get-AzureADUser -ObjectID user@domain.com | select otherMails
Get-AzureADUser -ObjectID user@domain.com | select Mobile
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber

Get-AzureADUser | select DisplayName,UserPrincipalName,otherMails,Mobile,TelephoneNumber | Format-Table
```

## <a name="next-steps"></a>Passos seguintes

Uma vez que as informações de contacto de autenticação são pré-povoadas para os utilizadores, complete o seguinte tutorial para permitir o reset da palavra-passe de autosserviço:

> [!div class="nextstepaction"]
> [Ativar o reset da palavra-passe de autosserviço AZure AD](tutorial-enable-sspr.md)
