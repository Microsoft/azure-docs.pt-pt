---
title: Políticas de reset de palavra-passe de autosserviço - Azure Ative Directory
description: Conheça as diferentes opções de política de redefinição de senha de autosserviço do Azure Ative Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/05/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.custom: contperfq4
ms.openlocfilehash: 344fee9482cd935375d25fab80b1f365d72586f8
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2020
ms.locfileid: "96743671"
---
# <a name="password-policies-and-account-restrictions-in-azure-active-directory"></a>Políticas de palavra-passe e restrições de conta no Azure Ative Directory

No Azure Ative Directory (Azure AD), existe uma política de palavra-passe que define configurações como a complexidade, comprimento ou idade da palavra-passe. Há também uma política que define caracteres e comprimentos aceitáveis para nomes de utilizadores.

Quando a palavra-passe de autosserviço é utilizada para alterar ou redefinir uma palavra-passe em AD Azure, a política de palavra-passe é verificada. Se a palavra-passe não cumprir os requisitos da apólice, o utilizador é solicitado a tentar novamente. Os administradores da Azure têm algumas restrições na utilização de SSPR que são diferentes das contas regulares do utilizador.

Este artigo descreve as definições de política de palavra-passe e os requisitos de complexidade associados às contas de utilizador no seu inquilino AD Azure e como pode utilizar o PowerShell para verificar ou definir definições de validade da palavra-passe.

## <a name="username-policies"></a><a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>Políticas de nome de utilizador

Todas as contas que se inscrevem no Azure AD devem ter um valor único de atributo de utilizador principal (UPN) associado à sua conta. Em ambientes híbridos com um ambiente de serviços de domínio de diretório ativo (AD DS) sincronizado com Azure AD usando Azure AD Connect, por padrão, o Azure AD UPN é definido para o UPN on-prem.

O quadro que se segue descreve as políticas de nome de utilizador que se aplicam tanto às contas AD DS no local que são sincronizadas com a AZure AD, como para contas de utilizadores criadas diretamente em AD Azure:

| Propriedade | Requisitos do Nome UserPrincipal |
| --- | --- |
| Caracteres permitidos |<ul> <li>A – Z</li> <li>a - z</li><li>0 – 9</li> <li> ' \. - \_ ! \# ^ \~</li></ul> |
| Caracteres não são permitidos |<ul> <li>Qualquer " \@ \" personagem que não está separando o nome de utilizador do domínio.</li> <li>Não pode conter um caráter de época "." imediatamente antes do \@ \" " símbolo</li></ul> |
| Restrições de comprimento |<ul> <li>O comprimento total não deve exceder 113 caracteres</li><li>Pode haver até 64 caracteres antes do " \@ \" símbolo</li><li>Pode haver até 48 caracteres após o " \@ \" símbolo</li></ul> |

## <a name="azure-ad-password-policies"></a><a name="password-policies-that-only-apply-to-cloud-user-accounts"></a>Políticas de senha AD AZure

Uma política de palavra-passe é aplicada a todas as contas de utilizador que são criadas e geridas diretamente no AZure AD. Algumas destas definições de política de palavra-passe não podem ser modificadas, embora possa [configurar senhas proibidas personalizadas para proteção de password AD AD](tutorial-configure-custom-password-protection.md) ou parâmetros de bloqueio de conta.

Por predefinição, uma conta é bloqueada após 10 tentativas de inscrição falhadas com a senha errada. O utilizador está bloqueado por um minuto. Outras tentativas de entrada incorretas bloqueiam o utilizador durante o período de tempo. [O bloqueio inteligente](howto-password-smart-lockout.md) rastreia os últimos três haques de palavra-passe estragados para evitar o incremento do contador de bloqueio para a mesma palavra-passe. Se alguém introduzir a mesma palavra-passe errada várias vezes, este comportamento não fará com que a conta bloqueie. Pode definir o limiar de bloqueio inteligente e a duração.

A política de palavra-passe AD AD não se aplica às contas de utilizador sincronizadas a partir de um ambiente AD DS no local utilizando o Azure AD Connect, a menos que ative *EnforceCloudPasswordPolicyForPasswordSyncedUsers*.

São definidas as seguintes opções de política de palavra-passe AZure AD. A menos que seja notado, não pode alterar estas definições:

