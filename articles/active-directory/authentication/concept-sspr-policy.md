---
title: Políticas de redefinição de senha de autosserviço - Diretório Ativo Azure
description: Configure Azure AD auto-service palavra-passe redefinir opções políticas de política
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd6cacae9c7af705b0de7b59e0f25f25637a5a89
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79263936"
---
# <a name="password-policies-and-restrictions-in-azure-active-directory"></a>Políticas e restrições de password sinuosas no Diretório Ativo do Azure

Este artigo descreve as políticas de password e os requisitos de complexidade associados às contas dos utilizadores no seu inquilino azure Ative Directory (Azure AD).

## <a name="administrator-reset-policy-differences"></a>Administradores redefinir diferenças de política

A **Microsoft aplica uma forte política de redefinição de *palavra-passe de dois portas* por defeito para qualquer função de administrador do Azure,** esta política pode ser diferente da que definiu para os seus utilizadores e não pode ser alterada. Deve testar sempre a funcionalidade de redefinição de palavra-passe como utilizador sem quaisquer funções de administrador Azure atribuídas.

Com uma política de dois portais, os administradores não têm a capacidade de **usar questões**de segurança.

A política de dois portais requer duas peças de dados de autenticação, tais como um endereço de **e-mail,** **aplicação autenticadora**ou um número de **telefone**. Aplica-se uma política de dois portas nas seguintes circunstâncias:

* Todas as seguintes funções de administrador do Azure são afetadas:
  * Administrador de helpdesk
  * Administrador de suporte de serviços
  * Administrador de faturação
  * Parceiro de Suporte de Escalão 1
  * Parceiro de Suporte de Escalão 2
  * Administrador de intercâmbio
  * Skype para administrador de negócios
  * Administrador de utilizadores
  * Escritores de diretórios
  * Administrador global ou administrador da empresa
  * Administrador do SharePoint
  * Administrador de conformidade
  * Administrador de candidatura
  * Administrador de segurança
  * Administrador privilegiado
  * Administrador insinado
  * Administrador de serviço de procuração de pedidos
  * Administrador da Dinâmica 365
  * Administrador de serviço power BI
  * Administrador de autenticação
  * Administrador de Autenticação Privilegiada

* Se decorridos 30 dias numa assinatura experimental; ou
* Foi configurado um domínio personalizado para o seu inquilino DaD Azure, como *contoso.com;* ou
* O Azure AD Connect está a sincronizar identidades do seu diretório no local

### <a name="exceptions"></a>Exceções

Uma política de um portão requer um pedaço de dados de autenticação, como um endereço de e-mail *ou* número de telefone. Aplica-se uma política de um portão nas seguintes circunstâncias:

* É nos primeiros 30 dias de uma assinatura de teste; ou
* Um domínio personalizado não foi configurado para o seu inquilino Azure AD, pelo que está a usar o padrão * *.onmicrosoft.com*. Note que o domínio predefinido * *.onmicrosoft.com* não é recomendado para uso da produção; e
* Azure AD Connect não está a sincronizar identidades

## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>Políticas userPrincipalName que se aplicam a todas as contas de utilizador

Todas as contas de utilizador que necessitem de iniciar sessão no Azure AD devem ter um valor de atributo exclusivo do utilizador (UPN) associado à sua conta. O quadro seguinte descreve as políticas aplicáveis tanto nas contas de utilizadores do Ative Directory no local que são sincronizadas na nuvem e nas contas de utilizadores apenas na nuvem:

| Propriedade | Requisitos de Nome Principal do Utilizador |
| --- | --- |
| Personagens permitidos |<ul> <li>A – Z</li> <li>a - z</li><li>0 – 9</li> <li> ' \. - \_! \# ^ \~</li></ul> |
| Personagens não permitidos |<ul> <li>Qualquer "\@\" personagem que não esteja a separar o nome de utilizador do domínio.</li> <li>Não pode conter um personagem de época "." imediatamente antes do símbolo "\@\"</li></ul> |
| Restrições de comprimento |<ul> <li>O comprimento total não deve exceder 113 caracteres</li><li>Pode haver até 64 caracteres antes do símbolo "\@\"</li><li>Pode haver até 48 caracteres após o símbolo "\@\"</li></ul> |

