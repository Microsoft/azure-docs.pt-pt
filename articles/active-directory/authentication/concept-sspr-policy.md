---
title: Políticas de redefinição de senha de autoatendimento do Azure AD – Azure Active Directory
description: Configurar opções de política de redefinição de senha de autoatendimento do Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8f5371334fb383b15514c879ceb262fa78d7fca
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70084948"
---
# <a name="password-policies-and-restrictions-in-azure-active-directory"></a>Políticas e restrições de senha no Azure Active Directory

Este artigo descreve as políticas de senha e os requisitos de complexidade associados às contas de usuário no locatário do Azure Active Directory (Azure AD).

## <a name="administrator-reset-policy-differences"></a>Diferenças de política de redefinição de administrador

A **Microsoft impõe uma política de redefinição de senha de *dois portão* padrão forte para qualquer função de administrador do Azure** essa política pode ser diferente da que você definiu para os usuários e não pode ser alterada. Você sempre deve testar a funcionalidade de redefinição de senha como um usuário sem nenhuma função de administrador do Azure atribuída.

Com uma política de duas portas, **os administradores não têm a capacidade de usar perguntas de segurança**.

A política de duas portas requer duas partes de dados de autenticação, como um **endereço de email**, um **aplicativo autenticador**ou um **número de telefone**. Uma política de duas portas se aplica nas seguintes circunstâncias:

* Todas as seguintes funções de administrador do Azure são afetadas:
  * Administrador de assistência técnica
  * Administrador de suporte de serviço
  * Administrador de Faturação
  * Parceiro de Suporte de Escalão 1
  * Parceiro de Suporte de Escalão 2
  * Administrador do Exchange
  * Administrador do Skype para Empresas
  * Administrador de utilizadores
  * Escritores em diretórios
  * Administrador global ou administrador da empresa
  * Administrator do SharePoint
  * Administrador de Conformidade
  * Administrador de aplicações
  * Administrador de segurança
  * Administrador de Funções Privilegiadas
  * Administrador do Intune
  * Administrador do serviço de proxy de aplicativo
  * Administrador do Dynamics 365
  * Administrador de serviço do Power BI
  * Administrador de autenticação
  * Administrador de autenticação privilegiada

* Se forem decorridos 30 dias em uma assinatura de avaliação; or
* Um domínio intuitivo está presente, como contoso.com; or
* Azure AD Connect está sincronizando identidades do seu diretório local

### <a name="exceptions"></a>Exceções

Uma política de uma porta requer uma parte dos dados de autenticação, como um endereço de email *ou* número de telefone. Uma política de uma porta se aplica nas seguintes circunstâncias:

* Está dentro dos primeiros 30 dias de uma assinatura de avaliação; or
* Um domínio intuitivo não está presente (*. onmicrosoft.com); e
* Azure AD Connect não está sincronizando identidades

## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>Políticas UserPrincipalName que se aplicam a todas as contas de usuário

Cada conta de usuário que precisa entrar no Azure AD deve ter um valor de atributo UPN (nome principal de usuário) exclusivo associado à sua conta. A tabela a seguir descreve as políticas que se aplicam a contas de usuário Active Directory locais que são sincronizadas para a nuvem e para contas de usuário somente em nuvem:

| Propriedade | Requisitos de UserPrincipalName |
| --- | --- |
| Caracteres permitidos |<ul> <li>A – Z</li> <li>a - z</li><li>0 – 9</li> <li> ' \. - \_ ! \# ^ \~</li></ul> |
| Caracteres não permitidos |<ul> <li>Qualquer "\@ \" caractere que não esteja separando o nome de usuário do domínio.</li> <li>Não pode conter um caractere de ponto "." imediatamente antes\@ do símbolo "\"</li></ul> |
| Restrições de comprimento |<ul> <li>O comprimento total não deve exceder 113 caracteres</li><li>Pode haver até 64 caracteres antes do símbolo "\@ \"</li><li>Pode haver até 48 caracteres após o símbolo de "\@ \"</li></ul> |