| Propriedade | Requisitos |
| --- | --- |
| Caracteres permitidos |<ul><li>A – Z</li><li>a - z</li><li>0 – 9</li> <li># $ % ^ & * - _ ! + = { { } &#124; \ : '' . ? / \` ~ " ( ) ;</li> <li>espaço em branco</li></ul> |
| Caracteres não são permitidos | Caracteres unicódigo. |
| Restrições de senha |<ul><li>Um mínimo de 8 caracteres e um máximo de 256 caracteres.</li><li>Requer três de quatro dos seguintes:<ul><li>Personagens minúsculos.</li><li>Personagens maiúsculas.</li><li>Números (0-9).</li><li>Símbolos (ver as restrições de senha anteriores).</li></ul></li></ul> |
| Duração da expiração da palavra-passe (idade máxima da senha) |<ul><li>Valor predefinido: **90** dias.</li><li>O valor é configurável utilizando o `Set-MsolPasswordPolicy` cmdlet do Módulo de Diretório Ativo Azure para o Windows PowerShell.</li></ul> |
| Notificação de expiração da palavra-passe (Quando os utilizadores são notificados da expiração da palavra-passe) |<ul><li>Valor predefinido: **14** dias (antes da palavra-passe expirar).</li><li>O valor é configurável utilizando o `Set-MsolPasswordPolicy` cmdlet.</li></ul> |
| Prazo de validade (Deixe que as palavras-passe nunca expirem) |<ul><li>Valor predefinido: **falso** (indica que a palavra-passe tem uma data de validade).</li><li>O valor pode ser configurado para contas individuais de utilizador utilizando o `Set-MsolUser` cmdlet.</li></ul> |
| Histórico de mudança de palavra-passe | A última palavra-passe *não pode* ser usada novamente quando o utilizador muda uma palavra-passe. |
| Histórico de reset de palavra-passe | A última palavra-passe *pode* ser novamente utilizada quando o utilizador reinicia uma palavra-passe esquecida. |

## <a name="administrator-reset-policy-differences"></a>Administrator reset policy differences (Diferenças da política de reposição de administrador)

Por padrão, as contas do administrador são ativadas para o reset da palavra-passe *de autosserviço* e uma forte política de reset de senha de dois prazos por defeito é aplicada. Esta política pode ser diferente da que definiu para os seus utilizadores, e esta política não pode ser alterada. Deve sempre testar a funcionalidade de reset da palavra-passe como utilizador sem quaisquer funções de administrador Azure atribuídas.

Com uma política de dois portões, os administradores não têm a capacidade de usar questões de segurança.

A política de dois portões requer duas peças de dados de autenticação, como um endereço de e-mail, aplicação autenticadora ou um número de telefone. Nas seguintes circunstâncias, aplica-se uma política de dois prazos:

* Todas as seguintes funções de administrador da Azure são afetadas:
  * Administrador da Helpdesk
  * Administrador de suporte de serviços
  * Administrador de faturação
  * Suporte parceiro Tier1
  * Suporte parceiro Tier2
  * Administrador do Exchange
  * Administrador do Skype para Empresas
  * Administrador de utilizadores
  * Escritores de diretórios
  * Administrador global ou administrador da empresa
  * Administrador do SharePoint
  * Administrador de conformidade
  * Administrador de aplicação
  * Administrador de segurança
  * Administrador privilegiado
  * Administrador do Intune
  * Administrador de serviço de procuração de aplicação
  * Administrador dinâmico 365
  * Administrador do serviço Power BI
  * Administrador de autenticação
  * Administrador de Autenticação Privilegiada

* Se tiverem decorrido 30 dias de subscrição experimental; ou
* Um domínio personalizado foi configurado para o seu inquilino AD Azure, como *contoso.com;* ou
* Azure AD Connect está sincronizando identidades do seu diretório no local

Pode desativar a utilização de SSPR para contas de administrador utilizando o [cmdlet PowerShell set-MsolCompanySettings.](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0) O `-SelfServePasswordResetEnabled $False` parâmetro desativa a SSPR para administradores.

### <a name="exceptions"></a>Exceções

Uma política de um portão requer uma peça de dados de autenticação, como um endereço de e-mail ou número de telefone. Nas seguintes circunstâncias, aplica-se uma política de um portão único:

* É dentro dos primeiros 30 dias de uma assinatura experimental; ou
* Um domínio personalizado não foi configurado para o seu inquilino AD Azure, pelo que está a usar o padrão **.onmicrosoft.com*. O domínio predefinido **.onmicrosoft.com* não é recomendado para uso de produção; e
* Azure AD Connect não sincroniza identidades

## <a name="password-expiration-policies"></a><a name="set-password-expiration-policies-in-azure-ad"></a>Políticas de expiração de palavra-passe

Um *administrador ou* administrador de *utilizador* global pode utilizar o [Módulo AD AZure do Microsoft para o Windows PowerShell](/powershell/module/Azuread/?view=azureadps-2.0) para definir as palavras-passe do utilizador para não expirar.

Também pode utilizar cmdlets PowerShell para remover a configuração de nunca expirar ou para ver quais as palavras-passe do utilizador definidas para nunca expirar.

Esta orientação aplica-se a outros fornecedores, como o Intune e o Microsoft 365, que também contam com a Azure AD para serviços de identidade e diretório. A expiração da palavra-passe é a única parte da política que pode ser alterada.