## <a name="password-policies-that-only-apply-to-cloud-user-accounts"></a>Políticas de senha que se aplicam apenas às contas de utilizadores na nuvem

O quadro seguinte descreve as definições de política de passwordaplicadas às contas de utilizador que são criadas e geridas em Azure AD:

| Propriedade | Requisitos |
| --- | --- |
| Personagens permitidos |<ul><li>A – Z</li><li>a - z</li><li>0 – 9</li> <li>@ # $ % ^ & * - _ ! + = [ ] { } &#124; \ : ‘ , . ? / \` ~ " ( )</li> <li>espaço em branco</li></ul> |
| Personagens não permitidos | Personagens unicódigo. |
| Restrições de senha |<ul><li>Um mínimo de 8 caracteres e um máximo de 256 caracteres.</li><li>Requer três em cada quatro dos seguintes:<ul><li>Personagens minúsculos.</li><li>Personagens maiúsculas.</li><li>Números (0-9).</li><li>Símbolos (ver as restrições de senha anteriores).</li></ul></li></ul> |
| Duração da validade da palavra-passe (Idade máxima da senha) |<ul><li>Valor predefinido: **90** dias.</li><li>O valor é configurável utilizando o `Set-MsolPasswordPolicy` cmdlet do Módulo de Diretório Ativo Azure para windows PowerShell.</li></ul> |
| Notificação de validade da palavra-passe (Quando os utilizadores são notificados da expiração da palavra-passe) |<ul><li>Valor predefinido: **14** dias (antes de a palavra-passe expirar).</li><li>O valor é configurável utilizando o `Set-MsolPasswordPolicy` cmdlet.</li></ul> |
| Caducidade da palavra-passe (Deixe a palavra-passe nunca expirar) |<ul><li>Valor predefinido: **falso** (indica que a palavra-passe tem uma data de validade).</li><li>O valor pode ser configurado para contas individuais de utilizador utilizando o `Set-MsolUser` cmdlet.</li></ul> |
| Histórico de mudança de palavra-passe | A última palavra-passe *não pode* ser usada novamente quando o utilizador muda uma palavra-passe. |
| Histórico de redefinição de palavra-passe | A última palavra-passe *pode* ser novamente utilizada quando o utilizador repõe uma palavra-passe esquecida. |
| Bloqueio de conta | Após 10 tentativas de entrada infrutíferas com a senha errada, o utilizador fica bloqueado durante um minuto. Outras tentativas de entrada incorretas bloqueiam o utilizador durante o tempo crescente. [O bloqueio inteligente](howto-password-smart-lockout.md) rastreia as últimas três hashes de senha para evitar incrementar o contador de bloqueio para a mesma senha. Se alguém introduzir a mesma senha má várias vezes, este comportamento não fará com que a conta se bloqueie. |

## <a name="set-password-expiration-policies-in-azure-ad"></a>Definir políticas de expiração de palavra-passe em Azure AD

Um administrador ou administrador de utilizador global para um serviço na nuvem da Microsoft pode utilizar o Módulo AD Microsoft Azure para o Windows PowerShell para definir as palavras-passe dos utilizadores para não expirar. Também pode utilizar cmdlets Do Windows PowerShell para remover a configuração de nunca expira ou para ver quais as palavras-passe do utilizador que não estão definidas para nunca expirar. 

Esta orientação aplica-se a outros prestadores, como o Intune e o Office 365, que também dependem da Azure AD para serviços de identidade e diretório. A expiração da palavra-passe é a única parte da política que pode ser alterada.