## <a name="password-policies-that-only-apply-to-cloud-user-accounts"></a>Políticas de senha que se aplicam somente a contas de usuário de nuvem

A tabela a seguir descreve as configurações de política de senha aplicadas a contas de usuário que são criadas e gerenciadas no Azure AD:

| Propriedade | Requisitos |
| --- | --- |
| Caracteres permitidos |<ul><li>A – Z</li><li>a - z</li><li>0 – 9</li> <li>@ # $ % ^ & * - _ ! + = [ ] { } &#124; \ : ‘ , . ? / \` ~ " ( ) ;</li> <li>espaço em branco</li></ul> |
| Caracteres não permitidos | Caracteres Unicode. |
| Restrições de senha |<ul><li>Um mínimo de 8 caracteres e um máximo de 256 caracteres.</li><li>Requer três de quatro dos seguintes:<ul><li>Caracteres minúsculos.</li><li>Caracteres maiúsculos.</li><li>Números (0-9).</li><li>Símbolos (consulte as restrições de senha anteriores).</li></ul></li></ul> |
| Duração da expiração da senha (duração máxima da senha) |<ul><li>Valor padrão: **90** dias.</li><li>O valor é configurável usando o `Set-MsolPasswordPolicy` cmdlet do módulo Azure Active Directory para o Windows PowerShell.</li></ul> |
| Notificação de expiração de senha (quando os usuários são notificados da expiração da senha) |<ul><li>Valor padrão: **14** dias (antes de a senha expirar).</li><li>O valor é configurável usando o `Set-MsolPasswordPolicy` cmdlet.</li></ul> |
| Expiração de senha (as senhas já expiram) |<ul><li>Valor padrão: **false** dias (indica que a expiração de senha está habilitada).</li><li>O valor pode ser configurado para contas de usuário individuais usando o `Set-MsolUser` cmdlet.</li></ul> |
| Histórico de alterações de senha | A última senha *não pode* ser usada novamente quando o usuário altera uma senha. |
| Histórico de redefinição de senha | A última senha *pode* ser usada novamente quando o usuário redefine uma senha esquecida. |
| Bloqueio de conta | Após 10 tentativas de entrada malsucedidas com a senha incorreta, o usuário será bloqueado por um minuto. Outras tentativas de entrada incorretas bloqueiam o usuário para aumentar a duração do tempo. O [bloqueio inteligente](howto-password-smart-lockout.md) rastreia os três últimos hashes de senha inválidos para evitar incrementar o contador de bloqueios para a mesma senha. Se alguém inserir a mesma senha inválida várias vezes, esse comportamento não causará o bloqueio da conta. |

## <a name="set-password-expiration-policies-in-azure-ad"></a>Definir políticas de expiração de senha no Azure AD

Um administrador global ou administrador de usuário para um serviço de nuvem da Microsoft pode usar o Módulo Microsoft Azure AD para Windows PowerShell para definir senhas de usuário para não expirar. Você também pode usar os cmdlets do Windows PowerShell para remover a configuração de nunca expira ou para ver quais senhas de usuário estão definidas para nunca expirar. 

Essa orientação se aplica a outros provedores, como o Intune e o Office 365, que também dependem do Azure AD para serviços de identidade e diretório. A expiração da senha é a única parte da política que pode ser alterada.