> [!NOTE]
> Apenas as palavras-passe para contas de utilizador que não sejam sincronizadas através do Azure AD Connect podem ser configuradas para não expirarem. Para obter mais informações sobre a sincronização do diretório, consulte [Connect AD com Azure AD](../hybrid/whatis-hybrid-identity.md).

### <a name="set-or-check-the-password-policies-by-using-powershell"></a>Set or check the password policies by using PowerShell (Utilizar o PowerShell para definir ou verificar as políticas de palavras-passe)

Para começar, [faça o download e instale o módulo Azure AD PowerShell](/powershell/module/Azuread/?view=azureadps-2.0) e [conecte-o ao seu inquilino AD Azure.](/powershell/module/azuread/connect-azuread?view=azureadps-2.0#examples)

Depois de instalar o módulo, utilize os seguintes passos para completar cada tarefa conforme necessário.

### <a name="check-the-expiration-policy-for-a-password"></a>Verifique a política de expiração para obter uma senha

1. Abra uma pressão PowerShell e [ligue-se ao seu inquilino Azure AD](/powershell/module/azuread/connect-azuread?view=azureadps-2.0#examples) utilizando um *administrador global* ou conta *de administrador de utilizador.*
1. Executar um dos seguintes comandos para um utilizador individual ou para todos os utilizadores:

   * Para ver se a palavra-passe de um único utilizador está definida para nunca expirar, execute o cmdlet seguinte. `<user ID>`Substitua-o pelo ID do utilizador do utilizador que pretende verificar, como *o driley \@ contoso.onmicrosoft.com:*

       ```powershell
       Get-AzureADUser -ObjectId <user ID> | Select-Object @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
       ```

   * Para ver a **Palavra-Passe nunca expira** a definição para todos os utilizadores, execute o seguinte cmdlet:

       ```powershell
       Get-AzureADUser -All $true | Select-Object UserPrincipalName, @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
       ```

### <a name="set-a-password-to-expire"></a>Definir uma senha para expirar

1. Abra uma pressão PowerShell e [ligue-se ao seu inquilino Azure AD](/powershell/module/azuread/connect-azuread?view=azureadps-2.0#examples) utilizando um *administrador global* ou conta *de administrador de utilizador.*
1. Executar um dos seguintes comandos para um utilizador individual ou para todos os utilizadores:

   * Para definir a palavra-passe de um utilizador de modo a que a palavra-passe expire, execute o cmdlet seguinte. `<user ID>`Substitua-o pelo ID do utilizador do utilizador que pretende verificar, como *o driley \@ contoso.onmicrosoft.com*

       ```powershell
       Set-AzureADUser -ObjectId <user ID> -PasswordPolicies None
       ```

   * Para definir as palavras-passe de todos os utilizadores da organização para que expirem, utilize o seguinte cmdlet:

       ```powershell
       Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies None
       ```

### <a name="set-a-password-to-never-expire"></a>Desafine uma senha para nunca expirar

1. Abra uma pressão PowerShell e [ligue-se ao seu inquilino Azure AD](/powershell/module/azuread/connect-azuread?view=azureadps-2.0#examples) utilizando um *administrador global* ou conta *de administrador de utilizador.*
1. Executar um dos seguintes comandos para um utilizador individual ou para todos os utilizadores:

   * Para definir a palavra-passe de um utilizador para nunca expirar, executar o cmdlet seguinte. `<user ID>`Substitua-o pelo ID do utilizador do utilizador que pretende verificar, como *o driley \@ contoso.onmicrosoft.com*

       ```powershell
       Set-AzureADUser -ObjectId <user ID> -PasswordPolicies DisablePasswordExpiration
       ```

   * Para definir as palavras-passe de todos os utilizadores de uma organização para nunca expirar, executar o seguinte cmdlet:

       ```powershell
       Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies DisablePasswordExpiration
       ```

   > [!WARNING]
   > As palavras-passe definidas `-PasswordPolicies DisablePasswordExpiration` para a idade ainda com base no `pwdLastSet` atributo. Com base no `pwdLastSet` atributo, se alterar a expiração para `-PasswordPolicies None` , todas as palavras-passe com `pwdLastSet` mais de 90 dias exigem que o utilizador as altere da próxima vez que iniciar sôs. Esta alteração pode afetar um grande número de utilizadores.

## <a name="next-steps"></a>Passos seguintes

Para começar com o SSPR, consulte [Tutorial: Ative os utilizadores para desbloquearem a sua conta ou redefinirem as palavras-passe utilizando o reset da palavra-passe de autosserviço do Azure Ative Directory](tutorial-enable-sspr.md).

Se você ou utilizadores tiverem problemas com SSPR, consulte [o reset da palavra-passe de autosserviço de resolução de problemas](./troubleshoot-sspr.md)