> [!NOTE]
> Apenas as palavras-passe para contas de utilizador que não sejam sincronizadas através da sincronização do diretório podem ser configuradas para não expirar. Para obter mais informações sobre sincronização de diretórios, consulte [Connect AD com Azure AD](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

## <a name="set-or-check-the-password-policies-by-using-powershell"></a>Definir ou verificar as políticas de palavra-passe utilizando o PowerShell

Para começar, precisa [de descarregar e instalar o módulo PowerShell Da AD Azure](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0). Depois de o ter instalado, pode utilizar os seguintes passos para configurar cada campo.

### <a name="check-the-expiration-policy-for-a-password"></a>Verifique a política de expiração para obter uma senha

1. Ligue-se ao Windows PowerShell utilizando as credenciais do administrador de utilizador ou do administrador da empresa.
1. Executar um dos seguintes comandos:

   * Para ver se a palavra-passe de um único utilizador está definida para nunca expirar, execute o seguinte cmdlet utilizando a UPN (por exemplo, *ode abril\@contoso.onmicrosoft.com)* ou o ID do utilizador que pretende verificar:

   ```powershell
   Get-AzureADUser -ObjectId <user ID> | Select-Object @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
   ```

   * Para ver a **Palavra-passe nunca expira** a definição para todos os utilizadores, execute o seguinte cmdlet:

   ```powershell
   Get-AzureADUser -All $true | Select-Object UserPrincipalName, @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
   ```

### <a name="set-a-password-to-expire"></a>Detete uma palavra-passe para expirar

1. Ligue-se ao Windows PowerShell utilizando as credenciais do administrador de utilizador ou do administrador da empresa.
1. Executar um dos seguintes comandos:

   * Para definir a palavra-passe de um utilizador para que a palavra-passe expire, execute o seguinte cmdlet utilizando a UPN ou o ID do utilizador:

   ```powershell
   Set-AzureADUser -ObjectId <user ID> -PasswordPolicies None
   ```

   * Para definir as palavras-passe de todos os utilizadores da organização para que expirem, utilize o seguinte cmdlet:

   ```powershell
   Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies None
   ```

### <a name="set-a-password-to-never-expire"></a>Desdefinir uma palavra-passe para nunca expirar

1. Ligue-se ao Windows PowerShell utilizando as credenciais do administrador de utilizador ou do administrador da empresa.
1. Executar um dos seguintes comandos:

   * Para definir a palavra-passe de um utilizador para nunca expirar, execute o seguinte cmdlet utilizando a UPN ou o ID do utilizador:

   ```powershell
   Set-AzureADUser -ObjectId <user ID> -PasswordPolicies DisablePasswordExpiration
   ```

   * Para definir as palavras-passe de todos os utilizadores de uma organização que nunca expirem, execute o seguinte cmdlet:

   ```powershell
   Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies DisablePasswordExpiration
   ```

   > [!WARNING]
   > As palavras-passe definidas para `-PasswordPolicies DisablePasswordExpiration` ainda envelhecem com base no atributo `pwdLastSet`. Se definir as palavras-passe do utilizador para nunca expirar e, em seguida, mais de 90 dias passarem, as palavras-passe expiram. Com base no atributo `pwdLastSet`, se alterar a expiração para `-PasswordPolicies None`, todas as palavras-passe com uma `pwdLastSet` com mais de 90 dias exigem que o utilizador as altere da próxima vez que iniciar a sua inscrição. Esta alteração pode afetar um grande número de utilizadores.

## <a name="next-steps"></a>Passos Seguintes

Os seguintes artigos fornecem informações adicionais sobre o reset de palavra-passe através do Azure AD:

* [Como posso concluir uma implementação com êxito da SSPR?](howto-sspr-deployment.md)
* [Reponha ou altere a palavra-passe](../user-help/active-directory-passwords-update-your-own-password.md).
* [Registe-se na reposição personalizada de palavras-passe](../user-help/active-directory-passwords-reset-register.md).
* [Tem alguma pergunta sobre licenciamento?](concept-sspr-licensing.md)
* [Que dados são utilizados pela SSPR e que dados devem ser preenchidos por si para os seus utilizadores?](howto-sspr-authenticationdata.md)
* [Que métodos de autenticação estão disponíveis para os utilizadores?](concept-sspr-howitworks.md#authentication-methods)
* [O que é a repetição de escrita de palavras-passe e por que me deve interessar?](howto-sspr-writeback.md)
* [Como posso comunicar a atividade da SSPR?](howto-sspr-reporting.md)
* [Quais são todas as opções na SSPR e o que significam?](concept-sspr-howitworks.md)
* [Acho que algo está partido. Como posso resolver problemas com a SSPR?](active-directory-passwords-troubleshoot.md)
* [Tenho uma pergunta que ainda não foi abordada](active-directory-passwords-faq.md)