> [!NOTE]
> Somente senhas para contas de usuário que não são sincronizadas por meio da sincronização de diretório podem ser configuradas para não expirar. Para obter mais informações sobre a sincronização de diretório, consulte [conectar o AD ao Azure ad](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

## <a name="set-or-check-the-password-policies-by-using-powershell"></a>Definir ou verificar as políticas de senha usando o PowerShell

Para começar, você precisa [baixar e instalar o módulo do PowerShell do Azure ad](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0). Depois de instalá-lo, você pode usar as etapas a seguir para configurar cada campo.

### <a name="check-the-expiration-policy-for-a-password"></a>Verificar a política de expiração para uma senha

1. Conecte-se ao Windows PowerShell usando suas credenciais de administrador de usuário ou administrador da empresa.
1. Execute um dos seguintes comandos:

   * Para ver se a senha de um único usuário está definida para nunca expirar, execute o seguinte cmdlet usando o UPN (por exemplo, *abril\@de contoso.onmicrosoft.com*) ou a ID de usuário do usuário que você deseja verificar:

   ```powershell
   Get-AzureADUser -ObjectId <user ID> | Select-Object @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
   ```

   * Para ver a configuração a **senha nunca expira** para todos os usuários, execute o seguinte cmdlet:

   ```powershell
   Get-AzureADUser -All $true | Select-Object UserPrincipalName, @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
   ```

### <a name="set-a-password-to-expire"></a>Definir uma senha para expirar

1. Conecte-se ao Windows PowerShell usando suas credenciais de administrador de usuário ou administrador da empresa.
1. Execute um dos seguintes comandos:

   * Para definir a senha de um usuário para que a senha expire, execute o seguinte cmdlet usando o UPN ou a ID de usuário do usuário:

   ```powershell
   Set-AzureADUser -ObjectId <user ID> -PasswordPolicies None
   ```

   * Para definir as senhas de todos os usuários na organização para que eles expirem, use o seguinte cmdlet:

   ```powershell
   Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies None
   ```

### <a name="set-a-password-to-never-expire"></a>Definir uma senha para nunca expirar

1. Conecte-se ao Windows PowerShell usando suas credenciais de administrador de usuário ou administrador da empresa.
1. Execute um dos seguintes comandos:

   * Para definir a senha de um usuário para nunca expirar, execute o seguinte cmdlet usando o UPN ou a ID de usuário do usuário:

   ```powershell
   Set-AzureADUser -ObjectId <user ID> -PasswordPolicies DisablePasswordExpiration
   ```

   * Para definir as senhas de todos os usuários em uma organização para nunca expirarem, execute o seguinte cmdlet:

   ```powershell
   Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies DisablePasswordExpiration
   ```

   > [!WARNING]
   > As senhas definidas `-PasswordPolicies DisablePasswordExpiration` como ainda age com base `pwdLastSet` no atributo. Se você definir as senhas de usuário para nunca expirarem e, em seguida, 90 + dias passarão, as senhas expirarão. Com base no `pwdLastSet` atributo, se você alterar a expiração `-PasswordPolicies None`para, `pwdLastSet` todas as senhas com mais de 90 dias exigirão que o usuário as altere na próxima vez que entrar. Essa alteração pode afetar um grande número de usuários.

## <a name="next-steps"></a>Passos Seguintes

Os artigos a seguir fornecem informações adicionais sobre a redefinição de senha por meio do Azure AD:

* [Como posso concluir uma implementação com êxito da SSPR?](howto-sspr-deployment.md)
* [Reponha ou altere a palavra-passe](../user-help/active-directory-passwords-update-your-own-password.md).
* [Registe-se na reposição personalizada de palavras-passe](../user-help/active-directory-passwords-reset-register.md).
* [Tem alguma pergunta sobre licenciamento?](concept-sspr-licensing.md)
* [Que dados são utilizados pela SSPR e que dados devem ser preenchidos por si para os seus utilizadores?](howto-sspr-authenticationdata.md)
* [Que métodos de autenticação estão disponíveis para os utilizadores?](concept-sspr-howitworks.md#authentication-methods)
* [O que é a repetição de escrita de palavras-passe e por que me deve interessar?](howto-sspr-writeback.md)
* [Como posso comunicar a atividade da SSPR?](howto-sspr-reporting.md)
* [Quais são todas as opções na SSPR e o que significam?](concept-sspr-howitworks.md)
* [Creio que algo está a funcionar incorretamente. Como posso resolver problemas da SSPR?](active-directory-passwords-troubleshoot.md)
* [Tenho uma pergunta que ainda não foi abordada](active-directory-passwords-faq.md)
